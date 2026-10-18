# Drift: privacy and safety

Drift connects two devices you own. This page explains what it can see, where that goes, and how
it was tested before release.

## The short version

- Drift has **no servers**. Your phone and your PC talk directly to each other, encrypted.
- Drift has **no accounts, analytics, ads or usage reporting**. It doesn't phone home.
- **Only devices you've paired** (by scanning a QR code and clicking Allow on the PC) can connect.
- The most powerful features (controlling the PC, browsing its files) are **off until you turn them on**.

## What Drift can see, and why

| Drift touches | Why | Where it goes |
|---|---|---|
| Files you send | To send them | Only to your other device |
| Your clipboard | Copy on one, paste on the other | Only to your other device. Copies from password managers are skipped |
| Your phone's photos | Gallery on the PC | Only to your PC, when you open Gallery there |
| Phone notifications | To show them on the PC | Only to your PC, and only from apps you choose |
| Call state (ringing / in a call / idle) | To pause PC music during calls | Only to your PC. Drift never sees who's calling |
| Your PC's screen | When you choose "See your PC's screen" | Only to your phone, never recorded. No sound is ever sent |

Drift does **not** read your contacts, call log, SMS, location, accounts or advertising ID.

## Where things are kept

- **Pairing keys:** on the phone, in Android's hardware-backed keystore. On the PC, in your own
  Windows profile (`%APPDATA%\Drift`), readable only by your account.
- **Clipboard history:** your last 200 text copies, on each device. *Clear history* on both
  Clipboard screens deletes it.
- **Logs:** on each device, for troubleshooting. They record events like "sent a file
  (photo.jpg)", never clipboard text, message contents or passwords. They leave your device only
  if you copy and send them yourself.
- **No cloud backup.** Drift's data is excluded from Android backups and phone-to-phone transfer.

## Network

- Every connection uses TLS with certificates pinned at pairing, checked on both ends.
- The PC app listens on one port (47821) and answers only paired phones. Anyone else on your
  Wi-Fi gets "not paired".
- The PC announces itself on your home network (its name and Drift's port) so your phone can find
  it. That's the only thing others on your Wi-Fi can see.
- Away from home, Drift works through Tailscale, a private network between your own devices.
  Drift doesn't add any other servers.

## How it was tested (v2.0)

Before release, both apps were checked on a real phone (Android 16), an Android emulator and a
Windows 11 PC:

- **No hidden connections.** Every web address in both apps was reviewed, and the PC app's live
  network connections were listed: its only connection was to the paired phone.
- **No tracking libraries.** The phone app was checked for Google and third-party reporting code.
  One QR-scanning library that included Google usage reporting was **replaced** with an offline
  one (ZXing), which also made the app 24 MB smaller.
- **Outsiders are refused.** Every PC endpoint was tried without pairing: all refused. Pairing
  codes are random, single-use, expire in 5 minutes and still need your click on the PC.
- **Files can't escape their folder.** Names like `..\..\evil.exe` are cleaned, and saving never
  overwrites an existing file.
- **Private PC folders are protected.** AppData and Drift's own keys can't be browsed from the
  phone, even by typing their path (this was found and **fixed** during testing).
- **Hostile input is ignored.** Links like `javascript:`, `file:` or system settings pages sent to
  the PC are refused; only normal web links open. Garbage and oversized messages never crash it.
- **Safer received files.** Files from the phone get Windows' "came from another device" mark, so
  Windows warns before running a received program.
- **Older phones.** Android's compatibility checker found four crashes that would have hit
  Android 10 and 11. All were **fixed** before release.

## Questions or a security problem?

Message [@byte.sizedesign](https://www.instagram.com/byte.sizedesign/) on Instagram, or open an
issue on this page. For a security problem, please message privately first so it can be fixed
before it's public.
