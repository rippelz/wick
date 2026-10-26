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
