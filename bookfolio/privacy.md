---
layout: default
title: "Bookfolio Privacy Policy"
description: "What the Bookfolio app does with your data. Short answer: no Bookfolio server; your library is on your phone, plus Android's backup, Google Drive if you turn sync on, and telemetry only if you turn it on."
permalink: /bookfolio/privacy/
---

# Bookfolio Privacy Policy

*Last updated: 16 September 2026*

Lire cette page en [français](/bookfolio/privacy-fr/).

Bookfolio is a reading tracker. This page describes what it does with your
data. It is longer than we would like, because the app talks to Google in
several ways, and each one deserves a plain sentence.

---

## In one sentence

**There is no Bookfolio server and no Bookfolio account.** Your library lives
on your phone. Copies of it leave the phone in three cases: Android's own
backup, which is on by default; Google Drive sync, if you turn it on; and
telemetry, if you turn it on. Searching for a book sends your search terms to
Google Books, or to Open Library when Google Books has nothing.

---

## What the app stores, and where

Everything is stored **locally on your device**, in files that only Bookfolio
can read:

| Data | Where |
|---|---|
| The books you add: title, authors, publisher, year, ISBN, categories, summary, cover address | local database (`bookfolio.db`) |
| Your reading: status (To Read, Reading, Read, DNF), start and finish dates, page progress, rating, notes, wishlist, owned | same database |
| Your collections | same database |
| Your activity log | same database |
| Your recent searches | same database |
| Your settings: theme, accent colour, reminders, yearly goal, telemetry choices | local preferences (`bookfolio_prefs.xml`) |
| The Google authorization token, if you connected Google | an encrypted store, separate from the files above |
| Cover images, once downloaded | the app's image cache |

None of this is transmitted to the author of the app, nor readable by him.

---

## What leaves the device, and when

Six cases. The first is not triggered by you, which is why it comes first.

**Android's automatic backup.** Android copies `bookfolio.db` and
`bookfolio_prefs.xml` to the backup space attached to your Google account, as
it does for the other apps on your phone, and it carries them across when you
transfer to a new device. That is what gives you your library back when you
reinstall the app or change phones.

- **Android does it, not Bookfolio.** The app only declares which files are
  worth backing up. The Google authorization token is not among them.
- **It is encrypted with your device lock code**, since Android 9. Google
  holds the copy without being able to read it, and the author of Bookfolio
  has no access to it.
- **It is on by default, and you can turn it off**, in Android's settings,
  under Google then Backup, for the whole device or for Bookfolio alone.
  Settings › Sync & data › "Back up now" only asks Android to run the backup
  it would run anyway.

**Searching for a book.** When you type a title, an author or an ISBN, or scan
a barcode, the app sends the search terms to the **Google Books API**, and to
**Open Library** when Google Books returns nothing. Requests to Google Books
carry the app's package name and signing certificate, which is how Google
restricts the API key to this app; they carry nothing about you. Cover images
are then downloaded from Google Books or Open Library. Barcode recognition
happens on the device: no image or video leaves it.

**Google Drive sync.** If you connect Google, the app asks for exactly one
authorization: `drive.appdata`, the private folder that Google Drive keeps for
each app. That authorization gives Bookfolio no access to the rest of your
Drive, and no identity data: not your name, not your e-mail address, not your
profile picture. Two things are written to that folder:

- a complete archive of your library and settings (`bookfolio.db` and
  `bookfolio_prefs.xml`), so that a new device can restore it;
- a per-book sync file: ISBN, status, dates, progress, rating, wishlist,
  owned, notes, plus title, authors and cover address so that another device
  can recognise the book. It is merged in both directions between your
  devices.

The token that authorizes this is stored encrypted on the device.
Settings › "Disconnect Google account" deletes that token from this device; it
does not delete what is already in your Drive. To delete that, open Google
Drive's settings, Manage apps, and delete Bookfolio's hidden app data.

Bookfolio no longer writes to your Google Books shelves. That synchronization
was retired in April 2026.

**Telemetry, if you turn it on.** Bookfolio includes two Google libraries,
**Firebase Analytics** (which screens are opened, which features are used,
which version runs) and **Firebase Crashlytics** (crash diagnostics). Both are
**off by default**: the app ships with collection disabled, and nothing is
sent until you switch them on, during onboarding or in Settings › Sync & data.
You can switch them off at any time. The events the app sends contain no
personal data and nothing about your books. What Firebase does with them, and
for how long it keeps them, is governed by Google's Firebase privacy policy.
The advertising identifier is removed from the app entirely, whatever you
choose.

**A tip or the supporter badge.** Payment is handled by Google Play, not by
Bookfolio. You give your payment details to Google; the app never sees them.
Bookfolio receives only the confirmation that the purchase went through, and
reads your supporter status back from Google Play. What Google collects on
that occasion is covered by Google's own privacy policy.

**A review on the Play Store.** After a while, the app may offer once to rate
it. The exchange happens between your device and Google Play.

---

## Permissions, and why they exist

The ones you would see if you inspected the app:

| Permission | Reason |
|---|---|
| Camera | Scanning barcodes, only while the scanner is open. Optional: typing the ISBN finds the same book, and a device without a camera can install the app. |
| Notifications | Reading reminders: the book in progress, the wishlist, a weekly summary. Local, optional, and off unless you turn them on. |
| Internet, network state | Book search, cover images, Google Drive sync. |
| Vibration | A short buzz when a barcode is read. |
| Google Play billing | Tips and the supporter badge. |

The advertising identifier permission that Firebase would normally add is
explicitly removed from the app.

---

## What Bookfolio does not do

- No ads, no ad network, no advertising identifier.
- No selling or sharing of data with third parties.
- No Bookfolio account, no e-mail address asked for.
- No location, no contacts, no access to your photos; the camera shows only
  a live preview and records nothing.
- No telemetry unless you turn it on.

---

## Children

Bookfolio is not directed at children under 13 and does not knowingly collect
data from them.

---

## Your rights

Your data is on your device and, if you chose so, in your own Google account.
You control it directly:

- **Access and correction**: everything is visible and editable in the app.
- **Deletion on the device**: uninstalling the app deletes the database, the
  preferences and the cache. If Android's backup is on, an earlier copy may
  survive until the next backup replaces it.
- **Deletion in Google Drive**: from Drive's settings, Manage apps, delete
  Bookfolio's hidden app data.
- **Telemetry**: switch it off in Settings; requests about data already sent
  to Firebase go through Google.

There is nothing to ask us for, because we hold nothing.

---

## Changes

This policy changes when the app changes. The date at the top is
authoritative; the full history is in the project's repository.

## Contact

Write to [mferyapps@gmail.com](mailto:mferyapps@gmail.com).
