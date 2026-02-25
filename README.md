# 🛡️ Theo-Auth-Ecosystem: Enterprise-Grade Identity & Security

**A high-security, production-ready authentication engine featuring dual-layer verification (OTP + OAuth 2.0) and automated account protection protocols to mitigate brute-force attacks.**

![Authentication Flow](https://via.placeholder.com/800x400?text=Insert+Architecture+Diagram+Here)

---

## 🚀 The Core Problem
Most boilerplate authentication systems lack production-ready security layers, leaving applications vulnerable to automated login attacks and database bloat from unverified email sign-ups. Applications often struggle to balance seamless "One-Tap" social login with the strict verification required for secure local accounts.

## 💡 The Solution
**Theo-Auth-Ecosystem** addresses these vulnerabilities by implementing a multi-strategy authentication architecture. It provides:
* **Verified Onboarding:** An OTP-based registration flow that prevents database bloat from fake accounts.
* **Resilient Security:** In-memory login attempt tracking that automatically locks accounts during suspected attacks.
* **Seamless Integration:** A unified session-management system that treats Google OAuth and Local users as first-class citizens.

---

## 🛠️ Tech Stack & Architecture

### **Core Infrastructure**
* **Runtime:** Node.js (v18+)
* **Framework:** Express.js (Architected utilizing a scalable `views/`, `routes/`, `public/`, `database/` module structure)
* **Database:** SQLite3 (Relational persistence for ACID compliance)

### **Security & Identity**
* **Identity Provider:** Passport.js (Google OAuth 2.0 Strategy)
* **Encryption:** BcryptJS (Adaptive hashing with a cost-factor of 10)
* **Validation:** Express-Validator (Schema-based input sanitization)
* **Communication:** Nodemailer (Automated OTP & Reset link delivery)
* **State Management:** Express-Session (Secure cookie-based tracking)

---

## 🌟 Top 3 Features

1.  **Adaptive Account Lockout:** Architected a custom `loginAttempts` Map logic that tracks failed authentications and triggers a 15-minute cool-down after 3 failed attempts, drastically reducing the success rate of brute-force dictionary attacks.
2.  **State-Aware Password Recovery:** Engineered a secure reset flow utilizing `crypto`-generated 32-byte high-entropy tokens with strict 1-hour expiration windows, ensuring recovery links cannot be reused or easily guessed.
3.  **Temporal OTP Registration Buffer:** Implemented an intermediate session state to hold hashed credentials during the 3-minute OTP verification window, ensuring only fully verified users are ever committed to the primary database.

---

## 🧠 'The Why' (Architectural Decisions)

### **Why SQLite3 instead of a NoSQL alternative?**
For an authentication system, data integrity is paramount. SQLite was chosen because it provides **ACID compliance** out of the box. In a relational structure, user credentials, OAuth IDs, and reset tokens are strictly typed, preventing the "schema-less" data corruption risks inherent in NoSQL document stores for identity management.

### **Why a strict Separation of Concerns?**
The project was engineered with a dedicated routing architecture, isolating authentication, password recovery, and OAuth strategies. This decoupling ensures that business logic remains distinct from UI rendering, making the codebase highly maintainable and ready for future microservice extraction.

---

## 🚧 Challenges Overcome: The OTP Session Race Condition

**The Technical Challenge:** During registration, there was a need to verify an email via OTP without committing unverified or potentially malicious data to the SQLite database. Initial designs risked creating "ghost users" (unverified accounts reserving valid usernames/emails).

**The Engineering Solution:** This was resolved by engineering a **Temporal Session Buffer**. By utilizing express sessions, the system stores sanitized input, the hashed password, and the generated 6-digit OTP in a volatile state. The data is only inserted into the SQLite `users` table upon a successful, time-validated match of the OTP code. This approach maintains absolute database hygiene and enforces 100% email verification before account creation.

---

## 📥 Setup & Installation

### 1. Environment Configuration
Create a `.env` file in the root directory and populate the secure credentials:
```env
PORT=3000
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-app-password
GOOGLE_CLIENT_ID=your-google-oauth-id
GOOGLE_CLIENT_SECRET=your-google-oauth-secret
SESSION_SECRET=your-secure-session-key
