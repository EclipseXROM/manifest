# EclipseX Manifest Collection

This directory contains the layered manifest set for building the EclipseX ROM. It is designed to sit inside `.repo/local_manifests/` so you can keep using the official AOSP manifest as the base while selectively pulling in GrapheneOS, EvolutionX, CopperheadOS, and Docker components.

## Usage
for using only our manifest
```bash
repo init -u https://github.com/EclipseXROM/manifest.git 
```
for manual patching:
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
- `debloat.xml` – strips Google/bloat projects from the synced tree (see below)
- `graphene.xml` – privacy/GApps installer apps and reference frameworks from GrapheneOS
- `evox.xml` – customization overlays and launcher pieces from EvolutionX
- `security.xml` – CopperheadOS security patch references
- `extras.xml` – Docker/Moby sources and future EclipseX components (launcher, theme packs, etc.)

## EclipseX common (merged Manikineko userland)

`custom.xml` syncs [`common`](https://github.com/EclipseXROM/common) to
`vendor/eclipsex/common`. It carries the merged Manikineko userland: a
plain-Java first-party app suite (browser, calendar, files, music, notes,
recorder, radio, level, torch), the EclipseX Home launcher with a built-in
OOBE setup wizard, a signed-catalog app store (+ backend), branding
assets, and the debloat tooling. Wire it into a device with:

```make
$(call inherit-product, vendor/eclipsex/common/eclipsex.mk)
```

All org device trees (bluejay, angler, starlte, crownlte, gracerlte,
uefix64pc) already carry this hook in their product makefiles.

## Debloat / no-Google policy

`debloat.xml` removes ~145 source projects via `<remove-project>`: Google
search/ads surfaces (QuickSearchBox, AdServices, OnDevicePersonalization),
stock apps the suite replaces (Browser2, Calendar, Gallery2, Music,
Launcher3, ...), dev/demo leftovers, TV/automotive form factors, and the
multi-GB sync heaps — `kernel/prebuilts/*`, the emulator/VM stack
(cuttlefish, qemu-kernel, aemu, gfxstream), CTS/VTS/Tradefed test infra,
VNDK snapshots, and `prebuilts/sdk`+`ndk`. Roughly 10+ GB saved per sync.

Compatibility rule: anything that is an API surface stays — WebView, SAF,
providers, telephony, all mainline modules except the ads pair. Apps keep
working; we remove apps and telemetry, not the platform. A second,
product-level strip (`config/debloat.mk`) and an on-device runtime tool
(`debloat/debloat.sh`) ship inside `common`.

Update branch names in each file to match the upstream revision you intend to track (Android 17 once available). By default they point to `main`/`master` to guarantee the clone works even before retagging to the target release.

## Adding Your Own Forks
As EclipseX components mature, create forks under `github.com/EclipseXROM/` and reference them via the `eclipsex` remote in `default.xml`. Replace the upstream `project` entries with your forks once patches are merged.

## Licensing
Every manifest entry must point to a repository with a permissive (Apache-2.0/MIT/BSD) or otherwise compatible license. Keep `NOTICE` updated whenever you add or remove components.
