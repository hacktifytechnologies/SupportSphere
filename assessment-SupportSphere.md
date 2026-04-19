# Assessment — SupportSphere: Source Code Leak + Brute Force + GraphQL Data Extraction

---

## Question 1 — MCQ

**How was the valid username `ops.audit` discovered?**

- A) SQL Injection against the user table
- B) API brute force against `/api/users`
- C) **Source code inspection — leaked in frontend JS or page comments** ✅
- D) DNS enumeration

> **Answer:** C — Inspecting the application's HTML source or JavaScript files reveals the username in a comment or configuration value.

---

## Question 2 — MCQ

**Which attack obtained the password for `ops.audit`?**

- A) Cross-Site Scripting
- B) **Brute Force against `POST /login` — no rate limiting or lockout** ✅
- C) CSRF
- D) SSRF

> **Answer:** B — The login endpoint has no rate limiting, lockout, or CAPTCHA. Using Burp Intruder with a wordlist finds `Support@123`.

---

## Question 3 — MCQ

**Which backend endpoint was used to extract sensitive internal data?**

- A) `/admin`
- B) `/api/login`
- C) **`/graphql`** ✅
- D) `/upload`

> **Answer:** C — A GraphQL endpoint accessible to authenticated users allows querying sensitive fields including `internalNote` on tickets.

---

## Question 4 — Fill in the Blank

**What is the Emergency Override Key retrieved via the GraphQL query on ticket `TCK-9001`?**

**Answer:** `SPHERE-9921-X`

> Querying `{ ticket(id: "TCK-9001") { id title internalNote } }` against the `/graphql` endpoint returns this key in the `internalNote` field, which is accessible due to missing field-level authorization.

---

## Question 5 — Fill in the Blank

**What GraphQL field name on the ticket type exposes the sensitive internal data including the Emergency Override Key?**

**Answer:** `internalNote`

> The `internalNote` field is intended for privileged internal use but has no field-level authorization check. Any authenticated user can include it in a GraphQL query and retrieve its value, including the Emergency Override Key `SPHERE-9921-X`.

---

*Lab target:* `http://localhost:8089`
