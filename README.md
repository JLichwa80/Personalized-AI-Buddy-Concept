# Personalized AI

![Interaction diagram](interaction.png)

## Overview

**Personalized AI Buddy** is a local-first, privacy-preserving, skill-based AI system built around the individual. Unlike today's cloud-centric assistants, your AI Buddy treats personal data ownership, hardware-rooted trust, and user-taught skills as core architectural primitives.

> Intelligence should live where your life lives.

---

## Core Idea

Your Personalized AI Buddy runs primarily on your own devices, learns your habits and skills over time, keeps your data private by default, and acts on your behalf only with explicit approval.

It's not a chatbot. It's not a cloud API.  
It's an **operating environment for personal intelligence**.

---

## What Makes This Different

| Today's AI (Copilot, Gemini) | Personalized AI |
|-----------------------------|-----------------|
| Cloud-first | Local-first |
| Account-based memory | Hardware-bound memory |
| Prompt-driven | Skill-driven |
| Vendor-controlled | User-owned |
| Implicit trust | Cryptographic trust |

---

## Design Principles

1. **Local-first by default** — Personal data and memory stay local
2. **Privacy as architecture** — Not a feature, but a system property
3. **Skills over prompts** — Executable, testable intelligence
4. **Hardware-rooted trust** — Identity and encryption anchored in secure hardware
5. **Human-in-the-loop control** — Privileged actions always require approval
6. **Composable evolution** — From personal skills to physical robots

---

## Key Components

### AI Box
Your local compute device with GPU, secure storage, and HSM for trust.

### Two-Tier Data Storage
Hardened Vault (HSM-protected) for credentials and sensitive data; Encrypted Storage for documents, preferences, and personal information.

### Skills System
Learned, executable capabilities that evolve with you over time.

### Model Context Protocol (MCP)
Standard interface connecting skills to apps, devices, and systems.

### Optional Cloud Augmentation
Heavy compute tasks use cloud AI—but stateless, memory-free, and under your control.


## Target Users

- **Individual consumers** seeking Home AI Automation

---

## Documentation Structure

- **[Vision.md](Vision.md)** — Vision, manifesto, and why this matters
- **[System_Architecture.md](System_Architecture.md)** — Technical architecture and trust model
- **[Skills_Platform.md](Skills_Platform.md)** — Skills system, MCP, and developer platform
- **[Market_Comparison.md](Market_Comparison.md)** — How this differs from cloud AI
- **[Use_Cases.md](Use_Cases.md)** — Real-world applications and examples
- **[Security_Privacy.md](Security_Privacy.md)** — Security and privacy details

---

## Getting Started

1. Read the [Vision](Vision.md) to understand the why
2. Review the [System Architecture](System_Architecture.md) to see how it works
3. Explore the [Skills & Platform](Skills_Platform.md) to understand capabilities

---

## Current Status

This project focuses on **conceptual architecture and system design**. 

---

## License

TBD (conceptual documentation only)

---

## Contact

For questions, feedback, or collaboration you can contact me at jacklichwa@gmail.com or create an issue.
