# Playback — Releases & Update Feed

This repository hosts the [Sparkle](https://sparkle-project.org) auto-update
feed and the release binaries for **Playback**, the macOS screen-recording app.
It is intentionally **public**: Playback has a free tier, so unlicensed and
lapsed-license users must be able to fetch the feed and download the latest
build anonymously.

## What's here

- **`appcast.xml`** — the Sparkle feed, served directly from `main` via
  <https://raw.githubusercontent.com/kylemclaren/playback-releases/main/appcast.xml>
  (this is the `SUFeedURL` baked into every shipped build's `Info.plist`).
- **Release binaries** — each notarized `Playback-<version>.zip` is attached as
  a **GitHub Release asset** on its version tag, e.g.
  `https://github.com/kylemclaren/playback-releases/releases/download/v1.0.0/Playback-1.0.0.zip`.
  Sparkle downloads those immutable per-tag URLs (following GitHub's redirect
  to S3); integrity comes from the appcast's EdDSA signature, not the
  transport.

## How the feed is produced

The feed is **not** edited by hand. In the main (private) `playback` repo,
`scripts/appcast.sh` runs Sparkle's `generate_appcast` over a directory of
release zips, EdDSA-signs each enclosure, and stamps the machine-readable
`<playback:releaseDate>` element the app's updater uses to decide whether an
update falls inside a license's update window. The resulting `appcast.xml`
(and the new `.zip`) are published here. See `docs/design/updates.md` in the
main repo for the full design.

## Signing

Enclosures are signed with Playback's Ed25519 (EdDSA) key; the public half is
compiled into the app as `SUPublicEDKey`. The private half lives only in the
maintainer's login Keychain and is never committed here.
