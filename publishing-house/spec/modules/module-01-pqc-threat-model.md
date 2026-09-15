# Module 1: Fundamentals & the PQC Threat Model

### Brief Overview

This opening module sets the stage for the whole lab by explaining *why* post-quantum cryptography matters right now. Learners meet the "Harvest Now, Decrypt Later" (HNDL) attack, in which an adversary records today's encrypted traffic so it can be decrypted once a cryptographically relevant quantum computer exists. The module then introduces hybrid key exchange — combining the classical X25519 algorithm with the post-quantum ML-KEM-768 algorithm — as the defense that protects data in transit today. No configuration happens yet; this is the conceptual foundation the rest of the lab builds on.

### Audience and Time

- **Personas:** Linux users, administrators, and security-curious practitioners attending the RH1 lab series. Beginner level — new to Linux, with no prior TLS or cryptography knowledge assumed.
- **Prerequisites for this module:** None. Part 1 of the RH1 series provides helpful context but is not required.
- **Estimated duration:** 5 minutes.

### Learning Objectives

- Describe the "Harvest Now, Decrypt Later" (HNDL) threat and why recorded traffic is at risk today.
- Explain what post-quantum cryptography (PQC) is and the problem it solves for TLS.
- Identify hybrid key exchange (classical X25519 + ML-KEM-768) as the mechanism that defends against HNDL now.

### Lab Structure

| Section | Title | Duration |
|---------|-------|----------|
| 1 | The HNDL threat: record now, decrypt later | 2 min |
| 2 | What post-quantum cryptography changes | 2 min |
| 3 | Hybrid key exchange: X25519 + ML-KEM-768 | 1 min |

### Detailed Steps

1. Read the scenario introduction in the Showroom panel: an adversary is capturing encrypted network traffic today and storing it.
2. Learn why that captured traffic is a future risk — once a large quantum computer exists, classical key exchange (like plain X25519) could be broken and the stored traffic decrypted retroactively. This is the "Harvest Now, Decrypt Later" (HNDL) threat.
3. Read the plain-language definition of post-quantum cryptography (PQC): cryptographic algorithms designed to resist attacks from both classical and quantum computers.
4. Understand where PQC applies in this lab: the TLS *key exchange* step of the handshake, which is the part vulnerable to HNDL.
5. Meet the two algorithms used later in the lab — the classical **X25519** and the post-quantum **ML-KEM-768** — and why they are combined into a single *hybrid* key exchange (X25519 keeps proven classical security; ML-KEM-768 adds quantum resistance; if either holds, the session is safe).
6. Review the lab roadmap: deploy a classical TLS server (Module 2), upgrade it to hybrid PQC (Module 3), then break and fix it under real-world network conditions (Module 4).
7. Complete the end-of-module knowledge check to confirm understanding before moving on.

### Key Takeaways

- HNDL is a *present-day* threat: encrypted data captured now can be decrypted later once quantum computers mature.
- Post-quantum cryptography protects the TLS key exchange against that future decryption.
- Hybrid key exchange pairs the classical X25519 with the post-quantum ML-KEM-768 so the connection stays secure even if only one of the two algorithms holds.
- This module is conceptual only — no server changes are made yet.

### Infrastructure Notes

No system state is created or changed in this module. The end-of-module checkpoint is a trust-based knowledge check (short quiz) rather than a system validation.
