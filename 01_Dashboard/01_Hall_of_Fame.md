---
unlocked_badges: []
---

```dataviewjs
(async () => {
    const container = this.container;
    container.empty();

    if (!window.customJS || !customJS.RPG_Engine) {
        container.createEl('div', { text: "⚠️ Engine loading...", attr: {style: "color: #e74c3c;"} });
        return;
    }

    try {
        const engine = customJS.RPG_Engine;
        const PROFILE_PATH = "01_Dashboard/00_Profile.md";
        const profilePage = dv.page(PROFILE_PATH);
        const profileFile = app.vault.getAbstractFileByPath(PROFILE_PATH);
        
        const currentPath = dv.current().file.path;
        const hofFile = app.vault.getAbstractFileByPath(currentPath);
        const fm = dv.current();
        
        const unlockedBadges = Array.isArray(fm.unlocked_badges) ? fm.unlocked_badges : [];
        const ctx = await engine.getSharedContext(dv, profilePage);

        let coreFocus = ctx.globalTimeStats ? (ctx.globalTimeStats.totalMinutes || 0) : 0;
        let tagFocus = 0;
        if (ctx.globalTagMap) {
            for (let k in ctx.globalTagMap) {
                let keyLower = k.toLowerCase();
                if (!keyLower.includes("waste") && !keyLower.includes("rest")) {
                    tagFocus += ctx.globalTagMap[k];
                }
            }
        }
        const totalFocusMins = Math.max(coreFocus, tagFocus);
        const totalFocusHours = Math.floor(totalFocusMins / 60);

        const totalQuests = ctx.counters.totalTasks;
        const totalZk = ctx.zettelCount || 0;
        const totalProcrastinate = (ctx.globalTimeStats && ctx.globalTimeStats.totalProcrastinateMinutes) ? ctx.globalTimeStats.totalProcrastinateMinutes : 0;

        const categories = {
            "focus": { title: "⏳ Focus Mastery", desc: "Rewards for total pure focus time.", bg: "rgba(255, 255, 255, 0.02)" },
            "quests": { title: "⚔️ Quest Discipline", desc: "Rewards for successfully completed tasks.", bg: "rgba(255, 255, 255, 0.02)" },
            "zk": { title: "🧠 Neural Network (Zettelkasten)", desc: "Progress in creating knowledge cards.", bg: "rgba(255, 255, 255, 0.02)" },
            "eco": { title: "⚖️ Financial Capital", desc: "The scale of your current gold reserves in the wallet.", bg: "rgba(255, 255, 255, 0.02)" },
            "shame": { title: "💀 Hall of Shame", desc: "Ironic titles for wasted time.", bg: "rgba(231, 76, 60, 0.02)" }
        };

        const allAchievements = [
            { id: "f_1", cat: "focus", type: "auto", icon: "🥉", title: "First Blood", desc: "Gain 1 hour of focus", req: 1, current: totalFocusHours, rewardXp: 50, rewardGp: 25 },
            { id: "f_5", cat: "focus", type: "auto", icon: "🥉", title: "Getting Started", desc: "Gain 5 hours of focus", req: 5, current: totalFocusHours, rewardXp: 100, rewardGp: 50 },
            { id: "f_10", cat: "focus", type: "auto", icon: "🥈", title: "Intern", desc: "Gain 10 hours of focus", req: 10, current: totalFocusHours, rewardXp: 200, rewardGp: 100 },
            { id: "f_25", cat: "focus", type: "auto", icon: "🥈", title: "Nerd Habit", desc: "Gain 25 hours of focus", req: 25, current: totalFocusHours, rewardXp: 350, rewardGp: 150 },
            { id: "f_50", cat: "focus", type: "auto", icon: "🥇", title: "Iron Will", desc: "Gain 50 hours of focus", req: 50, current: totalFocusHours, rewardXp: 500, rewardGp: 250 },
            { id: "f_100", cat: "focus", type: "auto", icon: "🏅", title: "Truth Seeker", desc: "Gain 100 hours of focus", req: 100, current: totalFocusHours, rewardXp: 1000, rewardGp: 500 },
            { id: "f_250", cat: "focus", type: "auto", icon: "💠", title: "Flow Master", desc: "Gain 250 hours of focus", req: 250, current: totalFocusHours, rewardXp: 2000, rewardGp: 1000 },
            { id: "f_500", cat: "focus", type: "auto", icon: "💎", title: "Time Magus", desc: "Gain 500 hours of focus", req: 500, current: totalFocusHours, rewardXp: 3000, rewardGp: 1500 },
            { id: "f_1000", cat: "focus", type: "auto", icon: "🌌", title: "Reality Warper", desc: "Gain 1000 hours of focus", req: 1000, current: totalFocusHours, rewardXp: 6000, rewardGp: 3000 },
            { id: "f_5000", cat: "focus", type: "auto", icon: "👑", title: "The Absolute", desc: "Gain 5000 hours of focus", req: 5000, current: totalFocusHours, rewardXp: 25000, rewardGp: 10000 },
            { id: "q_1", cat: "quests", type: "auto", icon: "📝", title: "First Step", desc: "Complete 1 quest", req: 1, current: totalQuests, rewardXp: 10, rewardGp: 10 },
            { id: "q_10", cat: "quests", type: "auto", icon: "📝", title: "Gaining Momentum", desc: "Complete 10 quests", req: 10, current: totalQuests, rewardXp: 50, rewardGp: 25 },
            { id: "q_50", cat: "quests", type: "auto", icon: "⚔️", title: "Guild Novice", desc: "Complete 50 quests", req: 50, current: totalQuests, rewardXp: 100, rewardGp: 50 },
            { id: "q_100", cat: "quests", type: "auto", icon: "🛡️", title: "Mercenary", desc: "Complete 100 quests", req: 100, current: totalQuests, rewardXp: 250, rewardGp: 125 },
            { id: "q_250", cat: "quests", type: "auto", icon: "🎯", title: "Bounty Hunter", desc: "Complete 250 quests", req: 250, current: totalQuests, rewardXp: 500, rewardGp: 250 },
            { id: "q_500", cat: "quests", type: "auto", icon: "🦅", title: "Schedule Terror", desc: "Complete 500 quests", req: 500, current: totalQuests, rewardXp: 1000, rewardGp: 500 },
            { id: "q_1000", cat: "quests", type: "auto", icon: "🗡️", title: "Task Terminator", desc: "Complete 1000 quests", req: 1000, current: totalQuests, rewardXp: 2500, rewardGp: 1250 },
            { id: "q_5000", cat: "quests", type: "auto", icon: "🌋", title: "Efficiency Machine", desc: "Complete 5000 quests", req: 5000, current: totalQuests, rewardXp: 12000, rewardGp: 5000 },
            { id: "z_10", cat: "zk", type: "auto", icon: "💡", title: "Spark of Knowledge", desc: "Create 10 conceptual notes", req: 10, current: totalZk, rewardXp: 100, rewardGp: 50 },
            { id: "z_50", cat: "zk", type: "auto", icon: "📂", title: "Archivist", desc: "Create 50 conceptual notes", req: 50, current: totalZk, rewardXp: 250, rewardGp: 125 },
            { id: "z_100", cat: "zk", type: "auto", icon: "📖", title: "Library Keeper", desc: "Create 100 conceptual notes", req: 100, current: totalZk, rewardXp: 800, rewardGp: 400 },
            { id: "z_250", cat: "zk", type: "auto", icon: "👁️", title: "Thought Structure", desc: "Create 250 conceptual notes", req: 250, current: totalZk, rewardXp: 1500, rewardGp: 750 },
            { id: "z_500", cat: "zk", type: "auto", icon: "🕸️", title: "Mind Architect", desc: "Create 500 conceptual notes", req: 500, current: totalZk, rewardXp: 3000, rewardGp: 1500 },
            { id: "z_1000", cat: "zk", type: "auto", icon: "🧠", title: "Second Brain", desc: "Create 1000 conceptual notes", req: 1000, current: totalZk, rewardXp: 6000, rewardGp: 3000 },
            { id: "z_2000", cat: "zk", type: "auto", icon: "🌌", title: "Digital Demiurge", desc: "Create 2000 conceptual notes", req: 2000, current: totalZk, rewardXp: 10000, rewardGp: 5000 },
            { id: "z_5000", cat: "zk", type: "auto", icon: "🌐", title: "Universal Mind", desc: "Create 5000 conceptual notes", req: 5000, current: totalZk, rewardXp: 25000, rewardGp: 10000 },
            { id: "e_100", cat: "eco", type: "auto", icon: "👛", title: "First Capital", desc: "Accumulate 100 GP", req: 100, current: ctx.currentGold, rewardXp: 50, rewardGp: 0 },
            { id: "e_500", cat: "eco", type: "auto", icon: "👛", title: "Piggy Bank", desc: "Accumulate 500 GP", req: 500, current: ctx.currentGold, rewardXp: 150, rewardGp: 0 },
            { id: "e_1k", cat: "eco", type: "auto", icon: "💰", title: "Capitalist", desc: "Accumulate 1000 GP", req: 1000, current: ctx.currentGold, rewardXp: 500, rewardGp: 0 },
            { id: "e_2.5k", cat: "eco", type: "auto", icon: "💰", title: "Wealthy Mage", desc: "Accumulate 2500 GP", req: 2500, current: ctx.currentGold, rewardXp: 1000, rewardGp: 0 },
            { id: "e_5k", cat: "eco", type: "auto", icon: "🏦", title: "Tycoon", desc: "Accumulate 5000 GP", req: 5000, current: ctx.currentGold, rewardXp: 2000, rewardGp: 0 },
            { id: "s_1", cat: "shame", type: "irony", icon: "🤡", title: "Smells Like Laziness", desc: "Waste your first hour on procrastination", req: 60, current: totalProcrastinate, rewardXp: 0, rewardGp: 0 },
            { id: "s_10", cat: "shame", type: "irony", icon: "🤡", title: "TikTok Sponsor", desc: "Accumulate 10 hours of procrastination", req: 600, current: totalProcrastinate, rewardXp: 0, rewardGp: 0 },
            { id: "s_50", cat: "shame", type: "irony", icon: "🛋️", title: "Couch Knight", desc: "Accumulate 50 hours of procrastination", req: 3000, current: totalProcrastinate, rewardXp: 0, rewardGp: 0 },
            { id: "s_100", cat: "shame", type: "irony", icon: "🧟", title: "General of the Couch Army", desc: "Accumulate 100 hours of procrastination", req: 6000, current: totalProcrastinate, rewardXp: 0, rewardGp: 0 }
        ];

        const renderOrder = ["focus", "quests", "zk", "eco", "shame"];
        let earnedXp = 0, earnedGp = 0;

        allAchievements.forEach(a => {
            if (unlockedBadges.includes(a.id)) {
                earnedXp += a.rewardXp || 0;
                earnedGp += a.rewardGp || 0;
            }
        });

        const summary = container.createEl('div');
        summary.style.cssText = "display: flex; gap: 30px; background: rgba(255,255,255,0.03); padding: 15px 25px; border-radius: 10px; border: 1px solid rgba(255,255,255,0.08); margin-bottom: 30px;";

        summary.innerHTML = "<div style='display:flex; flex-direction:column;'><span style='color:var(--text-muted); font-size:0.85em; text-transform:uppercase;'>Unlocked</span><span style='font-size:1.5em; font-weight:bold;'>" + unlockedBadges.length + " / " + allAchievements.length + "</span></div>" +
        "<div style='display:flex; flex-direction:column;'><span style='color:var(--text-muted); font-size:0.85em; text-transform:uppercase;'>Bonus XP</span><span style='font-size:1.5em; font-weight:bold; color:#2ecc71;'>+" + earnedXp + "</span></div>" +
        "<div style='display:flex; flex-direction:column;'><span style='color:var(--text-muted); font-size:0.85em; text-transform:uppercase;'>Bonus GP</span><span style='font-size:1.5em; font-weight:bold; color:#f1c40f;'>+" + earnedGp + "</span></div>";

        const wrapper = container.createEl('div');

        for (let catKey of renderOrder) {
            const catAchs = allAchievements.filter(a => a.cat === catKey);
            if (catAchs.length === 0) continue; 
            
            const box = wrapper.createEl('div');
            box.style.cssText = "background: " + categories[catKey].bg + "; border: 1px solid rgba(255,255,255,0.06); border-radius: 12px; padding: 20px; margin-bottom: 30px; box-shadow: 0 4px 10px rgba(0,0,0,0.15);";

            const head = box.createEl('div');
            head.style.cssText = "margin-bottom: 15px; border-bottom: 1px dashed rgba(255,255,255,0.1); padding-bottom: 10px;";
            head.createEl('h3', { text: categories[catKey].title }).style.cssText = "margin: 0; font-size: 1.3em; font-weight: bold; color: var(--text-title);";
            head.createEl('p', { text: categories[catKey].desc }).style.cssText = "margin: 5px 0 0 0; font-size: 0.85em; color: var(--text-muted); font-style: italic;";

            const list = box.createEl('div');
            list.style.cssText = "display: flex; flex-direction: column; gap: 10px;";

            catAchs.forEach(ach => {
                const isUnlocked = unlockedBadges.includes(ach.id);
                let isReady = false;
                let progressPct = 0;

                if (!isUnlocked) {
                    isReady = ach.current >= ach.req;
                    progressPct = Math.min(100, Math.floor((ach.current / ach.req) * 100));
                }

                const row = list.createEl('div');
                let rowStyles = "display: flex; align-items: center; background: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.04); padding: 12px 15px; border-radius: 8px; justify-content: space-between; gap: 15px;";
                
                if (isUnlocked) rowStyles += "border-color: rgba(241,196,15,0.3); background: rgba(241,196,15,0.03);";
                else if (isReady) rowStyles += ach.type === 'irony' ? "border-color: rgba(231,76,60,0.4); background: rgba(231,76,60,0.04);" : "border-color: rgba(46,204,113,0.4); background: rgba(46,204,113,0.04);";
                else rowStyles += "opacity: 0.55; filter: grayscale(50%);";
                row.style.cssText = rowStyles;

                const leftSide = row.createEl('div');
                leftSide.style.cssText = "display: flex; align-items: center; gap: 15px; flex-grow: 1;";
                leftSide.createEl('span', { text: ach.icon }).style.cssText = "font-size: 1.8em; min-width: 35px; text-align: center;";
                
                const textBlock = leftSide.createEl('div');
                textBlock.createEl('div', { text: ach.title }).style.cssText = "font-weight: bold; font-size: 1em; color: var(--text-normal);";
                textBlock.createEl('div', { text: ach.desc }).style.cssText = "font-size: 0.8em; color: var(--text-muted); margin-top: 2px;";

                const rightSide = row.createEl('div');
                rightSide.style.cssText = "display: flex; align-items: center; gap: 20px; flex-shrink: 0;";

                if (!isUnlocked) {
                    const prog = rightSide.createEl('div');
                    prog.style.cssText = "display: flex; flex-direction: column; align-items: flex-end; width: 100px;";
                    const barBg = prog.createEl('div');
                    barBg.style.cssText = "width: 100%; background: rgba(0,0,0,0.3); height: 5px; border-radius: 3px; overflow: hidden; margin-bottom: 3px;";
                    barBg.createEl('div').style.cssText = "width: " + progressPct + "%; height: 100%; background: #3498db;";
                    prog.createEl('span', { text: Math.floor(ach.current) + "/" + ach.req }).style.cssText = "font-size: 0.7em; color: var(--text-muted); font-weight: bold;";
                }

                if (ach.rewardXp > 0 || ach.rewardGp > 0) {
                    const rew = rightSide.createEl('div');
                    rew.style.cssText = "display: flex; flex-direction: column; font-size: 0.8em; font-weight: bold; width: 75px; text-align: right;";
                    if (ach.rewardXp > 0) rew.createEl('span', { text: "+" + ach.rewardXp + " XP", attr: { style: "color:#2ecc71;" } });
                    if (ach.rewardGp > 0) rew.createEl('span', { text: "+" + ach.rewardGp + " GP", attr: { style: "color:#f1c40f;" } });
                }

                const action = rightSide.createEl('div');
                action.style.cssText = "width: 120px; text-align: right;";
                const btn = action.createEl('button');
                let btnStyles = "width: 100%; padding: 6px 10px; border: none; border-radius: 5px; font-weight: bold; font-size: 0.8em; cursor: pointer; text-align: center;";
                
                if (isUnlocked) {
                    btnStyles += "background: transparent; color: #f1c40f; border: 1px solid #f1c40f; cursor: default;";
                    btn.innerText = "🏆 UNLOCKED";
                } else if (isReady) {
                    btnStyles += ach.type === 'irony' ? "background: #e74c3c; color: #fff;" : "background: #2ecc71; color: #000;";
                    btn.innerText = ach.type === 'irony' ? "SHAMEFUL" : "CLAIM";
                    
                    btn.addEventListener('click', async () => {
                        btn.disabled = true; 
                        btn.innerText = "⏳ ...";
                        
                        try {
                            await app.fileManager.processFrontMatter(hofFile, (fMatter) => {
                                if (!fMatter.unlocked_badges) fMatter.unlocked_badges = [];
                                if (!fMatter.unlocked_badges.includes(ach.id)) fMatter.unlocked_badges.push(ach.id);
                            });
                            
                            if ((ach.rewardXp > 0 || ach.rewardGp > 0) && profileFile) {
                                await app.fileManager.processFrontMatter(profileFile, (pMatter) => {
                                    pMatter.bonus_xp = (parseInt(pMatter.bonus_xp) || 0) + ach.rewardXp;
                                    pMatter.bonus_gold = (parseInt(pMatter.bonus_gold) || 0) + ach.rewardGp;
                                });
                            }

                            engine.invalidateCache(); 
                            new Notice("Reward claimed: " + ach.title);
                        } catch (err) {
                            new Notice("Error while saving: " + err.message);
                            btn.disabled = false;
                        } finally {
                            setTimeout(() => {
                                app.commands.executeCommandById("dataview:dataview-refresh-views");
                            }, 100);
                        }
                    });
                } else {
                    btnStyles += "background: rgba(255,255,255,0.05); color: var(--text-muted); cursor: default; border: 1px solid rgba(255,255,255,0.08);";
                    btn.innerText = "🔒 LOCKED";
                }
                btn.style.cssText = btnStyles;
            });
        }
    } catch (err) {
        container.createEl('div', { text: "⚠️ System Error: " + err.message, attr: {style: "color: #e74c3c;"} });
    }
})();
```
