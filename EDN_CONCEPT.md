# Entitlement-Defined Networking (EDN)

**A Conceptual Model for Identity-Bound Network Adjacency**

*Research Note · November 2025*

---

## The Problem

Traditional network controls — NAC, VLANs, ACLs, microsegmentation, ZTNA — share a common pattern: they allow a device to attach to the network *before* filtering its traffic.

This creates three persistent gaps:

**Enumeration exposure.** During the window between attachment and policy enforcement, devices can discover their neighbors. ARP requests, IPv6 Neighbor Discovery, mDNS, DNS-SD — all leak information about what exists on the segment. An attacker who gains a foothold can map the network before any firewall rule fires.

**Residual reachability.** When a user's privileges change — they leave the company, change roles, or trigger a security event — their device often retains network adjacency until someone manually intervenes. The identity system knows the user is gone; the network doesn't.

**Audit ambiguity.** Network admission decisions are scattered across RADIUS logs, switch configs, firewall rules, and DHCP leases. There's no portable, signed, tamper-evident record of *why* a device was allowed onto a segment at a specific time.

Zero Trust has addressed these gaps at the application layer. We authenticate before accessing apps. We enforce least-privilege on API calls. We log everything.

But at layer 2/3 — the network fabric itself — we're still running the same attach-then-filter model we've used for decades.

---

## The Idea

What if we inverted the model?

What if devices couldn't reach *or enumerate* a network segment without first proving they're entitled to be there?

**Entitlement-Defined Networking (EDN)** is a conceptual model exploring this inversion. The core idea is simple:

> **Membership equals reachability.**
> 
> If you are not a member of a segment, you cannot send packets to it, receive packets from it, or discover what's on it.

In this model, network adjacency is granted by short-lived, cryptographically-bound tokens — not by physical or VLAN membership. A device presents a token proving its identity and its right to join a specific segment. If the token is valid, the device gains adjacency. If not, the device is invisible to the segment and the segment is invisible to the device.

This isn't about replacing firewalls or encryption. Those still matter. EDN operates at a different layer: *who is allowed to be adjacent in the first place*.

---

## Key Concepts

**Segment-0: The Sign-In Lane.** When a device first connects, it lands in a minimal bootstrap segment. From Segment-0, the device can reach only the identity provider and the EDN control plane — nothing else. No lateral movement. No discovery. Just enough connectivity to authenticate and request an entitlement.

**Entitlements, Not ACLs.** Access is granted by presenting a short-lived token bound to the device's cryptographic identity. The token asserts: "This device is allowed to join this segment, for this duration, with this scope." If the token expires or is revoked, adjacency ends.

**Anti-Enumeration by Default.** Across segment boundaries, discovery protocols are suppressed. ARP, Neighbor Discovery, mDNS — silent. You can't probe what you can't see. Within a segment, admitted members can discover each other normally.

**Revocation as a First-Class Primitive.** Ending access isn't an afterthought. When an entitlement is revoked — whether by explicit action or token expiry — the device loses adjacency immediately. The control plane can measure and report revocation latency as an SLA.

**Signed Evidence.** Every admission decision — granted, denied, renewed, revoked, expired — is cryptographically signed and logged. This creates a portable, tamper-evident audit trail of who was on what segment, when, and why.

---

## How It Might Work

Imagine a laptop connecting to a corporate network:

1. **Isolation.** The laptop links up. The enforcement plane (a gateway, a switch, a software agent) detects it and places it in Segment-0. The laptop gets an IP address, but it can only reach the identity provider.

2. **Authentication.** The laptop authenticates — presenting credentials, certificates, device attestation, whatever the organization requires. The identity provider verifies the user and device.

3. **Entitlement.** The identity system requests an entitlement token from the EDN control plane. The token is minted, cryptographically bound to the device, scoped to a specific segment, and set to expire in minutes.

4. **Admission.** The laptop presents the token to the enforcement plane. The token is validated. The laptop is moved from Segment-0 to its authorized segment. It can now communicate with other admitted members.

5. **Renewal or Revocation.** Before the token expires, the laptop renews it. If renewal fails — because the user was offboarded, the device failed a posture check, or an admin triggered a revocation — the laptop is returned to Segment-0. Adjacency ends.

This is a sketch of a possible flow, not a specification. The details of how tokens are structured, how enforcement is implemented, and how edge cases are handled are left intentionally open.

---

## What This Doesn't Solve

EDN is not a silver bullet. It's a model for *controlling adjacency*, not a complete security architecture.

It doesn't protect against malicious behavior *within* an authorized segment. If an attacker compromises a device that holds a valid entitlement, they can still do damage inside that segment.

It doesn't replace encryption. EDN controls who can be adjacent; TLS, IPsec, and MACsec protect what flows between them.

It doesn't eliminate the need for good operational hygiene. Small segments, short token lifetimes, scoped entitlements, and clear audit practices still matter.

And there are hard implementation problems — bootstrapping trust for new devices, handling legacy hardware that can't participate in token flows, suppressing discovery traffic on commodity network gear. These are solvable, but they're real.

---

## Why This Might Matter

We've spent two decades building zero-trust principles into application access. Identity-aware proxies, continuous verification, least-privilege APIs, signed audit logs — all standard practice for apps.

But the network layer is still stuck in the old model. Devices attach, then we filter. Enumeration happens before policy. Revocation is slow and manual. Audit is fragmented.

EDN is an exploration of what it might look like to bring zero-trust principles down the stack — to make network adjacency itself contingent on proof of entitlement.

Whether this exact model is the right answer, I don't know. But the *question* feels important: Why do we still grant network presence before verifying the right to be present?

---

*A more detailed normative draft — with API semantics, token schemas, conformance criteria, and implementation considerations — exists but is not published here.*

*Feedback, critique, and conversation welcome: [LinkedIn](https://linkedin.com/in/nicallen27)*
