---
title: "Configuring Local STT For OVOS/Neon"
author: "Mike Gray"
date: 2023-06-06T11:58:58-04:00
draft: false
type: post

slug: configuring-local-stt # Will take the filename as the slug.
description: "Configuring a local STT server for OVOS/Neon Personal Private Voice Assistants."
thumbnail: "images/stt.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

[Neon.AI](https://neon.ai) and [OpenVoice OS (OVOS)](https://openvoiceos.com/) both offer out-of-the-box smart speaker/voice assistant platforms, using a combination of their own aggregated text-to-speech (TTS) and speech-to-text (STT) hosted options as well as low-power open source engines in case the internet is not available. While both companies go out of their way to be as privacy-respecting as possible, ultimately I don't want my voice assistant to be sending my voice to a server somewhere else on the internet or sharing the text that will be spoken aloud. I want it to be as private as possible, and I want to be able to use it even if my internet is down.

OVOS and Neon both have the ability to leverage the [OVOS STT Plugin Server](https://github.com/OpenVoiceOS/ovos-stt-plugin-server), which puts a consistent API on the front of any supported OVOS or Neon STT plugin. This enables you to run your personal, private speech-to-text on a different machine from your Voice Assistant, and even run multiple STT engines at once. It also means you are not constrained by the hardware on your Voice Assistant - you could run FasterWhisper on a machine with a GPU, for example, and use it as your primary STT engine.

In this blog post, we're going to focus on running your STT server in Docker. If there's interest, I can write a follow-up post on running it natively on Linux.

**EDIT:** In response to this point, OVOS community member goldyfruit [shared his repo with multiple STT containers already available](https://github.com/OpenVoiceOS/ovos-docker-stt). If you want to try others without having to write your own Dockerfile from scratch, check it out!

**EDIT 2:** Jarbas pointed out that the latest version of the plugin (V0.0.4a1) does actually accept a list of URLs, which it will try in order until one responds. Example config (OVOS-specific):

```json
{
  "stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": [
        "http://<SERVER_IP>:5000/stt",
        "http://<SERVER_IP2>:5000/stt",
        "http://<SERVER_IP3>:5000/stt"
      ]
    }
  }
}
```

## How it works

If you [search the OVOS GitHub organization for STT plugins](https://github.com/orgs/OpenVoiceOS/repositories?q=ovos-stt-plugin&type=all&language=&sort=), you'll see they have quite a few options available. This includes Whisper, FasterWhisper, Deepgram, Chromium, Selene (if you're running your own private instance of Mycroft's Selene backend), and more. [Searching Neon's GitHub organization yields even more](https://github.com/NeonGeckoCom?q=neon-stt-plugin&type=all&language=&sort=) - NVIDIA NeMo, Google Cloud (not a local option), Mozilla Deepspeech, and others. These are the options you have available to you without writing an additional plugin.

Once you've chosen a plugin (we'll use NVIDIA NeMo for the sake of this post), you'll need to write a Dockerfile that runs the plugin server, loads the plugin, and has the correct configuration for it all to work. We'll build the Docker image and then run it.

## Clone plugin repository

Since your plugin has unique requirements that change over time, we're going to clone it to our machine so we have the latest version.

```bash
git clone https://github.com/NeonGeckoCom/neon-stt-plugin-nemo
cd neon-stt-plugin-nemo
```

## mycroft.conf

The Docker image will need to have a `mycroft.conf` file available to it in order to run. Create and open up an empty `mycroft.conf` file in your favorite text editor, then add the following configuration:

```json
{
  "stt": {
    "module": "neon_stt_plugin_nemo",
    "stt_module_name": {}
  }
}
```

Note that even though this is a Neon plugin, which usually requires a YAML configuration, we're using JSON in a `mycroft.conf` file. We got the configuration [from the plugin's GitHub page](https://github.com/NeonGeckoCom/neon-stt-plugin-nemo). If you're not comfortable switching betwen the two, [try a website like json2yaml.com](https://json2yaml.com/).

## Dockerfile

Next, in the same directory, create a file called `Dockerfile.stt` and open it in your favorite text editor:

```dockerfile
FROM python:3.10

COPY requirements/requirements.txt /

RUN pip install Cython
RUN pip install neon-stt-plugin-nemo ovos-messagebus-client
RUN pip install git+https://github.com/OpenVoiceOS/ovos-stt-http-server

RUN mkdir -p /etc/mycroft
COPY mycroft.conf /etc/mycroft.conf

EXPOSE 8080

RUN ovos-stt-server --engine neon-stt-plugin-nemo --port 8080
```

Next, run `docker build -t ovos-stt-server -f Dockerfile.stt .` to build the Docker image. This will take a few minutes, as it needs to download the base image and then install all of the dependencies. Once it's done, you can run it with `docker run -d --name ovos-stt-server -p 5000:8080 ovos-stt-server`. This will run the container in the background, name it `ovos-stt-server`, and forward port 8080 from the container to port 5000 on the host. You can change the port on the host if you want to run multiple STT servers on the same machine.

**NOTE:** This particular plugin has a Dockerfile already that will run with `ovos-stt-server`. Feel free to use that instead!

### Long-term usage

Running your STT server image using `docker run` is a great way to test and start out, but if you want this to persist, you'll want to [configure a systemd service to run it](https://blog.marcnuri.com/docker-container-as-linux-system-service) or use something like Kubernetes or Hashicorp Nomad to schedule the container. Those options are out of the scope of this article, but I may write a follow-up post on them.

## Configuring Neon/OVOS

Now that you have a server running, you'll need to point your OVOS or Neon device to that server to use for STT:

OVOS:

```json
{
  "stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": "http://<SERVER_IP>:5000/stt"
    }
  }
}
```

Neon:

```yaml
stt:
  module: ovos-stt-plugin-server
  ovos-stt-plugin-server:
    url: http://<SERVER_IP>:5000/stt
```

## Conclusion

The only real downsides to this approach are that the `ovos-stt-plugin-server` does not currently allow you to use it for both your primary STT and the fallback. As of the time of this writing (June 2023), it has no way to differentiate between multiple instances of the server. (**EDIT:** No longer true! See the note at the top of this post) This means that you probably want to try to set an on-device plugin such as Vosk or Silero as your fallback. That way, you can still use your Voice Assistant in case your server is down or unreachable for some reason. Just keep in mind that an on-device STT option is usually lower quality and consumes a great deal of resources, so make sure your Voice Assistant device can handle it.

I'm hopeful that over time, OVOS and Neon will make it even easier to run your own local, private STT server. In the meantime, it's not overly complex to run your own server, and it's a great way to keep all your data private and local.

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
