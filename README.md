# Game Day Matrix

Home Assistant blueprints that turn a HUB75 matrix running [hub75-studio](https://github.com/pavlov-net/hub75-studio) into a game-day scoreboard. Works great on the [Apollo Automation M-1](https://wiki.apolloautomation.com/) and any other controller hub75-studio supports.

NFL is first (college football works with it too). More sports will get their own blueprints later.

## NFL Game Day

- Switches the matrix to your Team Tracker page before kickoff (configurable lead-in, or right at game start)
- Flashes lights in your team's colors on every score, then puts them back how they were
- Runs your own actions with separate slots for touchdowns, field goals, and everything else, a good place for a WLED touchdown preset
- Keeps the final score up for a while after the game, then switches back to whatever page was showing before

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fbharvey88%2Fgameday-matrix%2Fblob%2Fmain%2Fblueprints%2Fgameday-nfl.yaml)

## Prerequisites

1. A HUB75 matrix flashed once with [hub75-studio](https://github.com/pavlov-net/hub75-studio) plus the zero-config **Team Tracker Live** page. The [firmware folder](firmware/) has a ready-to-flash example for the Apollo M-1 with two panels side by side (128x64). No teams, sensors, or page names go in the YAML: this blueprint pushes the game data to the device, so switching teams never means reflashing.
2. The [Team Tracker](https://github.com/vasqued2/ha-teamtracker) integration installed via HACS, with a sensor configured for your NFL or college football team.
3. Optional, for team logos on the page: hub75-studio's media proxy running and reachable from the device.

The original per-team compiled page (`packages/pages/teamtracker.yaml` upstream) still works with this blueprint too; the data push simply has nothing to talk to and skips.

## Setup

1. Click the import badge above and save the blueprint.
2. Create an automation from it and fill in:
   - **Team Tracker sensor**: your team's sensor.
   - **Matrix**: pick your matrix from the dropdown. It only lists hub75-studio devices (firmware built from the gameday branch or a recent factory image; older source builds don't identify themselves and won't appear until reflashed).
   - **Team Tracker page name** and **data push action**: leave both empty. The automation finds the device's page selector and `teamtracker_update` action on its own. They're only overrides for unusual setups.
   - **Timing**: how early to switch before kickoff and how long the final score lingers.
   - **Celebrations**: pick lights to flash in team colors, and add actions if you want more. Touchdowns and field goals get their own slots; extra points, two-point conversions, and safeties hit the other-score slot.

## Notes

- Opponent scores don't trigger anything. On purpose.
- The lights snapshot and restore uses `scene.create`, so if Home Assistant restarts mid-game the "switch back after the game" step is skipped and the matrix stays on the game page.
- Color-capable lights work best for the flash. Lights without RGB support may ignore the color and just blink.
- Touchdown detection watches the score jump: +6, +7, and +8 all count as a touchdown since the API often reports the extra point in the same update.
- hub75-studio's Team Tracker page renders a 64x64 layout. On larger layouts (like a 2x2 grid) it currently draws in one panel's worth of space.

## License

MIT
