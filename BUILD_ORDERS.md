# Build Order Guide

How to read, edit, and add build orders for each commander in `index.html`.

---

## Where the data lives

All build order data is in the first `<script>` block in `index.html`, inside the `commanders` array. Each entry looks like this:

```js
{
  name: "Raynor",
  faction: "Terran",
  portrait: "portraits/raynor.png",
  buildOrders: [
    {
      name: "Standard Bio",
      steps: [
        { time: "@14", action: "Supply Depot" },
        { time: "@16", action: "Command Center at expansion" },
        { time: "—",   action: "MULE calldowns constantly" },
      ]
    }
  ]
}
```

---

## Step format

Each step has two fields:

| Field | What to put |
|-------|-------------|
| `time` | The trigger — see notation below |
| `action` | Plain English description of what to do |

### Time notation

| Notation | Meaning | Example |
|----------|---------|---------|
| `@14` | At 14 supply | `@14` → build Supply Depot when you hit 14 supply |
| `0:30` | At a clock time | `1:10` → at 1 min 10 sec into the game |
| `—` | No specific timing; general tip or reminder | `—` → always use MULE calldowns |

You can mix both styles in the same build order. Use supply (`@N`) for the early game where supply count is the natural trigger, and clock times for things that happen at fixed intervals regardless of supply (like Tychus's hero spawning at 3:00).

---

## Adding or editing a step

Find the commander in the `commanders` array, then add/edit inside its `steps` array:

```js
// Before
steps: [
  { time: "@14", action: "Supply Depot" },
  { time: "@16", action: "Command Center at expansion" },
]

// After — added a step and edited one
steps: [
  { time: "@13", action: "Supply Depot" },          // edited supply count
  { time: "@16", action: "Command Center at natural" },
  { time: "@19", action: "Orbital Command" },        // new step
]
```

**Order matters** — steps are displayed top to bottom as written. Keep them roughly chronological.

---

## Adding a second build order (alternate strategy)

The `buildOrders` field is an array. Right now only index `[0]` is shown, but the structure is ready for multiple strategies. Add a second object to the array:

```js
buildOrders: [
  {
    name: "Standard Bio",   // shown in UI (not visible yet, but good for future use)
    steps: [ /* ... */ ]
  },
  {
    name: "Mech",           // alternate strategy
    steps: [
      { time: "@14", action: "Supply Depot" },
      { time: "@15", action: "Barracks → Factory" },
      // ...
    ]
  }
]
```

To switch which strategy is shown, change `buildOrders[0]` to `buildOrders[1]` in the `showBuildOrder` function (around line 730 in `index.html`):

```js
const build = commander.buildOrders?.[1];  // shows 2nd strategy
```

---

## Commander-specific notes

Quick reminders about mechanics that affect what "correct" looks like for each commander:

| Commander | Key quirks |
|-----------|-----------|
| **Raynor** | Stim + Combat Shields pre-researched. MULE from every Orbital every ~50s. |
| **Artanis** | Project Power Field top bar = free pylon power at game start. 200 supply cap — no supply worries. Chrono Boost Gateway and Nexus constantly. Guardian Shell means units revive once — never retreat. |
| **Zagara** | Drones cost 0.5 supply in-game display. Banelings are primary damage. Queens go on every Hatchery for inject. Spread Creep Tumors for army speed. |
| **Vorazun** | Dark Pylon top bar = instant 10 supply + power field. Everything is permanently cloaked — enemies without detection are free kills. Time Stop freezes all enemies on map. |
| **Abathur** | Biomass = 2× supply of killed unit (Hybrids = 12). Dump all biomass onto 1 unit for Brutalisk at 100. Symbiote on ally hero for bonus stats. |
| **Alarak** | Supplicants are sacrificed (3 per Ascendant) to fully charge Ascendants. Pylon Overcharge defends early. Destruction Wave clears clusters. |
| **Nova** | Hard 100 supply cap. Max 1 Barracks, 1 Factory, 1 Starport — no exceptions. Quality over quantity — every unit is elite. |
| **Stukov** | Uses **Zerg Overlords** for supply, not Terran depots. Infested Colonist Compounds auto-spawn egg waves. Infested Bunkers can uproot and walk. Infestation levels increase egg wave size. |
| **Dehaka** | Starts at 18 supply. Pack Dens (Glevig/Murvar/Dakrun) are the production buildings. Devour enemy units for Essence — Psionic units give splash bonus. |
| **Han & Horner** | Death effects are key: Hellion death = team attack/speed buff, Reaper death = cluster grenades. Theia Raven's Analyze Weakness multiplies all damage to a target. Don't go pure Horner air — Han units needed for death effects. |
| **Tychus** | Outlaws are permanent heroes — keep them alive. Odin calldown revives Tychus free. Recruit Rattlesnake (healer) first. 3 Engineering Bays recommended. |
| **Zeratul** | Protoss buildings need **no Pylon power**. Artifact Fragments appear ~every 5 min — collect immediately. Ancient Nexus auto-builds Assimilators; no worker assignment needed. Legions cost 800 min each with 120s cooldown. |
| **Mengsk** | **No Orbital Command** — supply comes from Supply Bunkers only. Mandate is the resource that enables top-bar calldowns. Laborers mine; Troopers fight; you can convert between them. Imperial Witness increases Mandate generation from nearby Conscripts. |

---

## Editing tips

- **Keep steps short** — they display in a narrow column on mobile. If a step is getting long, split it into two steps or use abbreviations (×2 instead of "times two", → instead of "then").
- **Use `—` liberally** for reminders that don't have a supply trigger — ongoing habits like "MULE every Orbital" or "Chrono Boost constantly" are worth including.
- **Test after editing** — open the app in a browser (run `npx serve -l 4000` from the project folder), hit Randomize until you land on the commander you edited, and check it looks right.
- **Don't break the JS syntax** — each step must end with a comma, and the last step in `steps: [...]` can also have a trailing comma (JS allows it). If the page goes blank, open browser DevTools → Console to see the error.
