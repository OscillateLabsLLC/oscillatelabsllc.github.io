---
title: "The Voice Assistant's Body: Understanding OVOS Hardware Integration"
author: "Mike Gray"
date: 2024-11-10
part: 4
difficulty: "Intermediate"
prerequisites:
  [
    "Understanding of OVOS Message Bus (part 1)",
    "Basic Linux system administration",
    "Basic understanding of hardware interfaces",
  ]
prevPost: "voice-assistant-mouth"
nextPost: "voice-assistant-brain"
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
> 4. **The Body: hardware integration** (this post)
> 5. [The Brain: skills](/posts/private-voice-assistant/voice-assistant-brain/)
> 6. [Configurations: advanced setups](/posts/private-voice-assistant/voice-assistant-configurations/)

While previous articles covered how your assistant listens, thinks, and speaks, now we'll explore how it interacts with physical hardware through the Platform Hardware Abstraction Layer (PHAL) system.

## Evolution of Hardware Support

The PHAL system's history helps explain its design. Originally, Mycroft AI's code was tightly coupled to their Mark 1 device, which included:

- LED panel for eyes and mouth
- Custom Arduino-controlled circuit board
- Specific audio hardware configuration

When Mycroft developed their Mark 2 device, they discovered this tight coupling made supporting new hardware difficult. OVOS solved this by abstracting hardware controls into plugins, making the assistant hardware-agnostic.

## Understanding PHAL

PHAL plugins can control various hardware components:

- Volume controls
- WiFi configuration
- Display screens
- Cooling fans
- Custom circuit boards
- Robotic components
- System-level functions

Messages flow through the bus to control hardware. As a theoretical example:

```json
{
  "type": "ovos.phal.volume.set",
  "data": {
    "level": 75
  }
}
```

Browse available [PHAL plugins](https://github.com/orgs/OpenVoiceOS/repositories?language=&q=phal&sort=&type=all) for supported hardware.

## Admin PHAL

Some hardware operations require root privileges. Rather than running all PHAL plugins as root, OVOS provides Admin PHAL plugins that:

- Only load when the service runs as root
- Handle privileged operations
- Keep other plugins running with minimal privileges

This follows the principle of least privilege, improving system security.

## Community Hardware Examples

The PHAL system's flexibility has enabled the community to integrate OVOS with diverse hardware:

- Mycroft Mark 1 (original reference design)
- Mycroft Mark 2 (complete hardware redesign)
- Google AIY Voice Kit
- Adafruit Voice Bonnet
- Standard laptops and desktops, including MacBooks
- Custom robots and devices

Each implementation uses different PHAL plugins to control its specific hardware while running the same core assistant software.

## Conclusion

The PHAL system gives OVOS incredible hardware flexibility. Whether you're running on a custom device or standard computer, PHAL plugins provide a consistent interface for hardware control while maintaining security through proper privilege separation.

---

_Next in series: [The Voice Assistant's Brain: Understanding OVOS Skills](/posts/private-voice-assistant/voice-assistant-brain)_

_Previous: [The Voice Assistant's Mouth: Understanding OVOS Audio Services](/posts/private-voice-assistant/voice-assistant-mouth)_
