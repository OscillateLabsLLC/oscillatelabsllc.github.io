---
title: "Open Source"
description: "Open-source projects and plugins from Oscillate Labs — AI agent memory, voice assistant tooling, and developer utilities."
showDate: false
showReadingTime: false
showTableOfContents: true
showAuthor: false
---

We build a lot in the open. Most of our open-source work clusters around three themes: **giving AI agents reliable infrastructure**, **connecting AI assistants to real systems**, and **private, local voice assistants** built on [Open Voice OS](https://www.openvoiceos.org/) (OVOS) and [Neon AI](https://neon.ai/).

Everything below lives on [our GitHub](https://github.com/OscillateLabsLLC).

## AI agent infrastructure

**[Engram](https://github.com/OscillateLabsLLC/engram)** — an event-sourced memory system for AI agents. There's no LLM in the write path, which provides fast, reliable episode storage with semantic search. An agent can remember across sessions without a model hallucinating its own history. [Documentation](https://oscillatelabsllc.github.io/engram/).

## Connecting AI to real systems

These are small, dependency-light Python client libraries — each one published to PyPI and shipped three ways from a single codebase: a plain **Python/CLI client**, a **Model Context Protocol (MCP) server**, and a bundled **Claude skill**. Write the integration once, use it from a script, from any MCP client, or directly from Claude.

- **[kiwix-mcp](https://github.com/OscillateLabsLLC/kiwix-mcp)** — turns a [Kiwix](https://kiwix.org) library into a private, local, searchable knowledge store your AI assistant can read: Wikipedia, Stack Exchange, medical and technical references, and more, all served from your own machine and fully available even when the internet isn't. Ships as a client library, MCP server, Claude skill, and OVOS integration from one package. `pip install kiwix-mcp`.
- **[ha-http-client](https://github.com/OscillateLabsLLC/home-assistant-client)** — a zero-dependency client for the [Home Assistant](https://www.home-assistant.io/) REST API, with a `ha-client` CLI and a bundled Claude Code skill so Claude and other LLMs can control your smart home directly. `pip install ha-http-client`.
- **[music-assistant-client](https://github.com/OscillateLabsLLC/music-assistant-client)** — a lightweight client for the [Music Assistant](https://music-assistant.io) API, with a bundled Claude skill for voice- and chat-driven media control. `pip install ma-http-client`.

## Voice assistant tooling (OVOS / Neon)

We maintain a family of plugins and skills for OVOS and Neon AI — the open-source, self-hosted voice assistant stack. These run on hardware you own, with no cloud dependency.

### Text-to-speech plugins

- **[Kokoro TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro)** — the 82M-parameter multilingual Kokoro model, wired up for OVOS.
- **[Pocket TTS](https://github.com/OscillateLabsLLC/ovos-tts-plugin-pocket-tts)** — Kyutai's lightweight, CPU-only TTS (~6× real-time on Apple Silicon, no GPU), with voice cloning.

### Dialog transformers

- **[Ollama dialog transformer](https://github.com/OscillateLabsLLC/ovos-ollama-dialog-transformer-plugin)** — routes assistant responses through a local [Ollama](https://ollama.com) model so your voice assistant answers in its own voice, fully offline.
- **[Kid-friendly dialog transformer](https://github.com/OscillateLabsLLC/ovos-kid-friendly-dialog-transformer)** — the same idea, tuned to keep responses appropriate for children.

### Skills

- **[Home Assistant](https://github.com/OscillateLabsLLC/skill-homeassistant)** — control your smart home by voice through OVOS or Neon.
- **[Music Assistant](https://github.com/OscillateLabsLLC/skill-musicassistant)** — voice control for [Music Assistant](https://music-assistant.io) media playback.
- ...and more on [GitHub](https://github.com/OscillateLabsLLC?q=skill-).

## Products

Looking for our shipped products instead? See **[BookTrove](/booktrove/support/)** — a book price tracker for people who buy physical books.
