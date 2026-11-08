---
title: "The Voice Assistant's Brain: Understanding OVOS Skills"
author: "Mike Gray"
date: 2024-11-10
part: 5
difficulty: "Intermediate"
prerequisites:
  [
    "Understanding of OVOS Message Bus (part 1)",
    "Basic programming concepts",
    "Basic understanding of natural language processing",
  ]
prevPost: "voice-assistant-body"
nextPost: "voice-assistant-configurations"
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
> 5. **The Brain: skills** (this post)
> 6. [Configurations: advanced setups](/posts/private-voice-assistant/voice-assistant-configurations/)

After exploring how your assistant communicates, speaks, listens, and controls hardware, let's examine how it processes and responds to commands through the core/skills service.

## Core Service Overview

The core service (called either "core" or "skills" depending on your OVOS implementation) coordinates two main components:

- Intent Engine
- Skills System

These components work together to understand user requests and execute appropriate actions.

## Intent Engine

The intent engine matches user requests with the appropriate skill. Currently, OVOS primarily uses Padatious, an intent parser created by Mycroft AI that generates models based on example phrases.

Example utterance handling:

```json
// Incoming utterance
{
    "type": "recognizer_loop:utterance",
    "data": {
        "utterances": ["what's the weather like in houston"]
    }
}

// Matched intent
{
    "type": "weather.forecast.location",
    "data": {
        "location": "houston",
        "utterance": "what's the weather like in houston"
    }
}
```

The OVOS development team is working to make intent engines pluggable, which would allow:

- Custom regex systems
- Different machine learning models
- Large Language Models (LLMs)
- External intent services such as Home Assistant's Assist platform

## Skills System

Skills extend your assistant's capabilities. Think of them as apps for your voice assistant. Common categories include:

### Built-in Skills

- Device configuration
- System control
- Basic inquiries

### Common Add-on Skills

- Weather forecasts
- News updates
- Music playback
- Timer/alarm functions
- Home automation
- Knowledge queries (Wikipedia, Wolfram Alpha)

### Custom Skills

- Integration with your own personal services
- Specialized functionality
- Personal automation

Browse available skills:

- [OVOS Skills](https://github.com/OpenVoiceOS/?q=skill&type=all&language=&sort=)
- [Neon Skills](https://github.com/NeonGeckoCom/?q=skill&type=all&language=&sort=)

## How It Works

1. Core service receives transcribed text via message bus
2. Intent engine matches text to skill patterns
3. Matched skill receives the request
4. Skill processes request and responds
5. Response flows back through message bus

## Future Developments

The OVOS roadmap includes several exciting possibilities:

- Pluggable intent engines
- Direct LLM integration
- Improved skill discovery
- Enhanced natural language understanding

## Conclusion

The core/skills service is what gives your assistant its personality and capabilities. Through skills, you can customize your assistant to handle exactly the tasks you need, while the intent engine ensures it understands your requests correctly.

---

_Next in series: [Building Voice Assistant Configurations: Advanced OVOS Setups](/posts/private-voice-assistant/voice-assistant-configurations)_

_Previous: [The Voice Assistant's Body: Understanding OVOS Hardware Integration](/posts/private-voice-assistant/voice-assistant-body)_
