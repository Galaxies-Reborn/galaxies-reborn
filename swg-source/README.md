# SWG Source

Unmodified upstream [SWGSource](https://github.com/SWG-Source) sources. This is the
baseline the other flavors fork from, offered as an install option in its own
right.

| Variant | Submodules | Branch |
| --- | --- | --- |
| `base` | [swg-main](https://github.com/SWG-Source/swg-main), [client-assets](https://github.com/SWG-Source/client-assets), [client-tools](https://github.com/SWG-Source/client-tools) | `master` |

`swg-main` recursively resolves `dsrc`, `src`, `serverdata`, `stationapi`, and
`exe` (`configs`) from SWG-Source, so it must be initialized recursively.

## Relationship to NGE

The NGE flavor is this baseline with Galaxies Reborn forks substituted for `src`
and `stationapi`. Every other pinned revision is currently identical between the
two.

## Pinning

Upstream `master` is a live branch. The submodules here are pinned to exact
revisions so an install stays reproducible; they do not follow `master`
automatically. Advance them deliberately:

```bash
git submodule update --remote swg-source/base/swg-main
git commit -am "Advance SWG Source base swg-main"
```

## Clients

No client torrent is published for this flavor. The `clients` block of
`channels/swg-source/base.json` is a placeholder, so this variant prepares a
server from upstream source but does not deliver a prebuilt client.
