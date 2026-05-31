# ⚔️ Obsidian Life RPG

**Obsidian Life RPG** is an autonomous life management framework that turns your knowledge base into a balanced role-playing game. No rigid databases or fragile JSON files: the system dynamically aggregates your stats on the fly from your daily notes using a powerful JavaScript engine. 

Your real-life actions are converted into game progress, while laziness and procrastination deal damage to your character.

---

## ✨ Core Features

* ⚙️ **Dynamic Engine (Zero-DB):** The core (`RPG_Engine.js`) scans the vault and calculates balances, levels, and stats in milliseconds. Your data is 100% safe and distributed across standard Markdown files.
* ⏱️ **Smart Time Management Console:** Built-in timer in your daily logs. It can chain sessions back-to-back, auto-stops if left unattended (>12 hours), and parses tags directly from the logs.
* 🐉 **Epic Projects (Hybrid Pool):** Assign difficulty ranks (from D to S) to your quests or set custom reward pools manually. Burnout protection: rewards are paid out in milestones—at 25%, 50%, 75%, and 100%.
* 🔥 **Yearly Heatmap & Streaks:** An optimized GitHub-style productivity matrix (Shadow DOM render) with absolute focus thresholds.
* 🏆 **Hall of Fame & Shop:** Over 30 automatic achievements (including a "Hall of Shame" for procrastination) and an in-game store with credit limit support.

---

## ⚖️ Game Mechanics (RPG Core)

Your life is now measured by three main resources:

| Resource | Description | How to gain / lose |
| :--- | :--- | :--- |
| **Experience (XP)** | Your progression bar. Every 1000 XP increases your Level and unlocks new Titles. | **+** Focus, task completion, writing Zettel notes.<br>**-** Never decreases. |
| **Gold (GP)** | Currency for buying legal rest (YouTube, games) and items in the Shop. | **+** Quest completion, focus, selling loot.<br>**-** Spending in the Shop. |
| **Energy (HP)** | Discipline bar (Max 100). If HP drops to 0, gold income is cut by 50%. | **+** Regular focus (1h+/day), Healing Potions.<br>**-** Failed tasks, lack of activity. |

---

## 📂 Vault Architecture

The framework is organized logically and requires no complex navigation:

* **`00_System`**
  The heart of the system. Houses the `RPG_Engine.js` engine, configs, and Templater templates.
* **`01_Dashboard`**
  Your command center. 
  * `Homepage` — The main citadel to start your day.
  * `00_Profile` — HUD, time analytics, Heatmap, and Debt Board.
  * `01_Hall_of_Fame` — Achievement showcase.
  * `02_Shop` & `03_Backpack` — Reward store and your inventory.
* **`02_Projects`**
  Epic Quest board. Manage long-term goals with automatic quartile reward calculations.
* **`04_Zettelkasten`**
  Your second brain. Creating conceptual notes passively grants XP and develops your character.
* **`05_Journal`**
  Adventure chronicles. Daily logs, focus/rest time tracker console, and atomic habits block.

---

## 🚀 Installation & Quick Start

The system requires three Community Plugins to run: **Dataview**, **CustomJS**, and **Templater**.

### Step 1. Vault Setup
1. Download the repository and open it as a new Vault in Obsidian.
2. Turn off `Safe Mode` to allow plugins to run.

### Step 2. Plugin Setup
1. **Dataview:** Go to plugin settings and enable `Enable JavaScript Queries` and `Enable DataviewJS`.
2. **CustomJS:** Make sure the plugin is enabled. It will automatically pick up the `RPG_Engine` class from the vault files.
3. **Templater:** Set the template folder to `00_System` and enable `Trigger Templater on new file creation`.

### Step 3. Launch the Game
Open the **`Homepage.md`** page in the root of the vault. Click on today's date in the calendar (Chronology Scroll) to create your first journal and start the focus timer!

---

## 🛠️ Support & Extensibility

The system is designed to be highly modular:
* Want to add new items? Edit the `itemDb` object inside `RPG_Engine.js`.
* Want new achievements? Add them to the `allAchievements` array inside the `01_Hall_of_Fame.md` file.