---
layout: default
title: "Privacy Policy — Almanac"
description: "What the Almanac app does with your data. Short answer — it never leaves your phone."
permalink: /almanac/privacy-en/
---

# Privacy Policy — Almanac

*Last updated: 15 August 2026*

Lire cette page en [français](/almanac/privacy/).

Almanac is a home-maintenance app. This page describes what it does with your
data. It is short because the app does very little with it.

---

## In one sentence

**Your maintenance record never leaves your phone.** There is no Almanac server,
no account, no sign-up, and the app's own code makes no network calls.

---

## What the app stores, and where

Everything is stored **locally on your device**, in two files only Almanac can
read:

| Data | Where |
|---|---|
| The equipment you add | local SQLite database (`almanac.db`) |
| Your record of work: dates, notes, costs, companies | same database |
| Dates you find (last service, roof age…) | same database |
| Your settings: theme, season, reminders, display | local preferences |
| How many donations you have made | local preferences, kept separate from the above |

None of it is transmitted, backed up online, or visible to the app's author.

**Worth knowing before you need it:** if you uninstall Almanac, or use "Erase
everything", this data is gone for good. There is no copy anywhere to restore
from. The PDF export is the only way to keep your record outside the app.

---

## What leaves the device, and when

Three cases, all started by you.

**A donation.** Payment is handled by Google Play, not by Almanac. You give your
payment details to Google; the app never sees them and stores nothing about
them. Almanac receives only the confirmation that a purchase succeeded, and
keeps only a count. What Google collects in the process is covered by
[Google's privacy policy](https://policies.google.com/privacy).

**A Play Store review.** If you use "Rate the app", the exchange is between your
device and Google Play.

**A PDF export.** The file is created on your device, and you then choose what to
do with it through Android's share sheet. Almanac does not send it anywhere and
does not know where you send it.

---

## Permissions, and why they exist

Honesty requires listing the ones you would see if you inspected the app:

| Permission | Reason |
|---|---|
| Notifications | Due-date reminders. Deny it and the app works, without reminders. |
| Boot completed | Rescheduling your reminders after a restart, which would otherwise lose them. |
| Internet, network state | **Contributed by the Google Play libraries** (donations, reviews), not by Almanac's own code. |
| Foreground service, wake lock | Used by reminder scheduling. |

The Google Play libraries bundled in the app may send their own diagnostics to
Google. Almanac does not control that behaviour and adds nothing to it: there is
**no analytics, no advertising tracker and no crash-reporting tool** in this app.

---

## What Almanac does not do

- No ads, no ad network, no advertising ID.
- No selling or sharing of data with third parties.
- No account, no email address requested.
- No location, no contacts, no photos, no microphone.
- Nothing in the app is reserved for people who have donated.

---

## Children

Almanac is not directed at children and knowingly collects no data about them —
collecting none, from anyone.

---

## Your rights

Since your data lives only on your device, you control it directly: "Profile →
Erase everything" removes all of it, and uninstalling the app does the same.
There is nothing to request from us, because we hold nothing.

---

## Changes

This policy will change if the app changes. The date at the top is
authoritative; the full history is public in the project's repository.

## Contact

Write to [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
