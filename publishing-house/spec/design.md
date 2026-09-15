# Post-Quantum TLS on RHEL 10: Deploy, Break, and Fix a Quantum-Safe Web Server

<!-- This file is the design document for your lab or demo. -->

## Overview

This hands-on lab teaches how to protect web traffic against "Harvest Now, Decrypt Later" (HNDL) attacks using the post-quantum cryptography built into Red Hat Enterprise Linux 10. It is Part 2 of a three-part RH1 lab series and is the longest of the three (~35 minutes). Adversaries record encrypted traffic today so they can decrypt it once a cryptographically relevant quantum computer exists; hybrid post-quantum key exchange defends against that threat now.

Participants deploy an Nginx web server with classical TLS 1.3 on a RHEL 10 server, connect to it from a separate RHEL 10 client, upgrade the server to hybrid ML-KEM-768 + X25519 key exchange, then deliberately reproduce two real production failure modes — network MTU fragmentation of the larger PQC handshake and handshake failures from legacy clients — and apply the engineering fixes, including a hybrid fallback configuration.

## Target Audience

- **Role:** Linux users, administrators, and security-curious practitioners attending the RH1 lab series
- **Experience level:** Beginner — new to Linux; every command is provided and explained
- **What they already know:** Basic comfort following guided, step-by-step instructions; foundational concepts introduced in Part 1 of the series
- **What they don't know:** How TLS key exchange works, what post-quantum cryptography is, how to configure a web server, and why larger PQC handshakes break on real networks

## Prerequisites

- None required to begin — the lab is fully guided and self-contained. Part 1 of the series provides helpful context but is not mandatory.
- Can the lab validate these automatically? No — there are no entry prerequisites to check. Progress within the lab is validated automatically at each module via solve/validate checkpoints.

## Learning Objectives

1. Deploy an Nginx web server with classical TLS 1.3 (ECDSA P-256) on RHEL 10
2. Configure hybrid ML-KEM-768 + X25519 post-quantum key exchange in Nginx
3. Troubleshoot PQC-induced network failures — MTU fragmentation and legacy-client handshake errors
4. Implement a hybrid fallback configuration that preserves backward compatibility

## Content Type

Lab (hands-on)

## Products & Technologies

- Red Hat Enterprise Linux 10

<!-- Upstream projects / tools used in the lab, listed separately from Red Hat products: -->
- Nginx (upstream web server)
- OpenSSL 3.x (upstream TLS toolkit)

## Module Map

| Module | Title | Duration |
|--------|-------|----------|
| 1 | Fundamentals & the PQC Threat Model | 5 min |
| 2 | Deploy a Classical TLS Web Server | 8 min |
| 3 | Upgrade to Post-Quantum TLS | 10 min |
| 4 | Troubleshoot PQC Breaking Points & Fallback | 12 min |
| — | **Total hands-on** | **~35 min** |
| — | Intro / orientation | ~5 min |
| — | **Total lab** | **~40 min** |

<!-- Modules build on each other: theory → working baseline → PQC upgrade → break and fix. -->

## Difficulty Level

Beginner

## Environment

**Learner view:** When the lab starts, two pre-provisioned RHEL 10 machines are ready and reachable — a **server** VM (where the learner installs and configures the Nginx web server) and a **client** VM (used to connect to the server, inspect TLS handshakes with OpenSSL/curl, and simulate a legacy client). The learner interacts through the guided zero-touch Showroom interface with terminal access to both machines. All commands are provided as copy-paste blocks with plain-language explanations. The two-machine layout is required so that the network path between client and server is real — that is what makes the MTU fragmentation failure in Module 4 reproducible.

**Automation needed:** Yes

- Provision two RHEL 10 VMs per student (server + client) with network connectivity between them.
- Ensure the `nginx` and `openssl` packages are installable (repositories reachable or pre-staged).
- Provide per-module solve/validate automation for the zero-touch checkpoints.

## Infrastructure Requirements

- **Platform:** RHEL VMs
- **Cloud provider:** CNV
- **Topology:** Per-student (each learner gets their own pair of VMs)
- **Sizing:** 2 RHEL 10 VMs per student — 1 server (2 vCPU, 4GB RAM, 30GB disk) running Nginx + OpenSSL, and 1 client (2 vCPU, 4GB RAM, 30GB disk) for handshake tests and legacy-client simulation. A real network path between the two is required for the Module 4 MTU failure.
- **Automation approach:** Ansible
- **AI/MaaS:** None
- **External services:** Red Hat CDN — `cdn.redhat.com` and `subscription.rhsm.redhat.com` (for installing the `nginx` and `openssl` packages during provisioning). No external services are reached during the student session — all lab traffic stays between the two VMs.
- **AAP version:** N/A (AAP not used)
- **Non-GA products:** None — RHEL 10 is GA.

## Assessment Strategy (Optional)

This is a zero-touch lab, so each module ends with an automated solve/validate checkpoint:

- **Module 1:** Knowledge check — no system state to validate (trust-based / short quiz).
- **Module 2:** Validate that Nginx is serving classical TLS 1.3 on port 8443 (a request from the client returns the expected 200 response).
- **Module 3:** Validate that the server negotiates the hybrid `mlkem768_x25519` group (handshake from the client reports the PQC temp key).
- **Module 4:** Validate that PMTUD/MSS fixes allow the large PQC handshake to complete, and that the hybrid fallback lets a legacy X25519-only client connect successfully.
