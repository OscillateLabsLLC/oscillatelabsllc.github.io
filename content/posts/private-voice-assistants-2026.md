---
title: "Private Voice Assistants in 2026"
author: "Mike Gray"
date: 2026-11-08T00:00:00-06:00
draft: true
slug: private-voice-assistants-2026
description: "Where private, self-hosted voice assistants stand in 2026: what changed since 2023, what to run, and what's still missing."
keywords: ["ovos", "neon", "home assistant", "voice assistant", "privacy", "local ai"]
tags: ["ovos", "neon", "home assistant"]
categories: ["personal voice assistant"]
---

> **OUTLINE.** This is a draft outline, not a finished post. It follows up on [Private Personal Voice Assistants (2023)](/posts/private-personal-voice-assistants/).

## Hook

- In 2023 I surveyed the private voice assistant landscape. Mycroft had just collapsed, and the question was whether anything would survive it.
- Short answer in 2026: yes, and the hard parts (STT, TTS, intent matching) got dramatically better.

## What changed since 2023

- **Mycroft is gone; the community isn't.** Mark II owners still run OVOS on their devices (link: Mark II teardown post).
- **The speech stack moved to ONNX.** onnx-asr (Parakeet) and phoonnx are the OVOS defaults. Runs well on a CPU (link: 2026 STT and TTS posts).
- **Voices got good.** Kokoro, Pocket TTS, and OmniVoice sound natural. Voice cloning is now practical on a CPU.
- **Intent matching got smarter.** Switchboard: 84% correct on real household traffic vs 42% for stock OVOS, and stays quiet on overheard speech (link: Switchboard post and eval repo).
- **LLMs arrived, carefully.** Local LLMs for dialog (Ollama dialog transformer), with a deterministic path for things like unlocking doors (Doublecheck).
- **Home Assistant became the hub.** HA Assist and the Wyoming protocol. OVOS can now serve STT/TTS to HA, and the HA skill/Neon Hub integration covers the other direction.
- **Neon shifted focus** toward generative AI and its patents. Neon Hub and Neon Node exist but aren't marketed yet.

> **TODO (Mike):** Decide how much to say about Neon's direction publicly. You have client and inside knowledge here, so keep it to what's public.

## The 2026 landscape

For each option: who it's for, the hardware, and the trade-offs.

- **OVOS.** The community-run core. Most flexible, most plugins.
- **Neon.** OVOS-based, packaged. Neon Hub for self-hosting.
- **Home Assistant Assist.** Easiest if you already run HA. More limited as a general assistant.
- **Others.**

> **TODO (Mike):** Fill in "Others" from what you actually know. I don't want to list projects from memory that may be dead.

## Hardware in 2026

- The Mark II, still alive under OVOS.
- Raspberry Pi 5 plus an HAT or USB mic array.
- Satellites: cheap devices that only capture audio, with the compute on a server.
- Your Mac, as a full assistant (link: the 2026 Mac setup post).

## What I run at home

- Assistants per room, plus a shared speech server (ovos-speech-servers).
- Switchboard plus Hangup plus Doublecheck.
- Kiwix MCP for an offline knowledge base.

> **TODO (Mike):** Your real setup goes here. This is the section people will actually read.

## What's still missing

- Wake word training without a model (Vosk is the workaround).
- Consumer-friendly setup. It's still a hobbyist install.
- Good, cheap far-field microphone hardware.

> **TODO (Mike):** Add your own list. This section is the most opinionated part, and likely the most shared.

## Close

- Invite to forums and Matrix.
- Point to Oscillate's open-source work.
