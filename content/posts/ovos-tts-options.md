---
title: "TTS Options For OpenVoiceOS"
author: "Mike Gray"
date: 2024-12-01T11:58:58-04:00
draft: false
type: post

slug: tts-options-ovos
description: "Text-To-Speech options for OVOS/Neon Personal Private Voice Assistants."
thumbnail: "images/tts-options.webp"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2024).** Some steps here are out of date. For current options, see [TTS Options for OpenVoiceOS in 2026](/posts/tts-options-ovos-2026/).
{{< /alert >}}

[OpenVoiceOS, or OVOS,](https://openvoiceos.com) provides a flexible plugin system for text-to-speech (TTS) synthesis. This system allows you to choose the TTS engine that best suits your needs, whether you prioritize voice quality, speed, privacy, or language support.

## Running Plugins Directly

OVOS maintains several TTS plugins that can run directly on your voice assistant hardware. Here are the currently maintained plugins, categorized by their primary use case:

| Hardware/Environment | Model     | GitHub Link                                                                                                     | Notes                                                                                                                   |
| -------------------- | --------- | --------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| GPU-Optimized        | Coqui TTS | [ovos-tts-plugin-coqui](https://github.com/OpenVoiceOS/ovos-tts-plugin-coqui)                                   | Local TTS using Coqui models, best performance with GPU but some models work on CPU                                     |
| CPU-Capable          | Edge TTS  | [ovos-tts-plugin-edge-tts](https://github.com/OpenVoiceOS/ovos-tts-plugin-edge-tts)                             | Microsoft Edge TTS voices, requires internet connection but provides fast, high-quality output and streaming capability |
| CPU-Capable          | Piper     | [ovos-tts-plugin-piper](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper)                                   | Local TTS with multiple voice options, good performance on modern hardware (optimized for Raspberry Pi)                 |
| CPU-Capable          | Mimic     | [ovos-tts-plugin-mimic](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic)                                   | Classic Mycroft offline TTS engine, lightweight but robotic sounding                                                    |
| CPU-Capable          | ESpeak-NG | [ovos-tts-plugin-espeakNG](https://github.com/OpenVoiceOS/ovos-tts-plugin-espeakNG)                             | Lightweight offline TTS, supports many languages but robotic sounding                                                   |
| CPU-Capable          | Pico      | [ovos-tts-plugin-pico](https://github.com/OpenVoiceOS/ovos-tts-plugin-pico)                                     | Very lightweight offline TTS engine                                                                                     |
| API-Based/Cloud      | Azure     | [ovos-tts-plugin-azure](https://github.com/OpenVoiceOS/ovos-tts-plugin-azure)                                   | Microsoft Azure Cognitive Services TTS, paid service with high quality but no guarantee of privacy                      |
| API-Based/Cloud      | Polly     | [ovos-tts-plugin-polly](https://github.com/OpenVoiceOS/ovos-tts-plugin-polly)                                   | Amazon Polly TTS service, paid service with high quality but no guarantee of privacy                                    |
| API-Based/Cloud      | MaryTTS   | [ovos-tts-plugin-marytts](https://github.com/OpenVoiceOS/ovos-tts-plugin-marytts)                               | Connect to MaryTTS server, can be self-hosted, considered outdated and not well maintained                              |
| Language-Specific    | Nòs       | [ovos-tts-plugin-nos](https://github.com/OpenVoiceOS/ovos-tts-plugin-nos)                                       | Galician language TTS                                                                                                   |
| Language-Specific    | Cotovia   | [ovos-tts-plugin-cotovia](https://github.com/OpenVoiceOS/ovos-tts-plugin-cotovia)                               | Alternative Galician language TTS                                                                                       |
| Language-Specific    | Matxa     | [ovos-tts-plugin-matxa-multispeaker-cat](https://github.com/OpenVoiceOS/ovos-tts-plugin-matxa-multispeaker-cat) | Catalan language multi-speaker TTS                                                                                      |
| Novelty              | BeepSpeak | [ovos-tts-plugin-beepspeak](https://github.com/OpenVoiceOS/ovos-tts-plugin-beepspeak)                           | R2D2-style robotic sounds, works well but requires subtitles to be understood                                           |
| Novelty              | SAM       | [ovos-tts-plugin-SAM](https://github.com/OpenVoiceOS/ovos-tts-plugin-SAM)                                       | Software Automatic Mouth, retro-style speech synthesis, too digitized for regular use                                   |

Note: While Mimic2 and Mimic3-server plugins exist in archived form, they are no longer supported. Users are recommended to use Piper or other maintained alternatives for better performance and ongoing support.

## OVOS-TTS-Server

If you want to run your TTS engine on a different machine from your voice assistant, you can use the [OVOS TTS Server](https://github.com/OpenVoiceOS/ovos-tts-server). This server provides a consistent API interface for any supported OVOS TTS plugin, allowing you to:

- Run TTS on more powerful hardware separate from your voice assistant
- Share a single TTS server among multiple voice assistants
- Cache TTS results for improved performance
- Use the REST API for custom applications beyond voice assistants

The server can be particularly useful when running resource-intensive TTS models that might not perform well on lower-powered devices like a Raspberry Pi. I run the Coqui TTS plugin on a separate server to take advantage of its GPU acceleration and provide mid-high-quality TTS to all my voice assistants. I also have Piper running as a remote backup server.

## Conclusion

Questions? Comments? Feedback? Let me know on the [Open Conversational AI Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community.
