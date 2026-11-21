# Mapbase 8.0 port for the waf engine tree

Upstream: `../source-sdk-2013`, merge commit
`1a147905f667cae0a3d3894949debe25a60297b5` (Mapbase v8.0 in E:Z2).
The port uses the change relative to its first parent,
`cdf2d664`, and maps `sp/src/` to this repository's root.

## Integration

- Port the gameplay, animation, protagonist, timer, choreographed sentence,
  rendering, VGUI and VScript changes, preserving local engine/platform changes.
- Keep the engine's multi-word function pointer storage for 64-bit member calls
  while adding upstream's temporary Vector/QAngle return storage and refcounted
  script returns.
- Pass the class-description initialization argument explicitly in the member
  binding macro to avoid a Clang frontend crash.
- Declare KeyValues constructor defaults in the header for Clang compatibility.
- Client/server waf scripts parse their Mapbase VPC lists; those lists include
  the renamed animation state and new shared/client/server sources.
- Add the existing sqdbg implementation and Squirrel internal include paths to
  `vscript/wscript`, with Windows socket linkage.

## Upstream changes already present

The sqdbg sources and license already match the target commit. The response
system already includes the choreosentence response type and include-path fixes;
its local differences are retained.

## Tree-specific omissions

- Upstream README and Visual Studio ignore patterns are not applied to this
  engine repository's documentation/build layout.
- `vpc_scripts/newer_vs_toolsets.vpc` and its registry fix are Visual Studio
  support files absent from this waf tree.
- `utils/vbsp/matrixinvert.h` is already absent. The parallax-OBB code updated in
  upstream `utils/vbsp/map.cpp` does not exist here; no new tool feature is added.
- `utils/vbsp/vscript_funcs_vmfs.cpp` and its VScript tool infrastructure are
  absent. VBSP help entries for absent Mapbase tool options are omitted.
  The applicable thread-count help update is retained.

## Additional E:Z2 feature ports

The following upstream commits are ported with the same `sp/src/` mapping:

- `c26e96eeeec790aee133f0857b26d4798b23287e`: split weapon selection HUD,
  including explicit SLAM satchel/tripmine selection.
- `d7cb9c0da82be624f181a352b3dc4e3694fe88ed`: Vortigaunt attack-speed cvars.
- `1fd6b8a16d4fe019b3c545162ab37565934e7188`: energy grenade dynamic light.
  Its new client source is registered in `client_ez2.vpc`, parsed by waf.
- `68a1eb9df801465b38a9f479d55c56b3a4f0c353`: projected muzzle flashes and
  rocket trail lights.
- `22a62ba1b59996744624afdf3715d1362b029562`: Vortigaunt zap and Combine ball
  dynamic lights (applied after the projected texture changes).

The changes in `918f420c`, `d9fd3353`, `2edf4ed9`, and `b9af81eb` already
exist in the local ancestry as `d6c95916`: Pose parameter bounds checks use
`&&` in the setter, and client scripting can be disabled separately.

All 18 affected translation units in the existing Android ARM64 compilation
database passed `-fsyntax-only` with the configured compiler and flags. The
full client/server build was stopped after partial compilation to run these
focused checks; a complete link and runtime test have not been performed.
The lighting materials and split-selection weapon scripts/icons must come
from the corresponding game content; they are not supplied by these commits.

## Validation

Use the existing Android ARM64 / `entropyzero2` configuration:

```sh
python3 waf build --targets=client,server,vscript -j4
```

Gameplay assets, maps and scripts outside the source commit are not included.
Runtime validation requires the corresponding E:Z2 / Mapbase content.
