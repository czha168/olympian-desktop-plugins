# olympian-desktop-plugins

The official plugin catalog for [Olympian Desktop](https://github.com/czha168/Olympian-desktop).

This repository is the marketplace's distribution source (issue #76, ADR 0333
in the desktop repository): the client fetches
[`catalog.json`](catalog.json) from this repository's `main` branch, shows the
plugins it lists, and installs each package from the URL the entry names,
verifying it against the SHA-256 the entry carries. There is no plugin-center
service behind it — no account, no download-resolution endpoint, and no
per-installation identifier is sent anywhere.

## Layout

- `catalog.json` — the listing the in-app marketplace fetches.
- `packages/` — `.piplug` packages published in this repository (optional; a
  package may also live in its own plugin repository's releases).

## Catalog format

`catalog.json` is a catalog document (`schemaVersion` `1`):

- `providerId` is `official`; `artifactBaseUrl`, when set, is the prefix that
  relative package `url`s resolve against.
- Each plugin entry carries `id`, `name`, `description`, `author`, categories,
  and a `versions` array.
- Each version carries `version`, `publishedAt`, `shasum` (SHA-256 of the
  `.piplug` bytes), `url`, `sizeBytes`, `permissions`, and optionally
  `changelog` and `minOlympianDesktop`.
- `minOlympianDesktop` is a bare version meaning "requires Olympian Desktop of
  this version or newer". A leading `>=` is tolerated and means the same.

## Publishing a plugin

1. Build and pack the plugin into a `.piplug` package and record the printed
   SHA-256 (see *Plugin development* in the desktop repository's `docs/`).
2. Host the package somewhere the client can fetch: attach it to a release in
   the plugin's own GitHub repository, or add it under `packages/` here.
3. Open a pull request adding or updating the plugin's entry in
   `catalog.json` — new version, checksum, size, permissions, and, when the
   release needs a newer desktop, `minOlympianDesktop`.
4. After the pull request merges, the plugin appears in the marketplace on the
   next catalog refresh.

Packages are fetched only from hosts the client allowlists
(`github.com`, `githubusercontent.com`, `cnb.cool`, or the catalog's own host).

## History

The initial catalog entries were migrated from the PI-Desktop plugin center,
which Olympian Desktop no longer contacts. Their packages still live in the
distribution repository that center used; new publications should host
packages in this repository or in the plugin's own repository.
