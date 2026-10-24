# Building PicoGKRuntime on Linux (Axiom fork)

Built headless on Debian/Ubuntu x86_64 for the Axiom project.

1. Install deps: `cmake g++ libgl-dev libx11-dev libxrandr-dev libxinerama-dev libxcursor-dev libxi-dev` (plus OpenVDB/TBB/Boost/zlib deps as fetched by the submodules).
2. `git submodule update --init --recursive`
3. `cmake -B build -DCMAKE_BUILD_TYPE=Release && cmake --build build -j`
4. Output: `libpicogk.26.2.so` - place next to the .NET executable (or on LD_LIBRARY_PATH).

Local change: `API/PicoGKBuild.h` adjusted for the Linux build (see diff in this branch).
A prebuilt binary is attached to the `linux-26.2` release of this fork.

## Known issues
- `Voxels.IntersectImplicit` crashes on Linux (segfault). Workaround in Axiom: render the implicit to voxels first, then use `BoolIntersect`.
- `Voxels.CalculateProperties` reports wrong volume for parts with internal voids. Workaround in Axiom: compute volume from the exported mesh (trimesh) instead.
