# Game Day Matrix

A Home Assistant blueprint that turns a HUB75 matrix running [hub75-studio](https://github.com/pavlov-net/hub75-studio) into a game-day scoreboard. Works great on the [Apollo Automation M-1](https://wiki.apolloautomation.com/) and any other controller hub75-studio supports.

What it does:

- Switches the matrix to your Team Tracker page before kickoff (configurable lead-in, or right at game start)
- Flashes lights in your team's colors when your team scores, then puts them back how they were
- Runs your own actions on any score, with separate football slots for touchdowns and field goals, a good place for a WLED touchdown preset
- Keeps the final score up for a while after the game, then switches back to whatever page was showing before

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fbharvey88%2Fgameday-matrix%2Fblob%2Fmain%2Fblueprints%2Fgameday.yaml)

## Prerequisites

1. A HUB75 matrix flashed with [hub75-studio](https://github.com/pavlov-net/hub75-studio).
2. The Team Tracker page added to your device YAML. Uncomment the `teamtracker.yaml` package block and set your team:

   ```yaml
   - path: packages/pages/teamtracker.yaml
     vars:
       uid: "cowboys"
       page_friendly_name: "Team Tracker - Cowboys"
       entity_id: "sensor.team_tracker_dal"
   ```

3. The [Team Tracker](https://github.com/vasqued2/ha-teamtracker) integration installed via HACS, with a sensor configured for your team. Any league it supports works: NFL, college football, NBA, MLB, NHL, soccer, more.
4. Optional, for team logos on the page: hub75-studio's media proxy running and reachable from the device.

## Setup

1. Click the import badge above and save the blueprint.
2. Create an automation from it and fill in:
   - **Team Tracker sensor**: your team's sensor.
   - **Matrix page select**: the device's "Select Page" entity (search your entities for "Select Page").
   - **Team Tracker page name**: the `page_friendly_name` from your YAML, exactly. "Team Tracker - Cowboys" in the example above.
   - **Timing**: how early to switch before kickoff and how long the final score lingers.
   - **Celebrations**: pick lights to flash in team colors, and add actions if you want more. The touchdown and field goal slots only fire for football; every other sport uses the any-score action.

## Notes

- Opponent scores don't trigger anything. On purpose.
- The lights snapshot and restore uses `scene.create`, so if Home Assistant restarts mid-game the "switch back after the game" step is skipped and the matrix stays on the game page.
- Color-capable lights work best for the flash. Lights without RGB support may ignore the color and just blink.
- Touchdown detection watches the score jump: +6, +7, and +8 all count as a touchdown since the API often reports the extra point in the same update.
- hub75-studio's Team Tracker page renders a 64x64 layout. On larger layouts (like a 2x2 grid) it currently draws in one panel's worth of space.

## License

MIT
