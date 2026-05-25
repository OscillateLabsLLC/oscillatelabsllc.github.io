---
title: "BookTrove Privacy Policy"
date: 2026-05-24
description: "Privacy policy for the BookTrove book price tracker app by Oscillate Labs LLC."
showDate: false
showReadingTime: false
showTableOfContents: true
---

_Last updated: May 24, 2026_

BookTrove is published by **Oscillate Labs, LLC** ("we"). This policy explains
what the app does with your data. The short version: **BookTrove has no servers,
no account, and no analytics. Your data stays on your device** and is only sent
to the third-party services you choose to connect.

## What we collect

**We collect nothing.** BookTrove has no backend, no telemetry, no advertising,
and no analytics SDKs. We never see your data.

## What stays on your device

All of the following is stored locally on your device and never transmitted to us:

- Your wishlist (books from connected sources and any you add manually)
- Price history and price-drop records
- Your settings (price threshold, refresh interval, notification preferences)

Credentials you enter (Hardcover API key, Telegram bot token) are stored in your
device's secure storage — Keychain (iOS/macOS), Keystore (Android), libsecret
(Linux), or Credential Manager (Windows) — never in plain text.

## Data sent to third parties you connect

BookTrove only contacts a service when you have configured it. Each request goes
**directly from your device** to that service; it does not pass through us.

| Service | When | What is sent | Why |
|---|---|---|---|
| **AbeBooks** | On price refresh | Book titles/authors/ISBNs from your wishlist | To look up current used/new prices |
| **Goodreads** | If you add your user ID | Your public Goodreads user ID | To read your public want-to-read RSS feed |
| **Hardcover** | If you add an API key | Your Hardcover API key | To read your want-to-read list |
| **Telegram** | If you enable Telegram alerts | Your bot token, chat ID, and alert text | To deliver price-drop notifications |
| **WorldCat** | If you tap a WorldCat link | The book title/author/ISBN in the link | To open a library lookup in your browser |

**LibraryThing** import is fully local: you provide a TSV file you exported
yourself, and it is parsed on your device. Nothing is sent to LibraryThing.

Each of these services has its own privacy policy governing what it does with the
request. BookTrove is not affiliated with, or endorsed by, any of them.

## Permissions

- **Camera** (optional): used only to scan a book's barcode to fill in an ISBN.
  Images are processed on-device and never stored or transmitted. Declining the
  permission only disables barcode scanning; you can still type ISBNs.
- **Notifications** (optional): used to show price-drop and threshold alerts.
- **Background refresh / run** (optional): used to check prices on a schedule
  even when the app is closed, where the platform permits.

## Children

BookTrove is suitable for all ages and does not knowingly collect any personal
information from anyone, including children.

## Data deletion

Because all data is local, uninstalling the app removes everything. You can also
delete individual books (and their history) inside the app, and remove an
imported LibraryThing file from Settings.

## Changes

We may update this policy; the "Last updated" date above will change accordingly.

## Contact

Questions: [mike@oscillatelabs.net](mailto:mike@oscillatelabs.net)
