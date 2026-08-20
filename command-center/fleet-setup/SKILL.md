---
name: fleet-setup
description: Use this when the user asks about the fleet. This document details every eligible machine in the fleet and how to connect to them.
metadata:
  harness: [claude, codex, pi]
  platform: [darwin, linux]
  scope: command-center
  requires: "1password cli and key-based SSH access to the fleet"
---
# Authentication
All SSH keys are stored in the shared 1Password vault named agent and can be accessed via the 1Password CLI. Some entries are SSH keys; others are username/password credentials for SSH. Each password is named after its machine.

# Machines

## FAEX1 AMD AI MAX+ 395
- **IP:** 192.168.0.192  
- **Prompt nicknames:** “Strix Halo Machine”, “AMD AI MAX+ 395”, “Local AI Server”  
- **Description:** The AI Max+ 395 machine provides local inference. Equipped with 128 GB RAM and 4 TB storage, it is ideal for hosting models. Use it whenever a user requests local inference. Do not upload any skills from the skills file to this machine, as that no agent runs on it; it serves only as an endpoint for accessing locally hosted models.
- **Platform:** Fandora 44  

## Mac Mini
- **IP:** 192.168.0.87 
- **Harness**  "pi", "codex", "claude code", "grok build", "cursor cli", "opencode"
- **Prompt nicknames:** “Mac Mini”  
- **Description:** My primary development machine is an Apple Mac Mini with an M4 Pro chip, 4 TB storage, and 64 GB RAM. I do most of my coding on it.  
- **Platform:** darwin  

## Hermes Agent
- **IP:** 192.168.0.227  
- **Harness** hermes
- **Prompt nicknames:** “hermes”, “hermes agent”  
- **Description:** This virtual machine runs the Hermes agent in an isolated environment.  
- **Platform:** Ubuntu Server 26