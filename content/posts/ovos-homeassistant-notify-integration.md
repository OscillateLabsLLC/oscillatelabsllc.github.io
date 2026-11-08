---
title: "Home Assistant Integration for OVOS"
author: "Mike Gray"
date: 2023-06-17T16:36:17-05:00
draft: false
type: post

slug: ovos-homeassistant-notify-integration
description: "A custom Home Assistant integration that sends notifications to OVOS and Neon, replacing the outdated Mycroft integration."
thumbnail: "https://raw.githubusercontent.com/OpenVoiceOS/ovos_assets/master/Logo/Raw/ovos-logo-sticker-square.svg"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos", "home assistant"]
tags: ["homelab", "mycroft", "neon", "ovos", "home assistant"]
categories: ["home automation", "personal voice assistant", "home assistant"]
---

{{< alert >}}
**Update.** Sending messages straight to the OVOS message bus is not a recommended approach. Generally, you should proxy the bus behind a tool like HiveMind or Neon Hub. For general Home Assistant control, use [skill-homeassistant](https://github.com/OscillateLabsLLC/skill-homeassistant). Watch this space for updates on Neon Hub integration with Home Assistant!
{{< /alert >}}

Home Assistant has a [Mycroft integration available](https://www.home-assistant.io/integrations/mycroft/), but it is quite old, and as Neon and OVOS continue to separate from the original codebase it will become less functional. Additionally, it is not following Home Assistant integration development best practices. As of the 2023.6 series of releases, the integration does not work with Neon or OVOS running on the [Mycroft Mark 2](https://mycroft.ai/product/mark-ii/).

I've made a preliminary integration for OVOS (which also works with Neon, being based on the same codebase). It is available at [https://github.com/mikejgray/ovos-home-assistant-integration](https://github.com/mikejgray/ovos-home-assistant-integration). It does not follow Home Assistant's `ConfigFlow` convention right now, mostly because I was having difficulty understanding how to implement it, so it is a manual configuration. I will be working on that in the future.

Here are the steps to install it:

## Install from HACS

Since this is not an officially accepted Home Assistant core integration, you'll have to either/copy paste the files manually, or use [HACS (Home Assistant Community Store)](https://hacs.xyz/docs/community_guides). This guide assumes that you have HACS installed already. If not, check the link for a series of community guides that explain how to do so.

From HACS -> Integrations, select the three dots on the upper-right, then Custom Repositories, then add [https://github.com/mikejgray/ovos-home-assistant-integration](https://github.com/mikejgray/ovos-home-assistant-integration) as the Repository with Integrations as the Category.

Install version v0.0.2 or later (v.0.0.1 was me trying the "right" way and failing).

[Next, restart Home Assistant from Settings -> System.](https://community.home-assistant.io/t/how-do-i-restart-home-assistant-after-update/435582/13)

## Configuration

To set up the integration, you need access to your `config/configuration.yaml` file. The easiest way to do that from within Home Assistant is to use one of these two add-ons:

1. File editor
1. Studio Code Server

In configuration.yaml, the OVOS integration is configured the same way the Mycroft integration was, but replace mycroft with ovos. Example:

```yaml
ovos:
  host: 192.168.42.42 # Replace with your OVOS/Neon voice assistant IP/hostname
notify:
  - platform: ovos
    name: mark2 # Name it whatever you want, so you can find it in Home Assistant
```

All the integration does as of June 17, 2023, is accept text for OVOS/Neon to speak. You call the `notify.mark2` (or whatever you named it) service with the text you'd like it to speak, a connection is made to the Message Bus on the device, and the device will use its default Text-To-Speech to talk.

Note that if you are properly firewalling access to your device, you will need to allow a port 8181 connection to your device from Home Assistant!

## What comes next?

This is the first of hopefully many updates to come, including HiveMind support and possibly a `media_player` portion of this integration! Feedback/PRs welcome. Please be gentle because I'm having a heck of a time grokking how integrations are supposed to work, and the Home Assistant developer community has been less than welcoming so far. If anyone knows where to find some beginner-friendly communities for developing Home Assistant integrations, or even tutorials, please do send them my way.

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OpenVoiceOS - Development chat on Matrix](https://matrix.to/#/#openvoiceos-development:matrix.org).
