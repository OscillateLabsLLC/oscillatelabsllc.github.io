---
title: "My Mac Voice Assistant Setup in 2026"
author: "Mike Gray"
date: 2026-11-08T00:00:00-06:00
draft: true
slug: mac-voice-assistant-setup-2026
description: "How I run OpenVoiceOS on a Mac in 2026: ovos-installer, the Mac PHAL plugin, Visage, and on-device speech with CoreML and Pocket TTS."
keywords: ["ovos", "macos", "apple silicon", "voice assistant", "visage", "coreml"]
tags: ["ovos", "macos"]
categories: ["personal voice assistant"]
---

> **OUTLINE.** This is a draft outline, not a finished post. It replaces the archived [OVOS on a Mac (2023)](/posts/ovos-on-a-mac/). After it publishes, point that post's Archived note here.

## Hook

- In 2023, running OVOS on a Mac meant a manual install and a lot of workarounds.
- In 2026, ovos-installer supports macOS natively, and Apple Silicon is a good speech machine.

## Install

- ovos-installer on macOS: the command, what it sets up, and where the config lives.

> **TODO (Mike):** Add the exact install command and any Mac-specific prompts or gotchas.

## Make it feel native

- **[PHAL-plugin-mac](https://github.com/OscillateLabsLLC/ovos-phal-plugin-mac):** system commands, fake ducking for OCP/ovos-media, barge-in volume adjustment, GUI buttons, and service management. Needs `skill-ovos-volume`. Most system commands need a sudoers entry (show the snippet, and explain the security trade-off).
- **ovos-mac-control:** fast on/off for a native install. It fills the gaps in ovos-installer's per-service launchd helper.
  - `ovosctl start|stop|toggle|status`: one command for all five services. `stop` waits for a full shutdown, and the stopped state persists across reboots.
  - **Menu bar switch:** a SwiftBar plugin. 🎙 when everything is up, `🎙 n/5` when partly up, 🎙⚪️ when stopped. The dropdown has start, stop, and restart for each service, and opens its logs in Console.app.
  - **Config that survives upgrades:** the installer rewrites `mycroft.conf` on every run. Keep hand edits in `mycroft.conf.overlay.json`. `ovosctl upgrade` merges them back, and `ovosctl conf` reports drift.
  - **Log rotation:** the logs had grown to 260MB unrotated.
  - **Upgrade wrapper:** `ovosctl upgrade --branch` tests an installer branch. It also handles three gotchas: a virtualenv on `PATH`, pinning `OVOS_VENV_PYTHON`, and root-owned Ansible files.
  - **Visage and the GUI as LaunchAgents:** they stay up when OVOS stops, and reconnect when it comes back (`ovosctl gui install`).

> **TODO (Mike):** ovos-mac-control is on your private Forgejo. Publish it to OscillateLabsLLC (or a public mirror) before this post goes out, so readers can install it. Then link it here.

> **TODO (Mike):** Its README says "tested on Sonoma 14.6.1". Update that if you've run it on a newer macOS.

## A face: Visage

- [Visage](https://github.com/OscillateLabsLLC/ovos-visage): a browser GUI for OVOS in the style of the 2018 Mycroft Mark II concept.
- How to run it on a Mac, and whether you use the browser or the native macOS build.
- Screenshot or GIF.

## Speech on Apple Silicon

- **STT on-device:** the CoreML plugin (80 pre-converted models) or whisper.cpp. Compare with sending audio to a speech server.
- **TTS on-device:** Pocket TTS, about 6x real time on Apple Silicon, with voice cloning. Kokoro as the alternative. For the best cloned voice, Audio8 on MPS: RTF 0.54 on an M3 Max, using about 4.7 GB of unified memory.
- Link: the 2026 STT and TTS posts.

> **TODO (Mike):** Which do you actually run: on-device, or the Mac as a client of your speech server? Real latency numbers from your machine would make this section.

## Extras I use

- Kiwix MCP for offline answers.
- Home Assistant and Music Assistant skills.

> **TODO (Mike):** Keep this to what you run daily on the Mac.

## Close

- What's still rough on macOS.
- Forums and Matrix.
