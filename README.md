# FastFurnace (Minecraft / Skript)

FastFurnace is a **Minecraft server plugin implemented in Skript**.
It modifies in-game furnace behavior on Paper/Spigot servers and integrates
with Vault-based economies.

---

## Requirements

- Minecraft Paper / Spigot (tested on 1.21.x)
- Skript (older builds supported)
- Vault
- A Vault-compatible economy plugin

---

## Core Features

- Custom furnace item (`Fast Furnace`)
- No fuel required
- Hopper-compatible
- Smelting speed ramps over time
- Cost increases as speed increases
- Charges Vault currency per item
- Batched player messages (every 10 items)
- Owner + last-interactor charging logic
- Admin cleanup and purge commands
- Protection against ghost / stale furnace data

---

## Smelting Behavior

### Speed Ramp
- Starts at **3x speed**
- Increases by **+1x every 10 seconds**
- Maximum speed: **8x**
- Speed resets if:
  - Input slot becomes empty
  - Furnace is stopped
  - Furnace is broken

### Cost Ramp
- Base cost: **3 Vault currency per item**
- Each speed increase adds **+1 Vault currency per item**
- Cost = `base + speed stage`

Example:
- 3x speed → 3 per item
- 4x speed → 4 per item
- 8x speed → 8 per item

---

## Player Messaging

- Players are charged per item
- A summary message is sent every 10 items smelted
- Message includes:
  - Furnace world and coordinates
  - Item type
  - Total cost for the batch
  - Current smelting speed

Example message:
Fast Furnace @ world (123, 64, -42) → Smelted 10 IRON_INGOT for 47 Vault currency at 6x

---

## Commands

### Player Commands

- `/fastfurnace stopall`  
  Stops all Fast Furnaces owned by the player.

- `/fastfurnace startall`  
  Re-enables all Fast Furnaces owned by the player.

### Admin Commands

- `/fastfurnace give`  
  Gives the Fast Furnace item.

- `/fastfurnace purge <playerName>`  
  Hard-purges all tracked Fast Furnaces owned by a player. Useful for ghost furnaces or player complaints.

- `/fastfurnacecleanup`  
  Removes stale furnace entries where the block no longer exists (e.g. world edits, crashes, rollback restores).

---

## Ownership & Charging Rules

- Each furnace has an owner (placer)
- The last player to interact within a short timeout is charged instead
- If the payer is offline or lacks Vault currency:
  - Furnace immediately stops
  - No further smelting occurs

---

## Technical Notes

- Fuel slot is unused (burn time is forced)
- Furnace speed is implemented via tick-based cook-time acceleration
- All state is stored via Skript variables keyed by block location
- Cleanup commands are required if furnaces are removed while Skript is unloaded

---

## Known Limitations

- Furnace item name/lore cannot be recovered from placed blocks
- Purge-by-name relies on stored owner name (not UUID)
- Speed and cost reset when smelting stops
- Partial batch messages are not sent (only full batches of 10)

---

## Installation

1. Place `fastfurnace.sk` into `/plugins/Skript/scripts/`
2. Reload Skript:
   `/sk reload fastfurnace`
3. Ensure Vault and an economy plugin are loaded

---

## Configuration

All tuning is done via the `options:` section at the top of the script:
- Base cost
- Speed ramp timing
- Speed cap
- Interaction timeout
