# Game Day Matrix

Home Assistant blueprints that turn a HUB75 matrix running [hub75-studio](https://github.com/pavlov-net/hub75-studio) into a game-day scoreboard. Works great on the [Apollo Automation M-1](https://wiki.apolloautomation.com/) and any other controller hub75-studio supports.

Football (NFL and college) is first. Other sports will get their own blueprints later.

## Football Game Day

- Live scoreboard: team logos, scores, season records, timeout pips, gold abbreviation for the team with possession, and a bottom ticker you compose yourself (game clock, down and distance, play-by-play text, pre-game odds and TV network)
- Full-screen splashes on the matrix: "TOUCHDOWN!" in your team's color when you score, the opponent's plays acknowledged in theirs ("IU TOUCHDOWN"), and a victory splash when your team wins
- Switches the matrix to the scoreboard before kickoff (configurable lead-in), keeps it there through the game, lingers on the final, then switches back to whatever was showing
- Celebrations: your lights snap to team color in a single hit (one command, so mixed hardware stays in sync), hold a few seconds scaled to the play, and return to what they were doing; optional WLED effects on top, and free-form action slots for anything else
- Plays nice with HyperHDR/Hyperion ambient lighting: celebrations paint through the stream instantly, and the stream is only paused (and resumed) around optional WLED effects

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fbharvey88%2Fgameday-matrix%2Fblob%2Fmain%2Fblueprints%2Fgameday-football.yaml)

## Prerequisites

1. A HUB75 matrix flashed once with [hub75-studio](https://github.com/pavlov-net/hub75-studio) plus the zero-config **Team Tracker Live** page. The [firmware folder](firmware/) has a ready-to-flash example for the Apollo M-1; set the panel layout substitutions to match your hardware and the scoreboard lays itself out to fit (single 64x64 panel and two-wide 128x64 are both supported by one page file). No teams, sensors, or page names go in the YAML: the blueprint pushes the game data to the device, so switching teams never means reflashing.

   The page currently lives on the `gameday` branch of [this fork](https://github.com/bharvey88/hub75-studio); it has been offered upstream ([hub75-studio PR #148](https://github.com/pavlov-net/hub75-studio/pull/148)) and this README will point at the official repo when it lands.
2. The [Team Tracker](https://github.com/vasqued2/ha-teamtracker) integration installed via HACS, with a sensor configured for your NFL or college football team.
3. Optional, for team logos on the page: hub75-studio's media proxy running and reachable from the device.

The original per-team compiled page (`packages/pages/teamtracker.yaml` upstream) still works with this blueprint too; the data push simply has nothing to talk to and skips.

## Setup

1. Click the import badge above and save the blueprint.
2. Create an automation from it and fill in:
   - **Team Tracker sensor**: your team's sensor.
   - **Matrix**: pick your matrix from the dropdown. It only lists hub75-studio devices (firmware built from the gameday branch or a recent factory image; older source builds don't identify themselves and won't appear until reflashed).
   - **Ticker content**: tick what scrolls along the bottom during a game. Default is clock + down and distance + last play; play text is trimmed to 160 characters so an overturned-call saga doesn't scroll for a minute. Drop "Last play" if you prefer a calm board, add "TV network (pre-game)" to see where to watch before kickoff.
   - **Timing**: how early to switch before kickoff and how long the final score lingers.
   - **Celebrations**: pick lights; the team-color hit is the whole celebration by default. Optionally pick a WLED effect per score type from a dropdown curated for strips that sit behind a TV. If HyperHDR or Hyperion streams to those strips, add its entities to BOTH the celebration lights (so the color paints through the stream instantly) and "Ambient lighting to pause" (so optional effects can show).
   - Everything else can stay on its defaults: the automation finds the device's page selector and `teamtracker_update` action on its own.

## Notes

- During a game the board self-heals: reboots, restarts, or someone flipping the page get pulled back to the scoreboard within seconds. Toggle the automation off if you need the matrix for something else mid-game.
- Opponent scores show briefly on the matrix in their color, and nothing else: your lights never celebrate for them. A toggle turns even the matrix reaction off, and an action slot exists if you want a mourning ritual.
- Halftime replaces the stale play ticker with win probability and season records; bye weeks and empty schedules show an idle screen.
- Logos use ESPN's dark-background variants automatically, so mostly-black logos (looking at you, Cincinnati) stay visible on the black panel.
- Touchdown detection watches the score jump: +6, +7, and +8 all count as a touchdown since the API often reports the extra point in the same update. Extra points, two-point plays, and field goals get their own splash labels.
- The lights snapshot and restore uses `scene.create`, so if Home Assistant restarts mid-celebration a light can be left on the team color; the next celebration re-snapshots cleanly.

## License

MIT
