# 🗡️ Obsidian RPG Life Management System
Turn your life, habits, and learning into a full-fledged RPG game directly inside your Obsidian vault. 

[Читать на русском (Russian Version)](README_RU.md)

---

## 1. System Architecture

The system is designed as a decoupled architecture within Obsidian:
* **Core Engine (Backend):** A standalone class-based script `RPG_Engine.js` written in pure JavaScript. It operates via the `CustomJS` plugin. The engine bypasses resource-heavy text parsing, performing all calculations on the fly by querying the Obsidian metadata cache (YAML Frontmatter). The interface response time is under 4ms.
* **Interface Layer (Frontend):** Dynamic interactive widgets built using `DataviewJS` and asynchronous HTML/CSS rendering that react to internal system events (`CustomEvent`).

---

## 2. Core Loop

Every real-world action is logged through Obsidian interfaces and instantly converted into numerical character attributes: Experience (XP), Gold Points (GP), and Health Points (HP).

### Activity Conversion Table

| Real-World Activity | System Status | Reward / Penalty | Psychological Role |
| :--- | :--- | :--- | :--- |
| Deep Work / Study | `🔴 Focus` | +1 XP and +1 GP per 1 minute | Motivation for sustained concentration |
| Household Chores / Routine | `🪵 Routine` | +0.5 XP and +0.5 GP per 1 minute | Completing basic daily obligations |
| Deliberate Rest / Sleep | `⚪ Rest` | 0 XP / 0 GP (Balance tracking) | Burnout protection, legalizing downtime |
| Procrastination / Time Waste | `⚠️ Waste` | **-1 GP** per 1 minute | Direct economic loss for laziness |
| Writing Atomic Notes | Concept note inside ZK | +10 XP per concept card | Developing the Second Brain (Zettelkasten) |

---

## 3. Game Mechanics

### Seamless Switching (Link Start)
The time tracking panel in the daily note reads the Unix timestamp of the previous session's completion from the activity log. When the `🔗 Link start` checkbox is activated, the end time of the last session is automatically set as the start time for the new timer. This eliminates friction and time loss when switching directly between different tasks.

### Quest Combat System
Tasks in the to-do list act as opponents. Rewards or penalties are strictly tied to the difficulty specified in the frontmatter or inline fields of the task:

* **Easy Quest (`easy`):** +20 XP, +25 GP. Failure: -5 HP.
* **Medium Quest (`medium`):** +50 XP, +50 GP. Failure: -15 HP.
* **Hard Quest (`hard`):** +100 XP, +100 GP. Failure: -30 HP.
* **Micro-tasks (no tag):** Any standard Obsidian checkbox is automatically treated as a micro-errand, awarding a fixed +5 XP and +5 GP.
* **Partial Completion:** Checkboxes marked with `[/]` grant exactly 50% of the base quest reward. Failure marked with `[-]` inflicts damage on your HP.

### Leveling & Ranks Mathematics
Character progression is linear. Leveling up requires exactly 1000 experience points (XP). 

Current Level Calculation:
$$Level = \lfloor \frac{\text{Total XP}}{1000} \rfloor + 1$$

Remaining XP for the progress bar:
$$\text{XP Current} = \text{Total XP} \pmod{1000}$$

Depending on the level, system titles are assigned to the character: from `🔮 Novice Adept` (levels 1–9) to `🌌 Supreme Database Sage` (level 100+).

### Survival Mechanics (HP) & Trauma State
The maximum energy pool is 100 HP. Health is dynamically recalculated by the Core based on the balance of penalties and healing:
* **Inactivity Penalty:** If the time spent in `🔴 Focus` mode equals 0 over the past 24 hours, the character takes -10 HP of damage.
* **Discipline Healing:** Spending more than 60 minutes in Focus mode per day without failing any quests restores +10 HP.
* **Trauma State (Debuff):** If HP falls to 0 or below, the character is critically injured. **All gold earnings are cut exactly by 50%**. To remove the debuff, you must purchase and consume a Healing Potion from the shop.

### Economy & Credit Limit
GP (Gold Points) serve as a tool to legalize your rest. By completing tasks, you earn capital to buy entertainment tokens in the Shop (e.g., 1 hour of video games costs 220 GP).
* **Credit Limit:** The system allows you to make purchases with a negative balance, switching the Shop to `On Credit` mode. However, upon reaching a hard limit of **-500 GP**, the Shop locks completely until the debt is paid off through real activity.

### Board of Debts
Uncompleted quests from previous days do not simply disappear. A widget on the homepage aggregates them, sorts them chronologically (from oldest to newest), and lists them. The debt remains on the panel until the task is completed or force-failed with the `[-]` status (which inflicts the corresponding HP damage).

---

## 4. YAML Metadata Specification

To ensure the engine and Dataview function properly, metadata for key files must strictly match the following structures.

### Daily Note (`05_Journal/YYYY-MM-DD.md`)
```yaml
---
timer_state: null       # Active mode string (Focus/Routine/Rest/Waste)
timer_start: null       # Start time in ISO format
timer_elapsed: 0        # Accumulated pause time in milliseconds
timer_tags: ""          # Inputted session tags
last_timer_end: null    # Unix timestamp of the last stop for "Link Start"
focus_mins: 0           # Aggregated Focus minutes for the day
routine_mins: 0         # Aggregated Routine minutes for the day
rest_mins: 0            # Aggregated Rest minutes for the day
waste_mins: 0           # Aggregated Waste minutes for the day
daily_xp: 0             # XP earned from time tracking
daily_gold: 0           # GP earned from time tracking
hp_lost: 0              # Manual health penalty if needed
tags_stat: {}           # Minute-by-minute tag statistics (auto-generated)
---
```
### Character Profile (`01_Dashboard/00_Profile.md`)
```yaml
---
gold_spent: 0              # Total gold spent in the shop
bonus_xp: 0                # Total experience gained from achievements
bonus_gold: 0              # Total gold gained from achievements
total_potions_consumed: 0  # Total healing potions consumed all-time
potions_history: {}        # Potion consumption history by date { YYYY-MM-DD: 1 }
inventory:                 # Current stock of purchased items
  potion: 0
  walk: 0
  tea: 0
  youtube: 0
  games: 0
  social: 0
  dayoff: 0
archive_cutoff: "2000-01-01" # Date filter for the Board of Debts
---
```
## 5. Quick Start 
1. Download the repository archive and extract it to a local folder.
2. Open Obsidian, click "Open folder as vault", and select the extracted directory.
3. Navigate to Obsidian Settings -> Community Plugins and ensure that Dataview, CustomJS, and Templater are enabled.
4. In the Dataview settings, strictly enable: "Enable JavaScript Queries" and "Enable Inline JavaScript Queries".
5. Open the 01_Dashboard/Homepage.md file. The system is ready to use.
## 6. License & Support 
You can modify the core math in `99_System/Scripts/RPG_Engine.js` to fit your needs.
If this engine helped you organize your life and beat procrastination, feel free to support the developer:
