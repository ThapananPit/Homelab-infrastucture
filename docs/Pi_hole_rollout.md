# DNS: Pi-hole Rollout Plan

## Goal

Introduce network-wide DNS filtering (ad/tracker blocking, visibility into DNS queries) without risking an outage if it fails, and without prematurely treating an unproven service as core infrastructure.

## Current Setup

Pi-hole is currently running as a **VM on the TrueNAS server**, not on dedicated hardware.

- Server: `198.51.100.10` *(placeholder — internal LAN address)*
- Pi-hole VM: `198.51.100.20` *(placeholder — internal LAN address)*

This is a deliberate interim step, not the end state. Server uptime isn't yet consistent enough for me to be comfortable treating the Pi-hole VM as part of core infrastructure — if the server reboots or goes down for maintenance, DNS resolution for any device pointed at it goes down too. Running it as a VM first lets me validate Pi-hole's behavior (blocklist tuning, false positives, general reliability) before committing to a hardware move.

**Planned next step:** migrate Pi-hole to a dedicated Raspberry Pi once server uptime is predictable, so DNS no longer shares fate with the rest of the server's workloads. This removes the single point of failure the current setup still has.

## Rollout Strategy

Rather than pointing the router's DNS at Pi-hole immediately (which would make every device on the network dependent on it at once), I'm rolling it out gradually:

1. **Phase 1 (current):** Manually set Pi-hole as the DNS resolver on select non-essential devices only. This validates behavior under real usage without risking a network-wide outage if something's misconfigured.
2. **Phase 2:** Expand to additional devices as confidence builds, prioritizing lower-criticality devices first.
3. **Phase 3 (gated):** Point the MikroTik router's DNS at Pi-hole for the whole network — only once uptime is predictable and the hardware migration (VM → dedicated Pi) is complete.

## Fallback Handling

Every device currently pointed at Pi-hole has a **secondary/fallback DNS server** configured wherever the device supports it, so a Pi-hole outage degrades to normal DNS resolution rather than a full loss of connectivity. This was a deliberate addition before starting the rollout, not an afterthought.

## Static IP Reservations

While rolling this out, I also started converting select devices from DHCP to static IP, reserved at the router level. This isn't strictly required for Pi-hole, but it's a natural point to start doing it — predictable, easily identifiable addressing makes DNS rules, firewall policy, and troubleshooting simpler as the network grows.

## Cutover Criteria

Before Pi-hole becomes the router-wide DNS provider, the following need to be true:

- [ ] Pi-hole migrated off the TrueNAS server onto dedicated hardware (Raspberry Pi)
- [ ] Server/VM uptime logged and shown to be consistent over a meaningful period (target: 2+ weeks without unplanned downtime)
- [ ] Fallback DNS confirmed working on all rolled-out devices (tested by intentionally stopping Pi-hole and verifying resolution still works)
- [ ] No unresolved false-positive blocking issues from Phase 1/2 devices

## Status

Currently in **Phase 1** — Pi-hole running as a VM, rolled out to non-essential devices only, fallback DNS configured, static IP reservations in progress.
