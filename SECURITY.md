# Security scope

AntiM2Shell 0.1.0 is a strict client-side mitigation for the vanilla Minecraft 26.3 resource-pack TrueType loading path. It is not an antivirus, native-code sandbox, or a FreeType patch.

## Invariants

- Every invocation of the vanilla TTF definition loader is cancelled at method entry. No font bytes, resource metadata, pack identity or extension need to be inspected.
- The returned immutable provider owns no native memory and supports no glyphs. Closure is harmless. Other providers can supply missing glyphs; a TTF-only named font may be unreadable.
- The primary block has no allowlist, off switch, source-name heuristic or catch-and-resume path.
- Both vanilla glyph-load call sites additionally receive `FT_LOAD_NO_HINTING`. This is defense in depth, not a claim that arbitrary font parsing becomes safe.
- The three exact targets are mandatory, and the client initializer forces their transformation. Unsupported Minecraft versions are rejected by mod metadata.
- Logging uses a fixed, once-per-process warning, not resource-pack controlled text. Blocking counters are atomic and keep no per-font collection.

## Boundaries

The mitigation does not protect against malicious mod JARs, other mods bypassing vanilla font loading, arbitrary native calls, unrelated image/audio/parser bugs, or an already compromised system. Another mod can interfere with this mod's code; this is not a security boundary against code already executing in the JVM.

Resource-pack TrueType fonts are disabled regardless of trust. A successful block does not prove the font was malicious. Repeated reloads may increment the counter again.

The inspected official client is Minecraft 26.3, SHA-1 `e877b6a07acd633fb3bb475002175cec036e7b87`, obtained from Mojang's version manifest. No exploit code or malicious pack is part of development or validation.

## Validation requirements

Build success alone is insufficient. The `securityTest` task must run actual Fabric transformations, exercise the transformed loader with an inaccessible resource manager, and inspect the transformed glyph-loading methods. Tests must finish before the game opens a window and must not open font files or invoke native font parsing. The test-only mod is packaged separately and must not be installed by users.

Graphical rendering, a real multiplayer session and arbitrary modpack compatibility require separate testing. Passing the headless security checks does not claim those checks have been performed.
