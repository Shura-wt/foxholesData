# Foxhole Game Data

Community-maintained dataset extracted from **Foxhole**, the massively multiplayer war game developed and published by **Siege Camp**.

This repository exists because finding structured, machine-readable game data for Foxhole is difficult. The goal is to provide developers, content creators, and community tool builders with easy access to this data.

## Content

### JSON Data

| File | Items | Description |
|------|-------|-------------|
| `data/foxhole-catalog.json` | 379 items | Complete item catalog with detailed stats (damage, production costs, vehicle specs, weapon data, equipment slots, etc.) |
| `data/foxhole-crate-items.json` | 225 items | Cratable items with crate logistics info (quantities per crate, categories, wiki links) |

### Icons (820 PNG files)

| Folder | Count | Description |
|--------|-------|-------------|
| `data/icons/items/` | 296 | Ammunition, weapons, equipment, medical supplies |
| `data/icons/structures/` | 179 | Buildings, emplacements, defensive structures |
| `data/icons/vehicles/` | 177 | Tanks, trucks, aircraft, naval vessels |
| `data/icons/maps/` | 88 | Map UI icons (facilities, bases, resources) |
| `data/icons/hexmaps/` | 45 | Regional hexagonal map tiles |
| `data/icons/uniforms/` | 25 | Colonial and Warden faction uniforms |
| `data/icons/worldmap/` | 10 | World map UI elements |

## Data Structure

### Catalog Item (`foxhole-catalog.json`)

```json
{
  "codeName": "LightArtilleryAmmo",
  "displayName": "120mm",
  "description": "Payload for light artillery weapons.",
  "category": "HeavyAmmo",
  "iconLocal": "icons/items/LightArtilleryAmmoItemIcon.png",
  "quantityPerCrate": 5,
  "weight": 250,
  "crateProductionTime": 55,
  "costPerCrate": [
    { "ItemCodeName": "Cloth", "Quantity": 120 },
    { "ItemCodeName": "HeavyExplosive", "Quantity": 10 }
  ],
  "ammoData": { "damage": 400, "damageType": "HighExplosive", "explosionRadius": 1125 },
  "vehicleData": { "maxHealth": 0, "maxArmour": 0, "fuelCapacity": 0, "maxSpeed": 0 },
  "weaponData": { "damageMultiplier": 0, "fireRate": 0, "range": 0, "magazineSize": 0 }
}
```

### Crate Item (`foxhole-crate-items.json`)

```json
{
  "name": ".44",
  "codeName": "RevolverAmmo",
  "icon": "RevolverAmmoItemIcon.png",
  "amountPerCrate": 40,
  "category": "Ammunition",
  "wikiUrl": "https://foxhole.wiki.gg/wiki/.44"
}
```

## How the Data Was Extracted

4 Python scripts in a pipeline:

### 1. `scrape-foxhole-crates.py` — Wiki scraping

- Source: [foxhole.wiki.gg](https://foxhole.wiki.gg)
- Scrapes table pages (Ammunition, Weapons) for "In Crate" columns
- Visits individual item pages (Medical, Supplies, Equipment, Uniforms...) to extract infobox data
- Automatic discovery phase via wiki link crawling
- Output: `foxhole-crate-items.json` (name, icon, quantity/crate, category, wiki link)

### 2. `clean-crate-data.py` — Wiki data cleanup

- Removes false positives (non-item pages like "Update 0.46")
- Fixes categories (uniforms detected as Equipment, large materials, liquids...)
- Fixes broken icons (filter icons replaced with actual item icons)
- Re-fetches icons from the wiki when needed

### 3. `extract-foxhole-icons.py` — Game file extraction

- Reads Foxhole's `.pak` file directly (`War-WindowsNoEditor.pak` from Steam)
- Custom UE4 PAK v8 parser (magic, footer, index, ZLIB decompression)
- Extracts `.uasset`/`.uexp` textures from `Textures/UI/` (items, vehicles, structures, maps)
- A pre-existing `catalog.json` (extracted from game data via a third-party tool like FModel) guides which assets to extract

### 4. `build-catalog.py` — Final catalog assembly

- Merges the `catalog.json` (extracted game data) with local PNG icons
- Enriches each item: production costs, weapon/ammo/vehicle stats, slots, weight
- Resolves UE4 paths to local PNGs
- Output: `foxhole-catalog.json` (367KB, complete catalog)

### Data Sources

| Source | Data |
|--------|------|
| [foxhole.wiki.gg](https://foxhole.wiki.gg) | Quantities per crate, display names, wiki links |
| Game `.pak` file (Steam) | Raw textures/icons |
| `catalog.json` (pre-extracted via FModel or similar) | Internal game data (CodeName, stats, costs, UE4 categories) |

## Use Cases

- Companion websites and apps
- Logistics calculators and production planners
- Interactive maps
- Wiki tools and data visualization
- Discord bots

## Disclaimer & Intellectual Property

**Foxhole** is a game developed and published by [Siege Camp](https://www.siegecamp.com/). All game content, including but not limited to names, descriptions, icons, artwork, game mechanics data, and any other assets present in this repository, are the intellectual property of Siege Camp.

**This repository is not affiliated with, endorsed by, or sponsored by Siege Camp.**

The data and assets provided here are extracted from the game for **community and educational purposes only**. This project does not claim any ownership over Foxhole's intellectual property. If you use this data, please respect the following:

- **Do not use these assets for commercial purposes** without explicit permission from Siege Camp.
- **Credit Siege Camp** as the original creator of the game and its content.
- **Do not redistribute the assets** in a way that could harm or compete with the original game.
- If Siege Camp requests the removal of any content from this repository, it will be removed immediately.

This project falls under **fair use** for community tools and fan-made content. If you are a representative of Siege Camp and have concerns about this repository, please open an issue or contact the maintainer directly.

## Contributing

If you notice outdated or missing data (new updates, balance changes, new items), feel free to open an issue or submit a pull request.

## License

The **code and structure** of this repository (file organization, JSON schemas) are available under the [MIT License](LICENSE).

The **game assets and data** (icons, names, descriptions, stats) remain the property of Siege Camp and are subject to their terms of use.
