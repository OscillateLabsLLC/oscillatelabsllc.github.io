---
title: "TTS Options for OpenVoiceOS in 2026"
author: "Mike Gray"
date: 2026-11-08T00:00:00-06:00
draft: true
slug: tts-options-ovos-2026
description: "Which text-to-speech engines to use with OVOS and Neon in 2026, what each one needs, and what has been archived since 2024."
keywords: ["ovos", "neon", "tts", "text-to-speech", "onnx", "kokoro", "phoonnx", "voice assistant"]
tags: ["ovos", "neon", "tts"]
categories: ["personal voice assistant"]
---

> **TODO (Mike):** Delete every TODO block before you set `draft: false`. After this post publishes, point the Archived note on [the 2024 TTS post](/posts/tts-options-ovos/) at this one.

In late 2024 I wrote a guide to [TTS options for OpenVoiceOS](/posts/tts-options-ovos/). Since then, the plugin I recommended most, Piper, was archived. The Coqui company shut down. And several voices that sound far more natural became easy to run on a CPU.

This post covers what to pick, the full list of maintained plugins as of November 2026, and what was archived.

## Quick picks

| Your situation | Use this | Why |
| --- | --- | --- |
| Raspberry Pi, on-device | [phoonnx](https://github.com/TigreGotico/phoonnx) | The OVOS default. Runs Piper voices and several other model types through ONNX. |
| Most natural voice on a CPU | [Kokoro](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro) on a server | Natural voices in 9 languages, faster than real time on a modern CPU. |
| Apple Silicon, or you want voice cloning | [Pocket TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-pocket-tts) | CPU-only, about 6x real time on Apple Silicon, clones a voice from a sample. |
| Rare languages, or a voice from a description | [OmniVoice](https://github.com/OpenVoiceOS/ovos-tts-plugin-omnivoice) | 600+ languages. Designs a voice from a text description or clones one from a clip. |
| You don't mind the cloud | [Edge TTS](https://github.com/OpenVoiceOS/ovos-tts-plugin-edge-tts) | Microsoft's Edge voices. Fast and good, but needs the internet and isn't private. |

Disclosure: Oscillate Labs maintains the Kokoro and Pocket TTS plugins. Kokoro is still marked as a proof of concept, with no API stability promise.

> **TODO (Mike):** Decide whether to keep Kokoro in "quick picks" while its README says "POC, may be abandoned." Your speech-servers post calls it the state of the art, so either update the README status or soften this row.

> **TODO (Mike):** Add OmniVoice's hardware needs. Its README doesn't say whether it needs a GPU, and I haven't run it.

## Moving from Piper

The Piper plugin ([ovos-tts-plugin-piper](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper)) is archived. Piper support now lives in phoonnx, which also runs Matcha, GlowTTS, MMS, and other models. Install it with `pip install phoonnx`. You still need `espeak-ng` from your OS package manager, as before. Voice IDs use the format `piper/<lang_code>-<name>`.

The Piper repo's README has the [full config mapping](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper#readme) from the old plugin to phoonnx.

> **TODO (Mike):** Consider pasting a before/after config snippet here from the migration guide, so readers don't have to click through.

## Run TTS on a server

As with STT, you can run TTS on a bigger machine and point every assistant at it. Install `ovos-tts-plugin-server` on the assistant. My [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers) repo has a tested Kokoro recipe today, with phoonnx and Pocket TTS planned. See [Self-Hosted Speech Servers](/posts/ovos-speech-servers/) for details.

OVOS also provides [ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server) and [Docker images](https://github.com/OpenVoiceOS/ovos-docker-tts). If you also run Home Assistant, [wyoming-ovos-tts](https://github.com/OpenVoiceOS/wyoming-ovos-tts) exposes an OVOS TTS plugin over Home Assistant's Wyoming protocol.

## Maintained plugins (November 2026)

### Natural-sounding, offline

| Plugin | Notes |
| --- | --- |
| [phoonnx](https://github.com/TigreGotico/phoonnx) | The OVOS default and Piper's successor. Multilingual phonemization plus Piper, Matcha, GlowTTS, MMS, and other ONNX models. |
| [Kokoro](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro) | hexgrad's 82M-parameter multilingual model. Needs `espeak-ng`. Proof-of-concept status. |
| [Pocket TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-pocket-tts) | Kyutai's 100M-parameter, CPU-only model. Clones voices from audio samples. |
| [OmniVoice](https://github.com/OpenVoiceOS/ovos-tts-plugin-omnivoice) | k2-fsa's zero-shot model for 600+ languages. Picks a voice automatically, designs one from a text description, or clones a reference clip. |
| [Coqui](https://github.com/OpenVoiceOS/ovos-tts-plugin-coqui) | The Coqui company is gone, but this plugin is still maintained. Best with a GPU. |

### Lightweight and robotic

| Plugin | Notes |
| --- | --- |
| [eSpeak NG](https://github.com/OpenVoiceOS/ovos-tts-plugin-espeakNG) | Many languages, very light, robotic. |
| [Mimic](https://github.com/OpenVoiceOS/ovos-tts-plugin-mimic) | The classic Mycroft engine. Light and robotic. |
| [Pico](https://github.com/OpenVoiceOS/ovos-tts-plugin-pico) | Very light. |
| [MaryTTS](https://github.com/OpenVoiceOS/ovos-tts-plugin-marytts) | Connects to a MaryTTS server. The plugin is maintained, but MaryTTS itself is dated. |

### Cloud

| Plugin | Notes |
| --- | --- |
| [Edge TTS](https://github.com/OpenVoiceOS/ovos-tts-plugin-edge-tts) | Microsoft Edge voices. Free to use, fast, and supports streaming. Not private. |
| [Azure](https://github.com/OpenVoiceOS/ovos-tts-plugin-azure) | Paid, high quality, not private. |
| [Polly](https://github.com/OpenVoiceOS/ovos-tts-plugin-polly) | Amazon's TTS. Paid, high quality, not private. |
| [Google Translate](https://github.com/OpenVoiceOS/ovos-tts-plugin-google-tx) | Uses the undocumented Google Translate speech endpoint. The plugin's own README says it's a legal gray area that can break without notice. Avoid it for anything that matters. |

### Language-specific

| Plugin | Notes |
| --- | --- |
| [Cotovia](https://github.com/OpenVoiceOS/ovos-tts-plugin-cotovia) | Galician. |
| [AhoTTS](https://github.com/OpenVoiceOS/ovos-tts-plugin-ahotts) | Basque and Spanish. |

### Novelty

| Plugin | Notes |
| --- | --- |
| [BeepSpeak](https://github.com/OpenVoiceOS/ovos-tts-plugin-beepspeak) | R2-D2-style beeps. Fun, but you'll need subtitles. |
| [SAM](https://github.com/OpenVoiceOS/ovos-tts-plugin-SAM) | Software Automatic Mouth, the retro 1980s voice. |

### Post-processing

OVOS also has TTS *transformers* that process audio after synthesis. They include [sox effects](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin) and audio super-resolution models ([AudioSR](https://github.com/OpenVoiceOS/ovos-tts-transformer-audiosr), [FlashSR](https://github.com/OpenVoiceOS/ovos_tts_transformer_FlashSR), [NovaSR](https://github.com/OpenVoiceOS/ovos_tts_transformer_NovaSR)).

> **TODO (Mike):** I'm describing the AudioSR, FlashSR, and NovaSR transformers from their names and the AudioSR model. Confirm they upscale TTS output before this ships.

## Archived since 2024

These plugins are now archived. Don't start new setups with them.

- **Piper**: moved into phoonnx (see [above](#moving-from-piper)).
- **Mimic 2, Mimic 3, and the Mimic 3 server**: use phoonnx or Kokoro.
- **Nòs** (Galician) and **Cotovia remote**: use Cotovia.
- **Matxa** (Catalan multi-speaker).
- **Lux**.

> **TODO (Mike):** Add replacements for Matxa (Catalan) and Lux if you know them. OmniVoice or phoonnx's MMS voices may cover Catalan.

## What about Neon's plugins?

Neon's [Coqui](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui) and [Coqui remote](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui-remote) plugins are still maintained. Most of Neon's other TTS plugins haven't changed since 2022. The OVOS plugins above all work on Neon.

## Conclusion

For a Raspberry Pi, phoonnx is the easy default. For the best voice, run Kokoro or Pocket TTS on a server and share it with every assistant in the house.

Like the STT list, this one will go stale. The [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers) repo is tested weekly and will stay current.

Questions? Comments? Feedback? Let me know on the [Conversational AI Community Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org).
