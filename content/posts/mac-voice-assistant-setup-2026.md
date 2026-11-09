---
title: "My Mac Voice Assistant Setup in 2026"
author: "Mike Gray"
date: 2026-11-08T00:00:00-06:00
draft: false
slug: mac-voice-assistant-setup-2026
description: "How I run OpenVoiceOS on a Mac in 2026: ovos-installer, a menu bar switch, the native Visage GUI, and on-device speech with Parakeet and Kokoro."
keywords:
  [
    "ovos",
    "macos",
    "apple silicon",
    "voice assistant",
    "visage",
    "parakeet",
    "kokoro",
    "swiftbar",
  ]
tags: ["ovos", "macos"]
categories: ["personal voice assistant"]
---

Looking back on my 2023 Mac post, running OpenVoiceOS (OVOS) on a Mac was a chore. In 2026, things have changed. `ovos-installer` supports macOS natively, and both speech-to-text (STT) and text-to-speech (TTS) run well on Apple Silicon. I've also released some helpers through Oscillate Labs that improve the overall experience. This post is a guide to my current Mac setup, and how I make it feel native without living in the terminal and config files.

## Install OVOS

The official installer used to be terminal-only. It now has a setup wizard in the browser that walks you through every choice, then gives you the command to run. Start at [start.openvoiceos.org](https://start.openvoiceos.org/).

Before you run it, your Mac needs a few things. The installer checks for them and stops if they're missing:

- An Apple Silicon Mac on macOS 15 or later.
- [Homebrew](https://brew.sh), plus a newer Bash than the one macOS ships: `brew install bash`. Your login shell can stay zsh.
- The Xcode Command Line Tools: `xcode-select --install`.
- Microphone permission for your terminal app, under System Settings → Privacy & Security → Microphone. Without it, the assistant starts but never hears you.

Then run the command the wizard gives you. Without the wizard, it's:

```bash
sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/OpenVoiceOS/ovos-installer/main/installer.sh)"
```

On a Mac, the installer always uses a Python virtual environment and the alpha channel. Here's what you end up with:

- **Config** in `~/.config/mycroft/`, with the main file at `~/.config/mycroft/mycroft.conf`.
- **A virtual environment** in `~/.venvs/ovos`.
- **Five services** that run as LaunchAgents: the messagebus, core, audio, the listener, and PHAL (the hardware layer). The installer also adds an `ovos` shell command to manage them, one at a time.
- **The Mac PHAL plugin.** The installer adds [ovos-phal-plugin-mac](https://github.com/OscillateLabsLLC/ovos-phal-plugin-mac) automatically, along with the volume skill it needs. It lets OVOS change your Mac's volume, ducks the volume while you're talking to it, and adds commands like lock, sleep, screenshot, and dark mode.

Volume, lock, sleep, screenshots, and dark mode work out of the box. Rebooting, shutting down, turning SSH on or off, and syncing the clock need root, and the plugin doesn't run them that way yet, so leave those to the Mac itself for now.

## Make it feel native

The base install assumes you're comfortable on the command line. Starting, stopping, and checking on OVOS means juggling five services, and the installer rewrites your config every time you upgrade. I wanted it to feel more like a Mac app, so I wrote [ovos-mac-control](https://github.com/OscillateLabsLLC/ovos-mac-control). Install it with Homebrew:

```bash
brew trust --formula oscillatelabsllc/tap/ovos-mac-control
brew install oscillatelabsllc/tap/ovos-mac-control
```

(Homebrew now asks you to trust a formula from a third-party tap before it will install it.)

It gives you three things:

**One way to control it.** `ovosctl start`, `stop`, `toggle`, and `status` act on all five services at once. `stop` waits until everything has actually shut down, and OVOS stays off across reboots until you start it again.

**A menu bar icon.** A [SwiftBar](https://swiftbar.app) plugin puts the OVOS logo in your menu bar. It shows `3/5` beside the logo when only some services are up, and `off` when OVOS is stopped. The menu starts, stops, and restarts each service, and opens its logs in Console.app. To set it up, install SwiftBar and launch it once, so it asks you where to keep its plugins. Then link the plugin:

```bash
brew install --cask swiftbar
ln -sf "$(brew --prefix)/share/ovos-mac-control/ovos.5s.sh" "$(defaults read com.ameba.SwiftBar PluginDirectory)/ovos.5s.sh"
```

**Durable config.** The installer rewrites `mycroft.conf` every time it runs, so anything you added by hand disappears. Put those settings in `~/.config/mycroft/mycroft.conf.overlay.json` instead. `ovosctl upgrade` re-runs the installer and merges your overlay back in afterwards, and `ovosctl conf` tells you if the two have drifted apart.

It also rotates the logs, which had grown to 260MB on my Mac, and `ovosctl upgrade --branch` lets you test an installer branch.

## Visage

[Visage](https://github.com/OscillateLabsLLC/ovos-gui-visage) is a native GUI for OVOS, in the style of the 2018 Mycroft Mark II Kickstarter concept. It shows a face that listens, thinks, and talks, plus the clock, the weather, and whatever a skill puts on screen. Visage is fully compliant with the old OVOS GUI spec and also with the soon-to-be-released GUI spec.

By default it's a small desktop widget in a corner of the screen, beneath your other windows. You can also float it above everything, open it as an ordinary window, or run it full screen as a kiosk. In peek mode, it shrinks to just the face when nothing is happening, and grows when OVOS has something to show. A face icon in the menu bar switches between them.

![Visage sample view, with the face superimposed over the resting home screen.](https://github.com/OscillateLabsLLC/ovos-gui-visage/raw/main/docs/screenshots/face/colours-ember-speaking.png)

Setup takes three commands:

```bash
brew trust --cask oscillatelabsllc/tap/ovos-gui-visage
brew install --cask oscillatelabsllc/tap/ovos-gui-visage
ovosctl gui install
```

The first two install the app. `ovosctl gui install` sets up the rest. Visage talks to OVOS through the OVOS GUI service and the homescreen, so `ovosctl` installs those in their own virtual environment, away from `~/.venvs/ovos`, where the homescreen's older pinned packages would break OVOS. The first run takes a minute or two while it downloads them. Then it runs all three as LaunchAgents, so they start at login, restart if they crash, and reconnect whenever OVOS comes back. They're separate from the five OVOS services, so `ovosctl stop` leaves the face on screen. The menu bar icon gets a GUI section too, with an **Open Visage** item.

## Speech on Apple Silicon

There are far more options for running STT and TTS on Apple Silicon than there were in 2023. I compare them all in [STT Options for OVOS in 2026](/posts/stt-options-ovos-2026/) and [TTS Options for OVOS in 2026](/posts/tts-options-ovos-2026/). The two I use both run on-device, and both are fast:

- **STT:** [onnx-asr](https://github.com/OpenVoiceOS/ovos-stt-plugin-onnx-asr) with NVIDIA's Parakeet model (`nemo-parakeet-tdt-0.6b-v2`).
- **TTS:** [Kokoro](https://github.com/OscillateLabsLLC/ovos-tts-plugin-kokoro).

There's also a v3 Parakeet model. If you use OVOS in another European language, use v3: it adds 24 European languages to v2's English. If you only speak English with OVOS, stay on v2. It's slightly more accurate in English (an average word error rate of 6.05% against v3's 6.34% on the Hugging Face Open ASR Leaderboard).

One Mac-specific tip for Kokoro: run it on the CPU, not the GPU. The plugin's default is already the CPU, because on Apple Silicon it's about five times faster than Metal for this model.

I've also experimented with one-shot voice cloning using [Audio8](https://github.com/OscillateLabsLLC/ovos-tts-plugin-audio8) on the GPU. On an M3 Max it runs about twice as fast as real time, and the cloned voices are very good. I use it for novelty voices.

## Skill configuration

Most OVOS skills need little or no configuration, but sometimes you need to change a setting. Rather than remember where each skill keeps its settings file, I wrote [ovos-skill-config-tool](https://github.com/OscillateLabsLLC/ovos-skill-config-tool), which shows every skill's settings in your browser. `ovosctl` sets it up for you:

```bash
ovosctl settings install
```

After that it's always running. Choose **Skill Settings…** in the menu bar, or go to [http://localhost:8000](http://localhost:8000). The login is `ovos` / `ovos`. That's fine here, because `ovosctl` only lets it listen on your Mac itself. Skill settings can hold secrets like API tokens, so nothing else on your network can reach it.
## Extras I use

I'm fully in the Home Assistant ecosystem, and OVOS is my voice interface to it, through [the Home Assistant skill](https://github.com/OscillateLabsLLC/skill-homeassistant) I wrote and maintain. I also use [kiwix-mcp](https://github.com/OscillateLabsLLC/kiwix-mcp), which includes an OVOS skill that answers questions from my local, offline copy of Wikipedia and other references.

OVOS Common Play (OCP) works well with `mpv` on macOS, but all my music is already in the Open Home Foundation's [Music Assistant](https://www.music-assistant.io/). Rather than make OCP talk to Music Assistant, I wrote [a skill that controls Music Assistant directly](https://github.com/OscillateLabsLLC/skill-musicassistant). It only works with OCP turned off (you remove the two OCP pipeline plugins from `intents.pipeline` in `mycroft.conf`), so it's optional, and probably not the right choice for most people. I believe OVOS is working on native Music Assistant support in OCP, which will be a better solution in the long run.

## Close

There are still too many manual steps to get a Mac running OVOS in a sane way. I hope Mac installs become more native soon, with better tools for settings and skill configuration. In the meantime, I hope this post helps you get OVOS running on your Mac in a way that feels native and easy to use.

Questions? Comments? Suggestions? Email me at [mike@oscillatelabs.net](mailto:mike@oscillatelabs.net), or open an issue in one of my GitHub repos. I'm happy to support these tools and the people who use them.
