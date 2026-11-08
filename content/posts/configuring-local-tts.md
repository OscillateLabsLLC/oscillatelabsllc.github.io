---
title: "Configuring Local TTS"
author: "Mike Gray"
date: 2023-10-16T20:36:17-05:00
draft: false
type: post

slug: configuring-local-tts
thumbnail: "images/sam.jpg"

keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

On the journey towards creating a personal, private, open source voice assistant, one of the most important components is text-to-speech (TTS). This is the component that takes the text that the voice assistant wants to speak and turns it into an audio file that can be played through the assistant's speakers.

TTS has come a long way over the years and is much less compute-intensive than speech-to-text (STT), but there are still benefits to running your TTS off the actual assistant hardware. For one thing, it frees up resources on the assistant for other tasks. For another, multiple assistants can share the same TTS server, which can be useful if you want to have a consistent voice among them. Finally, you can use higher-quality voices on a more powerful machine than you can on a Raspberry Pi or other low-power device, and the real-time factor (RTF) is much lower (that's a good thing!).

OpenVoiceOS (OVOS) has created a wrapper plugin for remote TTS servers: [https://github.com/OpenVoiceOS/ovos-tts-server](https://github.com/OpenVoiceOS/ovos-tts-server) This plugin allows you to run any TTS server that is implemented as an OVOS (or Neon) plugin. OVOS maintains a long list of possible TTS servers, ranging from simple espeak and Mimic to something as high-quality and modern as Bark or Coqui. There's even support for SAM (Software Automatic Mouth)! You can find the full list here: [https://github.com/OpenVoiceOS?q=ovos-tts-plugin&type=all&language=&sort=](https://github.com/OpenVoiceOS?q=ovos-tts-plugin&type=all&language=&sort=)

In this post, we'll focus on running ovos-tts-server in a Docker container. We'll look at images that were created by OVOS community member goldyfruit and shared publicly at [https://github.com/OpenVoiceOS/ovos-docker-tts](https://github.com/OpenVoiceOS/ovos-docker-tts)).

## Prerequisites

You'll need a machine running Docker or comaptible alternative, such as Podman. You'll also need a text editor or IDE to create the configuration files we'll need. Finally, you'll need a machine capable of running the TTS server you choose. Fortunately there are many that even run well on a Raspberry Pi.

## Getting started

The simplest path forward is to run one of goldyfruit's pre-built images. We'll choose one of the most powerful TTS engines available that runs well on only CPU: [Piper](https://github.com/rhasspy/piper/).

Docker Compose is the easiest way to get started. Create a new directory and create a `docker-compose.yml` file in it. Add the following to the file:

```yaml
---
version: "3.9"

x-podman: &podman
  userns_mode: keep-id
  security_opt:
    - "label=disable"

x-logging: &default-logging
  driver: json-file
  options:
    mode: non-blocking
    max-buffer-size: 4m

volumes:
  ovos_tts_piper_cache:
    name: ovos_tts_piper_cache
    driver: local
  ovos_tts_piper_gradio_cache:
    name: ovos_tts_piper_gradio_cache
    driver: local

services:
  ovos_tts_piper:
    <<: *podman
    container_name: ovos_tts_piper
    hostname: ovos_tts_piper
    restart: unless-stopped
    image: docker.io/smartgic/ovos-tts-server-piper:${VERSION}
    logging: *default-logging
    pull_policy: always
    tty: true
    environment:
      TZ: $TZ
    ports:
      - "9666:9666"
    volumes:
      - ${CONFIG_FOLDER}:/home/${OVOS_USER}/.config/mycroft:ro,z
      - ovos_tts_piper_cache:/home/${OVOS_USER}/.local/share/piper_tts
      - ovos_tts_piper_gradio_cache:/home/${OVOS_USER}/gradio_cached_examples
```

This Docker Compose file will give you a bit more than simply issuing a `docker run` command. First, it offers support for Podman if you choose to use that. Second, it explicitly defines logging options that should optimize your performance. Next, it creates two volumes that will be used to store cached data - helpful to speed up frequent responses without consuming the container's tiny disk and causing it to crash. Finally, it defines the container itself, including the image to use, the ports to expose, and the volumes to mount.

You may have also noted that it needs some environment variables. Create a `.env` file and add the following, adjusting to your preferences:

```bash
CONFIG_FOLDER=~/ovos-tts-stt/config
OVOS_USER=ovos
TZ=America/Montreal
VERSION=alpha
```

The only one you may need to change is `TZ`, which is the timezone the container will use (list available [on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List)). The rest are fine as-is. Once OVOS 0.0.8 is released, you may want to change the `VERSION` variable to something different, but until then it's the best option.

Finally, it's time to start your TTS server. Run `docker compose up -d` to start the container in the background. It will take a few minutes to download the image and start the container, but once it's done, you'll have a Piper TTS server running on port 9666.

To test it, you can open your browser to [http://localhost:9666/synthesize/hello%20world](http://localhost:9666/synthesize/hello%20world). You should hear Alan Pope's baritone voice say "Hello world." If you don't, check the logs with `docker compose logs -f` to see if there are any errors.

## Changing configuration

The volume created from `CONFIG_FOLDER` is where you'd place a `mycroft.conf` file with the specific configuration you'd like for your plugin. For instance, for Piper, you can change the voice to something other than the default:

```json
{
  "tts": {
    "module": "ovos-tts-plugin-piper",
    "ovos-tts-plugin-piper": {
      "voice": "danny-low"
    }
  }
}
```

Restart the container by running `docker compose up -d` again and then test it out. You should hear a different voice.

## Different plugins

The configuration options for the different plugins are available at their GitHub repository, which is typically `https://github.com/OpenVoiceOS/<plugin_name>`. For example, the SAM plugin is available at [https://github.com/OpenVoiceOS/ovos-tts-plugin-sam/](https://github.com/OpenVoiceOS/ovos-tts-plugin-sam/) and takes a configuration like this:

```json
{
  "tts": {
    "module": "ovos-tts-plugin-SAM",
    "ovos-tts-plugin-SAM": {
      "voice": "elf"
    }
  }
}
```

You can adjust your `docker-compose.yml` file from [goldyfruit's example](https://github.com/OpenVoiceOS/ovos-docker-tts/blob/feat/initial/docker-compose.yml), as I did, or even run all of them at once. This can be a fun way to test out different TTS options.

You may also want to try [Coqui, which I covered in another post](/posts/coqui-tts-neon-ovos/).

In a future post, we'll talk about how to create a new container image to use a plugin that isn't already available from the community.

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
