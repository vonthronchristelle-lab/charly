<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Roblox Scripting Academy - Niveaux Infinis</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Fira+Code:wght@400;500&family=Inter:wght@400;600;800&display=swap');
        
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
        }

        .code-editor {
            font-family: 'Fira Code', monospace;
            background-color: #1e293b;
            resize: none;
            outline: none;
            font-size: 1.1rem;
        }

        .progress-bar {
            transition: width 0.5s ease-in-out;
        }

        .roblox-blue { color: #00a2ff; }
        .roblox-bg { background-color: #00a2ff; }
        
        .chat-bubble-ai {
            background: #1e293b;
            border-left: 4px solid #00a2ff;
        }

        .analysis-card {
            background: #1e293b;
            border: 2px solid #334155;
            border-radius: 16px;
            padding: 20px;
            margin-bottom: 12px;
            animation: slideIn 0.3s ease-out;
        }

        .checkpoint-gold {
            border-color: #fbbf24 !important;
            background: rgba(251, 191, 36, 0.1) !important;
        }

        .level-100-diamond {
            border-color: #22d3ee !important;
            background: rgba(34, 211, 238, 0.2) !important;
            animation: pulse-cyan 2s infinite;
        }

        @keyframes pulse-cyan {
            0% { box-shadow: 0 0 0 0 rgba(34, 211, 238, 0.4); }
            70% { box-shadow: 0 0 0 10px rgba(34, 211, 238, 0); }
            100% { box-shadow: 0 0 0 0 rgba(34, 211, 238, 0); }
        }

        @keyframes slideIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .level-item {
            background: #1e293b;
            border: 1px solid #334155;
            transition: all 0.2s ease;
            cursor: pointer;
        }
        
        .level-item:hover {
            border-color: #00a2ff;
            transform: translateX(5px);
            background: #334155;
        }

        .search-container {
            position: relative;
            margin-bottom: 1rem;
        }
        .search-icon {
            position: absolute;
            left: 1rem;
            top: 50%;
            transform: translateY(-50%);
            color: #64748b;
        }
        .search-input {
            width: 100%;
            background: #1e293b;
            border: 1px solid #334155;
            border-radius: 12px;
            padding: 0.75rem 1rem 0.75rem 2.5rem;
            color: white;
            outline: none;
        }

        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #0f172a; }
        ::-webkit-scrollbar-thumb { background: #334155; border-radius: 10px; }
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <header class="p-4 border-b border-slate-800 flex justify-between items-center bg-slate-900 shadow-xl">
        <div class="flex items-center gap-3">
            <div id="header-logo" class="w-10 h-10 roblox-bg rounded-lg flex items-center justify-center font-bold text-white text-xl">R</div>
            <h1 class="text-xl font-extrabold tracking-tight italic">ROBLOX <span class="roblox-blue">STUDIO MASTER</span></h1>
        </div>
        <div class="flex items-center gap-4">
            <div class="text-right">
                <p id="level-display" class="text-lg font-bold text-blue-400">Niveau 1</p>
                <p id="rank-display" class="text-[10px] text-slate-500 uppercase font-bold">Apprenti</p>
            </div>
            <div class="w-32 h-3 bg-slate-700 rounded-full overflow-hidden">
                <div id="progress-fill" class="progress-bar h-full roblox-bg" style="width: 1%"></div>
            </div>
        </div>
    </header>

    <main class="flex-1 flex flex-col md:flex-row overflow-hidden">
        <div class="w-full md:w-5/12 lg:w-4/12 flex flex-col border-r border-slate-800 bg-slate-900/50 relative">
            
            <button onclick="toggleLevelsMenu()" class="absolute left-4 top-20 z-10 w-10 h-10 bg-slate-800 border border-slate-700 rounded-full flex items-center justify-center hover:bg-blue-600 transition-colors shadow-lg">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 10h16M4 14h16M4 18h16" />
                </svg>
            </button>

            <div id="levels-overlay" class="absolute inset-0 bg-slate-900 z-20 p-6 hidden flex flex-col border-r border-slate-700">
                <div class="flex justify-between items-center mb-4">
                    <div>
                        <h3 class="text-xl font-black uppercase text-white">Ton Parcours</h3>
                        <p id="unlocked-count" class="text-[10px] text-blue-400 font-bold uppercase">1 débloqué</p>
                    </div>
                    <button onclick="toggleLevelsMenu()" class="text-slate-400 hover:text-white">Fermer</button>
                </div>

                <div class="search-container">
                    <span class="search-icon">🔍</span>
                    <input type="text" id="level-search" oninput="renderLevelsList()" placeholder="Rechercher un niveau..." class="search-input">
                </div>

                <div id="levels-list-full" class="space-y-3 overflow-y-auto flex-1 pr-2"></div>
            </div>

            <div class="flex border-b border-slate-800">
                <button onclick="switchTab('mission')" id="tab-mission" class="flex-1 py-4 text-sm font-black border-b-2 border-blue-500 bg-slate-800/50 text-white uppercase pl-12">Mission</button>
                <button onclick="switchTab('ai')" id="tab-ai" class="flex-1 py-4 text-sm font-black border-b-2 border-transparent text-slate-400 hover:text-white uppercase">IA ✨</button>
            </div>

            <div id="sidebar-content" class="flex-1 overflow-y-auto p-6 pt-12 flex flex-col">
                <div id="pane-mission" class="space-y-8">
                    <h2 id="mission-title" class="text-3xl font-black mb-4 italic text-white uppercase">Chargement...</h2>
                    <div id="mission-desc" class="text-slate-300 bg-slate-800/30 p-5 rounded-2xl border border-slate-700/50"></div>
                    <div class="p-5 bg-blue-500/10 border border-blue-500/20 rounded-2xl">
                        <h3 class="text-xs font-bold text-blue-400 uppercase mb-3">💡 Indice</h3>
                        <p id="mission-hint" class="text-sm italic text-slate-400 font-mono"></p>
                    </div>
                </div>

                <div id="pane-ai" class="hidden flex flex-col h-full">
                    <div id="ai-chat-history" class="flex-1 space-y-4 text-sm overflow-y-auto">
                        <div class="chat-bubble-ai p-5 rounded-2xl text-slate-300">Prêt à t'aider !</div>
                    </div>
                    <div class="mt-4 flex gap-2">
                        <input type="text" id="ai-input" class="flex-1 bg-slate-800 border border-slate-700 rounded-xl px-4 py-2 text-white outline-none">
                        <button onclick="askGemini()" class="px-4 py-2 bg-blue-600 rounded-xl font-bold">Envoyer</button>
                    </div>
                </div>
            </div>
        </div>

        <div class="flex-1 flex flex-col bg-slate-950">
            <div class="p-3 bg-slate-800 flex items-center justify-between">
                <span class="text-xs font-mono text-slate-400 px-4 py-1.5 bg-slate-900 rounded-full">Script.lua</span>
                <button onclick="runCode()" class="px-8 py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white rounded-xl text-sm font-black shadow-xl uppercase">Tester le Script</button>
            </div>
            <textarea id="editor" class="code-editor flex-1 p-10 text-blue-100 leading-relaxed" spellcheck="false"></textarea>
            <div class="h-1/4 border-t border-slate-800 bg-slate-900 p-6 font-mono text-xs overflow-y-auto" id="console"></div>
        </div>
    </main>

    <!-- Modal Success -->
    <div id="modal" class="fixed inset-0 bg-slate-950/95 hidden items-center justify-center z-50 p-6 backdrop-blur-md">
        <div class="bg-slate-900 border-2 border-emerald-500 rounded-[2.5rem] max-w-2xl w-full p-10 text-center">
            <h2 class="text-4xl font-black italic uppercase text-white mb-6">Mission Accomplie !</h2>
            <div id="analysis-container" class="text-left mb-8 max-h-60 overflow-y-auto"></div>
            <button onclick="nextLevelOrClose()" class="w-full py-5 roblox-bg text-white font-black rounded-2xl text-xl shadow-2xl">Niveau Suivant</button>
        </div>
    </div>

    <script>
        const apiKey = ""; 
        let currentLevel = 1;
        let maxUnlockedLevel = 1;
        let completedLevels = new Set();
        let logs = [];

        // Base de quêtes fixes (1 à 5)
        const BASE_QUESTS = [
            { lvl: 1, title: "Premier Message", func: "print()", desc: "Affiche <b>\"Hello Studio\"</b>.", hint: "print(\"Hello Studio\")", solution: "print(\"Hello Studio\")", check: (c, l) => l.includes("Hello Studio") },
            { lvl: 2, title: "Variables Flash", func: "local", desc: "Crée une variable <b>speed</b> valant 50 et affiche-la.", hint: "local speed = 50\nprint(speed)", solution: "local speed = 50\nprint(speed)", check: (c, l) => l.includes("50") },
            { lvl: 3, title: "Sécurité", func: "if", desc: "Affiche \"OK\" si <b>key</b> est 100.", hint: "local key = 100\nif key == 100 then\n  print(\"OK\")\nend", solution: "local key = 100\nif key == 100 then\n  print(\"OK\")\nend", check: (c, l) => l.includes("OK") },
            { lvl: 4, title: "Compteur", func: "for", desc: "Affiche de 1 à 5.", hint: "for i=1,5 do print(i) end", solution: "for i=1,5 do print(i) end", check: (c, l) => l.includes("1") && l.includes("5") },
            { lvl: 5, title: "Changement", func: "Color", desc: "Change la Color d'une Part en \"Blue\".", hint: "Part.Color = \"Blue\"", solution: "Part.Color = \"Blue\"", check: (c, l) => c.includes(".Color") && c.includes("Blue") }
        ];

        // Générateur de quêtes infinies
        function getQuest(lvl) {
            if (lvl <= BASE_QUESTS.length) return BASE_QUESTS[lvl-1];
            
            // Logique de génération automatique
            const types = ["Variable", "Boucle", "Condition", "Propriété"];
            const type = types[lvl % types.length];
            const val = lvl * 2;
            const isCheckpoint = lvl % 10 === 0;
            const isLevel100 = lvl === 100;

            let q = { lvl: lvl };

            if (isLevel100) {
                q.title = "MAÎTRE SUPRÊME";
                q.func = "THE FINAL BOSS";
                q.desc = `INCROYABLE ! Tu es au niveau 100. Pour valider ton titre, crée une boucle de 1 à 100 qui affiche "Master".`;
                q.hint = "for i=1,100 do print(\"Master\") end";
                q.check = (c, l) => l.filter(x => x === "Master").length >= 100;
            } else if (isCheckpoint) {
                q.title = `DÉFI DU BOSS ${lvl/10}`;
                q.func = "CHECKPOINT";
                q.desc = `Niveau spécial ! Affiche le message "Je suis un pro" exactement ${lvl} fois.`;
                q.hint = `for i=1,${lvl} do print(\"Je suis un pro\") end`;
                q.check = (c, l) => l.length >= lvl;
            } else {
                switch(type) {
                    case "Variable":
                        q.title = `Défi Valeur ${lvl}`;
                        q.func = "local";
                        q.desc = `Crée une variable <b>power</b> avec la valeur ${val} et affiche-la.`;
                        q.hint = `local power = ${val}\nprint(power)`;
                        q.check = (c, l) => l.includes(val.toString());
                        break;
                    case "Boucle":
                        q.title = `Séquence ${lvl}`;
                        q.func = "for";
                        q.desc = `Affiche les chiffres de 1 à ${lvl % 10 + 2}.`;
                        q.hint = `for i=1,${lvl % 10 + 2} do print(i) end`;
                        q.check = (c, l) => l.includes("1") && l.includes((lvl % 10 + 2).toString());
                        break;
                    case "Condition":
                        q.title = `Test Logic ${lvl}`;
                        q.func = "if";
                        q.desc = `Affiche "WIN" si <b>score</b> est égal à ${lvl}.`;
                        q.hint = `local score = ${lvl}\nif score == ${lvl} then print(\"WIN\") end`;
                        q.check = (c, l) => l.includes("WIN");
                        break;
                    case "Propriété":
                        q.title = `Modification ${lvl}`;
                        q.func = "Instance";
                        q.desc = `Change la Transparency d'une Part à 0.5.`;
                        q.hint = `Part.Transparency = 0.5`;
                        q.check = (c, l) => c.includes(".Transparency") && c.includes("0.5");
                        break;
                }
            }
            return q;
        }

        function toggleLevelsMenu() {
            const menu = document.getElementById('levels-overlay');
            menu.classList.toggle('hidden');
            if (!menu.classList.contains('hidden')) renderLevelsList();
        }

        function renderLevelsList() {
            const list = document.getElementById('levels-list-full');
            const search = document.getElementById('level-search').value.toLowerCase();
            list.innerHTML = "";
            
            // On affiche jusqu'au niveau max débloqué + 1 (pour voir le prochain)
            for (let i = 1; i <= maxUnlockedLevel + 1; i++) {
                const q = getQuest(i);
                if (!q.title.toLowerCase().includes(search) && !q.func.toLowerCase().includes(search)) continue;

                const isLocked = i > maxUnlockedLevel;
                const isCheckpoint = i % 10 === 0;
                const is100 = i === 100;
                
                const div = document.createElement('div');
                div.className = `level-item p-4 rounded-xl flex justify-between items-center border-l-4 ${isLocked ? 'opacity-50 grayscale' : ''}`;
                
                if (is100) div.classList.add('level-100-diamond');
                else if (isCheckpoint) div.classList.add('checkpoint-gold');
                else div.classList.add(completedLevels.has(i) ? 'border-emerald-500' : 'border-slate-700');

                div.onclick = () => { if(!isLocked) { loadLevel(i); toggleLevelsMenu(); } };
                
                div.innerHTML = `
                    <div>
                        <p class="text-xs font-bold text-blue-400">LVL ${i}</p>
                        <p class="font-black text-white uppercase text-sm">${q.title}</p>
                    </div>
                    <div class="text-xl">${isLocked ? '🔒' : (completedLevels.has(i) ? '✅' : '▶️')}</div>
                `;
                list.appendChild(div);
            }
            document.getElementById('unlocked-count').innerText = `${maxUnlockedLevel} débloqué(s)`;
        }

        function runCode() {
            const code = document.getElementById('editor').value;
            const consoleEl = document.getElementById('console');
            logs = [];
            consoleEl.innerHTML = "";

            try {
                const customPrint = (m) => {
                    logs.push(m.toString());
                    consoleEl.innerHTML += `<div class="text-blue-300 mb-1">[PRINT]: ${m}</div>`;
                };

                // Conversion basique Lua -> JS pour la simulation
                let jsCode = code
                    .replace(/local\s+/g, 'let ')
                    .replace(/print/g, 'customPrint')
                    .replace(/==/g, '===')
                    .replace(/for\s+(\w+)\s*=\s*(\d+)\s*,\s*(\d+)\s+do/g, 'for(let $1=$2; $1<=$3; $1++){')
                    .replace(/if\s+(.*)\s+then/g, 'if($1){')
                    .replace(/end/g, '}');

                const Part = { Color: "", Transparency: 0 };
                const fn = new Function('customPrint', 'Part', jsCode);
                fn(customPrint, Part);

                const q = getQuest(currentLevel);
                if (q.check(code, logs)) {
                    completedLevels.add(currentLevel);
                    if (currentLevel === maxUnlockedLevel) maxUnlockedLevel++;
                    document.getElementById('modal').style.display = 'flex';
                }
            } catch (e) {
                consoleEl.innerHTML += `<div class="text-red-400">[ERREUR]: ${e.message}</div>`;
            }
        }

        function loadLevel(lvl) {
            currentLevel = lvl;
            const q = getQuest(lvl);
            document.getElementById('mission-title').innerText = q.title;
            document.getElementById('mission-desc').innerHTML = q.desc;
            document.getElementById('mission-hint').innerText = q.hint;
            document.getElementById('level-display').innerText = `Niveau ${lvl}`;
            
            // Mise à jour du rang
            let rank = "Apprenti";
            if (lvl >= 100) rank = "MAÎTRE SUPRÊME 💎";
            else if (lvl >= 50) rank = "Expert Studio 🛠️";
            else if (lvl >= 20) rank = "Scripteur Avancé";
            else if (lvl >= 10) rank = "Codeur Junior";
            document.getElementById('rank-display').innerText = rank;

            // Couleur logo
            const logo = document.getElementById('header-logo');
            if (lvl >= 100) logo.style.background = "linear-gradient(45deg, #22d3ee, #a855f7)";
            else if (lvl % 10 === 0) logo.style.backgroundColor = "#fbbf24";
            else logo.style.backgroundColor = "#00a2ff";

            document.getElementById('progress-fill').style.width = `${(lvl % 10 === 0 ? 100 : (lvl % 10) * 10)}%`;
            document.getElementById('editor').value = `-- Mission Niveau ${lvl}\n`;
        }

        function nextLevelOrClose() {
            document.getElementById('modal').style.display = 'none';
            loadLevel(currentLevel + 1);
        }

        function switchTab(tab) {
            document.getElementById('pane-mission').classList.toggle('hidden', tab !== 'mission');
            document.getElementById('pane-ai').classList.toggle('hidden', tab !== 'ai');
        }

        window.onload = () => loadLevel(1);
    </script>
</body>
