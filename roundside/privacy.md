---
layout: default
title: "Roundside Privacy Policy"
description: "What the Roundside app does with your data. Short answer: there is no Roundside server, nothing you record ever leaves your phone, and the only two requests it makes are for public files."
permalink: /roundside/privacy/
---

# Roundside Privacy Policy

*Last updated: 4 September 2026*

Roundside is a fight tracker: you keep a list of fights to watch, mark what you
have seen, rate it, and results stay hidden until you say otherwise. This page
describes what the app does with your data. It is short because the app does
very little with it.

---

## In one sentence

**There is no Roundside server, no account and no sign-up.** Your watchlist,
what you have marked watched, your ratings and the fighters you follow are
stored on your phone and are never uploaded. The app makes exactly two kinds of
network request, both for public files, and neither carries anything about you.

---

## What the app stores, and where

Everything is stored **locally on your device**, in files only Roundside can
read:

| Data | Where |
|---|---|
| Fights you have added to your list, marked watched, or rated | local SQLite database (`roundside.db`) |
| Fighters you follow, and when you started | same database |
| Which announcements you have already been notified about | same database |
| The fight catalogue itself: cards, bouts, fighters, results | same database, replaced wholesale on each update |
| Your settings: theme, spoiler masking, notifications, promotions you have put away | local preferences |

None of it is transmitted to the app's author, or visible to them. There is no
identifier of any kind — no account, no device id, no advertising id — so there
is nothing that could tie this device to a person even if the data did leave it.

**This matters more here than in most apps.** What you have watched is the whole
point of Roundside, and it is also the sort of thing nobody should have to
share. It is a column in a database on your phone, and there is no code in the
app that sends it anywhere.

---

## What leaves the device, and when

Three cases. Two are ordinary web requests the app makes on its own; the third
is Android's, not the app's.

**The fight catalogue.** Roundside ships with a snapshot of the catalogue inside
the app and refreshes it in the background from a public file on GitHub, at
`raw.githubusercontent.com/michaelfery/roundside-catalog`. The request asks for
a file and says nothing else. GitHub sees it the way any website sees a
visitor — which means it sees the IP address the request came from, as every web
server does. Nothing about your list, your ratings or your follows is included,
because the app has no way to send them.

**Fighter photographs.** Portraits are loaded as you scroll from ESPN's image
CDN, at `a.espncdn.com`. These are hotlinked rather than bundled, so ESPN's
servers see those requests the same way GitHub sees the one above: a file asked
for, from an IP address. The app sends no identifier with them. What it does
mean, stated plainly because most policies leave it out: **the fighters whose
pictures your phone loads are visible to ESPN as image requests**, in the same
way that any website loading images from a third party makes those requests
visible to it.

If the phone has no network, both simply fail and the app carries on with what
is already on it. Roundside is designed to work offline; the network only ever
refreshes the catalogue.

**Android's automatic backup.** Android may copy Roundside's database and
settings to the backup storage attached to your Google account, as it does for
other apps on your phone. It is what gives you back your list and your ratings
when you reinstall or move to a new phone.

- **Android performs it, not Roundside.** The app takes no part in it; it only
  declares that its files are worth backing up.
- **It is encrypted with your device's lock code**, on Android 9 and later.
  Google holds the copy without being able to read it, and the app's author has
  no access to it at all.
- **You can turn it off**, in Android's settings under Google then Backup,
  either for the whole device or for Roundside alone.
- **It is deliberate.** With no server, the database on your phone *is* your
  logbook — every fight you have ticked off, going back as far as you have used
  the app. Without this backup, a lost phone would take all of it with it.

---

## Permissions, and why they exist

The ones you would see if you inspected the app:

| Permission | Reason |
|---|---|
| Internet | Fetching the catalogue file and the fighter photographs described above. |
| Notifications | Telling you a fighter you follow has been booked, or that a card you track starts soon. Deny it and the app works, without notifications. |
| Network state, wake lock, foreground service, boot completed | **Contributed by the Android background-work library**, which runs the catalogue refresh and reschedules it after a restart. Not used for anything else. |

Notifications are built and shown **on the device**, from data already on it.
Nothing is sent anywhere to produce them, and no push service is involved.

There is **no analytics, no advertising tracker and no crash-reporting tool** in
this app. Not configured off — absent from the build.

---

## What Roundside does not do

- No ads, no ad network, no advertising id.
- No selling or sharing of data with third parties.
- No account, no email address requested, no login of any kind.
- No location, no contacts, no photos, no microphone.
- No purchases, and no payment code in the app.

---

## Children

Roundside is not directed at children and knowingly collects no data about
them: it collects none, from anyone.

---

## Your rights

Since your data lives only on your device, you control it directly. Uninstalling
the app removes everything it holds, and clearing the app's storage in Android's
settings does the same without removing the app. There is nothing to request
from us, because we hold nothing.

---

## Changes

This policy will change if the app changes. The date at the top is
authoritative.

## Contact

Write to [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
