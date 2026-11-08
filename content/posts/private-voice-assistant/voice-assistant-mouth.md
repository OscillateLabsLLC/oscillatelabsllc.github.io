---
title: "The Voice Assistant's Mouth: Understanding OVOS Audio Services"
author: "Mike Gray"
date: 2024-11-10
part: 3
difficulty: "Beginner-Intermediate"
prerequisites:
  [
    "Understanding of OVOS Message Bus (part 1)",
    "Basic understanding of audio formats",
    "Familiarity with Linux audio systems helpful but not required",
  ]
prevPost: "voice-assistant-ears"
nextPost: "voice-assistant-body"
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
> 3. **The Mouth: audio services** (this post)
> 4. [The Body: hardware integration](/posts/private-voice-assistant/voice-assistant-body/)
> 5. [The Brain: skills](/posts/private-voice-assistant/voice-assistant-brain/)
> 6. [Configurations: advanced setups](/posts/private-voice-assistant/voice-assistant-configurations/)

After exploring how your assistant listens in our previous article, let's look at how it speaks and plays audio. The audio service (`ovos-audio`) handles all sound output, from spoken responses to music playback.

## Audio Service Overview

Just as the listener service coordinates multiple components for hearing, the audio service manages two main components:

- Text-to-Speech (TTS)
- Audio playback

These components communicate through the message bus we covered in part 1, responding to requests from skills and other services.

## Text-to-Speech (TTS)

TTS transforms written text into spoken words. While commercial services like ElevenLabs offer incredibly natural voices, open-source alternatives provide advantages in speed and privacy.

### Popular Open Source Options

1. **Piper**:

   - Created by Mike Hansen (Nabu Casa)
   - Advantages:
     - CPU-optimized (no GPU required)
     - Fast enough for Raspberry Pi 4
     - Active development
     - Multiple voices available
   - Best for: Most users, especially on lower-power devices

2. **Coqui** (Community Forks):
   - Advantages:
     - High-quality output
     - Many voice options
     - Good with GPU acceleration
   - Best for: Users with GPU hardware

Example message flow for TTS:

```json
// Request speech synthesis
{
    "type": "speak",
    "data": {
        "text": "The weather is sunny today",
        "lang": "en-us"
    }
}

// Audio ready for playback
{
    "type": "audio.play",
    "data": {
        "file": "/tmp/tts_output.wav",
    }
}
```

Browse available [TTS plugins](https://github.com/OpenVoiceOS/?q=tts-plugin&type=all&language=&sort=) to find one that matches your needs.

## Audio Playback

The audio service handles playback of all sounds:

- TTS-generated speech
- Music and podcasts
- System sounds
- Skill-specific audio (games, notifications)

### Audio Formats and Sources

The service can handle:

- Local files (WAV, MP3, etc.)
- Streaming audio
- Multiple concurrent audio streams
- Different priority levels (speech interrupts music)

Browse [audio plugins](https://github.com/OpenVoiceOS/?q=audio-plugin&type=all&language=&sort=) for different backend options.

## Conclusion

The audio service might seem simpler than the listener service, but its flexibility enables everything from basic voice responses to full media center capabilities. Understanding its components helps you choose the right configuration for your needs.

---

_Next in series: [The Voice Assistant's Body: Understanding OVOS Hardware Integration](/posts/private-voice-assistant/voice-assistant-body)_

_Previous: [The Voice Assistant's Ears: Understanding OVOS Listener Services](/posts/private-voice-assistant/voice-assistant-ears)_
