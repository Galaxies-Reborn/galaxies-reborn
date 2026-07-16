# Galaxies Reborn

Umbrella repository for Galaxies Reborn source sets. Each era ("flavor") owns a
top-level folder, and each variant within it pins an exact set of source
repositories as Git submodules. A variant can then be reproduced with a single
recursive clone.

Cloning this repository transfers only manifests and submodule pointers, a few
kilobytes. No source is fetched until a specific variant is initialized, so
selecting NGE never downloads CU or Pre-CU content.

## Flavors

| Flavor | Folder | Status | Description |
| --- | --- | --- | --- |
| SWG Source | `swg-source/` | Available | Unmodified upstream SWGSource. The baseline the others fork from. |
| NGE | `nge/` | Available | New Game Enhancements era, on the Galaxies Reborn forks. |
| CU | `cu/` | Not published | Combat Upgrade era. |
| Pre-CU | `pre-cu/` | Not published | Pre-Combat Upgrade era. |

Each flavor carries its own sources and tools; they are not interchangeable.

## Layout

```
channels/
  index.json                     registry of flavors and their variants
  <flavor>/<variant>.json        manifest for one variant
<flavor>/
  <variant>/
    swg-main/                    server source (recursive: dsrc, src, serverdata, stationapi, exe)
    client-assets/               client asset source
    client-tools/                client build tools (optional)
```

## Variants

| Flavor | Variant | Branch | Description |
| --- | --- | --- | --- |
| SWG Source | `base` | `master` | Unmodified upstream SWGSource server and client sources, pinned to a known-good revision. |
| NGE | `x64-dx9-vanilla` | `x64-dx9-vanilla` | Baseline 64-bit DirectX 9 regular and God clients with matching server revisions. |

The NGE variant is the SWG Source baseline with Galaxies Reborn forks substituted
for `src` and `stationapi`; every other pinned revision is currently identical
between the two.

## Cloning a Variant

Clone the umbrella, then initialize only the variant you want:

```bash
git clone https://github.com/Galaxies-Reborn/galaxies-reborn.git
cd galaxies-reborn
git submodule update --init --recursive nge/x64-dx9-vanilla/swg-main
git submodule update --init nge/x64-dx9-vanilla/client-assets
```

`client-tools` is only needed to build the client from source and is left
uninitialized by default. Initializing the two required submodules above
transfers roughly 250 MB; adding `client-tools` raises that to roughly 620 MB.

## Submodule Wiring

Each variant's submodules track the branch of the same name. `swg-main` carries
its own `.gitmodules` and, on the `x64-dx9-vanilla` branch, resolves `stationapi`
and `src` to the Galaxies Reborn forks, while `dsrc`, `serverdata`, and `exe`
(`configs`) resolve to SWG-Source upstream. Recursive initialization of
`swg-main` is therefore required; those nested revisions are pinned by
`swg-main` itself, not by this repository.

## Adding a Variant

1. Create the branch of the same name in each participating source repository.
2. Add the submodules under `<flavor>/<variant>/`, tracking that branch:

   ```bash
   git submodule add -b <variant> https://github.com/Galaxies-Reborn/swg-main.git <flavor>/<variant>/swg-main
   ```

3. Copy an existing manifest into `channels/<flavor>/<variant>.json` and update
   every path and client entry.
4. Register the variant under its flavor in `channels/index.json`, and set the
   flavor's `available` to `true` if this is its first variant.

Advancing a variant is a submodule pointer update:

```bash
git submodule update --remote nge/x64-dx9-vanilla/swg-main
git commit -am "Advance NGE x64-dx9-vanilla swg-main"
```

## Client Payloads

Only torrent metadata is committed. Client payloads are distributed out of band
and seeded separately; see the `clients` block of each variant manifest for the
info hash, payload size, and executable paths.
