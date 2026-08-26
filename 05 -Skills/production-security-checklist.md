# Production Security Pre-Flight Checklist

Before any phase-to-phase deployment to production infrastructure (e.g. Vercel, Railway,
Cloudflare R2, or equivalent), review these top security vulnerabilities often introduced during
AI-assisted development. Read through these precautions carefully — this applies to any project
in this vault heading toward production, not just one specific stack.

---

## 1. Exposed API Keys

**The Risk:** When AI writes code, it sometimes puts your API keys directly in the code itself.
The moment you push that to GitHub, it is public. Anyone can find it, use it, and run up massive
charges on your cloud/API accounts before you even notice.

**The Fix:**
- Never let an API key sit inside your code.
- Every key goes in a `.env` file or environment variables on the hosting platform.
- **Action:** Before you push anything, search the entire project for "key", "secret", or "token"
  (e.g. `JWT_SECRET`, `R2_SECRET_ACCESS_KEY`) and confirm none of them are hardcoded.

---

## 2. Broken Authentication

**The Risk:** Most apps only test if the login *works*. Nobody tests what happens when you try to
bypass it. An attacker doesn't log in through the front door; they look for the window you forgot
to close.

**The Fix:**
- After building login, try to access protected pages (admin routes, other users' private data)
  directly through the URL *without* logging in.
- **Action:** If you can get through, the authorization logic (Spring Security filters, Next.js
  middleware, etc.) is broken and needs to be patched before shipping.

---

## 3. No Input Validation

**The Risk:** Your app trusts everything a user types into it. No checks, no limits, no rules.
This is how attackers inject malicious data straight into your database through a simple text
field, or manipulate values like a payment reference number.

**The Fix:**
- Add input validation to every form and API endpoint.
- **Action:** Set character limits, block special characters where unnecessary, and enforce strict
  type checking (e.g. Spring's `@Valid`/`@Pattern`) before any data touches the database.

---

## 4. Missing Row-Level Security

**The Risk:** If database queries aren't properly scoped, every user in the app can potentially
see every other user's data (order history, wallet balance, etc.) — not because they hacked it,
but because the door was left open.

**The Fix:**
- Check database access patterns.
- **Action:** Ensure every query fetching user-owned data enforces ownership (e.g.
  `WHERE user_id = ?`). For strict database-level enforcement on Postgres, consider enabling Row
  Level Security (RLS) on tables holding user-scoped or financial data.

---

## 5. Outdated Packages

**The Risk:** AI recommends packages based on its training data. Some of those packages have known
vulnerabilities that have been public for months, shipped without anyone noticing.

**The Fix:**
- Scan dependencies before deploying.
- **Action (frontend):** `npm audit` (or `npx snyk test`) in the frontend project folder.
- **Action (backend):** `mvn dependency-check:check` (or `mvn snyk:test` for Maven/Spring Boot) to
  scan every package the app uses and fix known issues.

---

## How to Use This

Walk through all five items before any real production deployment — not just once at the start of
a project, but again before each deployment that adds new endpoints, forms, or dependencies. This
is a pre-flight check, not a one-time setup step.
