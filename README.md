# EclipseX Manifest Collection

This directory contains the layered manifest set for building the EclipseX ROM. It is designed to sit inside `.repo/local_manifests/` so you can keep using the official AOSP manifest as the base while selectively pulling in GrapheneOS, EvolutionX, CopperheadOS, and Docker components.

## Usage
1. Initialize AOSP Android 17 (or appropriate release) as usual:
   ```bash
   repo init -u https://android.googlesource.com/platform/manifest -b master
   ```
2. Clone this manifest set into your checkout:
   ```bash
   git clone https://github.com/EclipseXROM/manifest .repo/local_manifests
   ```
3. (Optional) Remove manifests you do not yet need.
4. Sync:
   ```bash
   repo sync -c --no-tags
   ```

`default.xml` automatically includes the topical manifests:
- `graphene.xml` – privacy/GApps installer apps and reference frameworks from GrapheneOS
- `evox.xml` – customization overlays and launcher pieces from EvolutionX
- `security.xml` – CopperheadOS security patch references
- `extras.xml` – Docker/Moby sources and future EclipseX components (launcher, theme packs, etc.)

Update branch names in each file to match the upstream revision you intend to track (Android 17 once available). By default they point to `main`/`master` to guarantee the clone works even before retagging to the target release.

## Adding Your Own Forks
As EclipseX components mature, create forks under `github.com/EclipseXROM/` and reference them via the `eclipsex` remote in `default.xml`. Replace the upstream `project` entries with your forks once patches are merged.

## Licensing
Every manifest entry must point to a repository with a permissive (Apache-2.0/MIT/BSD) or otherwise compatible license. Keep `NOTICE` updated whenever you add or remove components.
