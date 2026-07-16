# Galaxies Reborn

Umbrella repository for Star Wars Galaxies server source sets. Each project owns
a top-level folder and pins its sources as Git submodules, so a variant can be
reproduced with a single recursive clone.

Cloning this repository transfers only manifests and submodule pointers, a few
kilobytes. No source is fetched until a specific variant is initialized, so
choosing one project never downloads another's content.

## Projects

| Project | Folder | Description |
| --- | --- | --- |
| Galaxies Reborn | `galaxies-reborn/` | Galaxies Reborn source sets, grouped by era. |
| SWGEmu | `swgemu/` | [SWGEmu](https://www.swgemu.com/) Core3, an independent Pre-CU emulator with its own container stack. |
| SWG Source | `swg-source/` | Unmodified upstream [SWGSource](https://github.com/SWG-Source). The baseline Galaxies Reborn forks from. |

Galaxies Reborn groups its variants under era flavors. SWGEmu and SWG Source
offer their variants directly, so their folders are one level shallower. A
project uses one shape or the other, never both.

| Project | Flavor | Variant | Branch | Status |
| --- | --- | --- | --- | --- |
| Galaxies Reborn | NGE | `x64-dx9-vanilla` | `x64-dx9-vanilla` | Available |
| Galaxies Reborn | CU | — | — | Not published |
| Galaxies Reborn | Pre-CU | — | — | Not published |
| SWGEmu | — | `core3` | `unstable` | Available |
| SWG Source | — | `base` | `master` | Available |

## Layout

```
channels/
  index.json                       registry of projects, flavors, and variants
  <project>/[<flavor>/]<variant>.json
<project>/[<flavor>/]<variant>/
  <submodule>/
```

## Cloning a Variant

Clone the umbrella, then initialize only the variant you want:

```bash
git clone https://github.com/Galaxies-Reborn/galaxies-reborn.git
cd galaxies-reborn
git -c core.longpaths=true submodule update --init --recursive galaxies-reborn/nge/x64-dx9-vanilla/swg-main
git -c core.longpaths=true submodule update --init galaxies-reborn/nge/x64-dx9-vanilla/client-assets
```

`core.longpaths=true` is required on Windows. Nested submodule object paths
exceed the 260-character limit, and without it Git fails to clone them **while
still exiting zero** — leaving an empty tree that looks like success.

`client-tools` is only needed to build a client from source and is left
uninitialized by default.

| Variant | Required | With optional |
| --- | --- | --- |
| `galaxies-reborn/nge/x64-dx9-vanilla` | ~216 MB | ~582 MB |
| `swgemu/core3` | ~503 MB | ~503 MB |
| `swg-source/base` | ~216 MB | ~562 MB |

## Pipelines

Source lineages do not share a build. Each variant manifest declares the
pipeline that can drive it:

| Pipeline | Used by | Stack |
| --- | --- | --- |
| `swgSource` | Galaxies Reborn, SWG Source | swg-main with Oracle and the Compose stack. |
| `core3` | SWGEmu | Core3's own Dockerfile, MySQL, and CMake build. |

## Submodule Wiring

Variant submodules track the branch of the same name. `swg-main` carries its own
`.gitmodules`; on `x64-dx9-vanilla` it resolves `stationapi` and `src` to the
Galaxies Reborn forks, while `dsrc`, `serverdata`, and `exe` (`configs`) resolve
to SWG-Source upstream. Those nested revisions are pinned by `swg-main` itself,
not by this repository, so recursive initialization is required.

The NGE variant is the SWG Source baseline with Galaxies Reborn forks
substituted for `src` and `stationapi`; every other pinned revision, including
`client-assets`, is currently identical between the two.

## Client Payloads

Only torrent metadata is committed, and only where a payload exists. A variant
that ships no prebuilt client declares `"clients": { "published": false }`
rather than carrying placeholder paths. SWGEmu additionally requires retail
`.tre` files that the operator must supply; see [swgemu/README.md](swgemu/README.md).

## Adding a Variant

1. Create the branch of the same name in each participating source repository.
2. Add the submodules under `<project>/[<flavor>/]<variant>/`, tracking that branch.
3. Copy an existing manifest to `channels/<project>/[<flavor>/]<variant>.json`.
   `variantPath` must equal the project, flavor, and id joined by `/`; the
   launcher rejects a manifest that disagrees.
4. Register it in `channels/index.json`, setting `available` where appropriate.

Advancing a variant is a submodule pointer update:

```bash
git submodule update --remote galaxies-reborn/nge/x64-dx9-vanilla/swg-main
git commit -am "Advance NGE x64-dx9-vanilla swg-main"
```
