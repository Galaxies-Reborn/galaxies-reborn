# SWGEmu

[SWGEmu](https://www.swgemu.com/) Core3 — an independent Pre-CU emulator, reverse
engineered from scratch. It shares no code with SWGSource.

| Variant | Submodule | Branch |
| --- | --- | --- |
| `core3` | [Core3](https://github.com/swgemu/Core3) | `unstable` |

[engine3](https://github.com/swgemu/engine3) is not a separate entry here: Core3
declares it in its own `.gitmodules` at `MMOCoreORB/utils/engine3`, so it arrives
with a recursive initialization. Listing it twice would clone it twice.

`unstable` is SWGEmu's primary development branch, not a pre-release one. The
submodule is pinned to an exact revision regardless, so installs stay
reproducible.

## Separate Build Pipeline

Core3 does not use the swg-main Compose stack. It carries its own `docker/`
directory with a Dockerfile, builds with CMake, and uses MySQL rather than
Oracle. Reborn Launcher drives it through a dedicated pipeline
(`"pipeline": "core3"` in the variant manifest).

## Client Files Are Not Included

Core3 requires `.tre` files from a retail Star Wars Galaxies client, which are
not distributed here and cannot be. The operator must supply them; the container
expects them mounted at `/tre`.

No client payload is published for this variant, so `channels/swgemu/core3.json`
declares `"clients": { "published": false }` and the launcher offers no client
download for it.
