# Clans of King

**Clans of King** is a full server-side Minecraft plugin for **Paper/Purpur** with **Geyser + Floodgate** support.  
It combines economy, shop, sell GUI, clans, territory claims, border walls, expansion, ranks, rewards, and a clan map — all persistent with SQLite.

> Designed so both **Java** and **Bedrock** players can use the same commands and inventory GUIs without client mods.

---

## Features

### Economy
- Persistent money (SQLite)
- `/balance`, `/pay <player> <amount>`
- Validation: positive amounts, no self-pay, insufficient funds, overflow protection

### Shop & Sell
- `/shop` — category GUI (combat, armor, tools, farming, building, enchantment, potion, rare)
- Configurable buy/sell prices (`shop.yml`)
- Quantity selector and safe purchase flow
- `/shell` — sell GUI: put items in, close inventory to sell
- Anti-dupe handling for GUI click/drag/close

### Clan System
- `/clan create <name>` — creator becomes **KING**
- Join requests: `/clan join <name>` → king accept/deny (command or GUI)
- Roles: **KING**, **OFFICER**, **MEMBER**
- Kick, promote, demote, leave, disband
- Clan level & XP (kills, bosses, raids, etc.)

### Territory & Walls
- **Clan Claim Podium** — place to claim initial **100×100** territory
- Server-side protection (break/place/containers/doors)
- Overlap prevention
- **Border walls**: stone brick fortress around the claim
  - Ground-aware (follows terrain)
  - Uniform top height (aligned to highest ground on the border)
  - Height **6**, thickness **3**
  - Torches along the wall
- **Clan Expansion Torch** — place on border/wall to expand (+10) and **move walls** to the new edge
- Torches from bosses/raids (configurable)

### Clan Home & Respawn
- Podium location becomes **clan home** (coordinates saved)
- Members respawn at clan home when bed spawn is unavailable

### Clan Map
- `/clan map` — normal world map with:
  - Gold border overlay on territory
  - Soft green edge markers
  - White cross at clan home
- Dominion list GUI (clan names, members, land size)

### Player Progression
- Kills / deaths (player kills only)
- Ranks: Lord → Hero → Baron → Duke → ArchDuke → King → Emperor
- **Waste** title when a king loses their clan (cleared when creating a new clan)
- Scoreboard HUD: name, clan, rank, money, kills, deaths, members, clan level, XP bar

### Technical
- SQLite persistence
- UUID-based identity
- Async-friendly DB access where safe
- GUI holder identification (not title-only)
- Paper API, Java 17+

---

## Requirements

| Requirement | Version / notes |
|-------------|-----------------|
| Server | **Paper** or **Purpur** 1.20.x (API 1.20) |
| Java | **17+** |
| Optional | **Geyser** + **Floodgate** for Bedrock players |

No Fabric/Forge client mods required.

---

## Installation

1. Build the plugin (or use a release JAR):
   ```bash
   mvn clean package -DskipTests
   ```
2. Copy `target/ClansOfKing.jar` into your server `plugins/` folder.
3. Start the server once to generate configs:
   - `plugins/ClansOfKing/config.yml`
   - `plugins/ClansOfKing/messages.yml`
   - `plugins/ClansOfKing/shop.yml`
   - `plugins/ClansOfKing/ranks.yml`
   - `plugins/ClansOfKing/clansofking.db` (SQLite)
4. Edit configs as needed, then restart (or reload if you add a reload command later).

---

## Commands

| Command | Description |
|---------|-------------|
| `/balance` | Show your money |
| `/pay <player> <amount>` | Pay another player |
| `/shop` | Open shop GUI |
| `/shell` | Open sell GUI |
| `/clan` | Open clan GUI (if in a clan) |
| `/clan create <name>` | Create a clan (you become KING) |
| `/clan join <name>` | Request to join a clan |
| `/clan accept [player]` | Accept join request (KING) |
| `/clan deny [player]` | Deny join request (KING) |
| `/clan map` | Clan map item + dominion GUI + home coords |
| `/clan kick <player>` | Kick member |
| `/clan promote <player>` | Promote to OFFICER |
| `/clan demote <player>` | Demote to MEMBER |
| `/clan leave` | Leave clan (not KING) |
| `/clan disband` | Disband clan (KING only) |

Aliases: `/bal`, `/money`, `/c`

---

## Permissions

| Permission | Default | Description |
|------------|---------|-------------|
| `clansofking.balance` | true | Use `/balance` |
| `clansofking.pay` | true | Use `/pay` |
| `clansofking.shop` | true | Use `/shop` |
| `clansofking.shell` | true | Use `/shell` |
| `clansofking.clan.create` | true | Create a clan |
| `clansofking.clan.join` | true | Join a clan |
| `clansofking.admin` | op | Admin access |
| `clansofking.*` | op | All permissions |

---

## Configuration

### `config.yml`
- Starting money, max money
- Initial claim size, expansion amount
- Clan name rules
- Clan XP / player XP sources
- Expansion torch rewards (wither, dragon, warden, raid)
- Protection toggles
- HUD settings

### `shop.yml`
Categories and items with `buy` / `sell` prices.

### `ranks.yml`
Player rank XP thresholds and clan level XP table.

### `messages.yml`
All player-facing messages (color codes with `&`).

---

## Gameplay loop (short)

1. `/clan create MyClan` → receive **Claim Podium**
2. Place podium → **100×100 claim**, **border walls**, **home**, optional map
3. Earn **Expansion Torches** (Wither, Ender Dragon, Warden, Raid)
4. Place torch on **border wall** → territory expands, walls move
5. `/shop` to buy, `/shell` to sell, `/clan` to manage members & requests
6. Progress ranks via XP; clan levels up from activity

---

## Building from source

```bash
git clone https://github.com/YOUR_USER/ClansOfKing.git
cd ClansOfKing
mvn clean package -DskipTests
```

Output: `target/ClansOfKing.jar` (shaded, includes SQLite JDBC).

**Stack:** Maven, Paper API 1.20.4, SQLite JDBC, Java 17.

---

## Project structure

```text
src/main/java/com/clansofking/
├── ClansOfKing.java          # Main plugin
├── command/                  # Commands + tab complete
├── clan/                     # Clan manager
├── economy/                  # Money
├── land/                     # Claims + walls
├── map/                      # Clan map overlay
├── rank/                     # Player ranks
├── player/                   # Player data + HUD
├── reward/                   # Boss/raid rewards
├── gui/                      # Shop, sell, clan GUIs
├── database/                 # SQLite
├── listener/                 # Events
├── model/                    # Data models
└── util/                     # Config, messages, items
```

---

## Geyser / Bedrock notes

- All primary UIs are **inventory GUIs** (work on Bedrock via Geyser).
- No client-side minimap mod; clan map is a **vanilla filled map** + server GUI list.
- Use **Floodgate** so Bedrock players get stable UUIDs.

---

## Keep inventory (server tip)

This is a **vanilla gamerule** on the Paper server (not Geyser):

```text
gamerule keepInventory true
```

Run it on the **Paper/Purpur console**, not the Geyser or proxy console.

---

## License

Specify your license here (e.g. MIT, GPL-3.0).  
If you have not chosen one yet, add a `LICENSE` file before publishing.

---

## Credits

- Built for Paper/Purpur + Geyser servers  
- Plugin name: **Clans of King**  
- Package: `com.clansofking`

---

## Disclaimer

This plugin modifies world blocks (claim walls) and player economy data.  
Always **backup** your world and `plugins/ClansOfKing/` before updating.
