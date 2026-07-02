# Homelab Infrastructure

Personal homelab for network security experimentation, self-hosting, and hardware integration projects. Built and maintained as a hands-on complement to my Cybersecurity Engineering studies — a live environment for practicing network segmentation, remote access architecture, and systems administration beyond coursework.

## Why

I wanted a real environment to apply security concepts in practice rather than only in labs/CTFs — designing for redundancy, secure remote access under real ISP constraints, and isolating experimental/untrusted workloads from core infrastructure.

## Architecture Overview

*(insert sanitized network diagram here — RFC 5737 documentation ranges, generic hostnames)*

The lab is built around a repurposed PC running **TrueNAS** as the core server, with workloads separated into VMs by trust level and purpose. Remote access is handled via a dedicated router-level VPN rather than host-level exposure, with a deliberate design choice to avoid port forwarding entirely.

## Components

| Component | Role | Notes |
|---|---|---|
| TrueNAS server | Core host / storage | Repurposed PC, hosts VMs |
| Linux VM (Klipper) | 3D printer control | Isolated from other workloads |
| Isolated VM (OpenClaw) | LLM-agent experimentation | Sandboxed; runs OpenClaw code only, no local inference |
| Main PC (GPU) | LLM inference backend | Serves inference API to the OpenClaw VM over the internal network |
| MikroTik router | Network edge / VPN gateway | Runs WireGuard for remote access |
| VPS (relay) | CGNAT bypass | Persistent WireGuard keepalive tunnel; see below |
| Raspberry Pi (planned) | Pi-hole DNS | Being split onto dedicated hardware to remove single point of failure |

## Design Decisions Worth Noting

**Remote access without port forwarding.** My ISP only provides CGNAT and gates real static/public IP access behind an ISP-controlled service. Rather than depend on that (and risk losing remote access entirely on an ISP switch), I set up a lightweight VPS as a WireGuard relay with a persistent keepalive tunnel from the MikroTik router. This makes remote access **ISP-agnostic** — the architecture doesn't change if I switch providers, since the tunnel originates from my side rather than depending on inbound port forwarding.

**Workload isolation by trust level.** The OpenClaw VM is deliberately sandboxed and separated from the TrueNAS host's other services, since it's experimental/agentic code. It runs on older hardware not powerful enough for local inference, so it calls out to an inference API served by my main PC's GPU over the internal network — keeping the heavier compute on dedicated hardware while the agent logic stays isolated.

**No single point of failure for DNS.** Pi-hole is being moved to a dedicated Raspberry Pi rather than run as another VM on the main server, so DNS resolution doesn't go down if the TrueNAS box needs maintenance or reboots.

## What I've Learned So Far

- Practical tradeoffs between port forwarding and VPN-relay approaches for remote access under restrictive ISPs
- VM-level isolation strategies for running less-trusted/experimental workloads (agentic LLM tooling) alongside stable services
- Splitting compute-heavy inference from lightweight agent logic across separate machines on a local network
- Designing for redundancy (DNS single-point-of-failure removal) as an ongoing iteration, not a one-time setup

## Roadmap

- [ ] Finish Pi-hole migration to dedicated Raspberry Pi
- [ ] Document network segmentation / VLAN policy
- [ ] Write up OpenClaw isolation setup in detail
- [ ] Add sanitized topology diagram

## Contact

- LinkedIn: [add link]
- Portfolio: [add link]
