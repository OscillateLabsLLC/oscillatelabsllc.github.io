---
title: "Oscillate Labs"
description: "A small software studio building cross-platform apps, private voice assistants, and AI integrations that run on hardware you own."

hero:
  headline: "Apps, voice assistants, and AI tools that run on your own hardware."
  subhead: "Oscillate Labs is a small software studio. We take projects from idea to shipped product, and we publish a lot of our work as open source."
  cta:
    - label: "Email us about a project"
      url: "mailto:mike@oscillatelabs.net"
      primary: true
    - label: "Open source"
      url: "/projects/"
      primary: false

capabilities:
  - title: "Cross-platform apps"
    body: "One Flutter codebase for iOS, Android, macOS, Windows, and Linux. BookTrove and Neon Node both ship this way."
  - title: "AI integrations"
    body: "We write one well-tested client and ship it as a Python library, a CLI, an [MCP](https://modelcontextprotocol.io) server, and a Claude skill."
  - title: "Private voice and AI"
    body: "Voice assistants and agents built on [OVOS](https://www.openvoiceos.org/) and [Neon AI](https://neon.ai/) that keep your data on your own machines."

work:
  - name: "BookTrove"
    url: "/booktrove/support/"
    kind: "Product"
    meta: "Flutter · in beta"
    body: "Watches used-book prices for your want-to-read list and tells you when a book drops below your target. No account and no server. Your data stays on your device."
  - name: "Neon Node"
    url: "https://neon.ai/"
    kind: "Client work"
    meta: "Flutter · for Neon AI"
    body: "The voice client app for Neon AI's private assistant on iOS, Android, and macOS, with an on-device wake word and voice commands that act on the device itself."
  - name: "Neon Hub"
    url: "https://github.com/NeonGeckoCom/neon-hub-installer"
    kind: "Client work"
    meta: "Docker · for Neon AI"
    body: "Installers for Neon AI's self-hosted assistant server on Linux, macOS, and Windows, plus a guide for running it on a cloud server and a bootable Debian image."
  - name: "Switchboard"
    url: "https://github.com/OscillateLabsLLC/switchboard"
    kind: "Open source"
    meta: "Python · OVOS"
    body: "Picks which skill should handle a voice request, and stays quiet when the speech wasn't meant for the assistant. On held-out household traffic it handled 84% of real requests correctly, against 42% for stock OVOS. [The data and code behind that number are public.](https://github.com/OscillateLabsLLC/switchboard-eval)"
  - name: "Home Assistant, both directions"
    url: "https://github.com/OscillateLabsLLC/skill-homeassistant"
    kind: "Open source"
    meta: "Python · Home Assistant"
    body: "A [skill](https://github.com/OscillateLabsLLC/skill-homeassistant) that lets OVOS and Neon control your smart home by voice, and our most popular repo. Plus a [HACS integration](https://github.com/OscillateLabsLLC/ha-neon-hub) that goes the other way, plugging a Neon Hub's speech-to-text, text-to-speech, and conversation agent into Home Assistant's Assist. By our own assessment, it meets every rule of Home Assistant's integration quality scale through Platinum."
  - name: "OVOS message bus in Rust"
    url: "https://github.com/OscillateLabsLLC/ovos-rust-messagebus"
    kind: "Open source"
    meta: "Rust · OVOS, Neon"
    body: "A Rust implementation of the message bus that connects OVOS and Neon components. OVOS made it the default for new installations shortly after its release."
  - name: "Engram"
    url: "https://github.com/OscillateLabsLLC/engram"
    kind: "Open source"
    meta: "Go · MCP"
    body: "Long-term memory for AI agents. Memories are stored as events with no LLM in the write path, so an agent can't rewrite its own history. We use this one daily in our own agents."
---

## Work with us

We take on a few client projects at a time. Tell us what you want to build, and we'll tell you honestly whether we're the right fit.

[mike@oscillatelabs.net](mailto:mike@oscillatelabs.net)
