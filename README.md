# EDN: Entitlement-Defined Networking

**A Conceptual Model for Identity-Bound Network Adjacency**

---

## The Idea

Traditional network controls allow devices to attach *before* filtering traffic. This creates enumeration exposure, residual reachability after privilege changes, and audit gaps.

**EDN inverts this:** devices cannot reach or enumerate a segment without first proving entitlement.

> **Membership equals reachability.**
>
> No valid token → no adjacency → no discovery → no packets.

---

## What's Here

📄 **[EDN_CONCEPT.md](EDN_CONCEPT.md)** — The conceptual model (~1,100 words)

This is a research note exploring identity-bound network adjacency. It covers:

- The enumeration/residual reachability problem
- Segment-0 as a "sign-in lane"
- Entitlements vs. ACLs
- Anti-enumeration by default
- Revocation as a first-class primitive
- Signed evidence for every decision

---

## What This Isn't

This is not a product, not a specification, and not an implementation guide.

It's a thought experiment about what it might look like to bring zero-trust principles down the stack — to make network adjacency itself contingent on proof of entitlement.

A more detailed normative draft exists but is not published here.

---

## Author

**Nic Allen**

Feedback, critique, and conversation welcome: [LinkedIn](https://linkedin.com/in/nicallen27) [Email](mailto:nic@mangataventures.com)

---
Copyright © 2025 Nic Allen. All Rights Reserved. This document is provided for research and evaluation purposes only. No part of this architecture may be implemented in commercial software without express written permission.
