# Advanced IDOR / BOLA Methodology (2025/2026)

This methodology synthesizes the most advanced, unconventional IDOR bypass techniques from top-tier bug bounty reports, moving far beyond basic ID swapping.

## 1. Identifier Obfuscation & UUID Mastery
*   **The "Two-Endpoint" UUID Discovery:** UUIDs are unguessable, but developers often leak them. Find a "low-privilege" endpoint (`/api/v1/users/search`, `/api/v1/team/members`, or GraphQL queries) that returns UUIDs. Use those leaked UUIDs in sensitive endpoints.
*   **The Sandwich Attack (UUIDv1):** If the target uses UUIDv1 (timestamp-based), create two resources on your own account in quick succession. The UUIDs will have a static prefix/suffix with only the timestamp segment changing. You can "sandwich" a victim's UUID by brute-forcing the small timestamp gap.
*   **Zero/Default UUID Fallbacks:** If the server fails to parse an ID, it may fall back to `00000000-0000-0000-0000-000000000000`. Test this "Zero UUID" to see if it bypasses ownership checks.
*   **Unicode Normalization:** Use visually similar characters that normalize to the same value after security checks. (e.g., using `U+FF11` "１" instead of "1"). The filter sees a special character, but the DB queries `1`.

## 2. Parser Differentials & Smuggling
*   **Numeric Representation Smuggling (Scientific Notation):** Exploit inconsistencies between WAFs/APIs and databases. If `id=123` is blocked (403), try `id=1.23e2`, `id=1230e-1`, or `id=123.00`.
*   **Type Confusion:** Change the JSON type. If the API expects an integer, send a string: `{"id": "123"}`. If it expects a string, send an integer or boolean.
*   **JSON Array/Object Wrapping (NoSQL Hybrids):** Wrap the ID in an array or object to bypass simple string-matching filters.
    *   `{"id": ["<victim_id>"]}`
    *   `{"id": {"$in": ["<victim_id>"]}}`
*   **HTTP Parameter Pollution (HPP):** Supply multiple instances to confuse the parser.
    *   `GET /api/profile?id=<attacker_id>&id=<victim_id>` (Backend authorizes first ID, DB queries second).

## 3. Architecture & Logic Flaws
*   **Session Misbinding (Flow-Based IDOR):** Identified in $10k+ HackerOne bounties (e.g., Mozilla Account Deletion). An attacker starts a multi-step flow (like account deletion or password reset) using their own account to generate a valid "flow token". In the final API call, they swap their `user_id` for the Victim's `user_id`. The server trusts the flow token but fails to verify if the token actually belongs to the provided `user_id`.
*   **JWT Binding Bypass:** The backend validates the JWT signature perfectly but fails to verify if the `user_id` parameter in the POST body matches the `sub` claim inside the JWT. Always attempt to alter the JSON body while providing your own valid JWT.
*   **Cross-Tenant Privilege Escalation (The PayPal Business Hack):** When adding users to a team/business account, an endpoint like `POST /api/v1/users` might take a `merchant_id` or `org_id` in the body. By changing this to a Victim's `org_id`, you can add your attacker email as an Admin to *their* organization.
*   **"Access Code" Separation Logic:** An endpoint requires `reservation_id` and `access_code`. The server verifies that both exist in the database but fails to verify they belong to the *same* object. Use the Victim's `reservation_id` and the Attacker's valid `access_code`.
*   **Second-Order IDOR:** Modify your own `user_id` or `org_id` in a low-risk "Profile Settings" area to the Victim's ID. Wait for or trigger a background process (e.g., "Generate Monthly Invoice" or "Export CSV"). The async worker pulls the Victim's data using your maliciously stored ID.
*   **GraphQL Nested Queries & Mutations:** Authorization is often only checked at the top-level root query. 
    *   **Mutations:** Endpoints like `CreateOrUpdateHackerCertification` or `DeleteCampaign` might take an `id` argument in the variables. Developers often forget to verify ownership inside the resolver.
    *   **Batching:** Use query batching (`[{"query": "..."}, {"query": "..."}]`) to bypass rate limits while brute-forcing IDs.

## 4. Blind IDOR Execution
*   **Out-of-Band (OOB) Verification:** When modifying a resource (PUT/DELETE) returns a generic `{"success": true}` regardless of whether it worked, you MUST verify the change via another channel.
    *   Log into the Victim account (Account B) to see if the state changed.
    *   Check for triggered emails (e.g., changing a Victim's email to one you control).
    *   Measure response time (Timing Attack): 50ms = Authorized but ID not found; 10ms = Unauthorized.

## 5. Routing & Protocol Bypasses
*   **API Version Downgrading (Zombie APIs):** Developers often harden the latest API version (e.g., `/api/v3/profile/123`) but leave legacy endpoints active for backward compatibility. Always test `/api/v1/` or `/api/v2/` if the current version blocks your IDOR attempt.
*   **Method Overriding & Verb Tampering:** If `PUT` is blocked, send `POST` with the header `X-HTTP-Method-Override: PUT` or `_method=PUT` in the body.
*   **Path Traversal Normalization:** Bypass strict routing rules by adding traversal characters.
    *   `/api/v1/users/attacker_id/..;/victim_id` (Spring Boot bypass).
*   **Extension Manipulation:** Append `.json`, `.xml`, `.csv` to the endpoint to hit different route handlers that might lack authentication middlewares.

## 6. Advanced Header & Data Smuggling
*   **Identity Spoofing Headers:** The backend may trust specific headers over the session cookie. Inject these headers with the Victim's ID:
    *   `X-User-Id: <victim_id>`
    *   `X-Account-Id: <victim_id>`
    *   `X-Authenticated-User: <victim_id>`
*   **Gateway / WAF Bypasses:** If the IDOR endpoint is blocked by a reverse proxy, try reaching it via:
    *   `X-Original-URL: /api/v1/admin/delete/<victim_id>`
    *   `X-Rewrite-URL: /api/v1/admin/delete/<victim_id>`
*   **Wildcard Injection:** Instead of a numeric ID or UUID, send wildcards to bypass strict validation or force the database to dump records.
    *   `GET /api/users/%`
    *   `GET /api/users/*`