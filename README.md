# L4D2 Versus Single — Zonemod-like

**[English](README.md) | [中文](README_zh.md)**

A single-player **Versus** experience for Left 4 Dead 2, based on [Versus Single](https://steamcommunity.com/sharedfiles/filedetails/?id=186808048) by def075 (Workshop ID: 186808048), rebuilt with **Zonemod-style scoring**, **official-aligned distance score**, and **rebalanced weapons**.

You play as one survivor in a team of four (3 AI bots); all Special Infected are AI-controlled. Full Versus pacing, solo.

---

## Features

### Zonemod-style scoring

```
Total = Distance + (HealthBonus + DamageBonus) × survivor ratio
```

- **Survivor ratio** = alive survivors ÷ 4 (fewer alive, more penalty)
- Total is written live to the engine convar `vs_survival_bonus` (engine settlement = cvar × alive)

### Official-aligned Distance score

- **Mid-level**: `Dist = official progress % × map full score` — matches the TAB scoreboard on regular maps
- **End-round settlement**: `Dist` forced to the map's full score on survival (works for gascan / holdout / regular finales alike)
- Built-in full-score table: **57 official maps** (c1–c14) + **31 custom workshop VPKs** (~135 maps, extracted from each mission's `VersusCompletionScore`; maps without an official value default to 500)

### DamageBonus rules

| Situation | Deduction |
|---|---|
| Damage taken at 1 real HP (black health) | damage × 1/4 |
| Each incapacitation (incl. first) | −12% |
| Death after incap (not revived) | −12% |
| Death on 3rd incap | −12% |
| Damage while incapacitated | none |

- Reset to 100% each round start

### Rebalanced weapons

| Weapon | Damage | Pellets | Total | Damage dropoff | Vertical / Horizontal spread | Reload |
|---|---|---|---|---|---|---|
| Pump Shotgun | 16/pellet | 17 | **272** | 0.7 | 3 / 5 | 0.473s/shell |
| Chrome Shotgun | 28/pellet | 9 | **252** | 0.65 | 4.5 / 5.5 | 0.473s/shell |
| SMG | 22 | 1 | 22 | 0.81 | spread/shot 0.22, max move 2 | 1.9s |
| Silenced SMG | 25 | 1 | 25 | 0.81 | spread/shot 0.25, max move 2.35 | 2.24s |

### Real-time chat output

- Every **5 seconds**, one line in chat (no "Console:" prefix):

  ```
  [VS Single] Dist:xxx HB:xxx<health%> DB:xxx<bonus%> Bonus:xxx
  ```

- Final summary printed immediately on round win (Dist = full score), no further refresh afterwards

### Other

- **Tank**: 3000 HP (4500 in-game after the Versus ×1.5 multiplier), appears at 100% chance between 15%–90% flow
- **Witch**: disabled
- Give pills to all survivors at the start of map
- Specials: 1s ghost delay, max 1 Hunter / 1 Smoker; horde 25 per wave, 100s interval(In fact, certain areas of some maps can simultaneously respawn more than one Hunter or Smoker)
- Up to 99 team switches per round (`vs_max_team_switches 99`)

---

## File structure

```
├── addoninfo.txt              # add-on metadata (title, author, version)
├── modes/
│   └── versus_single.txt      # game-mode definition (base "versus" + cvars)
├── scripts/
│   ├── vscripts/
│   │   └── versus_single.nut  # all mod logic (Squirrel vscript)
│   └── weapons/
│       ├── weapon_pumpshotgun.txt
│       ├── weapon_shotgun_chrome.txt
│       ├── weapon_smg.txt
│       └── weapon_smg_silenced.txt
└── README.md
```

---

## Supported custom workshop MAPs (31)

`dark carnival remix` · `snow_town` · `dead_center_2025` · `dead center rebirth fixed` · `parish overgrowth` · `noecho` · `outline` · `nomercyrehab` · `ccrerouted` · `dead center reconstructed` · `dead_air_redux_aw` · `deadbeforedawn2_dc` · `daybreak_v3` · `ihatemountains2` · `suicideblitz2` · `cmpn_FatalFreightFix` · `energycrisis` · `downpour` · `deathsentence` · `tourofterror` · `deadbeatescape` · `hauntedforest_v3` · `bloodtracks` · `detourahead` · `city17l4d2` · `l4d2_diescraper_362` · `carriedoff` · `openroad` · `tripday` · `undead_zone` · `deathaboard2`

Each map's full score is taken from its mission `VersusCompletionScore` (the official scoreboard cap for that map); 16 maps without an official value (e.g. all of tourofterror) default to **500**.

---

## Known issues

- **Finale mid-level distance** (gascan / holdout maps): the official distance includes task progress (gas cans poured / defense waves), which vscript cannot read — mid-level output shows the position-based value. **End-round settlement is correct** (full score).
- **No on-screen HUD**: all screen-text channels are broken in L4D2 (engine splitscreen bug) — all output goes through **chat**.
- Case-insensitive map matching is applied (some workshop missions list map names in mixed case vs. lowercase .bsp filenames).

---

## Credits

- Original mod: **Versus Single** by **def075** (Workshop ID 186808048)
- Scoring system adapted from [Zonemod Docs - Sirplease](https://sirplease.net/docs/modes/zonemod#general)
- Rebalanced weapon data adapted from [Zonemod Docs - Sirplease](https://sirplease.net/docs/modes/zonemod#items)
- This rebuild was done entirely by **deepseek-v4-flash-0731** — credit where credit is due
