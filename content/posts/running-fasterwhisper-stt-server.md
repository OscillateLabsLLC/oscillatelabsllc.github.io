---
title: "Running A FasterWhisper STT Server"
author: "Mike Gray"
date: 2023-06-06T12:38:14-04:00
draft: false
type: post

slug: running-fasterwhisper-stt-server # Will take the filename as the slug.
description: "Running a FasterWhisper STT Server for Neon and OVOS Personal Private Voice Assistants."
thumbnail: "images/whisper.jpg"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

Piggybacking off [my post about running a local, private STT server](../configuring-local-stt), we're going to take a look at running a FasterWhisper STT Docker image. FasterWhisper is an improvement on the open source Whisper STT project that adds some interesting features, including the ability to run exclusively via CPU, and using less memory. The large model is multi-lingual and does an excellent job of capturing punctuation. FasterWhisper is the current preferred STT solution for the OVOS team.

It even transcribes error sounds!
![As demonstrated by Jarbas, the lead developer for OVOS, FasterWhisper even transcribes the error sound on OVOS!](/images/beepbeep.png)

**NOTE:** OVOS community member goldyfruit [shared his repo with multiple STT containers already available](https://github.com/OpenVoiceOS/ovos-docker-stt), if you'd like to compare his work or even just use it instead of building your own.

## mycroft.conf

Create a `mycroft.conf` file with the settings below.

[Note that you can configure the settings to your preferences](https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper/). Whisper's English-only models do perform better than the general models if you know that you're only going to speak English to your assistant. If you have any intention of going multilingual, though, the general models are the way to go.

```json
{
  "stt": {
    "module": "ovos-stt-plugin-fasterwhisper",
    "ovos-stt-plugin-fasterwhisper": {
      "model": "medium.en",
      "use_cuda": false,
      "compute_type": "float16",
      "beam_size": 5
    }
  }
}
```

## Dockerfile

Credit to OVOS community member nold360 for this Dockerfile! Create a file called `Dockerfile` with the below contents.

```dockerfile
FROM python:3.11-slim
ARG ENGINE=ovos-stt-plugin-fasterwhisper
ENV ENGINE=${ENGINE}
ENV PATH=/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/home/ovos/local/bin:/home/ovos/local/usr/bin:/home/ovos/.local/bin

RUN adduser --disabled-password ovos
USER ovos
WORKDIR /home/ovos

RUN pip3 install --no-cache-dir \
                 flask \
                 SpeechRecognition \
                 mycroft-messagebus-client \
                 ${ENGINE} \
                 ovos-stt-http-server

COPY mycroft.conf /home/ovos/.config/mycroft/mycroft.conf

CMD ovos-stt-server --engine ${ENGINE}
```

As nold360 told me, "using --build-arg ENGINE=... you can basically build any stt server with this."

## Build and run

Next, run `docker build -t ovos-stt-server .` to build the Docker image. This will take a few minutes, as it needs to download the base image and then install all of the dependencies. Once it's done, you can run it with `docker run -d --name ovos-stt-server -p 8080:8080 ovos-stt-server`. This will run the container in the background, name it `ovos-stt-server`, and forward port 8080 from the container to port 8080 on the host. You can change the port on the host if you want to run multiple STT servers on the same machine.

Running your STT server image using `docker run` is a great way to test and start out, but if you want this to persist, you'll want to [configure a systemd service to run it](https://blog.marcnuri.com/docker-container-as-linux-system-service) or use something like Kubernetes or Hashicorp Nomad to schedule the container. Those options are out of the scope of this article, but I may write a follow-up post on them.

## Configuring Neon/OVOS

Now that you have a server running, you'll need to point your OVOS or Neon device to that server to use for STT:

OVOS:

```json
{
  "stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": "http://<SERVER_IP>:8080/stt"
    }
  }
}
```

Neon:

```yaml
stt:
  module: ovos-stt-plugin-server
  ovos-stt-plugin-server:
    url: http://<SERVER_IP>:8080/stt
```

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
