# Glossary

## Core Terminology

### System
- **Personalized AI Buddy** (or **AI Buddy**) - The overall AI system that serves you
- **AI Box** - The physical hardware device (GPU, storage, HSM) that runs your AI Buddy

### Data Storage (Two Components)

Both storage tiers use HSM keys (hardware). The difference is the level of compliance and security requirements.

1. **Vault**
   - FIPS Level security with HSM protection
   - Contains highly sensitive information that could cause damage if exposed
   - Examples: credentials, passwords, API keys, social security numbers, financial account details, private cryptographic keys
   - Requires explicit HSM authorization for access
   - Highest security tier

2. **Encrypted Storage**
   - Encrypted with HSM keys (hardware)
   - Does NOT require FIPS Level compliance
   - Contains personal but less critical information
   - Examples: addresses, phone numbers, travel preferences, documents, emails, photos, browsing history, calendar data
   - Standard security tier with hardware-backed encryption

### Security Hardware

1. **HSM (Hardware Security Module)**
   - Physical hardware component
   - Stores and manages encryption keys
   - Provides cryptographic operations
   - Keys never leave the HSM
   - Hardware-rooted trust
   - Used for BOTH Vault and Encrypted Storage

2. **TEE (Trusted Execution Environment) Enclave**
   - NOT hardware - it's secure processing
   - Isolated area within the CPU
   - Protects code and data from OS and hypervisor
   - Hardware-based memory encryption
   - Ensures confidentiality and integrity during execution

### Skills & Protocols
- **Skills System** - The framework for executable capabilities
- **MCP (Model Context Protocol)** - The protocol specification
- **MCP Server** - Implementation of the MCP protocol

### Cloud Services

- **AI Notebook Service** (like Google NotebookLM)
  - Cloud-based document processing
  - NOT local - runs in the cloud
  - Used for large-scale document processing
  - Can create audio, video, summaries from uploaded documents
  - **Important:** NO sensitive or personal information should be stored here
  - Only for non-sensitive content processing (research papers, articles, public documents)

---

## Usage Guidelines

**When to use Vault:**
- Anything that could be used for identity theft
- Financial access credentials
- Legal/medical authentication
- Cryptographic private keys
- FIPS Level security required

**When to use Encrypted Storage:**
- Personal preferences and habits
- Documents and media
- Communication history
- Publicly discoverable information
- Usage patterns and analytics
- HSM-backed but FIPS Level not required

**When to use AI Notebook Service (Cloud):**
- Processing large collections of non-sensitive documents
- Creating audio/video content from documents
- Generating summaries of public information
- Research on non-personal topics
- NEVER use for sensitive or personal information

**HSM vs TEE Enclave:**
- Use "HSM" when talking about key storage and cryptographic identity
- Use "TEE Enclave" when talking about secure code execution
- Often used together: HSM stores keys, TEE Enclave executes sensitive operations
