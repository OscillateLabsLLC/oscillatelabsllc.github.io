---
title: "The Voice Assistant's Ears: Understanding OVOS Listener Services"
author: "Mike Gray"
date: 2024-11-10
part: 2
difficulty: "Intermediate"
prerequisites:
  [
    "Understanding of OVOS Message Bus (previous article)",
    "Basic familiarity with audio processing concepts",
    "Basic knowledge of Linux audio systems",
  ]
prevPost: "voice-assistant-nervous-system"
nextPost: "voice-assistant-mouth"
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
> 2. **The Ears: listener services** (this post)
> 3. [The Mouth: audio services](/posts/private-voice-assistant/voice-assistant-mouth/)
> 4. [The Body: hardware integration](/posts/private-voice-assistant/voice-assistant-body/)
> 5. [The Brain: skills](/posts/private-voice-assistant/voice-assistant-brain/)
> 6. [Configurations: advanced setups](/posts/private-voice-assistant/voice-assistant-configurations/)

Continuing our exploration of OVOS and Neon.AI components, let's examine how your assistant hears and understands spoken commands. The listener service (`ovos-dinkum-listener`) is like your assistant's ears and early speech processing - it handles everything from detecting wake words to converting speech to text.

## Listener Architecture

The listener service coordinates four critical components:

- Microphone input
- Wake word detection
- Voice Activity Detection (VAD)
- Speech-to-Text (STT)

These components communicate through the message bus we discussed in the previous article, working together to turn audio into text commands your assistant can process.

## Component Details

### 1. Microphone Plugins

Microphone plugins capture audio input from your hardware. OVOS supports multiple backends:

- alsa (recommended for Linux)
- pyaudio (most reliable for Windows/MacOS, also works with Linux)
- sounddevice (experimental, works for all 3 major platforms)

Browse available [microphone plugins](https://github.com/OpenVoiceOS/?q=microphone-plugin&type=all&language=&sort=) to find one that matches your system.

### 2. Wake Word Detection

Wake word detection (also called hotword detection) listens for specific trigger phrases like "Hey Mycroft" or "Hey Neon". Popular options include:

- **OpenWakeWord**:

  - Modern synthetic training approach
  - Adopted by Home Assistant
  - Pre-trained models available
  - Can create custom wake words

- **Precise-Lite**:

  - Lightweight Mycroft engine
  - Good for resource-constrained devices (minimum suggested: Raspberry Pi 3B+)

- **VOSK**:
  - Text-based detection
  - No custom model training needed
  - More flexible but less precise

Browse [wake word plugins](https://github.com/OpenVoiceOS/?q=ww-plugin&type=all&language=&sort=) for more options.

### 3. Voice Activity Detection (VAD)

VAD determines when someone is speaking versus background noise. This helps:

- Reduce processing load
- Trim silence from recordings
- Improve STT accuracy

Popular VAD options:

- **Silero**: Community favorite, works across languages. _Speech detection_, so it's more accurate than noise-based VAD.
- **WebRTC**: Lightweight, good for web integration. _Noise-based_, so it's less accurate but faster.

Browse [VAD plugins](https://github.com/OpenVoiceOS/?q=vad&type=all&language=&sort=) for more choices.

### 4. Speech-to-Text (STT)

STT converts spoken audio to text. This is typically the most resource-intensive component. Current leading options:

- **NVIDIA NeMo** (Citrinet model):

  - Fast with GPU acceleration
  - Community-optimized CPU versions available
  - Neon offers a Raspberry Pi-optimized version

- **OpenAI Whisper**:
  - Excellent accuracy
  - Multiple model sizes
  - GPU recommended but CPU versions available

Browse [STT plugins](https://github.com/OpenVoiceOS/?q=stt&type=all&language=&sort=) for more options.

## How It All Works Together

Let's follow the flow of a voice command:

1. The listener connects to the message bus
2. Microphone plugin starts capturing audio
3. Wake word plugin monitors the audio stream
4. When wake word is detected:
   - VAD starts monitoring for speech
   - STT prepares to receive audio
5. When speech is detected:
   - VAD trims silence
   - Audio is sent to STT
6. STT converts speech to text
7. Text is sent over message bus to other components

This message flow looks something like:

```json
// Wake word detected
{
    "type": "recognizer_loop:wakeword",
    "data": {
        "wake_word": "hey_mycroft"
    }
}

// Speech detected and processed
{
    "type": "recognizer_loop:utterance",
    "data": {
        "utterances": ["what's the weather like today"]
    }
}
```

## Resource Considerations

STT is usually the most demanding component. You have several options:

1. **Full Local Processing**:

   - Requires decent CPU or GPU (not recommended on Raspberry Pi boards yet)
   - Complete privacy
   - Fastest response time

2. **Distributed Processing**:

   - Run STT on a more powerful machine
   - Use lighter models on satellites
   - Requires network connectivity

3. **Hybrid Approach**:
   - Light models for common commands
   - Powerful models for complex speech

## Conclusion

The listener service represents your assistant's ability to hear and understand speech. While complex, its modular design lets you choose components that match your hardware capabilities and privacy requirements.

---

_Next in series: [How the Voice Assistant Speaks: Understanding OVOS Audio Services](/posts/private-voice-assistant/voice-assistant-mouth)_

_Previous: [The Voice Assistant's Nervous System: Understanding the OVOS Message Bus](/posts/private-voice-assistant/voice-assistant-nervous-system)_
