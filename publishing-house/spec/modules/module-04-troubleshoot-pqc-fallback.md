# Module 4: Troubleshoot PQC Breaking Points & Fallback

### Brief Overview

The largest and most realistic module: learners deliberately reproduce two production failure modes that hybrid PQC introduces, then apply the engineering fixes. First, because the post-quantum handshake is larger than a classical one, it can be fragmented by a network path with a smaller MTU — causing connections to hang or fail. Learners trigger this on the real client-to-server path and fix it with Path MTU Discovery (PMTUD) and MSS clamping. Second, a legacy client that only speaks classical X25519 fails the handshake against a PQC-only server. Learners reproduce that failure and fix it with a hybrid *fallback* configuration that offers both PQC and classical groups. This module is why the lab needs two real machines.

### Audience and Time

- **Personas:** Beginner Linux users and administrators who completed the PQC upgrade in Module 3.
- **Prerequisites for this module:** A working hybrid PQC Nginx server (Module 3) reachable from the client VM.
- **Estimated duration:** 12 minutes.

### Learning Objectives

- Reproduce MTU fragmentation of the larger PQC handshake on the real client/server network path.
- Fix the fragmentation failure using Path MTU Discovery (PMTUD) and MSS clamping.
- Reproduce a legacy-client handshake failure against a PQC-only server.
- Implement a hybrid fallback configuration (`ssl_ecdh_curve mlkem768_x25519:X25519:prime256v1`) that preserves backward compatibility.

### Lab Structure

| Section | Title | Duration |
|---------|-------|----------|
| 1 | Reproduce the MTU fragmentation failure | 3 min |
| 2 | Fix it with PMTUD / MSS clamping | 3 min |
| 3 | Reproduce the legacy-client handshake failure | 3 min |
| 4 | Fix it with hybrid fallback | 3 min |

### Detailed Steps

1. Read the explanation that the hybrid PQC handshake is *larger* than a classical one because ML-KEM-768 keys are bigger, so it is more sensitive to network MTU limits.
2. On the **server** (or the path between the VMs), follow the provided commands to lower the effective MTU / simulate a constrained path, reproducing the condition where large handshake packets get fragmented.
3. Switch to the **client** VM and run the provided `openssl s_client` / `curl` command against the server. Observe the handshake hanging or failing — this is the MTU fragmentation failure.
4. Apply the fix: run the provided commands to enable Path MTU Discovery (PMTUD) and clamp the TCP MSS so packets fit the path without fragmenting.
5. Re-run the client test and confirm the large PQC handshake now completes successfully.
6. Read the explanation of the second failure mode: a legacy client that only supports classical X25519 cannot negotiate with a server offering only the PQC group.
7. From the **client** VM, run the provided command that simulates a legacy X25519-only client connecting to the server (for example, an `openssl s_client` invocation restricted to classical groups). Observe the handshake failure.
8. Switch to the **server** VM and edit the Nginx configuration, replacing the single-group directive with the provided hybrid fallback list: `ssl_ecdh_curve mlkem768_x25519:X25519:prime256v1;`. Read the explanation that this offers the PQC group first but falls back to classical groups for older clients.
9. Test the configuration (`sudo nginx -t`) and reload Nginx (`sudo systemctl reload nginx`).
10. From the client, re-run *both* tests: the legacy X25519-only client now connects successfully, and a modern client still negotiates the hybrid `mlkem768_x25519` group. Confirm backward compatibility without sacrificing PQC protection.

### Key Takeaways

- Hybrid PQC handshakes are larger than classical ones and can be broken by network MTU limits — a real, path-dependent failure.
- PMTUD and MSS clamping let the larger PQC handshake traverse constrained network paths.
- A PQC-only server rejects legacy clients that only speak classical key exchange.
- A hybrid fallback list (`mlkem768_x25519:X25519:prime256v1`) preserves backward compatibility: modern clients get PQC, legacy clients still connect.
- Two real machines are what make these network-level failures observable and fixable.

### Infrastructure Notes

- Requires two RHEL 10 VMs with a real network path between them so MTU fragmentation of the larger PQC handshake is reproducible.
- The MTU section adjusts network path MTU and applies PMTUD / TCP MSS clamping; the fallback section edits the Nginx `ssl_ecdh_curve` directive and reloads the service.
- Nginx continues to listen on TCP port **8443**.
- Module checkpoint: automated validation confirms the PMTUD/MSS fixes let the large PQC handshake complete, and that the hybrid fallback lets a legacy X25519-only client connect successfully.
