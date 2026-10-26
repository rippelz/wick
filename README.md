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
    ln -s "$PWD/wick" "$PWD/wick-play" ~/.local/bin/
    cp icon/wick.svg ~/.local/share/icons/hicolor/scalable/apps/
    cp desktop/wick.desktop ~/.local/share/applications/   # opens wick-launcher
Needs Waxley (set up and run once), Sober installed from Flathub (the fork is copied from it),
gcc (curlshim), lune (place conversion), python3 with `cryptography`, gamescope, sudo for poke.py.

## Pinned versions

**Easiest:** run Waxley's `fetch-deps`, then `./fetch-deps` here: it installs the pinned Sober
straight into wick's fork and drops in the APK + assets from this repo's private
[`deps` release](https://github.com/rippelz/wick/releases/tag/deps), so no Sober install of your
own is needed. The binaries are Roblox's and Sober's: keep the repo private.
`versions.env` lists the fork's builds; `wick` warns when the fork differs (`poke.py`'s offsets
only match these).
- Sober: `flatpak install flathub org.vinegarhq.Sober`, then
  `flatpak update --commit=<SOBER_COMMIT> org.vinegarhq.Sober` before the first `wick` run (the
  fork is copied from it). Flathub keeps old commits.
- Roblox APK (`APK_VERSION`): Sober downloads the current one, so this only matches while Roblox
  still ships it. Otherwise it has to come from an APK archive; check it against `APK_SHA256`
  (`base.apk` in Sober's `data/sober/packages/x86_64/com.roblox.client/`).
