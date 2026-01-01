# Security & Privacy Architecture

## Overview

Security and privacy in Personalized AI Buddy are not features—they are **architectural foundations**. Every component is designed with the principle that trust must be cryptographically enforced, not policy-promised.

This document explains the security model, components, and workflows that make your AI Buddy trustworthy.

---

## Core Security Principles

### 1. Local-First by Design
- Your AI Buddy runs entirely on your local device
- No shared infrastructure serving multiple users
- Your data never goes to company servers by default
- Secured exclusively for you

### 2. Hardware-Rooted Trust
- Identity anchored in physical hardware (HSM)
- Keys never leave secure hardware
- Cryptographic guarantees replace policy promises

### 3. Explicit User Control
- No silent or automatic actions
- Privileged operations require explicit approval
- You remain in the center of all decisions

### 4. Zero Trust Architecture
- No implicit trust between components
- All connections authenticated
- All data flows encrypted
- Minimal privilege principle

---

## Security Components

### Hardware Security Module (HSM)

**What it is:**
Physical hardware component integrated into your AI Box.

**Purpose:**
- Stores cryptographic keys that never leave the hardware
- Provides hardware-rooted identity
- Enables cryptographic operations
- Protects access to Vault

**Key Responsibilities:**
- Generate and securely store encryption keys
- Sign operations cryptographically
- Authenticate device identity
- Enforce privileged operation approvals
- Protect sensitive credentials

**Critical Guarantee:**
Private keys are **non-exportable**. Even if someone steals your AI Node, they cannot extract the keys or impersonate your identity.

---

### TEE (Trusted Execution Environment) Enclave

**What it is:**
Secure processing area within your CPU (not separate hardware).

**Purpose:**
- Execute sensitive code in isolation
- Protect data during processing
- Shield from operating system and other processes

**How it works:**
- Isolated memory region within processor
- Hardware-based memory encryption
- Protected from OS, hypervisor, and other applications
- Ensures confidentiality and integrity during execution

**Used for:**
- Processing data from Vault
- Running privileged AI operations
- Executing sensitive skill code
- Secure multi-party computations

**Working Together:**
- **HSM** stores the keys
- **TEE Enclave** executes sensitive operations using those keys
- Neither exposes sensitive data outside secure boundaries

---

## Two-Tier Data Storage

Your data is stored in two separate tiers based on sensitivity level.

### Tier 1: Vault

**Purpose:**
Storage for highly sensitive data that could cause significant harm if compromised.

**Security Level:**
- FIPS Level security
- HSM-protected with highest compliance requirements

**Contents:**
- Login credentials and passwords
- API keys and access tokens
- Financial account details (bank accounts, credit cards)
- Government IDs (social security numbers, passport numbers, tax IDs)
- Private cryptographic keys
- Medical authentication credentials
- Any data usable for identity theft or financial fraud

**Security:**
- Keys stored in HSM (never exported)
- Every access requires HSM authorization
- Hardware-enforced access control
- Tamper-resistant
- FIPS Level compliance
- Access attempts logged and auditable

**Access Pattern:**
- Each access requires explicit HSM verification
- Cannot be bulk-exported
- Subject to privileged operation rules
- Time-limited access tokens

**Size:** Typically 1-10GB (sensitive data only)

---

### Tier 2: Encrypted Storage

**Purpose:**
Storage for personal but less critical information.

**Security Level:**
- Encrypted with HSM keys (hardware-backed)
- Does NOT require FIPS Level compliance
- Standard security tier

**Contents:**
- Documents, emails, media files
- Addresses and contact information
- Phone numbers and publicly discoverable data
- Travel preferences and history
- Calendar data and schedules
- Usage patterns and behavioral data
- Skill state and execution history
- Long-term semantic embeddings

**Security:**
- Encryption at rest with HSM keys (hardware)
- Keys managed by HSM (same hardware as Vault)
- Fine-grained access policies per skill
- Separate namespaces for isolation
- Access logged and auditable

**Important:** Both Vault and Encrypted Storage use HSM keys. The difference is FIPS Level compliance requirements, not hardware vs. software encryption.

**Access Control:**
- Skills request specific data access
- User grants permissions explicitly
- Access can be revoked instantly
- All access auditable

**Size:** Typically 500GB - 2TB

---

## Hardware-Bound Identity

### What It Is

Your AI Buddy has a unique cryptographic identity generated and stored in the HSM. This identity is **bound to your physical device** and cannot be copied or transferred.

### How It Works

**Identity Generation:**
1. HSM generates unique key pair during initial setup
2. Private key never leaves HSM
3. Public key becomes device identity
4. Device identity registered to you (the owner)

**Authentication:**
- Your AI Buddy authenticates to services using hardware-bound identity
- Services verify the signature from your HSM
- Impossible to impersonate without physical access to HSM
- Even with physical access, keys are non-exportable

### Use Cases

**Accessing Your Services:**
When your AI Buddy needs to access Amazon, airlines, banking, etc.:
1. Uses hardware-bound identity to authenticate
2. HSM provides cryptographic proof of identity
3. Retrieves credentials from Vault
4. Accesses service on your behalf

**Signing Operations:**
When executing privileged actions:
1. Operation prepared
2. HSM signs operation with your device identity
3. Cryptographic audit trail created
4. Non-repudiable proof of authorization

---

## Browser with Hardware-Bound Login

### How Your AI Buddy Accesses Services

Your AI Buddy uses a **browser with hardware-bound authentication** to access your online services (Amazon, airlines, banking, etc.).

**Architecture:**
- Local browser instance (Chromium-based or similar)
- Credentials stored in Vault
- Hardware-bound identity via HSM
- No cloud involvement for personal services

**Workflow Example - Amazon Order:**

```
1. You tell AI Buddy: "Order my usual coffee beans"
2. AI Buddy initiates browser session
3. HSM provides hardware-bound authentication
4. Browser accesses Vault for Amazon credentials
5. Logs into Amazon automatically
6. Navigates to your usual product
7. Adds to cart
8. Proceeds to checkout
9. [Privileged operation trigger] - Payment required
10. Sends approval request to your device
11. You approve with passkey/biometric
12. Completes purchase
13. Browser session closed, credentials returned to Vault
```

**Security Features:**
- Credentials never stored in browser memory permanently
- Hardware-bound session prevents hijacking
- All actions logged for audit
- Automatic session cleanup after use

---

## Privileged Operations & Approval Workflows

### What Are Privileged Operations?

Operations that could cause significant impact if misused:
- Financial transactions (payments, transfers)
- Legal actions (signing documents, contracts)
- Identity assertions (accessing medical records, government services)
- Account modifications (changing passwords, permissions)
- Large purchases or commitments

### Approval Workflow

**Step-by-Step Process:**

```
1. AI Buddy prepares operation
   ↓
2. System identifies as privileged (requires approval)
   ↓
3. Approval request generated with full context
   ↓
4. Request sent to your personal device (phone, tablet)
   ↓
5. You receive notification with details:
   - What action is being requested
   - What data will be accessed
   - What the impact will be
   ↓
6. You review and decide:
   - Approve (with passkey/biometric)
   - Deny
   - Modify parameters
   ↓
7. If approved:
   - HSM cryptographically signs authorization
   - Operation executes
   - Audit log created
   ↓
8. If denied:
   - Operation cancelled
   - AI Buddy informed
   - No action taken
```

**Example - Flight Booking:**

```
Approval Request:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✈️ Flight Booking

Action: Purchase flight ticket
Route: Chicago → Paris
Date: June 15, 2026
Airline: American Airlines
Cost: $847.50

Data Accessed:
• Passport information
• Traveler preferences
• Payment method: Visa ****4532

[Fingerprint to Approve]
[Deny]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**Security Guarantees:**
- Out-of-band approval (separate device)
- Biometric or hardware key required
- Time-limited approval window
- Full context displayed before approval
- Cryptographically signed authorization
- Non-repudiable audit trail

---

## Privacy Model

### What Stays Local

**Always Local (Never Leaves Your Device):**
- All data in Vault
- All data in Encrypted Storage
- Your credentials and passwords
- Your browsing history
- Your communication history (emails, messages)
- Your financial information
- Your health records
- Your usage patterns and preferences
- All AI Buddy interactions and conversations

**How This Is Enforced:**
- Network isolation by default
- Firewall rules prevent unauthorized outbound
- HSM prevents data export
- Skills cannot exfiltrate data
- All network access logged and auditable

---

### What Can Go to Cloud (Optional)

**Only When You Enable Cloud Research:**
- Generic, anonymized research queries
- Public information requests
- Non-personal context

**Example:**
❌ "Research vacation destinations based on my travel history"
✅ "Research vacation destinations in Europe for summer"

**Safeguards:**
- Cloud AI receives no personal identifiers
- No conversation history shared
- Stateless (cloud retains nothing)
- Results returned and stored locally
- Cloud can be disabled entirely

---

### AI Notebook Service

**What It Is:**
Cloud-based document processing service (similar to Google NotebookLM) for non-sensitive content.

**Use Cases:**
- Upload non-sensitive documents (research papers, articles, public information)
- Ask questions about the content
- Generate summaries and insights
- Create audio podcasts and video content
- Extract key concepts and connections

**Privacy Considerations:**
- **Cloud-based service** - NOT local processing
- **Never upload sensitive or personal information**
- Only use for public, non-sensitive content
- Examples of appropriate content:
  - Research papers and academic articles
  - Public industry reports
  - News articles and analysis
  - General educational materials

**Examples of INAPPROPRIATE content:**
- Personal documents
- Financial records
- Health information
- Customer data
- Proprietary business information
- Anything with credentials or personal identifiers

**Example Workflow:**
```
1. Upload 50 public research papers on quantum computing
2. AI Notebook Service indexes them (in cloud)
3. Ask: "What are the main approaches to error correction?"
4. Service answers from uploaded papers
5. Generate: "Create a 20-minute podcast discussing these papers"
6. Processing happens in cloud with non-sensitive content only
```

---

## Threat Model

### What We Protect Against

**✅ Protected:**
- Account compromise (credentials in HSM)
- Data breaches (no centralized data)
- Unauthorized access (hardware-bound identity)
- Credential theft (keys never exported)
- Man-in-the-middle attacks (encrypted connections)
- Rogue skills (sandboxing and signing)
- Unauthorized payments (approval required)
- Identity theft (HSM protection)
- Surveillance (local processing)

**⚠️ Partial Protection:**
- Physical device theft (data encrypted, but device could be tampered with)
- Supply chain attacks (hardware trust required)
- Social engineering (user can be tricked into approving)
- Zero-day exploits (security updates required)

**❌ Not Protected:**
- User deliberately exporting data
- User approving malicious operations
- Compromised hardware vendor
- Quantum computing attacks (future threat)

---

### Attack Scenarios & Mitigations

**Scenario 1: Stolen Device**

Attack: Thief steals your AI Box

Mitigations:
- All data encrypted (Vault + Encrypted Storage)
- HSM keys are non-exportable
- Device identity bound to hardware
- Cannot make payments or access services without approval
- Remote wipe capability (if implemented)

Result: Data remains secure, device unusable without HSM access

---

**Scenario 2: Compromised Skill**

Attack: Malicious skill attempts to exfiltrate data

Mitigations:
- Skills run in isolated sandboxes
- Network access requires explicit permission
- Cannot access Vault without HSM authorization
- All data access logged and auditable
- Skills must be signed and verified

Result: Malicious skill contained, cannot access sensitive data

---

**Scenario 3: Network Eavesdropping**

Attack: Attacker intercepts network traffic

Mitigations:
- All connections encrypted (TLS 1.3+)
- Hardware-bound identity prevents impersonation
- Certificate pinning for critical services
- Local processing minimizes network exposure

Result: Network traffic unreadable, connections authenticated

---

**Scenario 4: Social Engineering**

Attack: User tricked into approving malicious operation

Mitigations:
- Approval requests show full context and impact
- Out-of-band approval (separate device)
- Biometric confirmation required
- Unusual operations flagged for extra scrutiny
- Audit trail for forensics

Result: Reduced risk, but user remains ultimate authority

---

## Example Security Workflows

### Workflow 1: Flight Booking (End-to-End)

**You:** "Book a flight to Paris for June 15"

**Security Steps:**

```
1. Request Processing (Local AI)
   - Parsed by local AI models
   - No cloud involved
   
2. Credential Access (HSM + Vault)
   - AI Buddy requests American Airlines credentials
   - HSM authenticates request
   - Credentials retrieved from Vault
   - Time-limited access token issued

3. Browser Session (Hardware-Bound)
   - Local browser launched with hardware-bound identity
   - Logs into American Airlines
   - Searches available flights
   
4. Flight Selection (AI Decision)
   - AI Buddy finds 3-4 options
   - Presents to you for selection
   - You choose preferred flight

5. Booking Preparation (Local Processing)
   - AI Buddy prepares booking
   - Retrieves passport info from Vault
   - Retrieves traveler preferences from Encrypted Storage
   - Fills booking form

6. Payment Trigger (Privileged Operation)
   - System detects payment required ($847.50)
   - Flags as privileged operation
   - Booking paused

7. Approval Request (Out-of-Band)
   - Notification sent to your phone
   - Shows: flight details, cost, payment method
   - Requests biometric approval

8. User Approval (Cryptographic)
   - You review details on phone
   - Approve with fingerprint
   - HSM signs authorization
   - Authorization sent back to AI Node

9. Execution (Completion)
   - Payment processed
   - Booking confirmed
   - Confirmation stored locally
   - Credentials returned to Vault
   - Browser session closed

10. Audit Trail (Logging)
    - All steps logged with timestamps
    - Cryptographic signatures recorded
    - Retrievable for review
```

**Security Properties:**
- ✅ Credentials never exposed outside HSM/Vault
- ✅ Payment required explicit approval
- ✅ Hardware-bound identity throughout
- ✅ Full audit trail
- ✅ No cloud AI involved (personal data)

---

### Workflow 2: Research with Cloud AI

**You:** "Research Barcelona history and travel tips"

**Security Steps:**

```
1. Request Analysis (Local AI)
   - Local AI determines this needs external research
   - No personal information in query
   
2. Cloud Query (Anonymized)
   - Generic query sent to cloud AI:
     "Research: Barcelona history, attractions, travel tips"
   - No user identifier
   - No conversation history
   - No personal context

3. Cloud Processing (Stateless)
   - Cloud AI researches from public sources
   - Gathers from 100s of websites, videos, guides
   - Structures information
   - Returns compiled research

4. Cloud Storage (AI Notebook Service)
   - Research results stored in AI Notebook Service (cloud)
   - Indexed for quick retrieval
   - Available for queries, summaries, podcast generation
   - Cloud AI retains nothing about YOU, only the public research content

5. Queries from Notebook
   - You ask: "Best tapas restaurants for vegetarians?"
   - AI Notebook Service answers from stored research
   - Can apply your preferences (vegetarian) to filter results
   - No repeated web searches needed

6. Continuous Use (Cloud-Based Notebook)
   - Questions answered from AI Notebook Service (requires internet)
   - Personal context (like dietary preferences) applied by local AI Buddy
   - Cloud retains research content but no personal identifiers
```

**Security Properties:**
- ✅ No personal identifiers sent to cloud
- ✅ Cloud query is stateless (your identity not retained)
- ✅ Research stored in cloud notebook (non-sensitive public info only)
- ✅ Personalization (dietary preferences, etc.) happens locally
- ✅ Cloud optional and can be disabled entirely

---

## Compliance & Regulatory Considerations

### GDPR (European Union)

**Data Minimization:**
- Only necessary data stored
- Personal data stays on user's device
- No unnecessary cloud processing

**Right to Deletion:**
- User owns all data
- Can delete any data at any time
- No recovery by third parties

**Data Portability:**
- User owns encryption keys
- Can export data in standard formats
- No vendor lock-in

**Privacy by Design:**
- Privacy is architectural, not policy-based
- Default to local processing
- Consent required for cloud usage

---

### HIPAA (Healthcare - US)

**Protected Health Information (PHI):**
- Stored in Vault or Encrypted Storage
- Never transmitted without explicit approval
- Access logged and auditable

**Technical Safeguards:**
- Encryption at rest (HSM-backed)
- Encryption in transit (TLS 1.3+)
- Access controls enforced by HSM

**Audit Controls:**
- All PHI access logged
- Cryptographic audit trail
- Non-repudiable records

---

### SOC 2 (Enterprise)

**Security:**
- Hardware-rooted trust
- Encryption everywhere
- Access controls

**Availability:**
- Local operation (no cloud dependency)
- Offline capable
- Fault tolerant

**Confidentiality:**
- Data never leaves user control
- No multi-tenant risks
- Cryptographic isolation

---

## Security Best Practices for Users

### Initial Setup
1. Generate strong device passphrase
2. Enable biometric approval on personal device
3. Verify HSM initialization
4. Back up recovery keys securely (offline)
5. Test approval workflow

### Ongoing Usage
1. Review audit logs regularly
2. Monitor unusual approval requests
3. Keep firmware and software updated
4. Use hardware key for high-value operations
5. Disable cloud AI when not needed

### If Device Compromised
1. Revoke device authorization remotely (if possible)
2. Report to relevant services
3. Recovery keys can restore to new device
4. Old HSM keys cannot be extracted or reused

---

## Future Security Enhancements

### Planned Improvements
- Multi-device synchronization (encrypted)
- Federated identity across family members
- Enhanced anomaly detection
- Post-quantum cryptography
- Hardware attestation v2
- Secure firmware updates with rollback

### Research Areas
- Brain-computer interface security
- Quantum-resistant algorithms
- Advanced threat detection
- Zero-knowledge proofs for privacy
- Homomorphic encryption for cloud compute

---

## Summary

Security and privacy in Personalized AI Buddy are **architectural guarantees**, not promises:

**Hardware-Rooted:**
- HSM stores keys that never leave hardware
- TEE Enclave executes sensitive operations securely
- Hardware-bound identity prevents impersonation

**Two-Tier Storage:**
- Vault for credentials (HSM-protected)
- Encrypted Storage for personal data (software-encrypted)
- Appropriate protection for each sensitivity level

**User in Control:**
- Explicit approval for privileged operations
- Out-of-band confirmation required
- Full visibility into what AI Buddy does

**Privacy by Design:**
- Local-first processing
- Optional cloud (stateless, anonymized)
- No surveillance possible
- You own all data

**Cryptographic Trust:**
- Every operation signed and auditable
- Non-repudiable audit trails
- Hardware-enforced boundaries
- No implicit trust anywhere

This architecture ensures your AI Buddy is **trustworthy by design**, not by policy.
