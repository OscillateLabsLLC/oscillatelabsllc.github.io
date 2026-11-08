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
| Best voice cloning, and you have a GPU | [Audio8](https://github.com/OscillateLabsLLC/ovos-tts-plugin-audio8) on a server | Clones a voice from one short clip. Faster than real time on an NVIDIA GPU or Apple Silicon, but too slow on a CPU. |
| You don't mind the cloud | [Edge TTS](https://github.com/OpenVoiceOS/ovos-tts-plugin-edge-tts) | Microsoft's Edge voices. Fast and good, but needs the internet and isn't private. |

Disclosure: Oscillate Labs maintains the Kokoro, Pocket TTS, and Audio8 plugins. I use Kokoro every day.
## Moving from Piper

The Piper plugin ([ovos-tts-plugin-piper](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper)) is archived. Piper support now lives in phoonnx, which also runs Matcha, GlowTTS, MMS, and other models. Install it with `pip install phoonnx`. You still need `espeak-ng` from your OS package manager, as before. Voice IDs use the format `piper/<lang_code>-<name>`.

Change the module and the voice ID in `mycroft.conf`. Before:

```json
"tts": {
  "module": "ovos-tts-plugin-piper",
  "ovos-tts-plugin-piper": {
    "voice": "alan-low"
  }
}
```

After:

```json
"tts": {
  "module": "ovos-tts-plugin-phoonnx",
  "ovos-tts-plugin-phoonnx": {
    "voice": "piper/en_GB-alan-low"
  }
}
```

If you leave `voice` unset, phoonnx picks the best voice for your language. phoonnx's [VOICES.md](https://github.com/TigreGotico/phoonnx/blob/dev/VOICES.md) lists every voice ID. The Piper repo's README covers [local model files and remote URLs](https://github.com/OpenVoiceOS/ovos-tts-plugin-piper#readme).

## Run TTS on a server

As with STT, you can run TTS on a bigger machine and point every assistant at it. Install `ovos-tts-plugin-server` on the assistant. My [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers) repo has a tested Kokoro recipe today, with phoonnx and Pocket TTS planned. See [Self-Hosted Speech Servers](/posts/ovos-speech-servers/) for details.

OVOS also provides [ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server) and [Docker images](https://github.com/OpenVoiceOS/ovos-docker-tts). If you also run Home Assistant, [wyoming-ovos-tts](https://github.com/OpenVoiceOS/wyoming-ovos-tts) exposes an OVOS TTS plugin over Home Assistant's Wyoming protocol.

## Maintained plugins (November 2026)

### Natural-sounding, offline

| Plugin | Notes |
| --- | --- |
| [phoonnx](https://github.com/TigreGotico/phoonnx) | The OVOS default and Piper's successor. Multilingual phonemization plus Piper, Matcha, GlowTTS, MMS, and other ONNX models. |
| [Kokoro](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro) | hexgrad's 82M-parameter multilingual model. Needs `espeak-ng`. Sends timed visemes, so a Mark 1 mouth stays in sync with no G2P plugin. |
| [Pocket TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-pocket-tts) | Kyutai's 100M-parameter, CPU-only model. Clones voices from audio samples. |
| [Audio8](https://github.com/OscillateLabsLLC/ovos-tts-plugin-audio8) | Audio8 TTS Realistic 0.6B. One-shot voice cloning from a reference clip, with optional streaming. Needs a GPU: RTF 0.27 on an RTX 3090 and 0.54 on an M3 Max, but 1.49 on a CPU. Not for a Pi. Proof-of-concept status. |
| [OmniVoice](https://github.com/OpenVoiceOS/ovos-tts-plugin-omnivoice) | k2-fsa's zero-shot model for 600+ languages. Picks a voice automatically, designs one from a text description, or clones a reference clip. New in 2026 and still alpha. A heavy install (PyTorch plus a multi-gigabyte model), so run it on a server. I haven't tried it. |
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

OVOS also has TTS *transformers* that process audio after synthesis and before playback, so they work with any TTS plugin. They include [sox effects](https://github.com/OpenVoiceOS/ovos-tts-transformer-sox-plugin) and three super-resolution transformers. Those upscale low-sample-rate voices (such as Piper's `low` voices) to 48 kHz, so speech sounds brighter and less muffled:

- [FlashSR](https://github.com/OpenVoiceOS/ovos_tts_transformer_FlashSR): ONNX, uses a GPU if one is available, otherwise runs on a CPU. The light option.
- [AudioSR](https://github.com/OpenVoiceOS/ovos-tts-transformer-audiosr): pure ONNX, with no PyTorch at runtime. If the upscale fails, you get the original audio.
- [NovaSR](https://github.com/OpenVoiceOS/ovos_tts_transformer_NovaSR): PyTorch. Its own README recommends FlashSR as the lighter choice.

I haven't tried them. If your TTS already outputs 48 kHz audio, they do nothing.

## Archived since 2024

These plugins are now archived. Don't start new setups with them.

- **Piper**: moved into phoonnx (see [above](#moving-from-piper)).
- **Mimic 2, Mimic 3, and the Mimic 3 server**: use phoonnx or Kokoro.
- **Nòs** (Galician) and **Cotovia remote**: use Cotovia.
- **Matxa** (Catalan multi-speaker): moved into phoonnx. The same model is `OpenVoiceOS/matxa-cat-multispeaker-vocos-2stage`, and you pick a speaker with `speaker_id`. The [Matxa repo](https://github.com/OpenVoiceOS/ovos-tts-plugin-matxa-multispeaker-cat) maps the old voices to speaker IDs.
- **Lux**: a voice-cloning plugin built on ZipVoice. Its repo doesn't name a replacement. For voice cloning, use Audio8 if you have a GPU, or Pocket TTS on a CPU.

## What about Neon's plugins?

Neon's [Coqui](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui) and [Coqui remote](https://github.com/NeonGeckoCom/neon-tts-plugin-coqui-remote) plugins are still maintained. Most of Neon's other TTS plugins haven't changed since 2022. The OVOS plugins above all work on Neon.

## Conclusion

For a Raspberry Pi, phoonnx is the easy default. For the best voice, run Kokoro or Pocket TTS on a server and share it with every assistant in the house.

Like the STT list, this one will go stale. The [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers) repo is tested weekly and will stay current.

Questions? Comments? Feedback? Let me know on the [Conversational AI Community Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org).
