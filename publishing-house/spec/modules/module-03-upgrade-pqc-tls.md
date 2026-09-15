# Module 3: Upgrade to Post-Quantum TLS

### Brief Overview

With a working classical TLS 1.3 server in place, learners now upgrade it to hybrid post-quantum key exchange. They first confirm the RHEL 10 system is using the **DEFAULT** crypto policy, then add a single Nginx directive — `ssl_ecdh_curve mlkem768_x25519` — to enable the hybrid ML-KEM-768 + X25519 group. Finally they return to the **client** VM and verify with `openssl s_client` that the handshake actually negotiates the post-quantum group. This is the payoff module: the same web server is now defending its traffic against the HNDL threat introduced in Module 1.

### Audience and Time

- **Personas:** Beginner Linux users and administrators who completed the classical deployment in Module 2.
- **Prerequisites for this module:** A running classical TLS 1.3 Nginx server on port 8443 (Module 2). Access to both server and client VMs.
- **Estimated duration:** 10 minutes.

### Learning Objectives

- Verify that the RHEL 10 system is using the DEFAULT system-wide crypto policy.
- Configure hybrid ML-KEM-768 + X25519 post-quantum key exchange in Nginx using `ssl_ecdh_curve mlkem768_x25519`.
- Verify from the client VM that the TLS handshake negotiates the hybrid post-quantum group.

### Lab Structure

| Section | Title | Duration |
|---------|-------|----------|
| 1 | Confirm the DEFAULT crypto policy | 2 min |
| 2 | Enable hybrid PQC key exchange in Nginx | 3 min |
| 3 | Reload Nginx | 1 min |
| 4 | Verify the PQC handshake from the client | 4 min |

### Detailed Steps

1. Select the **server** VM terminal tab.
2. Run the provided command to display the active system-wide crypto policy (for example, `update-crypto-policies --show`) and confirm it reports **DEFAULT**. Read the explanation that the DEFAULT policy on RHEL 10 already permits the hybrid ML-KEM-768 + X25519 group — no policy change is needed.
3. Open the Nginx server-block configuration file edited in Module 2.
4. Add the provided directive `ssl_ecdh_curve mlkem768_x25519;` to the TLS server block. Read the explanation: this tells Nginx to offer the hybrid post-quantum key-exchange group.
5. Run the provided command to test the configuration syntax (for example, `sudo nginx -t`) and confirm OK.
6. Reload Nginx to apply the change (for example, `sudo systemctl reload nginx`).
7. Switch to the **client** VM terminal tab.
8. Run the provided `openssl s_client` command against the server on port 8443, requesting the connection details.
9. Inspect the handshake output and locate the negotiated group / temporary key line. Confirm it reports the hybrid **mlkem768_x25519** group (the PQC "temp key"), proving the connection now uses post-quantum key exchange.
10. Optionally re-run the Module 2 `curl` test to confirm the site still serves normally — functionality is unchanged, but the key exchange is now quantum-safe.

### Key Takeaways

- On RHEL 10 the DEFAULT crypto policy already allows hybrid PQC — no policy override is required.
- A single Nginx directive, `ssl_ecdh_curve mlkem768_x25519`, switches the server to hybrid ML-KEM-768 + X25519 key exchange.
- The client-side `openssl s_client` output is how you prove the handshake really used the post-quantum group.
- The site behaves identically to the classical baseline while now defending against HNDL.

### Infrastructure Notes

- Relies on the RHEL 10 DEFAULT crypto policy, which supports the `mlkem768_x25519` group; no `update-crypto-policies` change is performed.
- Nginx continues to listen on TCP port **8443**; the client VM must retain network connectivity to the server.
- Module checkpoint: automated validation confirms the server negotiates the hybrid `mlkem768_x25519` group (a handshake from the client reports the PQC temp key).
