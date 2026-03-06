Pacman Style Mini Game in Java
 
## Project Overview
A 2D tile-based dungeon crawler game built in Java. The game procedurally generates a world from a user-supplied seed, places a player avatar, enemies, and collectibles, and supports save/load via a flat text file.
 
## Project Structure
```
proj5/
  src/
    core/          # Game logic (main package)
    tileengine/    # Rendering engine (TERenderer, TETile, Tileset)
    utils/         # FileUtils, RandomUtils
    demo/          # Demo programs (not part of main game)
  test/
    TestRoomCreation.java
Saved_Game.txt     # Persisted save state (project root)
name.txt           # User's display name (project root)
```
 
## Key Classes
| Class | Role |
|---|---|
| `Main` | Entry point — calls `Game.createMenu()` |
| `Game` | Top-level controller: menu, game loop, HUD, enemy/collectible spawning |
| `World` | Procedural world generation (rooms + MST hallways), seed-driven `Random` |
| `Room` | Rectangle room with center helpers |
| `HallwayGenerator` | Builds MST edges between rooms using Kruskal's / Disjoint Sets |
| `User` | Player position and health |
| `Enemy` | Random-walk AI that moves every 500 ms |
| `Collectible` | Health pickup at room center |
| `GameState` | Serializable snapshot of game for save/load |
| `SaveLoad` | Reads/writes `Saved_Game.txt` using `algs4.In` / `PrintWriter` |
| `GameAudio` | Plays menu, exploration, walk, hit, and collect sounds |
 
## Build & Run
- **IDE**: IntelliJ IDEA
- **Compile**: `javac` args stored in `proj5/javac.20251201_165745.args`
- **Entry point**: `core.Main`
- **Dependencies**: `edu.princeton.cs.algs4` (algs4.jar), StdDraw for graphics/audio
- Jar files are git-ignored (`*.jar`)
 
## Controls
| Key | Action |
|---|---|
| `N` | New game (prompts for numeric seed, confirm with `S`) |
| `L` | Load game from `Saved_Game.txt` |
| `Q` | Quit from menu |
| `W/A/S/D` | Move avatar up/left/down/right |
| `:Q` | Save and quit during gameplay |
 
## World Generation Details
- World is 150×60 tiles; HUD occupies 5 extra rows above
- `World.generateWorld()`: creates `countRooms` (10–30) non-overlapping rooms, then connects them with L-shaped hallways along a MST
- Rooms, enemies, collectibles all placed at room centers
- `World.r` (`Random`) is shared and stateful — order of calls matters for reproducibility
 
## Save File Format (`Saved_Game.txt`)
```
<seed>
<userX>,<userY>,<userHealth>
<enemyCount>
<enemyX>,<enemyY>   (repeated enemyCount times)
<collectibleCount>
<collX>,<collY>,<collHealth>   (repeated collectibleCount times)
```
 
## Coding Conventions
- Package: `core` (main game), `tileengine`, `utils`
- Constants are `private static final` at the top of each class
- Tile comparisons use reference equality against `Tileset` constants (e.g., `== Tileset.WALL`)
- Enemy movement uses `World.r` for randomness — do not introduce a separate `Random` for enemies
- `StdDraw.enableDoubleBuffering()` is used; always call `StdDraw.show()` after drawing
 
## Known Design Decisions
- `SaveLoad` writes to the **project root** (not `proj5/`), so the working directory when running must be the repo root
- Enemy collision with player decrements health by 1 per move cycle; health 0 = `System.exit(0)`
- Health can exceed `MAXHEALTH` if multiple collectibles are picked up (no cap enforced)
- `World.getRandomRoom()` skips index 0 (`rooms.get(1 + r.nextInt(countRooms - 1))`)
