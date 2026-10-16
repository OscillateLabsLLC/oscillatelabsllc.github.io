---
title: "Open Source"
description: "Open-source projects from Oscillate Labs: voice assistant tooling for OVOS and Neon, AI agent memory, and clients that connect AI assistants to real systems."
showDate: false
showReadingTime: false
showTableOfContents: true
showAuthor: false
---

Most of our open-source work falls into three areas: **private, local voice assistants** built on [Open Voice OS](https://www.openvoiceos.org/) (OVOS) and [Neon AI](https://neon.ai/), **infrastructure for AI agents**, and **clients that connect AI assistants to real systems**.

Everything below is on [our GitHub](https://github.com/OscillateLabsLLC). For client work and products, see the [homepage](/).

## Voice assistants (OVOS / Neon)

OVOS and Neon are open-source voice assistants that run on hardware you own, with no cloud dependency. We build the pieces we wished existed.

### Understanding requests

- **[Switchboard](https://github.com/OscillateLabsLLC/switchboard)** replaces OVOS's intent matchers. Its design is loosely based on how Alexa and Google Assistant historically worked. Each request goes through an exact phrase match, then a small classifier, then optionally a local LLM, and Switchboard can decide the speech wasn't meant for the assistant at all. On held-out traffic from a real household it handled 84% of requests correctly on a Neon speaker, against 42% for stock OVOS, and acted on 7 of 71 overheard utterances where stock Neon acted on all 71. The data, code, and model answers are in [switchboard-eval](https://github.com/OscillateLabsLLC/switchboard-eval).
- **[Hangup](https://github.com/OscillateLabsLLC/hangup)** quietly logs the speech Switchboard declines and says nothing.
- **[Doublecheck](https://github.com/OscillateLabsLLC/doublecheck)** asks before running a high-risk request like unlocking a door, and runs it only on a clear yes.

### Core services

- **[OVOS message bus in Rust](https://github.com/OscillateLabsLLC/ovos-rust-messagebus)** is a Rust implementation of the message bus that every OVOS and Neon component talks over. It's a single drop-in replacement binary that reads your existing `mycroft.conf` or `neon.yaml`. _Shortly after its release, OVOS adopted it as the default message bus for new installations._

### Smart home

- **[Home Assistant skill](https://github.com/OscillateLabsLLC/skill-homeassistant)** gives you voice control of your smart home through OVOS or Neon.
- **[Neon Hub for Home Assistant](https://github.com/OscillateLabsLLC/ha-neon-hub)** goes the other direction. It exposes a Neon Hub's speech-to-text, text-to-speech, and conversation agent inside Home Assistant, with Zeroconf discovery and a full Assist pipeline. By our own assessment, it meets every rule of Home Assistant's [integration quality scale](https://developers.home-assistant.io/docs/core/integration-quality-scale/) through Platinum. Install it through HACS.
- **[OVOS TTS for Home Assistant](https://github.com/OscillateLabsLLC/ha-ovos-tts)** adds Home Assistant TTS entities backed by an `ovos-tts-server`.
- **[Music Assistant skill](https://github.com/OscillateLabsLLC/skill-musicassistant)** is voice control for [Music Assistant](https://music-assistant.io) playback.

### Text-to-speech

- **[Kokoro TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro)** wires the 82M-parameter multilingual Kokoro model into OVOS.
- **[Pocket TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-pocket-tts)** runs Kyutai's CPU-only TTS at about 6× real time on Apple Silicon, with voice cloning.

### Dialog transformers

- **[Ollama dialog transformer](https://github.com/OscillateLabsLLC/ovos-ollama-dialog-transformer-plugin)** rewrites the assistant's responses with a local [Ollama](https://ollama.com) model, fully offline.
- **[Kid-friendly dialog transformer](https://github.com/OscillateLabsLLC/ovos-kid-friendly-dialog-transformer)** uses the same approach to keep responses appropriate for children.

### Interfaces and tools

- **[Visage](https://github.com/OscillateLabsLLC/ovos-visage)** is a browser GUI for OVOS in the style of the 2018 Mycroft Mark II Kickstarter concept.
- **[Virtual Mark 1](https://github.com/OscillateLabsLLC/ovos-virtual-mark1)** recreates the Mycroft Mark 1 faceplate in software: an emulated Arduino and a browser display.
- **[Skill config tool](https://github.com/OscillateLabsLLC/ovos-skill-config-tool)** is a web UI for configuring OVOS and Neon skills.
- More skills and plugins are on [GitHub](https://github.com/OscillateLabsLLC?q=ovos).

## AI agent infrastructure

**[Engram](https://github.com/OscillateLabsLLC/engram)** is an event-sourced memory system for AI agents. There's no LLM in the write path, so storage is fast and predictable, search is semantic, and an agent can't hallucinate its own history. [Documentation](https://oscillatelabsllc.github.io/engram/).

## Connecting AI to real systems

These are small Python clients with few dependencies. Each is published to PyPI and ships three ways from one codebase: a **Python library and CLI**, a **Model Context Protocol (MCP) server**, and a bundled **Claude skill**. Write the integration once and use it from a script, from any MCP client, or from Claude.

- **[kiwix-mcp](https://github.com/OscillateLabsLLC/kiwix-mcp)** turns a [Kiwix](https://kiwix.org) library into a local knowledge store your AI assistant can search: Wikipedia, Stack Exchange, medical and technical references, and more, served from your own machine and available when the internet isn't. It also ships an OVOS integration - Mike runs this one at home. `pip install kiwix-mcp`
- **[ha-http-client](https://github.com/OscillateLabsLLC/home-assistant-client)** is a zero-dependency client for the [Home Assistant](https://www.home-assistant.io/) REST API, with an `ha-client` CLI and a Claude Code skill. `pip install ha-http-client`
- **[music-assistant-client](https://github.com/OscillateLabsLLC/music-assistant-client)** is a lightweight client for the [Music Assistant](https://music-assistant.io) API, with a Claude skill for media control. `pip install ma-http-client`

## Friends

We work closely with these projects. Most of our voice work is built on them.

- **[OpenVoiceOS](https://www.openvoiceos.org/)** is the community-run, open-source voice assistant at the center of our voice work. Our Rust message bus is its default for new installations. [Source code](https://github.com/OpenVoiceOS)
- **[Neon AI](https://neon.ai/)** builds a private voice assistant on top of OVOS. We built the Neon Hub installers and the Neon Node app with them. [Source code](https://github.com/NeonGeckoCom)
