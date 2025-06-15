# 🏳️ The Great Juche Jaguar GraphQL Heist – DoD Cyber Sentinel 2025

Welcome to my walkthrough of **The Great Juche Jaguar GraphQL Heist**, a 300-point web exploitation challenge from the **DoD Cyber Sentinel Skills Challenge – Summer 2025**. This write-up details how I chained multiple vulnerabilities, including SSRF and GraphQL introspection, to extract internal secrets and retrieve the flag.

---

## 📁 About This Challenge

This was a high-difficulty CTF challenge that simulated a vulnerable cloud-connected API protected behind a proxy. The challenge required exploiting GCP metadata exposure through SSRF and manipulating a GraphQL endpoint to uncover and trigger a hidden mutation.

---

## 🚩 Objectives

- Abuse SSRF via query parameter injection
- Reach internal GCP metadata at `169.254.169.254`
- Extract a secret authentication header (`X-JJ-SECRET`)
- Access a protected GraphQL endpoint using the secret
- Run introspection queries to discover hidden mutation logic
- Execute the mutation and decode the returned data to retrieve the flag

---

## 🧰 Tools Used

- `curl`  
- `base64`  
- Manual GraphQL query crafting  
- Logical exploitation & metadata awareness

---

## 📄 Write-Up

The full walkthrough for this challenge is documented [here](full-writeup.md).

---

## 🧠 What I Learned

- How to exploit SSRF to access cloud provider metadata (GCP)  
- The risks of placing secrets in metadata without strict access control  
- Chaining vulnerabilities across cloud and application layers  
- Manual GraphQL introspection without a GUI client  
- Recognizing base64 encoding patterns in API responses

---

## ✅ Challenge Host

> **Platform:** DoD Cyber Sentinel Skills Challenge – Summer 2025  
> **My Rank:** 247th out of ~2200 participants  
> **Points Earned:** 300  
