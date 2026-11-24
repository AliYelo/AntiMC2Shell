AntiM2Shell - Minecraft FreeType RCE Defense Mod
A lightweight client-side Fabric mod for Minecraft 26.3 designed to mitigate font-based remote code execution (RCE) attacks, including bikini/minecraft2shell, by blocking resource-pack TrueType fonts before they are read or passed to FreeType.

AntiM2Shell blocks resource-pack `ttf` providers **before font files are read or passed to FreeType**. The same rule applies to server packs, local packs, and resource reloads. It does not rely on pack names, file extensions, or trusted-server lists.

This is a temporary mitigation for a specific attack path, not an antivirus or protection against every Minecraft vulnerability. It does not patch FreeType itself or replace updating to official game versions that include security fixes.

## Installation

1. Use Fabric Loader **0.20.1 or later** with Minecraft **26.3**.
2. Place `build/libs/antim2shell-0.1.0.jar` in your game profile's `mods` folder.
3. Do not install the `-sources.jar` or the test JAR. Fabric API is not required.

The mod is client-only. Other Minecraft versions are rejected by the compatibility check. If the required Mixin injections cannot be applied, startup fails instead of silently continuing without protection.

## Effect on appearance

- All resource-pack TTF fonts are blocked, including fonts from trusted packs.
- Custom text and icons that rely entirely on TTF fonts may appear as missing-character boxes. Automatic fallback to the default font is not guaranteed.
- Other providers in the same font definition can still supply glyphs. The mod does not modify bitmap, unihex, or space providers, or texture images.
- The first block produces a single warning in the game log, avoiding a flood of per-pack messages. A blocked font is not necessarily malicious.

## How protection works

1. Loading is cancelled at the entry to `TrueTypeGlyphProviderDefinition.load(ResourceManager)`, returning an empty provider that owns no native resources.
2. As an additional defense, `FT_LOAD_NO_HINTING` is added to vanilla glyph-loading calls in `TrueTypeGlyphProvider.loadGlyph` and `NativeImage.copyFromFont`.
3. Exact method signatures, a required Mixin configuration, and mandatory single-injection counts are used. All three target classes are loaded during client initialization to force the transformations early.

There is no protection toggle, remote configuration, automatic updater, telemetry, or network request made by the mod. It is not a sandbox that prevents other mods from calling native libraries directly.

## Building and safe validation

With JDK 25 installed and `JAVA_HOME` pointing to it:

```powershell
.\gradlew.bat --no-daemon clean build
```

The SHA-256 checksum for Gradle Wrapper's 9.8.2 distribution is pinned. The project uses Loom 1.19.4 and Fabric Loader 0.20.1. The first build downloads official Minecraft files and development dependencies.

The `check` task runs a separate test mod through the real Fabric/Knot/Mixin loader. The test exits before the game window opens. It uses a mock resource manager that fails on any resource access; no exploit pack, malicious font, or attack script is used. The test mod is not included in the release JAR.

See [SECURITY.md](SECURITY.md) for the detailed scope and limitations.

## Development references

- [Fabric 26.3 example project](https://github.com/FabricMC/fabric-example-mod/tree/26.3)
- [FreeType glyph-loading flags](https://freetype.org/freetype2/docs/reference/ft2-glyph_retrieval.html)
- [FontGuard](https://github.com/callmelovevsick/FontGuard): reviewed for comparison. AntiM2Shell is an independent implementation that does not rely on guessing a font's source from its pack name.

This repository does not contain exploit packs or attack code.
