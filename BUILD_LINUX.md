# Building PicoGKRuntime on Linux (Axiom fork)

Built headless on 2026-10-24 for the Axiom project. Items are marked **[verified]** (from the build dir's
`CMakeCache.txt`, `/var/log/apt/history.log`, or the Axiom README) or **[unverified]**.

## Environment [verified]
- Debian 13 (13.7), x86_64; GCC 14 (`/usr/bin/c++`); CMake 3.31.6; Ninja.

## Dependencies [verified: apt history]
```
apt-get install -y build-essential cmake ninja-build libtbb-dev libboost-iostreams-dev \
  libboost-system-dev libblosc-dev zlib1g-dev libglfw3-dev libgl-dev libx11-dev \
  libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev
```
CMake picked up system Boost 1.83.0 and `/usr/lib/x86_64-linux-gnu/libblosc.so` [verified: CMakeCache].
OpenVDB is the bundled `openvdb` submodule, ABI 13, core shared+static, no NanoVDB/AX/Python/tests [verified: CMakeCache].
(The same apt line also installed unrelated FEA/CAD tools; only the packages above are listed here.
Whether each one is strictly required — e.g. the GLFW/X11 dev packages for a headless build — is **[unverified]**.)

## Build
```
git submodule update --init --recursive          # [unverified: exact command not logged]
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release   # generator + build type [verified: CMakeCache]; other -D flags [unverified]
ninja -C build                                    # [verified: Axiom README rebuild command]
cp build/lib/picogk.so libpicogk.26.2.so          # [verified]
```
Output is `build/lib/picogk.so`; .NET probes `DllImport("picogk.26.2")`, so it is renamed to
`libpicogk.26.2.so` and placed next to the executable [verified: Axiom README].
A prebuilt binary is attached to release `linux-26.2` of this fork.

## Source changes
Only `API/PicoGKBuild.h` changed, and only its auto-generated `PICOGK_BUILD` timestamp. No code changes were required.

## Known issues [verified: observed in Axiom]
- `Voxels.IntersectImplicit()` aborts with OpenVDB `expected grid A outside value > 0, got 0`.
  Workaround: render the implicit into its own `Voxels(lib, implicit, bbox)` and use `BoolIntersect`.
- `Voxels.CalculateProperties()` volume ignores internal voids after booleans (e.g. sphere minus inner sphere
  reports full volume). Workaround: use mesh (trimesh) volume.
