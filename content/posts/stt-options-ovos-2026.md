---
title: "STT Options for OpenVoiceOS in 2026"
author: "Mike Gray"
date: 2026-11-08T00:00:00-06:00
draft: false
slug: stt-options-ovos-2026
description: "Which speech-to-text engines to use with OVOS and Neon in 2026, what each one needs, and what has been archived since 2024."
keywords: ["ovos", "neon", "stt", "speech-to-text", "onnx", "voice assistant"]
tags: ["ovos", "neon", "stt"]
categories: ["personal voice assistant"]
---

In late 2024 I wrote a guide to [STT options for OpenVoiceOS](/posts/stt-options-ovos/). Two years later, most of that table is wrong. Several plugins are archived, and the best options didn't exist yet.

The biggest change is ONNX. Most new OVOS speech plugins run models through ONNX Runtime instead of PyTorch. They install faster, use less memory, and run well on a CPU. The OVOS default STT is now [onnx-asr](https://github.com/OpenVoiceOS/ovos-stt-plugin-onnx-asr), which runs NVIDIA's Parakeet model.

This post covers what to pick, the full list of maintained plugins as of November 2026, and what was archived.

## Quick picks

| Your situation                        | Use this                                                                                                                                     | Why                                                                                                |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- |
| Most people                           | [onnx-asr](https://github.com/OpenVoiceOS/ovos-stt-plugin-onnx-asr) on a server                                                              | The OVOS default. Fast and accurate on a CPU with 2 GB of RAM, with punctuation.                   |
| Apple Silicon Mac                     | [CoreML](https://github.com/OpenVoiceOS/ovos-stt-plugin-coreml) or [whisper.cpp](https://github.com/OpenVoiceOS/ovos-stt-plugin-whispercpp)  | Both run on-device and use Apple's hardware acceleration. onnx-asr is still fast on Apple Silicon. |
| You have an NVIDIA GPU                | [FasterWhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper) or [NeMo](https://github.com/OpenVoiceOS/ovos-stt-plugin-nemo) | Large models, many languages, fast with CUDA.                                                      |
| Accuracy matters most                 | [ROVER](https://github.com/OpenVoiceOS/ovos-stt-plugin-rover)                                                                                | Runs several engines at once and takes a majority vote.                                            |
| Basque, Galician, Catalan, or Spanish | [whisper-lm](https://github.com/OpenVoiceOS/ovos-stt-plugin-whisper-lm)                                                                      | Whisper plus language models trained for those languages.                                          |

My advice from 2024 still holds for the Raspberry Pi: don't run STT on the assistant itself. Run it on a bigger machine on your network and point the assistant at it. The [next section](#run-stt-on-a-server) shows how.

I haven't tested a Pi 5. [sherpa-onnx](https://github.com/OpenVoiceOS/ovos-stt-plugin-sherpa-onnx) ships small Moonshine and Transducer models that may make on-device STT practical there. If you've tried them, tell me how it went!

## Run STT on a server

The client side is the same for every engine. Install `ovos-stt-plugin-server` on the assistant and point it at your server. Swapping engines is then a server-side change only.

For the server, I keep a repo of tested recipes: [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers). Each recipe runs one engine behind the standard OVOS server API, in Docker or a Python virtual environment, and CI sends real audio to every recipe each week. onnx-asr is the STT recipe today. I wrote more about it in [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).

If you'd rather build your own, OVOS provides [ovos-stt-server](https://github.com/OpenVoiceOS/ovos-stt-server) and [Docker images](https://github.com/OpenVoiceOS/ovos-docker-stt). If you also run Home Assistant, note that ovos-stt-server doesn't speak Wyoming, Home Assistant's voice protocol. [wyoming-ovos-stt](https://github.com/OpenVoiceOS/wyoming-ovos-stt) bridges the gap. It can load an OVOS STT plugin itself, or sit in front of your ovos-stt-server.

## Maintained OVOS plugins (November 2026)

### Offline, ONNX-based

| Plugin                                                                    | Notes                                                                                                                               |
| ------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| [onnx-asr](https://github.com/OpenVoiceOS/ovos-stt-plugin-onnx-asr)       | The default. Runs Parakeet, Canary, Whisper, GigaAM, and other models exported to ONNX, with no PyTorch dependency.                 |
| [sherpa-onnx](https://github.com/OpenVoiceOS/ovos-stt-plugin-sherpa-onnx) | Built on k2-fsa's Sherpa-ONNX. Supports Transducer, Whisper, Moonshine, Paraformer, and CTC models, and downloads them by model ID. |
| [citrinet](https://github.com/OpenVoiceOS/ovos-stt-plugin-citrinet)       | An older ONNX-converted NeMo model. Still maintained, but onnx-asr has better models.                                               |

### Apple hardware

| Plugin                                                                   | Notes                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [CoreML](https://github.com/OpenVoiceOS/ovos-stt-plugin-coreml)          | Runs on-device with CoreML. One plugin covers four NeMo model families, with 80 pre-converted models on [Hugging Face](https://huggingface.co/collections/OpenVoiceOS/stt-asr-coreml-69a0fa2e3ccaf5a7690de254). Not on PyPI yet, so install it from GitHub. |
| [whisper.cpp](https://github.com/OpenVoiceOS/ovos-stt-plugin-whispercpp) | A C/C++ port of Whisper. Optimized for Apple Silicon, and also fast on x86 CPUs with AVX.                                                                                                                                                                   |

### GPU-friendly

| Plugin                                                                           | Notes                                                                                         |
| -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| [FasterWhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper)    | Whisper with CTranslate2. Fast with CUDA, multilingual.                                       |
| [Whisper (transformers)](https://github.com/OpenVoiceOS/ovos-stt-plugin-whisper) | Whisper through Hugging Face `transformers`. Use it for models FasterWhisper doesn't support. |
| [NeMo](https://github.com/OpenVoiceOS/ovos-stt-plugin-nemo)                      | NVIDIA NeMo models. Best with a GPU.                                                          |
| [wav2vec2](https://github.com/OpenVoiceOS/ovos-stt-plugin-wav2vec2)              | Benefits from a GPU but doesn't require one.                                                  |

### Combining engines

| Plugin                                                        | Notes                                                                                                                                                                                              |
| ------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [ROVER](https://github.com/OpenVoiceOS/ovos-stt-plugin-rover) | A meta-engine. It runs several STT plugins in parallel, aligns their transcripts, and keeps the words most of them agree on. More accurate than any single engine, at the cost of running several. |

### Language-specific

| Plugin                                                                  | Notes                                                                                                                                                                                          |
| ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [whisper-lm](https://github.com/OpenVoiceOS/ovos-stt-plugin-whisper-lm) | Whisper combined with KenLM n-gram models or an LLM for Basque, Galician, Catalan, and Spanish. The language models are large (8 to 20 GB), and some dependencies need Python 3.11 or earlier. |

### Legacy and cloud

| Plugin                                                                      | Notes                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| [Vosk](https://github.com/OpenVoiceOS/ovos-stt-plugin-vosk)                 | Small and CPU-friendly, but not accurate enough for general STT. It is still the best way to make a custom wake word without training a model. |
| [Pocketsphinx](https://github.com/OpenVoiceOS/ovos-stt-plugin-pocketsphinx) | Still maintained, but its own README calls it "very low accuracy and NOT recommended."                                                         |
| [Chromium](https://github.com/OpenVoiceOS/ovos-stt-plugin-chromium)         | Uses an unofficial Google API. Fast, but Google doesn't support it and it isn't private.                                                       |
| [Azure](https://github.com/OpenVoiceOS/ovos-stt-plugin-azure)               | Microsoft's cloud STT. High quality and usage-priced, but not private.                                                                         |

## Archived since 2024

These plugins from the 2024 post are now archived. Don't start new setups with them. Most of them name a replacement:

| Archived plugin                                                                                   | Use instead                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Meta MMS](https://github.com/OpenVoiceOS/ovos-stt-plugin-mms)                                    | [wav2vec2](https://github.com/OpenVoiceOS/ovos-stt-plugin-wav2vec2). The repo has a migration guide.                                                                   |
| [Nòs](https://github.com/OpenVoiceOS/ovos-stt-plugin-nos) (Galician)                              | wav2vec2, with the same Proxecto Nós model.                                                                                                                            |
| [HiTZ](https://github.com/OpenVoiceOS/ovos-stt-plugin-HiTZ) (Basque)                              | onnx-asr with `OpenVoiceOS/stt-eu-conformer-ctc-large-onnx`. These are ONNX exports of the same HiTZ models, and they run on a CPU. NeMo also works if you have a GPU. |
| [Iberian FasterWhisper](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper-zuazo)       | No named replacement. whisper-lm uses the same Zuazo fine-tuned Whisper models.                                                                                        |
| [projectAINA-remote](https://github.com/OpenVoiceOS/ovos-stt-plugin-projectAINA-remote) (Catalan) | No named replacement. whisper-lm has a Catalan language model.                                                                                                         |

whisper-lm is heavy. Its language models are 8 to 20 GB each, it supports only Python 3.11 or older, and pairing it with an LLM needs a GPU with a lot of memory.

## What about Neon's plugins?

In 2024 I listed several STT plugins from Neon. Most haven't changed in years. Silero and Vosk were last updated in 2021–2022, and the NeMo plugins, which run Neon-tuned custom models, in mid-2025. Neon builds on OVOS, so the OVOS plugins above work on Neon too, and they're where the active work is.

## Conclusion

The OVOS speech stack is in a better place than it was two years ago. For most people, the answer is short: run onnx-asr on a server and point every assistant at it.

Engine lists like this one go stale. The [ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers) repo is tested weekly, so it stays current even when this post doesn't.

Questions? Comments? Feedback? Let me know on the [Conversational AI Community Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org).
