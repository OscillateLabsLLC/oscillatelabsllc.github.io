---
title: "STT Options For OpenVoiceOS"
author: "Mike Gray"
date: 2024-11-30T11:58:58-04:00
draft: false
type: post

slug: stt-options-ovos
description: "Speech-To-Text options for OVOS/Neon Personal Private Voice Assistants."
thumbnail: "images/stt-options.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2024).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

[OpenVoiceOS, or OVOS,](https://openvoiceos.com) is the spiritual successor (and fork) to the Mycroft voice assistant. It is a privacy-focused, open-source voice assistant that you can run on your own hardware. OVOS has a plugin system that allows you to swap out the default speech-to-text (STT) engine for one that you prefer.

The plugin system, while powerful, can be confusing due to the sheer number of options available. This post will cover some of the STT options available to you and when you might use them.

## Running Plugins Directly

While the open source community typically runs voice assistants on Raspberry Pi or other low-cost hardware, some run their voice assistants on more powerful hardware. If you have a powerful machine, or the model doesn't take much to run, you can run your STT plugin directly on your voice assistant hardware.

You may notice that there aren't any recommendations for STT plugins that run on Raspberry Pi. While you could run citrinet, VOSK, or smaller fasterwhisper models on a Raspberry Pi, the performance will be too slow to use comfortably in a voice assistant. If you have strong privacy needs, [hosting a local STT server on a more powerful machine](../fasterwhisper-stt-server-script) and pointing your voice assistant to it is a better option. Otherwise, you might consider using a cloud-based STT service such as Microsoft Azure STT.

Here are the STT plugins directly maintained by OVOS as of this writing (late November 2024):

| Hardware/Environment | Model                           | GitHub Link                                                                                               | Notes                                                                                                                         |
| -------------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| GPU-Optimized        | NeMo with GPU                   | [ovos-stt-plugin-nemo](https://github.com/OpenVoiceOS/ovos-stt-plugin-nemo)                               | Best performance with GPU acceleration, but many models exist optimized for CPU                                               |
| GPU-Optimized        | fasterwhisper                   | [ovos-stt-plugin-fasterwhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper)             | Fast and accurate with GPU acceleration, multilingual models available                                                        |
| GPU-Optimized        | Meta MMS                        | [ovos-stt-plugin-mms](https://github.com/OpenVoiceOS/ovos-stt-plugin-mms)                                 | GPU recommended for optimal performance                                                                                       |
| GPU-Optimized        | wav2vec2                        | [ovos-stt-plugin-wav2vec2](https://github.com/OpenVoiceOS/ovos-stt-plugin-wav2vec2)                       | Benefits from GPU acceleration, but not required                                                                              |
| CPU-Capable          | VOSK                            | [ovos-stt-plugin-vosk](https://github.com/OpenVoiceOS/ovos-stt-plugin-vosk)                               | Works well on CPU, suitable for smaller devices, not accurate and not recommended for most use cases .                        |
| CPU-Capable          | citrinet                        | [ovos-stt-plugin-citrinet](https://github.com/OpenVoiceOS/ovos-stt-plugin-citrinet)                       | ONNX-converted NeMo model for CPU usage. Only citrinet can be exported to ONNX, and this plugin cannot run other NeMo models. |
| API-Based/Cloud      | Chromium                        | [ovos-stt-plugin-chromium](https://github.com/OpenVoiceOS/ovos-stt-plugin-chromium)                       | Uses deprecated API, very fast but unsupported by Google                                                                      |
| API-Based/Cloud      | Microsoft Azure STT             | [ovos-stt-plugin-azure](https://github.com/OpenVoiceOS/ovos-stt-plugin-azure)                             | Cloud-based service, usage-based cost, fast and high quality but not private                                                  |
| Language-Specific    | projectAINA-remote              | [ovos-stt-plugin-projectAINA-remote](https://github.com/OpenVoiceOS/ovos-stt-plugin-projectAINA-remote)   | Catalan models                                                                                                                |
| Language-Specific    | HiTZ                            | [ovos-stt-plugin-HiTZ](https://github.com/OpenVoiceOS/ovos-stt-plugin-HiTZ)                               | Basque models                                                                                                                 |
| Language-Specific    | Nòs                             | [ovos-stt-plugin-nos](https://github.com/OpenVoiceOS/ovos-stt-plugin-nos)                                 | Galician models                                                                                                               |
| Language-Specific    | Iberian peninsula fasterwhisper | [ovos-stt-plugin-fasterwhisper-zuazo](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper-zuazo) | Optimized for Iberian languages                                                                                               |

OVOS offers many STT plugins as proofs-of-concept in their [OVOS Hatchery Git organization](https://github.com/OVOSHatchery?q=stt&type=all&language=&sort=). The plugins in this repository worked as of their last commit, but are not guaranteed to work in perpetuity or with the latest changes to OVOS. If you didn't see an STT engine you wanted to try then you might see it in the Hatchery, ready to be maintained by someone with time and interest.

## OVOS-STT-HTTP-Server

If you want to run multiple STT engines at once, or if you want to run your STT engine on a different machine from your voice assistant, you can use the [OVOS STT HTTP Server](https://github.com/OpenVoiceOS/ovos-stt-http-server). This server puts a consistent API on the front of any supported OVOS STT plugin, enabling you to run your personal, private speech-to-text on a different machine from your Voice Assistant. It also means you are not constrained by the hardware on your Voice Assistant - you could run FasterWhisper on a machine with a GPU, for example, and use it as your primary STT engine. Finally, you can use a single STT server for a number of local assistants, allowing you to take advantage of a more powerful machine for all your STT needs.

In addition to usage with voice assistants, the REST API can be used for any custom application that requires speech-to-text. For example, you could use it to automatically transcribe podcasts overnight when no one would typically be using the server. You could also use it to transcribe notes you took that day from meetings or lectures.

## STT Plugins From Neon

Neon.AI is a downstream partner of OVOS and a former Mycroft channel partner. They maintain a number of STT plugins that are compatible with OVOS, though some are for specialized use cases. Here are some of the plugins available from Neon:

| Hardware/Environment    | Model                | GitHub Link                                                                                                      | Notes                                                                                                                                                                                                       |
| ----------------------- | -------------------- | ---------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| API-Based/Cloud         | Custom NeMo citrinet | [neon-stt-plugin-nemo-remote](https://github.com/NeonGeckoCom/neon-stt-plugin-nemo-remote)                       | Point at a remote server with a custom trained NeMo model which can run on a Raspberry Pi 4 or higher, though it's a bit slow                                                                               |
| API-Based/Cloud         | Google Cloud STT     | [neon-stt-plugin-google_cloud_streaming](https://github.com/NeonGeckoCom/neon-stt-plugin-google_cloud_streaming) | Google Cloud STT models through their API, not private                                                                                                                                                      |
| CPU-Optimized           | Custom NeMo citrinet | [neon-stt-plugin-nemo](https://github.com/NeonGeckoCom/neon-stt-plugin-nemo)                                     | Custom trained NeMo model which can run on a Raspberry Pi 4 or higher, though it's a bit slow                                                                                                               |
| API-Based/CPU-Optimized | Custom NeMo citrinet | [neon-stt-nemo](https://github.com/NeonGeckoCom/streaming-stt-nemo)                                              | Custom trained NeMo model which can run on a Raspberry Pi 4 or higher, though it's a bit slow. This plugin has streaming capabilities to work on low-powered hardware (think ESP32 or Raspberry Pi Zero 2W) |
| CPU-Optimized           | Silero               | [neon-stt-plugin-silero](https://github.com/NeonGeckoCom/neon-stt-plugin-silero)                                 | Silero models, optimized for CPU usage, can run on a Raspberry Pi 4 or higher but not very accurate. Not recommended for anything but POCs.                                                                 |

There are a number of other Neon STT plugins, but they are either archived or use models that are no longer maintained or supported.

## Conclusion

Questions? Comments? Feedback? Let me know on the [Open Conversational AI Forums](https://https://community.openconversational.ai//) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community.
