---
layout: prose
lang: en
title: "FanLog Privacy Policy"
description: "What the FanLog apps do with your data. Short answer: there is no FanLog server, nothing you record ever leaves your phone, and the only requests they make are for public files."
permalink: /fanlog/privacy/
---

# FanLog Privacy Policy

*Last updated: 15 October 2026*

Lire cette page en [français](/fanlog/privacy-fr/).

FanLog is a family of apps for following a sport on your own schedule: **FanLog MMA**, **FanLog
Rugby**, and any app published later under the FanLog name. Each one keeps a list of the fights or
matches you mean to watch, lets you mark what you have seen and rate it, and keeps every result
hidden until you say otherwise. This page covers all of them; they are built from the same code and
handle your data the same way. It is short because they do very little with it.

---

## In one sentence

**There is no FanLog server, no account and no sign-up.** Your list, what you have marked watched,
your ratings and the athletes or teams you follow are stored on your phone and are never uploaded.
The apps make two kinds of network request, both for public files, and neither carries anything
about you.

---

## What the app stores, and where

Everything is stored **locally on your device**, in files only the app can read:

| Data | Where |
|---|---|
| Fights or matches you have added to your list, marked watched, or rated | local SQLite database (`fanlog.db`) |
| Athletes or teams you follow, and when you started | same database |
| Which announcements you have already been notified about | same database |
| The catalogue itself: events, fights or matches, athletes or teams, results | same database, replaced a season at a time on each update |
| Your settings: theme, text size, language, spoiler masking, notifications, competitions you have put away | local preferences |

None of it is transmitted to the apps' author, or visible to them. There is no identifier of any
kind — no account, no device id, no advertising id — so there is nothing that could tie this device
to a person even if the data did leave it.

**This matters more here than in most apps.** What you have watched is the whole point of FanLog,
and it is also the sort of thing nobody should have to share. It is a column in a database on your
phone, and there is no code in the apps that sends it anywhere.

---

## What leaves the device, and when

Four cases, and they are the only ones.

**The catalogue.** Each app ships with a snapshot of its catalogue inside it and refreshes it from
public files on GitHub, at `raw.githubusercontent.com/michaelfery/fanlog-catalog`. The request asks
for a file and says nothing else; the app names itself in it (`FanLog` and its version), the way a
browser does. GitHub sees it the way any website sees a visitor — which means it sees the IP
address the request came from, as every web server does. Nothing about your list, your ratings or
your follows is included, because the app has no way to send them.

**Pictures.** Athletes' photographs and, in the team-sport apps, clubs' crests are loaded as you
scroll from ESPN's image CDN, at `a.espncdn.com`. They are hotlinked rather than bundled, so ESPN's
servers see those requests the same way GitHub sees the one above: a file asked for, from an IP
address. The app sends no identifier with them. What it does mean, stated plainly because most
policies leave it out: **the athletes and clubs whose pictures your phone loads are visible to ESPN
as image requests**, in the same way that any website loading images from a third party makes those
requests visible to it.

If the phone has no network, both simply fail and the app carries on with what is already on it.
The apps are designed to work offline; the network only ever refreshes the catalogue.

**Android's automatic backup.** Android may copy the app's database and settings to the backup
storage attached to your Google account, as it does for other apps on your phone. It is what gives
you back your list and your ratings when you reinstall or move to a new phone.

- **Android performs it, not the app.** The app only declares which of its files are worth backing
  up: the database and the settings. The catalogue is not among them — it is downloaded again — and
  neither is a crash report.
- **It is encrypted with your device's lock code**, on Android 9 and later. Google holds the copy
  without being able to read it, and the apps' author has no access to it at all.
- **You can turn it off**, in Android's settings under Google then Backup, either for the whole
  device or for one app alone.
- **It is deliberate.** With no server, the database on your phone *is* your logbook — everything
  you have ticked off, going back as far as you have used the app. Without this backup, a lost
  phone would take all of it with it.

**The tip.** Settings offers one optional purchase, "Support FanLog", which unlocks nothing. It goes
entirely through Google Play Billing, on your device and your Google account: Google handles the
payment under its own terms, and the app learns only that the purchase exists, which it reads back
from Play to say thank you. The author never sees your name or your means of payment, and no
receipt is sent anywhere, because there is nowhere to send it.

---

## If the app crashes

It writes a report on the phone — the app's version, the Android version, the device model and the
place in the code where it broke; nothing about you. Settings then offers to send it, through the
app of your choice, after you have read it in full. Nothing is sent unless you send it, and the
report is gone once you discard it.

---

## Permissions, and why they exist

The ones you would see if you inspected an app:

| Permission | Reason |
|---|---|
| Internet | Fetching the catalogue files and the pictures described above. |
| Notifications | Telling you that someone you follow has been booked or scheduled, or that an event you track starts soon. Deny it and the app works, without notifications. |
| Network state, wake lock, foreground service, boot completed | **Contributed by the Android background-work library**, which runs the catalogue refresh and puts reminders back after a restart. Not used for anything else. |
| Billing | Google Play's, for the optional tip above. |

Notifications are built and shown **on the device**, from data already on it. Nothing is sent
anywhere to produce them, and no push service is involved.

There is **no analytics, no advertising tracker and no crash-reporting tool** in these apps. Not
configured off — absent from the build.

---

## What FanLog does not do

- No ads, no ad network, no advertising id.
- No selling or sharing of data with third parties.
- No account, no email address requested, no login of any kind.
- No location, no contacts, no photos, no microphone.
- Nothing locked behind a payment, and no subscription.

---

## Children

The FanLog apps are not directed at children and knowingly collect no data about them: they
collect none, from anyone.

---

## Your rights

Since your data lives only on your device, you control it directly. Uninstalling an app removes
everything it holds, and clearing the app's storage in Android's settings does the same without
removing it. There is nothing to request from us, because we hold nothing.

---

## Changes

This policy will change if the apps change. The date at the top is authoritative. An app added to
the family is covered here only once this page names it.

## Contact

Write to [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
