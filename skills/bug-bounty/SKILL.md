---
name: bug-bounty
description: "A comprehensive skill for bug bounty hunting, including reconnaissance, vulnerability scanning, and reporting. It provides a structured workflow for security professionals to discover and report vulnerabilities in web applications. Use this for recon, hunting, and reporting."
---
# Bug Bounty Hunting Skill

This skill provides a comprehensive workflow for bug bounty hunting, covering reconnaissance, vulnerability analysis, and reporting.

## Workflow

The bug bounty process is divided into three main phases:

1.  **Reconnaissance (`/recon`)**: Gather information about the target.
2.  **Hunting (`/hunt`)**: Actively search for vulnerabilities.
3.  **Reporting (`/report`)**: Document and report findings.

### 1. Reconnaissance

Use the `/recon <target>` command to initiate the reconnaissance phase. This involves:

*   **Subdomain Enumeration**: Discover subdomains using tools like `subfinder` and `amass`.
*   **Port Scanning**: Identify open ports and services using `nmap`.
*   **Web Discovery**: Find URLs and endpoints using `httpx` and `gau`.

### 2. Hunting

Use the `/hunt <target>` command to start the hunting phase. This involves:

*   **Vulnerability Scanning**: Use `nuclei` with custom templates to scan for known vulnerabilities.
*   **Fuzzing**: Use `ffuf` to discover hidden files and directories.
*   **Manual Testing**: Perform manual tests for common vulnerabilities like XSS, SQLi, and IDOR.

### 3. GraphQL Hunting (`/hunt_graphql`)

Use the `/hunt_graphql <target>` command to focus on GraphQL-specific vulnerabilities. This involves:

*   **Schema Reconnaissance**: Introspection mapping and bypasses (e.g., `__schema` prepending).
*   **Auth & Authz Testing**: Permission checks on queries/mutations and session invalidation.
*   **Object IDOR**: Exploiting the `Node` interface and cross-tenant access.
*   **Injection Testing**: Probing for SQLi, SSRF, XSS, and ReDoS in resolvers.
*   **DoS & Rate Limiting**: Complexity attacks and cost-calculation bypasses.

### 4. Account Takeover Hunting (`/hunt_accounttakeover`)

Use the `/hunt_accounttakeover <target>` command to systematically test for account takeover vulnerabilities. This involves:

*   **Password Reset Manipulation**: Testing for 0-click reset flows, token leakage, and rate limit bypasses.
*   **OAuth & SSO Integrity**: Auditing callback validation and CSRF protections in social logins.
*   **Session & Cookie Theft**: Probing for leakage via Request Smuggling or Cache Deception.
*   **IDOR in Account Management**: Testing for unauthorized updates to email, mobile, or passwords.

### 5. API Hunting (`/hunt_api`)

Use the `/hunt_api <target>` command to focus on RESTful and web API vulnerabilities. This involves:

*   **Credential Discovery**: Searching for leaked keys in JS, APKs, and GitHub.
*   **Auth & Access Control**: Auditing internal endpoints and deactivation bypasses.
*   **BOLA/IDOR**: Testing for cross-account data leakage via ID manipulation.
*   **Injection & SSRF**: Probing for SQLi/NoSQLi and server-side request forgery in integrations.

### 6. Reporting

Use the `/report` command to generate a detailed report of your findings. The report should include:

*   **Vulnerability Title**: A clear and concise title.
*   **Description**: A detailed description of the vulnerability.
*   **Impact**: The potential impact of the vulnerability.
*   **Steps to Reproduce**: A step-by-step guide to reproduce the vulnerability.
*   **Remediation**: Suggestions for fixing the vulnerability.

### 7. Authentication Hunting (/hunt_auth)

Use the `/hunt_auth <target>` command to focus on authentication bypasses and credential flaws. This involves:

*   **Pre-auth RCE & Redirect Bypass**: Exploiting critical services (VPNs, Jenkins, ForgeRock) and intercepting auth-redirects.
*   **Subdomain Takeover for Auth**: Hijacking subdomains to bypass domain-locked authentication or capture session cookies.
*   **MFA & OTP Verification**: Probing for rate-limit bypasses and reusable tokens in 2FA/MFA flows.
*   **SSO & Token Integrity**: Auditing SAML, JWT, and OAuth implementations for logic flaws and signature bypasses.

### 8. Authorization Hunting (/hunt_authorization)

Use the `/hunt_authorization <target>` command to focus on broken access control and privilege escalation. This involves:

*   **Email & Domain Bypasses**: Probing signup/invitation flows to bypass email confirmation or SAML domain enforcement.
*   **Admin Dashboard Discovery**: Identifying exposed administrative panels (PhpDebugBar, Spring Actuator) and testing for default credentials.
*   **Vertical & Horizontal Escalation**: Verifying if low-privileged roles can perform administrative actions or access cross-tenant data.
*   **BOLA/IDOR in Resource Management**: Manipulating IDs and UUIDs to read, modify, or delete resources (PII, invoices) belonging to others.

### 9. Business Logic Hunting (/hunt_businesslogic)

Use the `/hunt_businesslogic <target>` command to focus on flaws in application logic and workflows. This involves:

*   **Financial & Price Manipulation**: Testing negative quantities, currency arbitrage, and price tampering in checkout flows.
*   **Response & State Manipulation**: Bypassing client-side checks (2FA, premium access) by intercepting and modifying server responses.
*   **Workflow Integrity Bypasses**: Circumventing OTP, biometrics, or email verification via sequence flaws or account recovery logic.
*   **Resource & Template Abuse**: Exploiting "Clone" or "Template" features to leak private data across project boundaries.
*   **Logic-Based SSRF & Data Leakage**: Probing webhooks, PDF generators, and audit tools for unauthorized internal access or data exposure.

### 10. CSRF Hunting (/hunt_csrf)

Use the `/hunt_csrf <target>` command to focus on Cross-Site Request Forgery vulnerabilities. This involves:

*   **Integration & Linked Accounts**: Testing for missing CSRF protection when connecting payment providers (PayPal), social logins, or Slack/Jira integrations.
*   **Protection Bypasses**: Probing for weaknesses in token validation, such as bypasses via null bytes (%00), content-type spoofing, or GET-based mutations.
*   **Chained Attacks**: Leveraging CSRF to achieve Stored XSS, Self-XSS to Persistent XSS escalation, or full Account Takeover (ATO).
*   **API & GraphQL CSRF**: Testing for CSRF in authenticated API endpoints, including JSON/REST requests and GraphQL mutations triggered via GET.
*   **Mobile & Deep Link CSRF**: Identifying insecure deep links that trigger sensitive actions without user consent.

### 11. DoS Hunting (/hunt_dos)

Use the `/hunt_dos <target>` command to focus on availability and resource exhaustion flaws. This involves:

*   **Web Cache Poisoning DoS**: Poisoning CDNs/caches to serve error pages or block assets.
*   **Algorithmic Complexity**: Probing for ReDoS, hash collisions, and O(n^2) logic.
*   **GraphQL & API Exhaustion**: Testing circular queries, aliasing, and large JSON payloads.
*   **Protocol & Header Stress**: Exploiting HTTP/2 Continuation, Range headers, and Cookie Bombs.

### 12. File Reading Hunting (/hunt_filereading)

Use the `/hunt_filereading <target>` command to focus on Arbitrary File Read and Path Traversal vulnerabilities. This involves:

*   **LFI via Document Converters**: Testing HTML/Markdown injection in PDF/Image export features.
*   **Directory Traversal in File Ops**: Probing download, upload, and ZIP extraction features for `../` sequences.
*   **Nginx Alias & Proxy Traversal**: Identifying misconfigured Nginx directives allowing directory escape.
*   **Windows-Specific Traversal**: Exploiting UNC paths, drive names, and reserved device names (CON, PRN).
*   **SSRF-to-LFI Escalation**: Using Full Read SSRF to access local files via `file://` or internal metadata.

### 13. IDOR Hunting (/hunt_idor)

Use the `/hunt_idor <target>` command to focus on Insecure Direct Object Reference vulnerabilities. This involves:

*   **Cross-Tenant Data Probing**: Manipulating `organization_id`, `project_id`, or `shop_id` to access data from other accounts.
*   **GraphQL Mutation Tampering**: Testing state-changing mutations (e.g., `UpdateUser`, `DeleteComment`) for missing authorization.
*   **Financial & Order Manipulation**: Intercepting and modifying `order_id` or `payment_id` to view private transaction details.
*   **PII & Metadata Enumeration**: Probing numeric IDs and UUIDs to bulk-leak sensitive user info (email, phone, address).
*   **Administrative Logic Bypasses**: Accessing mod logs, audit trails, or administrative dashboards via unvalidated ID parameters.

### 14. Information Disclosure Hunting (/hunt_infodisclosure)

Use the `/hunt_infodisclosure <target>` command to focus on data leakage and sensitive info exposure. This involves:

*   **API & RPC Parameter Probing**: Testing for PII leakage in RPC calls and JSON/REST responses.
*   **Sensitive File Discovery**: Finding publicly accessible `.env`, `.git`, `.htaccess`, and `.dockerignore` files.
*   **Debug & Monitoring Exploitation**: Identifying enabled debug modes (Django, Laravel) and exposed monitoring endpoints (Sentry, metrics).
*   **Cloud & Storage Misconfigs**: Discovering public S3 buckets and insecure Salesforce objects.
*   **CORS & Browser-Based Leaks**: Auditing for cross-origin data theft and Referer-based leakage.

## Available Commands

*   `/recon <target>`: Perform reconnaissance on a target.
*   `/hunt <target>`: Hunt for vulnerabilities on a target.
*   `/hunt_graphql <target>`: Specialized GraphQL vulnerability research.
*   `/hunt_accounttakeover <target>`: Targeted Account Takeover (ATO) research.
*   `/hunt_api <target>`: Specialized REST API and Web API vulnerability research.
*   `/hunt_auth <target>`: Specialized Authentication and Credential vulnerability research.
*   `/hunt_authorization <target>`: Specialized Broken Access Control and Privilege Escalation research.
*   `/hunt_businesslogic <target>`: Specialized Business Logic and Workflow vulnerability research.
*   `/hunt_csrf <target>`: Specialized Cross-Site Request Forgery research.
*   `/hunt_dos <target>`: Specialized Denial of Service vulnerability research.
*   `/hunt_filereading <target>`: Specialized Arbitrary File Read and Path Traversal research.
*   `/hunt_idor <target>`: Specialized Insecure Direct Object Reference research.
*   `/hunt_infodisclosure <target>`: Specialized Information Disclosure and Data Leakage research.
*   `/report`: Generate a bug bounty report.
*   `/validate <finding>`: Validate a potential vulnerability.
*   `/autopilot`: Run the entire bug bounty workflow automatically.

## Skill Tree: hunt_graphql

*   **GraphQL Schema Reconnaissance**: Perform introspection to map types, queries, and mutations; bypass introspection blocks using techniques like `__schema` prepending or WebSockets.
*   **GraphQL Auth/Authz Testing**: Verify permission models for `Query` and `Mutation` types; check for broken session invalidation and unauthorized access by deactivated accounts.
*   **GraphQL Object IDOR**: Exploit the `Node` interface or specific ID-based parameters to access objects across tenants or accounts.
*   **GraphQL Injection Testing**: Probe parameters for SQLi, SSRF, XSS, and ReDoS within GraphQL resolvers and backend integrations.
*   **GraphQL DoS & Rate Limit Bypass**: Exploit query complexity, aliasing, or cost-calculation flaws (e.g., negative cost) to bypass rate limits or cause DoS.
*   **GraphQL Data Leakage Analysis**: Search for sensitive fields (emails, private comments, metadata) exposed in object types or through filters and search aggregates.
*   **GraphQL Logic Flaws & Race Conditions**: Identify inconsistencies between GraphQL and REST implementations or within complex mutation logic that lead to privilege escalation or persistent access.

## Skill Tree: hunt_accounttakeover

*   **Password Reset Manipulation**: Test for 0-click reset flows, token leakage (Host header poisoning, Referer leakage), lack of rate limiting on tokens (brute force), and improper token invalidation after email changes.
*   **OAuth & SSO Integrity**: Audit OAuth callback validation for open redirects, verify state parameter implementation to prevent CSRF, and identify misconfigurations in social login providers (e.g., Apple, Google OneTap).
*   **Session & Cookie Theft**: Probe for session cookie leakage via HTTP Request Smuggling, Web Cache Deception, and insecure browser features (e.g., referrer leakage, JS logging).
*   **Account Linking & Security CSRF**: Identify missing CSRF protection when connecting third-party accounts (Github, Yahoo, etc.) or updating critical security fields (email, mobile, security questions).
*   **IDOR in Auth Workflows**: Test for IDOR on endpoints managing sensitive account updates, such as email changes, password resets, or SCIM provisioning.
*   **Mobile ATO Exploitation**: Analyze insecure Android Intent handling, deep link vulnerabilities for 1-click takeovers, and universal XSS in Webviews leading to token theft.
*   **Administrative & Logic Bypasses**: Discover exposed administrative interfaces (Spring Actuator, PhpDebugBar) and registration logic flaws (e.g., duplicate email handling) that lead to privilege escalation or account takeover.
*   **Cache-Based ATO Attack**: Exploit Web Cache Deception to leak CSRF/session tokens and Cache Poisoning to inject malicious scripts that capture credentials or hijack sessions.

## Skill Tree: hunt_api

*   **API Credential & Key Discovery**: Search for leaked API keys and secrets in public repositories, JavaScript bundles, mobile app binaries (APK/IPA), and error messages or logs.
*   **API Auth & Access Control Auditing**: Identify unauthenticated internal/test endpoints; test for broken access control after account deactivation and bypass restrictions using custom HTTP methods (e.g., `_method=GET`) or URL manipulation.
*   **BOLA/IDOR in API Endpoints**: Exploit insecure direct object references to access private data (PII, messages, videos) by manipulating IDs in RESTful paths or JSON bodies.
*   **API-Specific Injection Probing**: Test for SQLi, NoSQLi, and command injection (e.g., Git flag injection) within API parameters and search aggregates.
*   **SSRF via API Gateways**: Probe for Server-Side Request Forgery through file export, link preview, and integration APIs that interact with internal or external resources.
*   **API Rate Limit & DoS Testing**: Bypass rate limits using header spoofing (e.g., `X-Forwarded-For`); exploit resource-heavy endpoints (e.g., search, decompression, large uploads) to cause service denial.
*   **API Logic & Race Conditions**: Identify race conditions in state-changing mutations (creation, updates) and analyze logic flaws in OAuth/SSO flows and mass assignment during object creation.
*   **Cross-Origin & CSRF in APIs**: Audit CORS configurations for cache poisoning; test for CSRF in state-changing API requests, including JSON-based endpoints and legacy API versions.

## Skill Tree: hunt_auth

*   **Pre-auth RCE & Redirect Bypass**: Identify critical services (VPNs, Jenkins, ForgeRock) vulnerable to pre-auth exploits; bypass auth by stopping/blocking redirects on administrative paths.
*   **Subdomain Takeover for Auth**: Exploit abandoned subdomains to bypass SSO/OAuth restrictions or steal session cookies through subdomain-specific configurations.
*   **MFA & OTP Verification Bypasses**: Circumvent 2FA/MFA via reusable codes, rate-limit bypass on OTP endpoints, or manipulation of the account recovery/registration flow.
*   **Sensitive Action Auth Bypasses**: Exploit missing password confirmation when changing sensitive account details (email, phone, 2FA settings) or updating credentials.
*   **SSO, SAML & JWT Integrity**: Probe for SAML response manipulation, insecure JWT signing (e.g., public key exploitation), and misconfigured SSO integrations (OneLogin, OAuth).
*   **Auth Token & Credential Discovery**: Discover exposed tokens in CI/CD logs, public Docker images, Spring Actuator endpoints, and local app installations.
*   **Protocol-Level Auth Faults**: Identify state leaks in Digest/NTLM authentication during connection reuse; exploit insecure LDAP/Basic Auth implementations.
*   **Login CSRF & Response Manipulation**: Bypass authentication via client-side response manipulation; exploit Login CSRF to link attacker-controlled accounts.

## Skill Tree: hunt_authorization

*   **Email & Domain Enforcement Bypasses**: Probe signup and invitation flows to bypass email confirmation (e.g., via SSO, OAuth, or SAML) and escalate privileges or join unauthorized organizations.
*   **Administrative Interface & Dashboard Discovery**: Identify exposed administrative panels, debug bars (PhpDebugBar), and management consoles; test for default credentials or unauthenticated access to sensitive functions.
*   **Horizontal & Vertical Privilege Escalation**: Verify if low-privileged roles (operators, collaborators, staff) can perform administrative actions, modify system settings, or access cross-tenant data.
*   **BOLA/IDOR in Resource Management**: Manipulate object identifiers (IDs, UUIDs, SOURCE_DOCUMENT_ID) to read, modify, or delete resources (invoices, orders, private comments, PII) belonging to others.
*   **OAuth, SSO & SAML Integration Auditing**: Audit authentication flows for `redirect_uri` manipulation, authorization code theft, CSRF on authorization endpoints, and SAML response spoofing.
*   **Unauthorized Information Disclosure Analysis**: Search for sensitive data (tax docs, bank accounts, secret keys, private videos) exposed to unauthorized roles through API endpoints or improperly secured files.
*   **Admin-Specific Injection & Stored XSS**: Probe administrative parameters for SQLi and Stored/Blind XSS that could lead to full database access or session hijacking of high-privileged users.
*   **Permission Model & OS-Level ACL Bypasses**: Identify flaws in permission models or OS-level ACLs (path traversal, UDS binding, symbolic links) to gain unauthorized file system access or escalate to root/system.
*   **Authorization Logic & Race Condition Probing**: Exploit race conditions in state-changing mutations (e.g., repo transfers, user conversions) to retain unauthorized access or bypass permission checks.

## Skill Tree: hunt_businesslogic

*   **Financial & Price Manipulation**: Test for negative quantities, currency arbitrage, and fractional value tampering in checkout, subscription, and payout flows to obtain services for free or reduced prices.
*   **Response & State Manipulation**: Bypass client-side security controls (2FA, premium features, admin panels) by manipulating HTTP response codes, JSON bodies (e.g., changing `is_admin: false` to `true`), or cookie values.
*   **Workflow & Integrity Bypasses**: Circumvent logical sequences such as OTP verification, biometrics, and email confirmation by abusing "forgot password" flows, session manipulation, or registration oversights.
*   **Resource & Template Exploitation**: Abuse "Template" or "Clone" functionalities to exfiltrate private metadata, confidential issues, snippets, or repository data from unauthorized projects or tenants.
*   **Logic-Based SSRF & XXE Probing**: Identify SSRF in webhook configurations, PDF generators, and file upload handlers; test for XXE in site audit tools or XML-based import/export features.
*   **Side-Channel & Functional Data Leakage**: Extract sensitive information (inactive products, internal user IDs, private comments) through logical flaws in search, "like" functions, report-as-abuse, or hover metadata.
*   **Race Condition & TOCTOU Probing**: Identify Time-of-Check to Time-of-Use vulnerabilities in file uploads, resource creation, and state-changing API calls that allow persistent unauthorized access.
*   **Cache-Based Logic Attacks**: Exploit URL path manipulation or host header injection to poison caches, redirecting users to malicious assets or leaking sensitive affiliate/session data.
*   **Administrative & Logic Escalation**: Discover elevation of privilege via insecure PATCH methods, unvalidated administrative nonces, or improper handling of "Remember Me" and session persistence.

## Skill Tree: hunt_csrf

*   **Integration & Account Linking CSRF**: Audit "Connect" features (PayPal, GitHub, Yahoo, Facebook) and third-party integrations (Slack, Jira) for missing state parameters or CSRF protections that lead to account takeover.
*   **CSRF Protection Bypass Techniques**: Test for bypasses using null bytes (%00) in state parameters, spoofing `Content-Type` headers (e.g., to `text/plain`), and executing sensitive mutations via GET requests (including GraphQL).
*   **Chained CSRF Exploitation**: Leverage CSRF to inject malicious payloads into profiles (Stored XSS), trigger Self-XSS, or perform critical account actions like password changes, email updates, and account deletion.
*   **Web Cache Deception & CSRF**: Exploit Web Cache Deception to leak CSRF tokens from pages missing `no-cache` headers, enabling subsequent authenticated attacks.
*   **JSON & API CSRF Probing**: Test for CSRF in authenticated POST/PUT/PATCH endpoints that lack custom headers (like `X-Requested-With`) or perform improper validation of the `Origin` and `Referer` headers.
*   **Mobile Deeplink & QR Code CSRF**: Identify insecure Android/iOS deep links or QR code login flows that allow attackers to perform actions (e.g., follow, post, donate session) on behalf of the user.
*   **Login & Logout CSRF**: Audit login forms for lack of protection (allowing attacker-controlled account linking) and logout endpoints (allowing forced user session termination).
*   **Administrative Panel CSRF**: Target management consoles and administrative dashboards to resume/pause runners, modify server configurations, or escalate user privileges via unvalidated requests.
*   **Flash & Legacy Cross-Domain CSRF**: Probe for CSRF using Flash-based cross-domain requests or legacy techniques like 307 redirects to bypass modern browser protections.

## Skill Tree: hunt_dos

*   **Web Cache Poisoning DoS**: Identify unkeyed inputs (headers, parameters) that can be used to poison the cache with error responses, incorrect CORS headers, or redirected assets.
*   **Algorithmic Complexity (ReDoS/Hash Collisions)**: Probe regex-heavy endpoints (search, validation) and hash-map implementations with crafted strings to trigger CPU spikes.
*   **GraphQL Resource Exhaustion**: Exploit circular introspection, deep nesting, and mutation aliasing to bypass query cost limits and consume backend resources.
*   **HTTP/2 & Protocol-Level Stress**: Test for CONTINUATION floods, header compression exploits (HPACK), and stream/connection limit bypasses in modern web servers.
*   **Resource Management & OOM**: Send massive payloads (JSON, Markdown, XML), large files, or many concurrent requests to trigger Out-of-Memory (OOM) crashes or disk exhaustion.
*   **Cookie Bomb & Header Overflow**: Inject large numbers of cookies or massive header values to exceed server limits, causing permanent DoS for specific users (Cookie Bomb).
*   **Service-Specific DoS (WordPress/RPC)**: Abuse legacy endpoints like `xmlrpc.php`, `wp-cron.php`, and exposed RPC/debug interfaces for resource amplification or service crashes.
*   **Application-Specific Logic DoS**: Identify features with recursive inclusion (templates, Mermaid), long string processing (passwords, usernames), or unvalidated hyperlinking.

## Skill Tree: hunt_filereading

*   **LFI/RFI via Document Converters**: Identify and exploit HTML/Markdown injection in PDF/Image export features to read local files via `<iframe src="file:///etc/passwd">` or similar tags.
*   **Directory Traversal in File Operations**: Probe filename and path parameters in attachment, download, and file-preview features for `../` sequences to access arbitrary files.
*   **ZIP/Archive Path Traversal**: Exploit insecure ZIP extraction routines that do not sanitize filenames, allowing attackers to overwrite sensitive files or read files outside the extraction directory.
*   **Nginx Alias & Proxy Traversal**: Identify misconfigured `alias` or `proxy_pass` directives that allow directory traversal via `%2e%2e/`, `..;/`, or trailing slash inconsistencies.
*   **Symlink Exploitation for File Read**: Use symbolic links within uploaded archives or via specific file system interactions to point to and read sensitive host files (e.g., `/etc/shadow`, `.aws/credentials`).
*   **Windows-Specific Path Traversal**: Exploit Windows-specific flaws like UNC paths (`\\attacker\share`), drive name handling (`C:`), and reserved device names (CON, PRN, AUX) to bypass sanitization.
*   **SSRF-to-LFI Escalation**: Leverage Full Read SSRF to access local files using `file://` or `netdoc://` protocols or by targeting internal metadata services (169.254.169.254).
*   **LFI via Parameter Manipulation**: Probe language (`lang`), theme, and template parameters for path traversal payloads that lead to arbitrary file inclusion or information disclosure.
*   **Full Path Disclosure (FPD) Exploitation**: Leverage FPD from error messages or debug logs to construct precise absolute path payloads for LFI and directory traversal attacks.
*   **Container & Worker Escape**: Identify vulnerabilities in containerized environments (e.g., through insecure mounts or debug interfaces) that allow reading files from the host machine.

## Skill Tree: hunt_idor

*   **Cross-Tenant Data Access Probing**: Manipulate `organization_id`, `project_id`, `shop_id`, and other tenant-specific identifiers in REST/GraphQL requests to access or modify data belonging to other accounts.
*   **GraphQL Mutation Manipulation**: Test state-changing mutations (e.g., `CreateOrUpdateHackerCertification`, `UpdateAtlasApplicationPerson`) for missing or improper authorization checks on object IDs.
*   **Financial & Transaction Tampering**: Intercept and modify `order_id`, `payment_id`, or `card_id` in checkout and billing flows to view private transaction details or use unauthorized payment methods.
*   **Account Management IDOR**: Probe endpoints managing sensitive account actions (e.g., `/updateUser`, `/deleteAccount`, `/changeEmail`) for vulnerabilities that allow unauthorized modification of victim profiles.
*   **Resource Enumeration & Bulk Disclosure**: Perform bulk lookups of numeric IDs and UUIDs to exfiltrate sensitive PII (email, phone, address) and metadata (inactive products, private comments).
*   **Attachment & Private Document Disclosure**: Discover and exploit IDOR in file-download and attachment-preview features (e.g., `/download?id=`) to exfiltrate private documents, PII, and PHI.
*   **Method-Based Protection Bypass**: Swapping HTTP methods (e.g., GET to POST/PUT) or headers to evade IDOR protection mechanisms that only validate specific request types.
*   **Administrative & Logic Bypass**: Target administrative endpoints, mod logs, and unreleased features by manipulating ID parameters to gain unauthorized access to high-privileged functions.
*   **Session & ID Misbinding**: Verify that object IDs provided in the request body or parameters are strictly validated against the authenticated session user to prevent cross-user data manipulation.

## Skill Tree: hunt_infodisclosure

*   **API & RPC Parameter Probing**: Test for PII leakage in RPC calls and JSON/REST responses by manipulating identifiers (e.g., `userUuid`) and probing for unauthenticated internal endpoints.
*   **Insecure Deep Link Analysis**: Identify mobile deep links that expose sensitive user data, session tokens, or transaction details to unauthorized applications or logs.
*   **Web Cache Poisoning for Data Leakage**: Exploit unkeyed inputs to poison caches and leak sensitive user-specific data, session headers, or CSRF tokens to unauthorized actors.
*   **Exposed Cloud & Storage Buckets**: Discover public Amazon S3 buckets, misconfigured Google Drive links, and insecure Salesforce objects containing private user images, documents, and medical records.
*   **Sensitive File & Directory Discovery**: Search for publicly accessible `.env`, `.git`, `.htaccess`, `.dockerignore`, `.DS_Store`, and `composer.lock` files that reveal secrets, credentials, and internal architecture.
*   **Debug Interface & Monitoring Exploitation**: Identify enabled debug modes (Django, Laravel, Spring Actuator) and monitoring endpoints (Sentry, expvar, metrics, Splunk) for environment and system disclosure.
*   **CORS & JSONP Data Theft**: Audit CORS policies for overly permissive configurations and JSONP callbacks for sensitive data exfiltration in cross-origin requests.
*   **IDOR-Chained PII Disclosure**: Chain insecure direct object references to perform bulk enumeration of sensitive fields like emails, phone numbers, SSNs, and soldier Spezialisierungen.
*   **Browser-Based & Referer Leakage**: Test for sensitive data leaked via Referer headers, browser cache (back button post-logout), and insecure Cookie headers (e.g., F5 BIG-IP).
*   **Service-Specific Info Disclosure**: Identify misconfigurations in Adobe Experience Manager (AEM), Jira (`QueryComponent`), Microsoft FrontPage, and Nginx aliases that leak internal system details.
*   **WebSocket & Side-Channel Disclosure**: Analyze WebSocket responses for excessive metadata and side-channel vulnerabilities (e.g., timing, search aggregates) that reveal private user activity.

## Skill Tree: hunt_mfa

*   **Rate Limit & Brute-Force Probing**: Test for missing or inadequate rate limits on OTP/code submission endpoints, allowing for brute-force attacks on 2FA codes.
*   **Session Persistence & Invalidation Bypasses**: Verify that sessions are properly invalidated after MFA activation, password reset, or logout; test if previously valid sessions remain active after enabling 2FA.
*   **Logical Flaw Exploitation**: Probe for the ability to disable or re-register MFA without password confirmation, email verification, or a valid OTP. Test for flows where MFA can be enabled without verifying ownership of the primary factor (email/phone).
*   **Race Condition & TOCTOU Bypasses**: Test for race conditions during the login flow or when performing sensitive actions that allow the MFA check to be bypassed.
*   **Code & Token Re-use**: Verify that OTPs, magic links, backup codes, and other MFA tokens are single-use and properly invalidated after use.
*   **MFA Not Enforced on Sensitive Actions**: Check that MFA is required for all critical account actions, such as changing passwords, updating email addresses, disabling security features, and viewing sensitive data.
*   **Response Manipulation Bypasses**: Intercept and modify server responses to bypass the MFA prompt (e.g., changing `mfa_required: true` to `false`).
*   **CSRF on MFA Management**: Identify and exploit CSRF vulnerabilities in the endpoints used to enable, disable, or manage MFA settings.
*   **OAuth & SSO Integration Bypasses**: Audit OAuth and SSO flows for weaknesses that allow bypassing MFA, such as linking a new SSO provider to an existing account without re-authentication.
*   **Backup Code & Recovery Flow Weaknesses**: Test for vulnerabilities in the backup code generation, storage, and recovery process, such as predictable codes or insecure recovery questions.
*   **Device Trust & "Remember Me" Flaws**: Analyze the "Remember Me" or device trust functionality for weaknesses, such as predictable cookies, insecure storage of trust tokens, or tokens that do not expire.

## Skill Tree: hunt_oauth

*   **Redirect URI Validation Bypass**: Exploit weaknesses in `redirect_uri` parsing by using path traversal, IDN homograph attacks, or misconfigured subdomain/path validation to steal authorization codes.
*   **State Parameter & CSRF Forgery**: Verify the implementation and validation of the `state` parameter to prevent CSRF attacks on the authorization flow and session fixation.
*   **Token Leakage via Referer & Browser History**: Identify scenarios where authorization codes or access tokens are leaked via Referer headers to third-party sites, or exposed in browser history through GET requests.
*   **Pre-Account Takeover via Email Verification Bypass**: Test for logical flaws where an attacker can link their OAuth identity to a victim's account without proper email verification, leading to account takeover.
*   **Scope Escalation & Consent Flaw**: Audit the `scope` parameter for excessive permissions and analyze the consent screen for UI redressing or clickjacking vulnerabilities.
*   **Mobile & Cross-Site OAuth Flaws**: Test for insecure mobile deep link handling, cross-site flashing, and postMessage misconfigurations that allow for token exfiltration.
*   **Provider-Specific Misconfigurations**: Identify and exploit misconfigurations in specific OAuth providers (e.g., Facebook, GitLab, Jira) that lead to authentication bypass or unauthorized access.
*   **Race Conditions in Token Exchange**: Probe for race conditions in the authorization code exchange process that may allow for token reuse or session hijacking.
*   **SSRF via OAuth Integrations**: Identify and exploit Server-Side Request Forgery vulnerabilities in OAuth integrations, such as Jira authorization controllers or other third-party webhooks.
*   **Client-Side & Implicit Flow Weaknesses**: Analyze client-side applications for insecure storage of secrets and vulnerabilities in the implicit grant flow that expose access tokens in the URL fragment.

## Skill Tree: hunt_openid

*   **Email Verification Bypass in SSO**: Exploit logical flaws in SSO flows to link an unverified email address to a victim's account, leading to account takeover.
*   **SAML Signature & Assertion Manipulation**: Test for SAML signature verification bypasses, assertion replay attacks (e.g., using expired or invalid assertions), and XML External Entity (XXE) injection in SAML parsers.
*   **Insecure Client-Side JWT Generation**: Identify and exploit client-side generation of JSON Web Tokens (JWTs) in SSO flows, allowing for token forgery and privilege escalation.
*   **SSRF in OpenID Connect Discovery & Registration**: Probe OpenID Connect discovery (`/.well-known/openid-configuration`) and dynamic client registration endpoints for Server-Side Request Forgery vulnerabilities.
*   **Login CSRF & Session Fixation**: Test for Cross-Site Request Forgery (CSRF) on login flows that use SSO/SAML, and identify session fixation vulnerabilities where an attacker can force a user to use a known session.
*   **Open Redirection in Callbacks & Logout**: Identify and exploit open redirection vulnerabilities in logout URLs and OAuth/SAML callback endpoints to steal tokens or phish users.
*   **Cross-Service Trust & Impersonation**: Analyze trust relationships between different services in an SSO ecosystem to identify opportunities for impersonation or unauthorized access.
*   **Information Disclosure via Discovery & Error Messages**: Scrutinize OpenID/SAML discovery endpoints and error messages for leakage of internal configuration, user information, or sensitive tokens.
*   **Provider-Specific Implementation Flaws**: Research and test for known vulnerabilities in specific OpenID/SAML providers and libraries (e.g., ORY Hydra, php-saml) that may be used by the target.
*   **DoS via SSO Login/Logout Flows**: Test for Denial of Service vulnerabilities by repeatedly initiating SSO login or logout requests, or by exploiting flaws in the session management of the identity provider.

## Skill Tree: hunt_openredirect

*   **Filter & Validation Bypass**: Exploit weaknesses in URL parsing by using path traversal (`/..//`), special characters (`@`, `\`, `#`), null bytes (`%00`), and non-latin characters (IDN homographs) to bypass redirect filters.
*   **Host Header Injection**: Manipulate the `Host` or `X-Forwarded-Host` headers to poison the server's understanding of its own domain, leading to redirects to arbitrary attacker-controlled sites.
*   **Chained Exploitation for Token Theft**: Leverage open redirects in OAuth flows or login/logout sequences to steal authentication tokens, API keys, or session cookies.
*   **CRLF Injection for Header Splitting**: Inject `CRLF` sequences (`%0d%0a`) into redirect parameters to split the HTTP headers and inject a malicious `Location` header or other arbitrary headers.
*   **Legacy & Third-Party Library Flaws**: Identify and exploit outdated or vulnerable third-party components (e.g., `swfupload.swf`) that have known open redirect vulnerabilities.
*   **Data URI & JavaScript URI Schemes**: Test for support of `data:` or `javascript:` URI schemes in redirect parameters, which can lead to XSS or other client-side attacks.
*   **Mobile & QR Code-Based Redirects**: Analyze QR code scanning functionality and mobile deep links for open redirect vulnerabilities that can be triggered through user interaction with physical or digital QR codes.
*   **POST-Based & Verb-Agnostic Redirects**: Test for open redirects in POST requests and verify if the vulnerability is verb-agnostic (i.e., works with GET, POST, PUT, etc.).
*   **DOM-Based & Client-Side Redirects**: Scrutinize client-side JavaScript for redirects that use sources like `document.location`, `window.location`, or `innerHTML` with user-controllable input.
*   **Content Spoofing & Text Injection**: Chain host header injection with open redirects to inject malicious content or text into the application's UI, enhancing phishing attacks.

## Skill Tree: hunt_racecondition

*   **Financial & Resource Exploitation**: Probe for race conditions in gift card redemption, loyalty point claims, and free credit distribution to achieve duplicate or inflated rewards.
*   **Limit & Quota Bypass**: Test for weaknesses in rate limits, invitation quotas, and resource creation limits (e.g., folders, workspaces, domains) by sending concurrent requests.
*   **TOCTOU & State Management Flaws**: Exploit Time-of-Check to Time-of-Use vulnerabilities in file operations, state transitions (e.g., following a user), and verification checks to bypass security controls.
*   **Authentication & Session Bypass**: Test for race conditions in 2FA activation/login, email verification, and OAuth token exchange to bypass authentication or create multiple valid sessions.
*   **Privilege Escalation & Local Exploitation**: Identify race conditions in local services (e.g., VPN clients, helper tools) or application logic that can be exploited for privilege escalation.
*   **Data Integrity & Consistency Issues**: Probe for race conditions in voting, liking, or feedback mechanisms that allow for inflated counts or inconsistent state.
*   **Duplicate Action & Payout Exploitation**: Test for race conditions in payout, re-test, and flag submission endpoints to trigger duplicate payments or actions.
*   **Concurrency in Team & Group Management**: Identify and exploit race conditions in team/group joining, member invitation, and role assignment to bypass restrictions or create invalid states.
*   **API & GraphQL Concurrency Flaws**: Test for race conditions in API endpoints and GraphQL mutations that manage resource creation, deletion, or modification.
*   **Client-Side Race Conditions**: Identify and exploit race conditions in client-side JavaScript, such as those involving Marketo forms or other third-party integrations, to trigger unintended behavior.

## Skill Tree: hunt_rce

*   **Dependency Confusion & Misconfiguration**: Probe for misconfigured package managers (npm, pip) that can be tricked into downloading malicious internal libraries from public registries.
*   **Command & Argument Injection**: Test for injection in file upload processors (ExifTool, ImageMagick), git commands (`--upload-pack`), and other shell-executing features.
*   **Insecure Deserialization**: Identify and exploit deserialization of untrusted data in Java (JBoss, WebLogic), .NET (Telerik UI), and PHP (GMP) applications.
*   **Server-Side Template Injection (SSTI)**: Probe for template injection in frameworks like Jinja2, Kramdown, Smarty, and other less common template engines.
*   **Vulnerable Component Exploitation**: Identify and exploit outdated or misconfigured software with known RCE vulnerabilities (e.g., JBoss, Apache Flink, Pulse Secure, Liferay Portal).
*   **Path Traversal to RCE**: Chain path traversal vulnerabilities with file writes, log poisoning, or configuration file overwrites to achieve remote code execution.
*   **SQL Injection to RCE**: Escalate SQL injection vulnerabilities to RCE by leveraging database features (e.g., `xp_cmdshell`, `UDFs`) or writing to web-accessible directories.
*   **Unrestricted File Upload**: Test for bypasses of file type restrictions, null byte injection, and other techniques to upload and execute malicious files (e.g., webshells).
*   **Buffer Overflow & Memory Corruption**: Identify and exploit buffer overflows and other memory corruption vulnerabilities in client-side applications (e.g., Steam Client) and network services.
*   **Electron & Desktop App RCE**: Test for RCE in Electron-based desktop applications via insecure `shell.openExternal()`, improper quarantine attribute handling, or XSS in privileged contexts.
*   **CI/CD Pipeline & Build Server Takeover**: Probe for vulnerabilities in CI/CD pipelines, such as misconfigured build steps, command injection in build scripts, or cache poisoning.

## Skill Tree: hunt_requestsmuggling

*   **CL.TE & TE.CL Desynchronization**: Identify and exploit desynchronization between frontend (Content-Length) and backend (Transfer-Encoding) servers to smuggle requests.
*   **H2 Downgrade Attacks**: Probe for vulnerabilities in HTTP/2 to HTTP/1.1 downgrades where headers can be smuggled, leading to desynchronization.
*   **Chained Exploitation for ATO & Credential Theft**: Leverage request smuggling to perform session hijacking, web cache poisoning, and steal credentials by capturing other users' requests.
*   **Header & Protocol Obfuscation**: Use malformed headers, character obfuscation (e.g., CR-to-Hyphen conversion), and protocol-level deviations to bypass detection and filtering mechanisms.
*   **Client-Side Desync**: Test for client-side desynchronization vulnerabilities in browsers and other HTTP clients that can be exploited for cache poisoning or session hijacking.
*   **Bypassing Security Controls**: Utilize request smuggling to bypass WAFs, reverse proxies, and other security controls by desynchronizing the inspection points.
*   **Cross-User & Cross-Tenant Attacks**: Exploit request smuggling to perform cross-user attacks, such as account takeover, or to access data from other tenants in a multi-tenant environment.
*   **SSRF via Request Smuggling**: Chain request smuggling with Server-Side Request Forgery (SSRF) to access internal services and exfiltrate data.
*   **Web Cache Poisoning & Deception**: Use request smuggling to poison web caches with malicious content or to deceive caches into storing and serving sensitive user data.
*   **Provider-Specific Vulnerabilities**: Research and test for known request smuggling vulnerabilities in specific reverse proxies, load balancers, and web servers (e.g., Apache Tomcat, Node.js, Skipper).

## Skill Tree: hunt_sqli

*   **Error-Based & Union-Based SQLi**: Probe for verbose database errors and use UNION queries to exfiltrate data from various parameters, including headers (User-Agent), cookies, and hidden form fields.
*   **Blind SQLi (Time-Based & Boolean)**: Use time delays (`SLEEP()`, `pg_sleep()`) and boolean logic to infer database content when no direct output is available, targeting both standard and unconventional parameters.
*   **Out-of-Band (OOB) SQLi**: Leverage DNS (`LOAD_FILE()`) or HTTP requests to exfiltrate data from the database server, bypassing firewalls and other restrictions.
*   **SQLi to RCE**: Escalate SQL injection to Remote Code Execution through database-specific functions (e.g., `xp_cmdshell` in MSSQL, User-Defined Functions in PostgreSQL) or by chaining with other vulnerabilities like insecure deserialization.
*   **WAF Bypass Techniques**: Use character encoding, obfuscation (e.g., comments, whitespace), case variation, and other techniques to bypass Web Application Firewalls and other security filters.
*   **Second-Order SQLi**: Identify and exploit stored SQL injection vulnerabilities where user input is stored in the database and later used in a vulnerable query.
*   **Database-Specific Exploitation**: Tailor payloads and exploitation techniques to the specific database engine in use (e.g., MSSQL, PostgreSQL, MySQL, Oracle, ClickHouse).
*   **GraphQL & API SQLi**: Probe GraphQL endpoints and API parameters for SQL injection, including those in JSON bodies and other non-standard input formats.
*   **NoSQL Injection**: Test for NoSQL injection vulnerabilities in applications that use MongoDB, CouchDB, and other NoSQL databases, targeting operators like `$where` and `$regex`.
*   **Authentication Bypass**: Exploit SQL injection in login forms and other authentication mechanisms to bypass access controls and gain unauthorized access.

## Skill Tree: hunt_ssrf

*   **Cloud Metadata & Credential Exfiltration**: Target cloud provider metadata endpoints (AWS, GCP, Azure, Oracle Cloud) to steal credentials, SSH keys, and other sensitive information.
*   **Filter & Validation Bypasses**: Use DNS rebinding, IP formatting (e.g., decimal, octal, hexadecimal), URL encoding, and special characters to bypass SSRF filters and access internal services.
*   **Blind & Full-Read SSRF**: Differentiate between blind SSRF (timing, DNS-based) and full-read SSRF that returns the response content, and tailor exploitation techniques accordingly.
*   **SSRF to LFI & RCE**: Chain SSRF with other vulnerabilities to achieve Local File Inclusion (`file://`), Remote Code Execution (`gopher://`), or internal service exploitation (e.g., Redis, Elasticsearch).
*   **Vulnerable Component Exploitation**: Identify and exploit SSRF in common components like FFmpeg, ImageMagick, document converters (PDF generators), and other media processing libraries.
*   **GraphQL & API SSRF**: Probe GraphQL endpoints and API parameters for SSRF, including those in JSON bodies, webhook URLs, and other non-standard input formats.
*   **XXE to SSRF**: Exploit XML External Entity (XXE) injection vulnerabilities to trigger SSRF and access internal resources.
*   **Cross-Protocol Exploitation**: Use different URL schemes (`http://`, `https://`, `ftp://`, `smb://`) to access a variety of internal services and protocols.
*   **Open Redirect to SSRF**: Chain open redirect vulnerabilities to bypass SSRF defenses and force the server to make requests to arbitrary internal or external locations.
*   **Infrastructure & Service-Specific SSRF**: Identify and exploit SSRF vulnerabilities in specific infrastructure components, such as Jira, Confluence, and other internal services.

## Skill Tree: hunt_ssti

*   **Template Engine Identification**: Use polyglot payloads (e.g., `{{7*7}}`, `<%= 7*7 %>`, `${7*7}`) to identify the underlying template engine (e.g., Jinja2, Smarty, Freemarker, Ruby ERB, Lodash).
*   **Sandbox Escape & Context Breakout**: Craft payloads to escape sandboxed environments and access underlying objects and methods, leading to information disclosure or RCE.
*   **SSTI to RCE**: Escalate SSTI to Remote Code Execution by accessing the operating system's command execution capabilities through built-in functions or objects.
*   **Blind & Error-Based SSTI**: Use time delays, DNS callbacks, and error messages to confirm and exploit blind SSTI when no direct output is visible.
*   **Client-Side Template Injection (CSTI)**: Probe for template injection vulnerabilities in client-side JavaScript frameworks like AngularJS and Vue.js, leading to XSS.
*   **Chaining with Other Vulnerabilities**: Combine SSTI with other vulnerabilities like path traversal, LFI, or RCE to achieve greater impact.
*   **Bypassing Filters & Sanitization**: Use character encoding, string concatenation, and other obfuscation techniques to bypass input filters and sanitization routines.
*   **Language-Specific Payloads**: Tailor payloads to the specific programming language and template engine in use (e.g., Python, Java, Ruby, PHP, JavaScript).
*   **Uncommon & Custom Template Engines**: Research and develop payloads for less common or custom-built template engines that may have unique vulnerabilities.

## Skill Tree: hunt_subdomaintakeover

*   **Dangling DNS Record Identification**: Scan for subdomains with CNAME or NS records pointing to services (e.g., S3, CloudFront, Heroku, GitHub Pages, Azure, Unbounce, Tilda) that have been de-provisioned or are misconfigured.
*   **Cloud Provider-Specific Exploitation**: Craft exploits for specific cloud providers by registering the dangling resource (e.g., creating a public S3 bucket with the same name).
*   **Broken Link & Resource Hijacking**: Identify and take over expired domains or resources linked from the target's web pages, including those in documentation, blog posts, and marketing materials.
*   **Chained Exploitation for ATO & Phishing**: Leverage subdomain takeovers to bypass authentication (e.g., by hosting a malicious file on the trusted domain), steal cookies, or create convincing phishing pages.
*   **Mass-Scanning & Automation**: Use tools like `subzy`, `subjack`, and `nuclei` to automate the discovery of vulnerable subdomains across a large number of targets.
*   **Bypassing Takeover Protections**: Identify and bypass protections against subdomain takeovers, such as by exploiting inconsistencies in DNS resolution or using alternative registration methods.
*   **Second-Order & Stored Takeovers**: Discover stored subdomain takeover vulnerabilities where a link to a vulnerable subdomain is stored in the application and can be triggered later.
*   **NS Record Hijacking**: Identify and exploit dangling NS records to take over an entire DNS zone, allowing for the creation of arbitrary subdomains and records.
*   **Domain & TLD Expiration**: Monitor for the expiration of domains and top-level domains (TLDs) that are still in use by the target application.
*   **Content Delivery Network (CDN) Takeovers**: Probe for misconfigured CDNs (e.g., Fastly, Cloudflare) where the origin server is no longer in use, allowing an attacker to serve malicious content.

## Skill Tree: hunt_upload

*   **Webshell & RCE via Unrestricted Upload**: Bypass file type restrictions (e.g., using null bytes, double extensions, case variations) to upload and execute webshells, leading to Remote Code Execution.
*   **Stored XSS via SVG & HTML Upload**: Upload malicious SVG or HTML files containing JavaScript payloads to trigger Stored Cross-Site Scripting in the context of the victim's browser.
*   **Stored XSS via File Metadata**: Inject malicious payloads into file metadata (e.g., EXIF data in images) that are later rendered on the page, leading to Stored XSS.
*   **SSRF via Media Processing**: Exploit vulnerabilities in server-side media processing libraries (e.g., FFmpeg, ImageMagick) to trigger Server-Side Request Forgery by providing a malicious URL as the input file.
*   **XXE via SVG & XML Upload**: Upload malicious XML or SVG files containing XML External Entity injection payloads to read local files, trigger SSRF, or cause a Denial of Service.
*   **Path Traversal & File Overwrite**: Exploit path traversal vulnerabilities in the filename parameter to overwrite sensitive files on the server (e.g., `.htaccess`, configuration files) or to upload files to arbitrary locations.
*   **DoS via Pixel Flood & Large Files**: Upload large or specially crafted image files (e.g., "pixel flood" images) to cause a Denial of Service by exhausting server resources.
*   **IDOR in File Upload & Management**: Identify and exploit Insecure Direct Object References in file upload, deletion, or management functions to access or modify files belonging to other users.
*   **Race Conditions in File Upload**: Test for race conditions in the file upload process that may allow an attacker to bypass security checks (e.g., malware scanning) before the file is moved to its final destination.
*   **Insecure Cloud Storage & S3 Bucket Uploads**: Identify and exploit misconfigured S3 buckets or other cloud storage that allows for public, unauthenticated file uploads.

## Skill Tree: hunt_webcache

*   **Unkeyed Input & Header Abuse**: Probe for unkeyed headers (`X-Forwarded-Host`, `X-HTTP-Method-Override`) and query parameters that can be used to poison the cache with malicious content.
*   **Web Cache Deception**: Exploit misconfigured caching of dynamic content (e.g., by requesting a non-existent static file on a dynamic endpoint) to leak sensitive user information, CSRF tokens, and session identifiers.
*   **Chained Exploitation (XSS, DoS, Info Disclosure)**: Leverage cache poisoning to achieve Stored XSS, Denial of Service (by caching error pages), and large-scale information disclosure.
*   **HTTP Request Smuggling to Cache Poisoning**: Use HTTP Request Smuggling to desynchronize the cache and poison it with malicious content that is served to other users.
*   **Cache Key Injection**: Inject malicious values into the cache key (e.g., via header manipulation) to manipulate the cached content and control what other users see.
*   **Fat GET & Parameter Cloaking**: Use "Fat GET" requests (GET requests with a body) and parameter cloaking techniques to bypass WAFs and cache poisoning defenses.
*   **CDN & Reverse Proxy-Specific Flaws**: Research and test for known cache poisoning vulnerabilities in specific CDNs (e.g., Cloudflare, Akamai) and reverse proxies (e.g., Varnish, Nginx).
*   **Time-to-Live (TTL) & Cache Control Analysis**: Analyze `Cache-Control` headers and the TTL of cached responses to understand the window of opportunity for a cache poisoning attack.
*   **GraphQL & API Cache Poisoning**: Probe GraphQL endpoints and API responses for cache poisoning vulnerabilities, especially those that reflect user input in the response.
*   **Cache Poisoning for Open Redirects**: Use cache poisoning to create open redirects on a trusted domain, leading to more effective phishing attacks.

## Skill Tree: hunt_xss

*   **Stored & Reflected XSS**: Probe for vulnerabilities in user-generated content (comments, profiles, forum posts), URL parameters, and other inputs that are reflected in the response without proper sanitization.
*   **DOM-Based XSS**: Analyze client-side JavaScript for vulnerabilities in sinks like `innerHTML`, `document.write`, `eval`, and `location.href`, especially those that use data from sources like `location.hash` and `postMessage`.
*   **Blind XSS**: Inject payloads that trigger on backend systems (e.g., admin panels, log viewers) or in other users' sessions, often through forms or other inputs that are not immediately visible.
*   **CSP & Filter Bypasses**: Craft payloads to bypass Content Security Policies (e.g., via JSONP endpoints, AngularJS template injection) and other XSS filters (e.g., using character encoding, case variations, event handlers).
*   **Chained Exploitation**: Leverage XSS to perform session hijacking, keylogging, phishing, and other attacks by chaining it with other vulnerabilities like CSRF, Open Redirect, or information disclosure.
*   **XSS in File Uploads & SVGs**: Upload malicious SVG files or other file types that can contain JavaScript to achieve Stored XSS.
*   **XSS in Email & Markdown**: Craft payloads that execute in email clients or Markdown parsers, bypassing sanitization routines.
*   **Prototype Pollution to XSS**: Exploit prototype pollution vulnerabilities in JavaScript libraries to achieve DOM-based XSS.
*   **XSS in Client-Side Templates**: Identify and exploit client-side template injection vulnerabilities in frameworks like AngularJS and Vue.js.
*   **PostMessage XSS**: Analyze `postMessage` handlers for insecure implementations that allow for cross-origin XSS.

## Skill Tree: hunt_xxe

*   **XXE for LFI & SSRF**: Exploit XXE to read local files (`file:///etc/passwd`) or trigger server-side requests to internal and external resources.
*   **XXE in File Uploads**: Probe for XXE in file upload features that process XML-based files, such as SVG, DOCX, PPTX, and XMP metadata in JPEGs.
*   **Blind & Out-of-Band (OOB) XXE**: Use external DTDs and DNS callbacks to confirm and exfiltrate data from blind XXE vulnerabilities where no direct output is visible.
*   **XXE to RCE**: Escalate XXE to Remote Code Execution by chaining with other vulnerabilities, such as insecure deserialization in PHP, or by leveraging specific features of the XML parser.
*   **DoS via Billion Laughs Attack**: Test for denial of service by submitting recursively defined XML entities, causing the XML parser to consume excessive memory and CPU.
*   **SOAP & XML-RPC XXE**: Target SOAP and XML-RPC endpoints with XXE payloads to exploit vulnerabilities in the backend web services.
*   **WAF Bypass Techniques**: Use character encoding, different entity types (e.g., parameter entities), and other obfuscation techniques to bypass Web Application Firewalls and XML parsers' security features.
*   **Content-Type & Body Manipulation**: Test for XXE by changing the `Content-Type` header to `application/xml` and sending an XML payload in the request body, even on endpoints not expecting XML.
*   **Error-Based XXE**: Leverage verbose error messages to exfiltrate data from the server by embedding external entities in the XML structure and observing the output.
*   **XXE in SAML Assertions**: Probe for XXE in SAML assertions during the SSO login process to compromise the authentication mechanism.
