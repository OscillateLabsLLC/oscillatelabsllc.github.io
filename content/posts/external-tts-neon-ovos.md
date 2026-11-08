---
title: "Using External (Private or Public) TTS on Neon and OVOS"
author: "Mike Gray"
date: 2023-07-26T00:06:50-05:00
draft: false
type: post

description: "Changing the TTS engine in the NeonAI Personal Voice Assistant to any supported external TTS."

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Update.** This still works. For self-hosted TTS servers, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

[Neon.AI](https://neon.ai) and [OpenVoice OS (OVOS)](https://openvoiceos.com/) both offer out-of-the-box smart speaker/voice assistant platforms, using a combination of their own aggregated text-to-speech (TTS) and speech-to-text (STT) hosted options as well as low-power open source engines in case the internet is not available. Recently, a community member was asking about ways to improve the performance of the Neon software on the Mycroft Mark 2 smart speaker. I realized that I'd done a post on [configuring Coqui-TTS for Neon](./coqui-tts-neon-ovos.md), but not how to configure another external TTS system. Let's fix that.

## Prerequisites

In order to use an external TTS server, one must be running, either online or locally. [https://github.com/OpenVoiceOS/status](https://github.com/OpenVoiceOS/status) is a list OVOS community-maintained TTS and STT servers and their status. For example purposes or quickstarts those are perfect. Ideally you may want to run your own using [ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server-plugin), which is the subject of [another blog post](../configuring-local-tts). Either way you'll need the URL and/or port number of your desired TTS server.

You'll also need to have an existing Neon or OVOS setup, either on specialized smart speaker hardware (Mycroft Mark 2 or Mark 1) or some custom hardware like a Raspberry Pi or x86 Mini PC.

If you're running Neon or OVOS, the necessary plugin is pre-installed. It's the default for most OVOS installations.

## Configuring Neon/OVOS

### Neon

If you're running Neon, you'll need to edit your `~/.config/neon/neon.yaml` file.

```yaml
tts:
  module: ovos-tts-plugin-server
  ovos-tts-plugin-server:
    host: https://pipertts.ziggyai.online/ # Replace with your TTS server URL, optionally with port specified
```

### OVOS

If you're running OVOS, you'll need to edit your `~/.config/mycroft/mycroft.conf` file and add this to the root of the object.

```json
{
  "tts": {
    "module": "ovos-tts-plugin-server",
    "ovos-tts-plugin-server": { "host": "https://pipertts.ziggyai.online/" }
  }
}
```

Replace the `host` value with your TTS server URL, optionally with port specified.

Also note that the `ovos-tts-plugin-server` section is entirely optional. If you don't specify any hosts, it will default to the OVOS community-maintained TTS servers.

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org).
