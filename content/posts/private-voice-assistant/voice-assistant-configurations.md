---
title: "Building Voice Assistant Configurations: Advanced OVOS Setups"
author: "Mike Gray"
date: 2024-11-10
part: 6
difficulty: "Advanced"
prerequisites:
  [
    "All previous articles in series",
    "Understanding of distributed systems",
    "Network security fundamentals",
    "Basic virtualization concepts",
  ]
prevPost: "voice-assistant-brain"
draft: false
type: post

keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories:
  ["home automation", "personal voice assistant", "building voice assistants"]
---

> **Private voice assistant series**
>
> 1. [The Nervous System: the message bus](/posts/private-voice-assistant/voice-assistant-nervous-system/)
> 2. [The Ears: listener services](/posts/private-voice-assistant/voice-assistant-ears/)
> 3. [The Mouth: audio services](/posts/private-voice-assistant/voice-assistant-mouth/)
> 4. [The Body: hardware integration](/posts/private-voice-assistant/voice-assistant-body/)
> 5. [The Brain: skills](/posts/private-voice-assistant/voice-assistant-brain/)
> 6. **Configurations: advanced setups** (this post)

After exploring each component of OVOS and Neon assistants, let's examine how to mix and match these components to create custom configurations. The modular nature of OVOS allows for setups ranging from minimal text-only systems to complex distributed networks.

## Text-Only Assistants

The simplest possible configuration requires just two components:

- Message bus
- Core/skills service

This minimal setup can be useful for:

- Development and testing
- Accessibility (vision/hearing impaired users)
- Integration with existing text interfaces
- Command-line or web-based interaction

In this configuration, the message flow is straightforward:

```json
// Direct text input
{
    "type": "recognizer_loop:utterance",
    "data": {
        "utterances": ["what time is it"]
    }
}

// Direct text output
{
    "type": "speak",
    "data": {
        "utterance": "It is 3:45 PM"
    }
}
```

## Distributed Assistants

While a single device running all services is an ideal the community has sought for years, current technology often requires distributing processing across multiple devices. Two major approaches have emerged: HiveMind and Neon Hub/Node systems.

Both systems allow you to distribute services across multiple devices, but they differ in their architecture and complexity. Both are used in production setups and have active communities supporting them.

### HiveMind Architecture

- Minimal on-device services
- Encrypted central message bus
- Satellites connect to central system
- Good for: DIY distributed setups with advanced teams

More details on its architecture are available [in the HiveMind documentation](https://jarbashivemind.github.io/HiveMind-community-docs/01_terminology/).

### Neon Hub/Node System

- RabbitMQ and REST APIs for secure communication
- Centralized processing options
- Configurable satellite capabilities
- Good for: Managed distributed setups

Information on running a Neon Hub is available in [its documentation](https://neongeckocom.github.io/neon-hub-installer/).

## Development Environment

For developers creating custom skills, a minimal testing setup can include:

- [Rust-based message bus](https://github.com/OscillateLabsLLC/ovos-rust-messagebus) (lightweight and fast)
- [SkillLoader class from ovos-workshop](https://github.com/OpenVoiceOS/OVOS-workshop/blob/f77e498cfc743f71bb81a9663f90e120d66f6654/ovos_workshop/skill_launcher.py#L137) for testing your skill code
- Message injection tools:
  - [ovos-say-to](https://github.com/OpenVoiceOS/ovos-bus-client/blob/7318b50cfdb8f2d140b8b955a7c03ce95e513616/setup.py#L78)
  - [Neon Mana utils](https://github.com/NeonGeckoCom/neon-mana-utils)

This allows testing without running a full assistant:

```json
// Inject test utterance
{
    "type": "recognizer_loop:utterance",
    "data": {
        "utterances": ["test phrase"],
        "lang": "en-us"
    }
}

// Monitor skill response
{
    "type": "skill.response",
    "data": {
        "skill_id": "test.skill",
        "result": "success"
    }
}
```

## Security Considerations

When building distributed setups, consider:

1. Message Bus Security

   - Default configuration is local-only
   - Unencrypted by default
   - No built-in authentication

2. Network Security

   - Use firewalls to restrict access
   - Consider VPN for remote satellites
   - Implement proper network segmentation

3. Service Isolation
   - Run services with minimal privileges
   - Use container isolation where appropriate
   - Separate sensitive components

## Transformer plugins

Transformer plugins are a new feature in OVOS 0.0.8 that allow you to modify parts of the assistant pipeline as they pass through. There are currently Audio, Utterance, Metadata, Dialog, and TTS transformer plugins available.

This feature allows you to:

- Modify audio data before it reaches the STT
- Change the text output of the STT
- Add metadata to messages
- Modify the dialog context
- Change the TTS output, both text and audio

More information is available in the [OVOS Technical Manual](https://openvoiceos.github.io/ovos-technical-manual/transformer_plugins/).

## Conclusion

OVOS's modular design enables incredible flexibility, from minimal development setups to complex distributed systems. Understanding the security implications and resource requirements of different configurations helps you build a setup that matches your needs while maintaining security and reliability.

---

_Previous: [The Voice Assistant's Brain: Understanding OVOS Skills](/posts/private-voice-assistant/voice-assistant-brain)_
