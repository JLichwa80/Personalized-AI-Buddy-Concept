# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **documentation-only repository** containing conceptual architecture and design documents for "Personalized AI Buddy" - a local-first, privacy-preserving, skill-based AI system. There is no executable code, build system, or tests.

## Key Concepts

- **Local-first AI**: Personal data and models run on user's device (AI Box), not cloud
- **Hardware-rooted trust**: HSM for cryptographic keys, TEE Enclave for secure execution
- **Two-tier storage**: Vault (HSM-protected credentials) + Encrypted Storage (documents, preferences)
- **Skills over prompts**: Executable, testable capabilities that persist across model updates
- **MCP (Model Context Protocol)**: Standard interface connecting skills to apps/devices

## Architecture Layers

1. **User Interaction Layer** - Voice, chat, mobile, AR/VR interfaces
2. **AI Box** - Local GPU/NPU compute with HSM and TEE
3. **Trust & Security Layer** - HSM key management, privileged operation approvals
4. **Two-Tier Data Storage** - Vault + Encrypted Storage
5. **Local AI Model Stack** - 7B-70B parameter models for local inference
6. **Skill System Layer** - Teach -> Build -> Test -> Deploy -> Evolve lifecycle
7. **Cloud AI Augmentation** - Optional, stateless research assistance

## Document Structure

- `Executive_Summary_Vision.md` - Vision and core beliefs
- `System_Architecture.md` - Technical architecture details
- `Skills_Platform.md` - Skills system, MCP, developer SDK
- `Roadmap_Evolution.md` - Timeline toward robotics (2027+)
- `Market_Comparison.md` - Differentiation from cloud AI
- `Use_Cases.md` - Real-world applications
- `Security_Privacy.md` - Security and privacy details
- `Glossary.md` - Term definitions
