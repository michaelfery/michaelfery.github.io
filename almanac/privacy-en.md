---
layout: default
title: "Almanac Privacy Policy"
description: "What the Almanac app does with your data. Short answer: there is no Almanac server, and the only copy off your phone is Android's encrypted backup."
permalink: /almanac/privacy-en/
---

# Almanac Privacy Policy

*Last updated: 30 August 2026*

Lire cette page en [français](/almanac/privacy/).

Almanac is a home-maintenance app. This page describes what it does with your
data. It is short because the app does very little with it.

---

## In one sentence

**There is no Almanac server, no account, no sign-up, and the app's own code
makes no network calls.** Your record stays on your phone, with one exception:
Android's automatic backup keeps an encrypted copy in your Google account so
that you still have it if you change devices. It is described below, and you can
turn it off.

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

None of it is transmitted to the app's author, or visible to them. The only
copy that exists elsewhere is the Android backup described below.

**Worth knowing before you need it:** "Erase everything" removes this data from
the device at once, with no undo. If the Android backup is on, an earlier copy
may survive until the next backup replaces it. The PDF export is still the only
way to keep your record in a form you control, readable without Almanac.

---

## What leaves the device, and when

Four cases. Three are started by you; the first is not, which is why it comes
first.

**Android's automatic backup.** Android copies Almanac's database and your
settings to the backup storage attached to your Google account, as it does for
the other apps on your phone. It is what gives you back your equipment and your
record when you reinstall the app or move to a new phone.

What is worth knowing about it, precisely:

- **Android performs it, not Almanac.** The app takes no part in it and still
  makes no network calls; it only declares which files are worth backing up.
- **It is encrypted with your device's lock code**, on Android 9 and later.
  Google holds the copy without being able to read it, and Almanac's author has
  no access to it at all.
- **It is on by default, and you can turn it off.** In Android's settings, under
  Google then Backup, either for the whole device or for Almanac alone.
- **It is a deliberate choice.** Almanac has no server, so the database on your
  phone *is* your record, including the work you entered for jobs done before
  you had the app. Without this backup, a lost phone would take with it the
  document you would show a buyer or an insurer. We would rather give you an
  encrypted copy you can refuse than a loss you could not undo.

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

---

## Children

Almanac is not directed at children and knowingly collects no data about them: it
collects none, from anyone.

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
