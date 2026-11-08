---
title: "Private Personal Voice Assistants"
author: "Mike Gray"
date: 2023-06-02T16:32:21-05:00
draft: false
type: post

slug: private-personal-voice-assistants
description: "An introduction to some private, personal voice assistants in 2023."
thumbnail: "images/star_trek_computer.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

I've posted a few times about configuring and customizing [OpenVoiceOS (OVOS)](https://openvoiceos.com) and [Neon](https://neon.ai), but I haven't yet written about what they are. OVOS and Neon are the spiritual and code successors to [Mycroft](https://mycroft.ai), a personal, private voice assistant that gained some traction in 2016 with a successful Kickstarter campaign. That campaign was for [their Mycroft Mark 1 device](https://mycroft.ai/product/mycroft-mark-1/), which was built around a Raspberry Pi 3 and designed as an alternative to Alexa, Google Home, and other voice assistants that are not designed with privacy in mind. Mycroft is open source, and the Mark 1 was designed to be a physical embodiment of the Mycroft software. The Mark 1 and the original Mycroft software spawned a community of developers and enthusiasts that have continued to develop the software and build new hardware.

In early 2023, [Mycroft announced that they were going bankrupt](https://mycroft.ai/blog/update-from-the-ceo-part-1/). A combination of patent trolls, supply chain issues due to the global pandemic, and some bad luck led to its demise. The community, however, was not ready to let Mycroft die.

A group of developers forked the Mycroft codebase and created [OVOS](https://openvoiceos.com) to work out bug fixes and new features while Mycroft focused on its Mark 2 device. After the announcement of Mycroft's bankruptcy, OVOS decided to formally become a non-profit foundation and carry the torch for open source, private voice assistant software.

[Neon](https://neon.ai), which is built on top of OVOS, is a separate company and was a channel partner of Mycroft. They have assumed responsibility for Mycroft's community forum, support for Mark 2 customers and devices, and focused their software efforts to support the Mark 2. They also have other products for use cases such as health care (where privacy is an absolute must) and education.

This is all my own summary as I understand the situation. For official word from the OVOS crew, [please see their post on the Mycroft Community Forums](https://community.mycroft.ai/t/faq-ovos-neon-and-the-future-of-the-mycroft-voice-assistant/13496). I am just an enthusiastic community member, sometimes contributor, and user of their software, which I fully intend to make the backbone of my own plans for a private, personal voice assistant at home. I will note that a few things in the post above are already out-of-date - for example, the Manjaro version of OVOS is considered deprecated, despite it being the original recommendation for OVOS skill development.

## My plans (subject to change)

Right now, since OVOS and Neon are both in a state of growth and stabilization, much of my efforts involve migrating my existing Mycroft Mark 2 and [Picroft](https://mycroft-ai.gitbook.io/docs/using-mycroft-ai/get-mycroft/picroft) devices to some combination of OVOS and Neon. For the Mark 2, Neon is my OS of choice, and for the Picrofts I've opted to go with OVOS. Neon and OVOS don't quite have feature parity with the Picroft devices I've been using for roughly a year, so I've been contributing to both projects to help get them to a point where I can migrate my devices. That includes:

- Maintaining the [Neon Home Assistant skill](https://github.com/mikejgray/neon-homeassistant-skill) and now [PHAL plugin](https://github.com/OpenVoiceOS/ovos-PHAL-plugin-homeassistant) that does the heavy lifting in the background. These will also become the foundation for OVOS' CommonIOT framework.
- Assisting community members with issues with their skills, such as contributing back to the Hubitat skill.
- Working on a [Plex skill fork](https://github.com/mikejgray/skill-plex) for my local music and media library, based on the original work from Neon's lead developer, Daniel McKnight.
- Contributing to both OVOS and Neon's developer and community documentation.
- Maintaining and modernizing older skills, such as the [OVOS Easter Egg skill](https://github.com/OpenVoiceOS/ovos-skill-easter-eggs), to improve my own skills as an OVOS/Neon skill developer and to help others learn from my work.
- Doing alpha and beta testing for OVOS/Neon OS packages and skills, as well as experimental builds for OVOS, such as a full Docker implementation and Mac OS implementation of the VA software.

I have one of my two Picrofts migrated to OVOS and my Mark 2 devices running Neon now. I plan to acquire another Mark 2 and retire the second Picroft and eventually try to find and purchase a pair of Mark 1 devices for my children.

Moving forward, I have a few items I'd like to build out:

- Community-facing Speech-to-Text and Text-to-Speech servers to contribute to the OVOS pool.
- Additional media skills for streaming services, which can be challenging because many streaming services are not friendly to voice assistants.
- Integration with my own private local Large Language Models (LLMs) running on my own hardware.
- Networking my VAs as an intercom system, to allow communication throughout my home from any room.

And beyond that, I have bigger picture ideas that I'd like to explore:

- Using my VAs as a system for conversing with background tasks performed by software agents in my homelab.
- Creating skills for my children to use to interact with their own private, personal voice assistants.
- Building an LCARS GUI for the VAs with screens, because I'm a gigantic Star Trek nerd.
- Building a Mark 2 GUI that emulates the Mark 1 face, because it's cute and I'd love to have that option. Plus it would earn points with my wife, which is always helpful for buy-in to all my mad science experiments!
- Skills to interact with my local library of ebooks, audiobooks, podcasts, Wikipedia, Stack Overflow, and other knowledge sources. This may or may not end up integrating into the LLM work I mentioned above.

I'm sure I'll have more ideas as I go, but this is more than enough to keep me busy for a while. I'm excited to see where this all goes, and I'm happy to be a part of the OVOS and Neon communities. If you're interested in learning more, please check out the links above, and do feel free to reach out. I'm happy to help where I can, and I'm always looking for new ideas and new people to collaborate with.

This is a lot of work to largely replicate what is available off the shelf from Amazon, Google, and other large tech companies. However, I consider it worthwhile, because I don't trust those companies to be good stewards of my personal information. I'd like my voice assistants to serve me and my family, not those companies, and I enjoy this work enough to make it worthwile.
