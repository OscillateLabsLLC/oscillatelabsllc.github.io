---
title: "Improving OCP Playback in Neon/OVOS"
author: "Mike Gray"
date: 2023-06-06T09:13:28-04:00
draft: false
type: post

slug: improving-ocp-playback # Will take the filename as the slug.
description: "Configuration options and plugins to improve media playback in OVOS Common Play on Neon and OVOS systems."
thumbnail: "images/jukebox.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

One of the major improvements in [OpenVoiceOS (OVOS)](https://openvoiceos.com) and, by extension, [Neon](https://neon.ai), is OVOS Common Play (OCP). It's an extension of the Mycroft Common Play framework that was intended to be a unified way to create skills for media playback (music, podcasts, movies, TV shows, etc.). OCP is fully featured enough to be considered a media player in its own right, and is the default media player in Neon and OVOS. This does mean that it is one of the few breaking changes in OVOS/Neon, but as older Mycroft media skills are converted to OCP and new skills are created, the loss in functionality should shrink.

## Configuration

OCP, like most OVOS services, is designed to be minimalist but extensible via plugins. One of OVOS' unifying concepts is "a voice assistant for anyone anywhere," and as is the case with any extensible system, it can become complex.

[The OVOS technical manual](https://openvoiceos.github.io/ovos-technical-manual/OCP/#advanced) documentation is where you can find the different configuration options for OCP. As of this writing (June 2023), there are not spoken intents to configure these, nor is there anything in the GUI that easily allows updates. You will need to update your user configuration. In OVOS systems this is typically at `~/.config/mycroft/mycroft.conf`, and on Neon systems, it will be at `~/.config/neon/neon.yaml`.

Let's break down some of the options and talk about a base configuration that should improve your playback experience, plus some drawbacks to consider:

### `playback_mode`

OVOS will usually default to playing media through the GUI service, which enables a widget to easily switch between your homescreen and the media player, as well as audio visualization. While this does allow for a cool-looking media player experience, the default configuration for the GUI player does not allow streaming or buffering properly - the media must download fully before playback. This is a bad experience for songs and podcasts, but terrible for video.

If you notice a large gap between songs or before your first playback, you may want to try adding this configuration to force using a different media plugin:

```json
{
  "Audio": {
    "backends": {
      "OCP": {
        "playback_mode": 40
      }
    }
  }
}
```

Or in Neon:

```yaml
Audio:
  backends:
    OCP:
      playback_mode: 40
```

`playback_mode` 40 translates to `FORCE_AUDIOSERVICE`, which will disable playback through the GUI and instead use the highest priority audio plugin. That means you will not have the widget available or audio visualization, but it should improve playback.

Please also note that the playback buttons - start, stop, skip, back, etc. - _will still work_, since they use a different mechanism for control. You only lose out on the ability to switch between your homescreen and the media player, as well as the audio visualization.

### QtAV Video Player

If you want to try to keep the GUI visualizations but allow buffering, there is another option: QtAV. QtAV is a cross-platform multimedia playback library based on Qt and FFmpeg.

While QtAV allows you to buffer media, there are a few key drawbacks. First, QtAV support in OVOS is no longer maintained, so enabling it may cause issues in the future. Second, QtAV does not come installed on any supported OVOS or Neon devices by default. You must install it yourself. Finally, QtAV support is only available for OVOS using Qt5. As Qt transitions fully to v6, the integration here will break because it is not maintained. Enabling it will solve problems now but likely cause issues in the future.

Something else worth calling out: QtAV is built with software using a GPL license. If you have concerns about GPL, you may want to avoid QtAV.

If you want to try it out, you can install it with:

#### Manjaro (older images of OVOS)

[Build from source per their documentation.](https://github.com/wang-bin/QtAV/wiki/Build-QtAV)

#### Ubuntu/Debian (Neon)

```bash
sudo apt-get update
sudo apt-get -y install qtav-players
```

#### RedHat/Fedora (custom x86 installations)

[Build from source per their documentation.](https://github.com/wang-bin/QtAV/wiki/Build-QtAV)

#### Buildroot (Mark 2 OVOS)

Unfortunately, installation via buildroot will be too difficult for the scope of this blog post.

Once installed, you can enable it with the following configuration:

```json
{
  "Audio": {
    "backends": {
      "OCP": {
        "video_player_backend": "qtav"
      }
    }
  }
}
```

Or on Neon:

```yaml
Audio:
  backends:
    OCP:
      video_player_backend: qtav
```

### MPRIS (Media Player Remote Interfacing Specification)

[MPRIS](https://specifications.freedesktop.org/mpris-spec/latest/) is a standard for interacting with media playerse across a variety of applications, including KDE Connect. OCP is MPRIS-compliant and can thus be used from apps like KDE Connect to control playback. The configuration to enable this is:

```json
{
  "Audio": {
    "backends": {
      "OCP": {
        "disable_mpris": false,
        "manage_external_players": true
      }
    }
  }
}
```

```yaml
Audio:
  backends:
    OCP:
      disable_mpris: false
      manage_external_players: true
```

Please note that you must have DBus available and running on your device for this to work properly. This is the case for most OVOS and Neon devices, but if you are running on a custom device, you may need to install DBus. This installation is beyond the scope of this article; [please join us on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org) if you need help.

### Forcing audio on headless devices

If you're running OVOS on a headless device, similar to the old Mycroft Picroft implementation, it may be worthwhile to set the following configuration to force audio-only playback. It may also improve performance on a Mark 2 in either Neon or OVOS.

```json
{
  "Audio": {
    "backends": {
      "OCP": {
        "playback_mode": 30
      }
    }
  }
}
```

Or in Neon:

```yaml
Audio:
  backends:
    OCP:
      playback_mode: 30
```

From the docs:

> FORCE_AUDIO = 30 - cast video to audio unconditionally (audio can still play in mycroft-gui)

## Feedback

OCP is a feature-rich media player already and will just continue to improve. Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
