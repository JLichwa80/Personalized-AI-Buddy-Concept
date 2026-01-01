# System Architecture

## Overview

Personalized AI is a local-first, skill-driven intelligence system that combines hardware-rooted trust, encrypted personal memory, and executable skills. This document provides a **technical system overview** suitable for architects, engineers, and technical stakeholders.

The architecture is designed to support **privacy, control, and evolvability** while maintaining flexibility to integrate with optional cloud services and, in the future, robotics.

---

## Core Architectural Principles

1. **Local-first intelligence** — Data and models primarily reside on the user's device
2. **Hardware-rooted trust** — Identity and keys anchored in HSM; sensitive execution in TEE Enclave
3. **Skill-based operation** — Intelligence organized around executable skills, not ephemeral prompts
4. **Composable micro-agents** — MCPs serve as modular, domain-specific intelligence connectors
5. **Explicit user control** — All privileged actions require explicit authorization

---

## High-Level System Layers

### 1. User Interaction Layer

**Interfaces:**
- Conversational AI (voice)
- Chat/text UI
- Web/TV interfaces
- AR/VR and smart glasses
- Mobile device for approvals (push notifications / authenticator)

**Role:** Adapt interaction to user context, handle input/output, manage feedback loops.

---

### 2. AI Box (Edge Compute)

**Hardware:**
- GPU/NPU for model inference
- CPU with TEE Enclave for secure execution
- HSM for cryptographic keys and identity
- NVMe/SSD storage for encrypted data

**Role:** The authoritative node for executing models, storing memory, and orchestrating skills.

**Characteristics:**
- Consumer-grade device (similar to high-end gaming PC or DGX-class system)
- Always-on local compute
- Offline-capable for core functions
- Network-connected for cloud augmentation

---

### 3. Trust & Security Layer

**Why Trust Is the Foundation:**

Personalized AI is only viable if users fully trust the system with their most sensitive data: identity, finances, health, communications, and habits. Unlike cloud-based AI where trust is outsourced to providers, **Personalized AI makes trust local, explicit, and verifiable**.

#### Core Security Principles

1. **Local-First Trust** — All sensitive operations and data remain on the personal device by default
2. **Cryptographic Identity** — Identity is device-bound and hardware-backed, not account-based
3. **Explicit Consent for Privileged Actions** — No silent execution of sensitive operations
4. **Zero Implicit Trust in the Cloud** — Cloud services are optional, stateless, and memory-free

#### Personal AI Identity

Each AI Box has a **unique cryptographic identity**:
- Generated and stored inside the **HSM**
- Non-exportable private keys
- Used for:
  - Authentication to services
  - Secure local access control
  - Attestation of trusted execution

This identity enables secure authentication. Some services support identity-based access with MFA (using IP, cached cookies, etc.); others still require traditional credentials (stored in Vault).

#### Hardware Security Module (HSM)

**What it is:** Physical hardware component that stores and manages encryption keys

**Responsibilities:**
- Key generation and storage
- Cryptographic operations (encryption, signing)
- Secure boot verification
- Device attestation
- Approval enforcement for privileged operations

**Critical guarantee:** Private keys **never leave the HSM**.

#### TEE (Trusted Execution Environment) Enclave

**What it is:** Secure processing area within the CPU (not separate hardware)

**How it works:**
- Isolated area within the processor
- Protects sensitive code and data during execution
- Hardware-based memory encryption
- Shields from OS, hypervisor, and other processes

**Used for:**
- Executing sensitive AI operations
- Processing data from Vault
- Running privileged skill code
- Secure multi-party computation

**Together:** HSM stores the keys, TEE Enclave executes sensitive operations using those keys.

#### Combined Security Model

- Only trusted, measured software can access sensitive memory
- AI models are signed and verified before execution
- Skills run in isolated sandboxes (TEE Enclave when needed)
- Memory access is policy-controlled

This ensures:
- No rogue skills
- No data exfiltration
- No unauthorized model access

---

### 4. Two-Tier Data Storage

Personal data is stored in two separate tiers based on sensitivity:

#### Vault (FIPS Level + HSM Protection)

**Purpose:** Storage for highly sensitive data that could cause significant harm if compromised

**Contents:**
- Credentials and passwords
- API keys and access tokens
- Financial account details
- Social security numbers and tax IDs
- Private cryptographic keys
- Medical authentication credentials

**Security:**
- Keys stored in HSM (never leave hardware)
- Requires explicit HSM authorization for each access
- Hardware-enforced access control
- Tamper-resistant

**Access Pattern:**
- Privileged access requires user approval (with defense-in-depth at target services)
- Non-privileged access (e.g., regular web browsing) verified locally
- All access attempts logged and auditable

#### Encrypted Storage (Software-Protected)

**Purpose:** Storage for personal but less sensitive information

**Contents:**
- Documents, emails, media
- Addresses and contact information
- Phone numbers and publicly discoverable data
- Travel preferences and history
- Calendar data and schedules
- Preferences and behavioral patterns
- Long-term semantic embeddings
- Skill state and execution history
- Usage patterns and feedback

**Security:**
- Standard encryption at rest
- Keys managed by local system
- Fine-grained access policies
- Separate namespaces per skill

**Access Control:**
- Skills request specific data access
- User grants permissions explicitly
- Access is logged and auditable
- Revocation is immediate and complete

---

### 5. Local AI Model Stack

**Components:**
- Medium-scale LLMs (7B-70B parameters optimized for local inference)
- Planning & reasoning models
- Multimodal models (vision, speech)
- Code AI models (e.g., Qwen-coder) for skill development and automation
- Embedding and memory retrieval engines
- Specialized small models for specific tasks

**Optimization:**
- Latency-sensitive (real-time response)
- Contextually aware (long-term memory integration)
- Personalization-focused, not benchmark-focused
- Efficient inference on consumer hardware

**Model Management:**
- Models are versioned and signed
- Updates are verified before deployment
- Multiple models can coexist for different tasks
- Fine-tuning happens locally on user data

---

### 6. Skill System Layer

See [Skills & Platform](Skills_Platform.md) for comprehensive details.

**Quick Overview:**
- Skills are first-class, executable units of intelligence
- Lifecycle: Teach → Compile → Test → Deploy → Evolve
- Permissions explicitly enforced via HSM
- MCPs provide modular connectors with embedded intelligence

---

### 7. Browser Automation Layer

**Role:** Primary interface for all external world interaction.

The AI Box uses a **Chromium-based web browser** as its main mechanism for interacting with external systems. Any website accessible to a web browser can be controlled by the local AI on behalf of the user.

**Key Principle:** Browser automation, not REST APIs or services, is the default approach for external interactions.

**Capabilities:**
- On-behalf-of authentication using Vault credentials
- Full browser interaction (navigation, forms, clicking, scrolling, reading content)
- Multi-step workflows (login → navigate → interact → complete)
- Works with any web-based system (banking, airlines, e-commerce, social media, enterprise apps, etc.)

**Security Model:**
- **Outbound only** — AI Box initiates all connections; nothing is exposed to the internet
- **No inbound services** — no exposed ports, cannot be called from outside
- Credentials retrieved from Vault via HSM, used only for the session
- Sandboxed browser execution
- All actions logged and auditable

**Exception:** Cloud AI uses direct API calls (stateless).

---

### 8. Cloud AI Augmentation (Optional)

**Usage:** Research, media generation, high-compute tasks

**Principles:**
- Personal data labeled and not used in interaction without user approval
- Output validated locally before use

**Typical Use Cases:**
- Large-scale research and summarization
- Advanced media generation (images, video)
- Model training and fine-tuning (available, but local GPU should be able to handle it)
- Large corpus processing

**Constraints:**
- No raw access to Personal Data Vault
- All outputs reviewed and validated locally
- Optional and user-controlled
- Final intelligence always lives locally

---

### 9. Privileged Operation & Approval Layer

**Definition:** Privileged operations are actions that could have significant real-world impact if misused.

**Examples:**
- Financial transactions and payments
- Legal document signing
- Medical data access
- Identity assertions
- Home automation (locks, security)
- Email sending on your behalf

**Approval Workflow:**

1. AI query requires privileged operation (access denied)
2. User receives out-of-band approval request:
   - Mobile push notification
   - Biometric confirmation (fingerprint, face)
   - Hardware key or PIN
   - Context display (what, why, impact)
3. User approves or denies
4. Cryptographic authorization generated
5. Execution proceeds (or is blocked)

**Security Guarantees:**
- AI bypass not possible — even if AI hallucinates or malfunctions, target services enforce MFA/permissions independently (defense-in-depth requirement)
- Approvals are cryptographically verified
- All privileged actions are logged

---

### 10. Robotics Layer (North Star)

**Concept:** Robots act as **peripherals executing learned skills**

**Key Points:**
- Skills already exist in local AI node
- Safety constraints enforced by HSM and hardware sensors
- Execution requires explicit user approval for risky actions
- Local decision-making (no cloud dependency for real-time control)

---

## Data Flow Summary

```
1. User input arrives via Interaction Layer (Voice, Chat)
   ↓
2. Input processed by Local AI Model Stack
   ↓
3. Memory and context retrieved from Encrypted Storage when needed
   ↓
4. AI uses Browser to complete tasks
   ↓
5. [Optional] AI waits for user approval (push notification / authenticator)
   ↓
6. [Optional] Cloud processing for heavy computation
   ↓
7. Safe outputs delivered via Interaction Layer
   ↓
8. Feedback recorded and encrypted, results shown in browser (TV, VR, Monitor)
```

---

## Security Architecture Deep Dive

### Comparison with Cloud AI

| Aspect | Cloud AI (Copilot/Gemini) | Personalized AI |
|--------|---------------------------|-----------------|
| Identity | Account-based | Hardware-backed |
| Data Storage | Provider-controlled | User-controlled |
| Keys | Provider-managed | User-owned (HSM) |
| Memory | Centralized | Local, encrypted |
| Privileged Actions | Policy-based | Explicit approval |
| Trust Model | Provider promises | Cryptographic guarantees |

### Threat Model

**Protected Against:**
- Unauthorized data access
- Skill tampering
- Cloud provider surveillance
- Account compromise
- Model poisoning
- Data exfiltration

**Assumes:**
- User owns and controls the device
- HSM is not physically compromised
- User approves privileged actions intentionally
- Local network is reasonably secure

**Does Not Protect Against:**
- Physical device theft (but data remains encrypted), box identity access can be disabled
- User being coerced into approval
- Zero-day exploits in hardware/firmware
- Supply chain attacks on hardware

---

## Network Architecture

### Local Network
- AI Box connects to home/office network
- Discovers and connects to local MCP servers (skills)
- No cloud dependency for core operations

### Internet Connectivity
- Outbound only
- Required for:
  - Browser based automation of external websites
  - Cloud AI research tasks
  - Cloud Storage (optional, BYOK preferred where possible; some services like NotebookLM are provider-managed)
  - Software updates
- All external traffic is auditable

### Zero Trust Capabilities
- No implicit trust between components
- All connections authenticated
- Minimal privilege principle
- Bring your own certificate, key (for cloud interaction)

---

## System Architecture Sketch

```
┌─────────────────────────────────────────────────────────┐
│                  User Interaction Layer                  │
│         (Voice, Chat, Mobile, AR/VR, Web UI)            │
└────────────────────┬────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────┐
│                     AI Box (Local)                       │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Local AI Models (LLM, Vision, Speech)           │  │
│  │  • Reasoning & Planning                          │  │
│  │  • Multimodal Processing                         │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Skills System & MCP Layer                       │  │
│  │  • Skill Execution Engine                        │  │
│  │  • MCP Servers (Email, Calendar, Devices, etc)   │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Browser Automation Layer                        │  │
│  │  • Chromium-based browser (Playwright/DevTools)  │  │
│  │  • On-behalf-of authentication                   │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Data Storage (Two-Tier)                         │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │ Vault (HSM-Protected)                    │   │  │
│  │  │ • Credentials & Keys                     │   │  │
│  │  │ • Financial/Medical Auth                 │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │ Encrypted Storage                        │   │  │
│  │  │ • Documents, Emails, Media               │   │  │
│  │  │ • Preferences & Patterns                 │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  └──────────────────────────────────────────────────┘  │
│                                                          │
│  ┌──────────────────────────────────────────────────┐  │
│  │  Trust & Security Layer                          │  │
│  │  • HSM (Key Storage & Crypto Operations)         │  │
│  │  • TEE Enclave (Secure Execution)                │  │
│  │  • Approval Gate for Privileged Actions ─────────┼──┼──► Mobile (Push)
│  └──────────────────────────────────────────────────┘  │
└───────────┬─────────────────┬────────────────┬─────────┘
            │                 │                │
            │ Outbound        │ Outbound       │ Outbound
            │ Only            │ Only           │ Only
            ▼                 ▼                ▼
    ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
    │  Cloud AI    │  │  World Wide  │  │   Robotics   │
    │  Services    │  │  Web (WWW)   │  │   & Smart    │
    │              │  │              │  │   Devices    │
    └──────────────┘  └──────────────┘  └──────────────┘

    ⬆ All connections are OUTBOUND ONLY - no exposed ports, no inbound services
    ⬆ Only inbound: User input via local network (Voice, Chat)
    ⬆ Mobile approval responses travel via push notification service (not direct inbound)
```

**Key Components:**
- **Top Layer:** How users interact with the system
- **AI Box:** Your local hardware running everything
- **Models:** AI intelligence running on your device
- **Skills & MCP:** Additional personalized knowledge, repetitive tasks automation
- **Browser Automation:** Chromium-based browser for external web interactions
- **Two-Tier Storage:** Vault (HSM-protected) + Encrypted Storage
- **Trust & Security:** HSM for keys, TEE for secure execution, Approval Gate
- **World Wide Web:** Outbound-only access to any website via browser automation
- **Optional Cloud:** Knowledge research, complex tasks (fallback)
- **Devices:** Physical world integration (home robots, smart home)

---

## Performance Characteristics

### Storage
- Vault: (credentials, identities)
- Encrypted Storage: (personal data)
- Model storage: (Voice Conversational Model, Coding Model)
- Skill storage
- Cloud Based Knowledge i.e. NotebookLM (Optional)

### Compute
- On-demand inference for user queries
- Scheduled tasks execution

---

## Scalability

### Personal Scale
- Designed for single user or family
- Scales through skill personalization, and knowledge (local RAG, cloud NotebookLM)

---

## Technical Implementation Notes

### Language & Frameworks
- Skills: Python, Playwright, Chrome DevTools (Stealth Mode)
- MCPs: Language-agnostic interfaces

### Hardware Requirements
- DGX Spark like with Blackwell GPU, HSM, CPU with TEE support

---


## Why This Architecture Matters

This architecture enables:
- Safe delegation of real-world actions
- User ownership and control

Without local trust, AI remains a tool.  
With trust, it becomes an extension of the user.

---

## Summary

Personalized AI architecture prioritizes:
- User trust and ownership
- Privacy by design
- Skill-based intelligence
- Local control with optional cloud augmentation (fallback scenarios, research tasks)
- Evolution toward home devices, robots control


