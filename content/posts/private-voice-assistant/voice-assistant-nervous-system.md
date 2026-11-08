---
title: "The Voice Assistant's Nervous System: Understanding the OVOS Message Bus"
author: "Mike Gray"
date: 2024-11-10
part: 1
difficulty: "Advanced"
prerequisites:
  [
    "Basic understanding of JSON",
    "Familiarity with websockets or message queues",
    "Basic knowledge of system security concepts",
  ]
nextPost: "voice-assistant-ears"
draft: false
type: post

keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories:
  ["home automation", "personal voice assistant", "building voice assistants"]
---

> **Private voice assistant series**
>
> 1. **The Nervous System: the message bus** (this post)
> 2. [The Ears: listener services](/posts/private-voice-assistant/voice-assistant-ears/)
> 3. [The Mouth: audio services](/posts/private-voice-assistant/voice-assistant-mouth/)
> 4. [The Body: hardware integration](/posts/private-voice-assistant/voice-assistant-body/)
> 5. [The Brain: skills](/posts/private-voice-assistant/voice-assistant-brain/)
> 6. [Configurations: advanced setups](/posts/private-voice-assistant/voice-assistant-configurations/)

[OpenVoiceOS (OVOS)](https://openvoiceos.com) and [Neon AI](https://neon.ai) offer powerful options for creating private, local voice assistants. While most users can get started with [`ovos-installer`](https://github.com/OpenVoiceOS/ovos-installer) or a [Neon image](https://neon.ai/download/), understanding how these assistants work internally helps you customize them effectively. Let's start with the most fundamental component: the message bus.

## What is a Message Bus?

Think of the message bus as your assistant's nervous system - it's how all the different parts communicate. Just as your nervous system carries signals between your brain, ears, and mouth, the message bus carries messages between your assistant's components.

## The OVOS Message Bus

The OVOS message bus (`ovos-messagebus`) is elegantly simple: it's a Python implementation of Node's event system using a library called `pyee`. Messages are just JSON strings sent through websockets to all connected components. Each component listens for messages it knows how to handle and ignores the rest.

Depending on which image or installation method for OVOS or Neon you're using, you may also see `neon-messagebus` or `mycroft-messagebus`. They all work essentially the same way but with different branding. For simplicity, we'll refer to it as the OVOS message bus, but know you can use any of these buses interchangeably in most cases.

### Message Structure

Messages in OVOS use a simple, consistent format. Let's break it down:

1. **Message Type**: Uses dot-separated names for clarity
   ```json
   "type": "skill.hotels.search"
   ```
2. **Data** (optional): Additional information for the request

   ```json
   "data": {
     "name": "Hilton Garden Inn",
     "location": "Houston, Texas"
   }
   ```

3. **Context**: Information about which part of the system sent the message
   ```json
   "context": {
     "source": "voice_satellite_1",
     "destination": "main_assistant"
   }
   ```

### Current Implementation Options

The message bus comes in three flavors:

1. **Python** (Original): The standard implementation
2. **C++**: Good for low-level integration, but can have issues under heavy load
3. **Rust**: The newest version, fully async and highly configurable via environment variables

## Security Considerations

The current message bus design prioritizes simplicity but comes with some security trade-offs:

1. **No Authentication**: Any component that connects to the bus can see all messages
2. **No Encryption**: Messages are sent in plain text
3. **No Message Routing**: Every component sees every message

For most home users running everything locally, these aren't major concerns. However, for distributed setups, you'll want additional security. Two approaches have emerged in the community:

### Secure Distributed Solutions

Both OVOS and Neon offer secure distributed solutions, which are currently in active alpha testing.

1. **[Neon Hub](https://neongeckocom.github.io/neon-hub-installer/)**

   - Uses RabbitMQ for external connections
   - Provides authentication and separate message queues
   - Handles response routing via context

2. **[HiveMind](https://jarbashivemind.github.io/HiveMind-community-docs/)**
   - AES 256 encryption for external connections
   - Satellites connect to the message bus via a secure websocket and messages are encrypted

Both solutions offer authentication in addition to encryption. They are both actively working on RBAC (Role-Based Access Control) and other security features for satellites. This provides a more flexible and secure solution for distributed setups.

## Future Improvements

The community has identified several potential enhancements:

- Authentication system
- Multiple message queues
- Better traffic segmentation

However, implementing these would require significant changes to all assistant components, since they're built around the current simple bus design.

## For Developers

If you're developing for OVOS or Neon, here are some best practices:

1. Keep message types simple and descriptive:

   - Good: `skill.weather.forecast`
   - Avoid: `skill.weather.get_three_day_forecast_with_precipitation`

2. Include only necessary data in messages
3. Always include context for distributed setups
4. Consider security implications when exposing the bus - **the official stance of OpenVoiceOS is that the message bus should never be exposed outside of localhost**
5. Use `message.forward` any time you can, rather than making a new Message object to preserve context

## Conclusion

While simple, the message bus is the backbone of OVOS and Neon assistants. Every other component we'll discuss in this series depends on it for communication. Understanding how it works is crucial for anyone wanting to customize or extend their assistant's capabilities.

---

_Next in series: [How the Voice Assistant Listens: Understanding OVOS Listener Services](/posts/private-voice-assistant/voice-assistant-ears)_
