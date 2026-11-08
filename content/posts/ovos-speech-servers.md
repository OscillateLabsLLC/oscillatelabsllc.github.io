---
title: "Self-Hosted Speech Servers for OVOS and Neon"
author: "Mike Gray"
date: 2026-11-05T00:00:00-06:00
draft: false
type: post

slug: ovos-speech-servers
thumbnail: "images/speech-servers.jpg"

summary: "My FasterWhisper server post aged badly, so I replaced it with a repo of tested STT and TTS servers for OVOS and Neon."
description: "A CI-tested repo of self-hosted speech-to-text and text-to-speech servers for OpenVoiceOS and Neon, runnable with Docker or a Python venv."

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos", "stt", "tts"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

In mid-2024, I wrote a post about how to run a self-hosted FasterWhisper STT server for OVOS and Neon. At the time, FasterWhisper was the best STT engine for OVOS, which made it a good reference (especially if you didn't want to run the thing in Docker). However, ongoing upkeep of a post like that is difficult. Bugs aren't easily fixed. The instructions get stale quietly and quickly.

The state of the art today is onnx-asr for STT and Kokoro for TTS (though phoonnx is the default in OVOS, and some prefer PocketTTS). However, it's likely that in another year or two, the state of the art will have evolved again. Rather than write another post that will degrade over time, I created a repo that runs CI tests on the servers and keeps them up to date. This post explains the repo and how to use it.

The repo is available at [https://github.com/OscillateLabsLLC/ovos-speech-servers](https://github.com/OscillateLabsLLC/ovos-speech-servers). On a weekly basis, a CI job sends real audio to every engine. Breaking upstream changes are caught before they reach users (hopefully). Issues and PRs are welcome, so the community can help keep the servers working. Best of all? This post is now evergreen. It will always point to the latest working servers.

The client side is the same for every engine: `ovos-stt-plugin-server` and `ovos-tts-plugin-server`. Swapping engines is a server-side change only. If you prefer to run the servers directly in a virtual environment, you can do that too. The repo has instructions for both Docker and venv.

## What's next, and how to help

I'd like to add more engines over time, starting with phoonnx, PocketTTS, and Audio8. Audio8 needs a GPU and clones voices, so I'll need to get some references that I have the rights to use. If you have a GPU and some reference clips, please reach out!

If you'd like to request an engine, you can do so with an issue. PRs are welcome.

Questions? Comments? Feedback? Let me know on the [Conversational AI Community Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
