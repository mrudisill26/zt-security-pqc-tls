# Module 2: Deploy a Classical TLS Web Server

### Brief Overview

In this module learners stand up a working baseline: an Nginx web server on the RHEL 10 **server** VM, secured with classical TLS 1.3 using an ECDSA P-256 certificate and listening on port 8443. They then switch to the RHEL 10 **client** VM and confirm the server is reachable and serving traffic over TLS. This gives everyone a known-good classical deployment to upgrade in Module 3 — and establishes the two-machine client/server network path that later modules depend on. Every command is provided as a copy-paste block with a plain-language explanation.

### Audience and Time

- **Personas:** Beginner Linux users and administrators. No prior web server or TLS configuration experience assumed.
- **Prerequisites for this module:** Completion of Module 1 (conceptual). Access to both the server and client VMs, which are pre-provisioned.
- **Estimated duration:** 8 minutes.

### Learning Objectives

- Install the Nginx web server on the RHEL 10 server VM.
- Generate an ECDSA P-256 certificate for the server.
- Configure Nginx to serve classical TLS 1.3 on port 8443.
- Verify the deployment from the separate client VM using a TLS client request.

### Lab Structure

| Section | Title | Duration |
|---------|-------|----------|
| 1 | Install Nginx on the server VM | 2 min |
| 2 | Create an ECDSA P-256 certificate | 2 min |
| 3 | Configure and start TLS 1.3 on port 8443 | 2 min |
| 4 | Verify from the client VM | 2 min |

### Detailed Steps

1. Select the **server** VM terminal tab in the Showroom interface.
2. Run the provided command to install the `nginx` package (for example, `sudo dnf install -y nginx`). Observe the installation completing successfully.
3. Run the provided `openssl` command to generate a self-signed **ECDSA P-256** certificate and its private key, saving them to the path referenced by the Nginx config. Read the explanation of what a certificate and private key are.
4. Open (or write) the Nginx server-block configuration file using the provided copy-paste block. The block sets the server to `listen` on port **8443** with `ssl`, points at the certificate and key, and enables **TLS 1.3**.
5. Run the provided command to test the Nginx configuration syntax (for example, `sudo nginx -t`) and confirm it reports OK.
6. Start and enable the Nginx service (for example, `sudo systemctl enable --now nginx`). Confirm the service is active.
7. If required, run the provided `firewall-cmd` command to open port 8443 on the server VM so the client can reach it.
8. Switch to the **client** VM terminal tab.
9. Run the provided test command from the client to connect to the server on port 8443 (for example, `curl -k https://<server>:8443/` or `openssl s_client -connect <server>:8443`). Observe the successful TLS 1.3 connection and the expected HTTP 200 response.
10. Note in the handshake output that the certificate uses ECDSA P-256 and that the negotiated key exchange is *classical* (no PQC yet) — this is the baseline Module 3 will upgrade.

### Key Takeaways

- Nginx can serve TLS 1.3 with an ECDSA P-256 certificate using a small, readable configuration.
- The server VM hosts the service; the client VM verifies it — the network path between them is real and is what makes later failure modes reproducible.
- Port 8443 is the agreed listening port for the entire lab.
- This classical TLS 1.3 deployment is the known-good baseline that the PQC upgrade builds on.

### Infrastructure Notes

- Requires the `nginx` and `openssl` packages to be installable on the server VM (repositories reachable or pre-staged).
- Nginx listens on TCP port **8443**; ensure the firewall on the server VM allows it and that the client VM has network connectivity to the server.
- Module checkpoint: automated validation confirms Nginx is serving classical TLS 1.3 on port 8443 (a request from the client returns the expected 200 response).
