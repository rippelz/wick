# Wick

Sober (Roblox's Android client) running fully offline, with [Waxley](https://github.com/rippelz/waxley)
as its backend.

    wick [place.rbxl|.rbxlx] [--hidden] [--url roblox://...] [--update]
    wick-play [place]          # builds the place for Sober, starts poke.py, runs wick

`wick --help` has the details. The GUI place picker is `wick-launcher`.

## Needs Waxley
Wick reuses Waxley's `mockblox.py`, `routes.py` and `creds.py`, its Studio copy (the game server)
and its data dir. The Waxley checkout is found from `WAXLEY_HOME`, else the `waxley` on `PATH`
(symlinks resolved), else `../waxley`. Run Waxley once first so `~/.local/share/waxley` is set up.

## What's here
- `wick`: the namespace (lo + dummy `os0`, no routes, fails closed), the frozen Sober fork, the
  hidden Studio game server.
- `sober/wickblox.py`, `sober/wick_routes.py`: mockblox with wick's CA and the Sober/mobile routes
  in front of Waxley's.
- `sober/curlshim.c`: points Sober's libcurl at the bundle with wick's CA.
- `sober/wick_place.py`, `wick_place.luau`, `wickc.cpp`: place conversion for Sober.
- `sober/poke.py`, `peek.py`: memory pokes (offsets tied to the forked build).
- `sober/fflags.json`, `server-flags.json`: client / server FFlags.

## Data
`~/.local/share/waxley/wick/` (fork flatpak, places, certs, `run/`). Override the base with `WAXLEY_DATA`.

## Install
Set up [Waxley](https://github.com/rippelz/waxley) first (its `fetch-deps`), then:

    git clone https://github.com/rippelz/wick && cd wick
    ./fetch-deps
    ln -s "$PWD/wick" "$PWD/wick-play" ~/.local/bin/
    cp icon/wick.svg ~/.local/share/icons/hicolor/scalable/apps/
    cp desktop/wick.desktop ~/.local/share/applications/   # opens wick-launcher

`fetch-deps` sets up everything at the builds pinned in `versions.env`, checking every download
against a hash, and skips what's already right (`--force` redoes it all):
- **The Sober fork**: Sober from Flathub pinned to `SOBER_COMMIT`, installed into wick's own
  flatpak installation (masked, remotes disabled). `poke.py`'s offsets only match this build.
  Your own Sober is left alone, but wick mounts the fork over a system-wide Sober, so one (any
  version) gets installed if it's missing.
- **The Roblox APK** (`APK_VERSION`) + its unpacked assets. Roblox only serves its newest APK,
  so this one is mirrored; the zip and the APK inside are both hash-checked.
- **Luau `LUAU_VERSION`**'s source (for `wick_place`'s compiler), the game server's prefix (a copy
  of Waxley's), and `defaults/` (mock manifest, the baseplate place), only if missing.

`wick` itself warns at startup when the fork differs from `versions.env`.

Also needs gcc + g++ (curlshim, wickc), lune (place conversion), python3 with
`cryptography`, gamescope, sudo for poke.py.
