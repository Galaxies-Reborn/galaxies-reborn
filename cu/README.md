# CU

Combat Upgrade era source sets.

No variant is published yet. When a CU source set exists, add its submodules
under `cu/<variant>/`, add a manifest at `channels/cu/<variant>.json`, and flip
`available` to `true` for the `cu` flavor in `channels/index.json`.

Reborn Launcher lists this flavor but offers no variants until then, and
downloads nothing for it.
