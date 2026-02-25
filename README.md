<p align="center">
  <img src="https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/rainbow.png" alt="rainbow line" width="100%">
</p>

<p align="center">
  <h1 align="center">🛡️ Theo-Auth-Ecosystem</h1>
  <p align="center">
    <strong>Enterprise-Grade Identity, Security, & Session Management</strong>
    <br />
    A high-security, production-ready authentication engine featuring dual-layer verification (OTP + OAuth 2.0) and automated account protection protocols.
  </p>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js" />
  <img src="https://img.shields.io/badge/Express.js-000000?style=for-the-badge&logo=express&logoColor=white" alt="Express" />
  <img src="https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white" alt="SQLite" />
  <img src="https://img.shields.io/badge/Passport-34E27A?style=for-the-badge&logo=passport&logoColor=white" alt="Passport" />
  <img src="https://img.shields.io/badge/Security-Bcrypt%20%7C%20OTP-red?style=for-the-badge" alt="Security" />
</p>

<p align="center">
  <img src="https://cdn.dribbble.com/users/1162077/screenshots/4382009/animated-login-screen.gif" alt="Animated Login Demo" width="600" style="border-radius: 10px; box-shadow: 0 4px 8px rgba(0,0,0,0.1);">
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/andreasbm/readme/master/assets/lines/rainbow.png" alt="rainbow line" width="100%">
</p>

## 🚀 The Core Problem
Most boilerplate authentication systems lack production-ready security layers, leaving applications vulnerable to automated login attacks and database bloat from unverified email sign-ups. Applications often struggle to balance seamless "One-Tap" social login with the strict verification required for secure local accounts.

## 💡 The Solution
**Theo-Auth-Ecosystem** addresses these vulnerabilities by implementing a multi-strategy authentication architecture. It provides:
* 🔐 **Verified Onboarding:** An OTP-based registration flow that prevents database bloat from fake accounts.
* 🛡️ **Resilient Security:** In-memory login attempt tracking that automatically locks accounts during suspected attacks.
* 🔄 **Seamless Integration:** A unified session-management system that treats Google OAuth and Local users as first-class citizens.

---

## 🛠️ Tech Stack & Architecture

<table>
  <tr>
    <td align="center" width="33%">
      <h3>Core Infrastructure</h3>
      <p><b>Node.js (v18+)</b></p>
      <p><b>Express.js</b> (Architected utilizing a scalable <code>views/</code>, <code>routes/</code>, <code>public/</code>, <code>database/</code> module structure)</p>
      <p><b>SQLite3</b> (Relational persistence)</p>
    </td>
    <td align="center" width="33%">
      <h3>Security & Identity</h3>
      <p><b>Passport.js</b> (Google OAuth 2.0)</p>
      <p><b>BcryptJS</b> (Cost-factor 10)</p>
      <p><b>Express-Validator</b></p>
    </td>
    <td align="center" width="33%">
      <h3>State & Comm.</h3>
      <p><b>Express-Session</b> (Secure cookie tracking)</p>
      <p><b>Nodemailer</b> (Automated OTP & Reset delivery)</p>
      <p><b>Crypto</b> (High-entropy tokens)</p>
    </td>
  </tr>
</table>

---

## 🌟 Top 3 Features

<details>
  <summary><b>1. Adaptive Account Lockout</b> <i>(Click to expand)</i></summary>
  <br>
  Architected a custom <code>loginAttempts</code> Map logic that tracks failed authentications and triggers a 15-minute cool-down after 3 failed attempts, drastically reducing the success rate of brute-force dictionary attacks.
</details>

<details>
  <summary><b>2. State-Aware Password Recovery</b> <i>(Click to expand)</i></summary>
  <br>
  Engineered a secure reset flow utilizing <code>crypto</code>-generated 32-byte high-entropy tokens with strict 1-hour expiration windows, ensuring recovery links cannot be reused or easily guessed.
</details>

<details>
  <summary><b>3. Temporal OTP Registration Buffer</b> <i>(Click to expand)</i></summary>
  <br>
  Implemented an intermediate session state to hold hashed credentials during the 3-minute OTP verification window, ensuring only fully verified users are ever committed to the primary database.
</details>

---

## 🧠 'The Why' (Architectural Decisions)

> **Why SQLite3 instead of a NoSQL alternative?**
> For an authentication system, data integrity is paramount. SQLite was chosen because it provides **ACID compliance** out of the box. In a relational structure, user credentials, OAuth IDs, and reset tokens are strictly typed, preventing the "schema-less" data corruption risks inherent in NoSQL document stores for identity management.

> **Why a strict Separation of Concerns?**
> The project was engineered with a dedicated routing architecture, isolating authentication, password recovery, and OAuth strategies. This decoupling ensures that business logic remains distinct from UI rendering, making the codebase highly maintainable and ready for future microservice extraction.

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
