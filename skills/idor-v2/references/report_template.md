# Vulnerability Report: Insecure Direct Object Reference (IDOR) on [Endpoint/Functionality]

## Description
An Insecure Direct Object Reference (IDOR) / Broken Object Level Authorization (BOLA) vulnerability was discovered in the `[Endpoint Path]` endpoint. This vulnerability allows an authenticated user to [action performed: read/modify/delete] resources belonging to other users without authorization.

## Impact
[Describe the exact impact. e.g., "An attacker can view the private Personal Identifiable Information (PII) of any registered user," or "An attacker can silently overwrite the billing address of targeted accounts."]
-   **Confidentiality:** [High/Medium/Low]
-   **Integrity:** [High/Medium/Low]
-   **Availability:** [High/Medium/Low]

## Prerequisites
-   Two authenticated user accounts (Attacker and Victim).
-   The resource ID (e.g., `[ID type]`) of the Victim's object.

## Steps to Reproduce (Burp Suite)
1.  Log into the application as the **Victim** and create/identify a target resource. Note the Resource ID: `<VICTIM_ID>`.
2.  Log into the application as the **Attacker**.
3.  Navigate to `[Attacker's functionality]`.
4.  Intercept the request in Burp Suite (e.g., `[HTTP Method] [Endpoint]`).
5.  In the intercepted request, locate the parameter `[Parameter Name]` containing the Attacker's ID.
6.  Change the value of `[Parameter Name]` to the Victim's ID (`<VICTIM_ID>`).
7.  Forward the request. Observe the response indicating successful unauthorized access/modification.

## Steps to Reproduce (cURL PoC)
Execute the following curl command using the **Attacker's** session cookies/headers, targeting the **Victim's** resource ID.

```bash
curl -i -s -k -X [METHOD] 'https://[TARGET]/[PATH]' \
-H 'Host: [TARGET]' \
-H 'Cookie: [ATTACKER_COOKIES]' \
-H 'Content-Type: application/json' \
--data-raw '{"[PARAMETER]":"<VICTIM_ID>"}'
```

**Verified Response:**
```json
[Paste the raw response body proving the exploit succeeded]
```