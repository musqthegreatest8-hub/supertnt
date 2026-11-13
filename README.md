# Super TNT (Forge 1.20.1 / 47.4.10, Java 17)

A fully standalone explosive mod based on the "Super TNT" specification. It has **no dependency on the
Pursuer or any other custom entity**.

## Build (get the .jar)
Requirements: **JDK 17** (Gradle 8.1.1 cannot run on JDK 20+) and Gradle 8.1.1.

**Option A - no local setup (GitHub):** create a new GitHub repo, upload the *contents* of this folder to its
root, open the **Actions** tab, run **Build mod jar**, then download the `supertnt-forge-1.20.1` artifact.
The jar inside is `supertnt-forge-1.20.1-0.1.0.jar`.

**Option B - locally:**
```
gradle wrapper --gradle-version 8.1.1
./gradlew build          # jar lands in build/libs/
./gradlew runClient      # test in a dev environment
```
(Or copy `gradlew`, `gradlew.bat` and `gradle/wrapper/gradle-wrapper.jar` from a Forge 1.20.1 MDK.)

## Publishing on CurseForge
Upload the jar from `build/libs/` as a new file: Game Version **Minecraft 1.20.1**, Mod Loader **Forge**,
Java **17**. Players need Forge 47.x (1.20.1).

## Implemented (spec phases 0-4)
- Red/orange Super TNT block + item, recipe, loot table, original SFX (generated), placeholder textures.
- Ignition (flint & steel, fire charge, redstone, flaming arrows, chain from explosions).
- Fuse selection: sneak + right-click in air cycles presets (15/30/45/60 s, configurable);
  `/supertnt fuse <seconds>` for a custom value (within min/max).
- Environment detection from geometry (64 probe rays, sky check, configurable threshold) - `/supertnt classify`.
- Server-authoritative Reminder Timer, shatter animation, then Collapse Timer. One HUD phase on the client, so
  the timers cannot overlap; a packet arriving mid-animation is parked until the animation ends.
- Enclosed mode: initial blast, warned ceiling drops (crack + dust, then fall), vulnerability-weighted
  secondary explosions, escalating rumble/shake (10/25/45/65/85), 10/5/3/2 s warnings, last-second hush,
  final collapse in outward-sweeping synchronized rings with per-tick and per-event caps.
- Open mode: expanding shockwave, ragged crater, scattered falling blocks, damage + knockback. No Collapse Timer.
- Protection: tag `supertnt:protected`, unbreakable blocks, block entities, extra id list.
- Persistence: active events saved per dimension (SavedData); scan caches rebuild lazily.
- Music: 7 independent slots (initial collapse, open explosion, lap 1-4, endless) with enable, track id,
  volume, fade in/out, loop, preview, reset, safe fallback to defaults, once-per-event playback, background
  music ducking that never touches SFX. Settings screen: Mods list -> Super TNT -> Config.
- Server config: `serverconfig/supertnt-server.toml`. Client config: `config/supertnt-client.toml`.

## Not implemented yet (spec phases 5-8)
Lap portals, exit registration, isolated lap instances, difficulty modes, Collapse Rush, checkpoints, rewards,
grading, vaults, Endless Collapse, emergency lighting, digital-glitch effects, an in-game GUI for the
*gameplay* settings (currently the TOML), and a per-ignition fuse GUI. Lap/endless music slots already exist
so the lap system can plug into them later.

## Notes
- Default music slots point at vanilla music discs as placeholders; custom tracks come from a resource pack's
  `sounds.json` (enter its sound event id in the slot). Original SFX are synthesized placeholders.
- Textures are simple generated placeholders.
- Only loaded chunks are ever read or modified.
- Created without network access: **not yet compiled or playtested** against Forge. Expect to fix a few
  mapping/signature issues on the first build.
