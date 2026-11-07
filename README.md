<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>THE CUNIVERSE - Erschaffe dein Universum</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;800;900&family=Rajdhani:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Rajdhani', sans-serif;
            background-color: #030014;
            color: #e2e8f0;
            overflow: hidden;
            user-select: none;
            touch-action: none;
        }
        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }
        .glass-panel {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.12);
        }
        .glass-button {
            background: linear-gradient(135deg, rgba(255,255,255,0.1) 0%, rgba(255,255,255,0.03) 100%);
            backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .glass-button:hover:not(:disabled) {
            border-color: rgba(168, 85, 247, 0.6);
            box-shadow: 0 0 20px rgba(168, 85, 247, 0.4);
            transform: translateY(-2px);
        }
        .glass-button:disabled {
            opacity: 0.4;
            cursor: not-allowed;
            filter: grayscale(80%);
        }
        .glow-text-purple {
            text-shadow: 0 0 12px rgba(168, 85, 247, 0.8), 0 0 24px rgba(168, 85, 247, 0.4);
        }
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.6);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(168, 85, 247, 0.4);
            border-radius: 3px;
        }
        
        /* Dynamic Drawer Transition */
        .inventory-drawer {
            transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s ease, margin-top 0.3s ease;
            max-height: 0px;
            opacity: 0;
            overflow: hidden;
            pointer-events: none;
        }
        .inventory-drawer.open {
            max-height: 600px;
            opacity: 1;
            margin-top: 0.75rem;
            pointer-events: auto;
        }

        .quest-drawer {
            transition: max-height 0.4s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.3s ease, margin-top 0.3s ease;
            max-height: 0px;
            opacity: 0;
            overflow: hidden;
            pointer-events: none;
        }
        .quest-drawer.open {
            max-height: 450px;
            opacity: 1;
            margin-top: 0.5rem;
            pointer-events: auto;
        }
    </style>
</head>
<body class="h-screen w-screen relative flex flex-col justify-between overflow-hidden">

    <!-- Canvas for Cosmic Simulation -->
    <canvas id="universeCanvas" class="absolute inset-0 w-full h-full cursor-grab active:cursor-grabbing z-0"></canvas>

    <!-- Overlay Screen: Level 0 - The Big Bang -->
    <div id="bigBangScreen" class="absolute inset-0 z-50 flex items-center justify-center p-4 bg-slate-950/90 backdrop-blur-md">
        <div class="glass-panel max-w-lg w-full p-8 rounded-3xl border border-purple-500/30 text-center shadow-2xl space-y-6 relative overflow-hidden">
            <div class="absolute -top-24 -left-24 w-48 h-48 bg-purple-600/20 rounded-full blur-3xl pointer-events-none"></div>
            <div class="absolute -bottom-24 -right-24 w-48 h-48 bg-cyan-600/20 rounded-full blur-3xl pointer-events-none"></div>
            
            <div class="space-y-2">
                <h1 class="text-4xl sm:text-5xl font-extrabold font-orbitron tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-purple-400 via-pink-400 to-cyan-400 glow-text-purple">
                    THE CUNIVERSE
                </h1>
                <p class="text-slate-400 text-sm sm:text-base">Erschaffe dein eigenes Universum aus der Dunkelheit.</p>
            </div>

            <div class="space-y-4 pt-4">
                <label for="universeNameInput" class="block text-left text-xs uppercase font-semibold text-purple-300 tracking-wider">
                    Wie lautet der Name deines Universums?
                </label>
                <input type="text" id="universeNameInput" maxlength="24" placeholder="z. B. Nova Prime" 
                    class="w-full bg-slate-900/80 border border-slate-700 focus:border-purple-500 focus:ring-2 focus:ring-purple-500/40 rounded-xl px-4 py-3 text-lg font-orbitron text-purple-200 placeholder-slate-600 outline-none transition">
                <p id="nameError" class="text-red-400 text-xs text-left hidden">Bitte gib einen gültigen Namen ein!</p>
            </div>

            <button id="btnBigBang" class="w-full py-4 rounded-xl font-orbitron font-bold text-lg text-white bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-500 hover:to-pink-500 shadow-lg shadow-purple-600/30 hover:shadow-purple-500/50 transition-all transform active:scale-95 flex items-center justify-center gap-3">
                <i class="fa-solid font-bold fa-atom text-xl animate-spin"></i> URKNALL AUSLÖSEN
            </button>
        </div>
    </div>

    <!-- Main Game UI Header -->
    <header id="gameHeader" class="relative z-10 p-4 flex flex-wrap justify-between items-center gap-4 pointer-events-none transition-opacity duration-500 opacity-0">
        <!-- Left: Universe Name & Level Info -->
        <div class="glass-panel px-5 py-3 rounded-2xl flex items-center gap-4 border border-purple-500/20 pointer-events-auto">
            <div class="w-10 h-10 rounded-xl bg-purple-900/50 border border-purple-400/40 flex items-center justify-center text-purple-300 shadow-inner">
                <i class="fa-solid fa-globe text-xl"></i>
            </div>
            <div>
                <h2 id="displayUniverseName" class="font-orbitron font-bold text-lg text-slate-100 tracking-wide">---</h2>
                <div class="flex items-center gap-2">
                    <span id="displayLevelTag" class="text-xs px-2 py-0.5 rounded font-orbitron font-semibold bg-purple-500/20 text-purple-300 border border-purple-500/30">
                        LVL 1: STERNE
                    </span>
                    <span id="displayXpText" class="text-xs text-slate-400 font-mono">0 / 100 XP</span>
                    <span id="displayXpRate" class="text-xs text-emerald-400 font-mono font-semibold ml-1">(+0 XP/min)</span>
                </div>
            </div>
        </div>

        <!-- Center: XP Progress Bar -->
        <div class="glass-panel px-5 py-3 rounded-2xl flex-1 max-w-md pointer-events-auto flex flex-col justify-center gap-1.5 border border-slate-700/50">
            <div class="flex justify-between items-center text-xs font-orbitron">
                <span class="text-slate-400">Kosmische Energie (XP)</span>
                <span id="xpPercent" class="text-cyan-400 font-bold">0%</span>
            </div>
            <div class="w-full h-2.5 bg-slate-900/80 rounded-full overflow-hidden border border-slate-700/50 p-0.5">
                <div id="xpProgressBar" class="h-full bg-gradient-to-r from-purple-500 via-pink-500 to-cyan-400 rounded-full transition-all duration-300 w-0"></div>
            </div>
        </div>

        <!-- Right: Utility Controls (Audio, Reset) -->
        <div class="glass-panel px-3 py-2 rounded-2xl flex items-center gap-2 pointer-events-auto border border-slate-700/50">
            <button id="btnAudioToggle" title="Audio ein/aus" class="w-10 h-10 rounded-xl glass-button flex items-center justify-center text-slate-300 hover:text-cyan-400">
                <i class="fa-solid fa-volume-xmark text-lg"></i>
            </button>
            <button id="btnResetGame" title="Universum zurücksetzen" class="w-10 h-10 rounded-xl glass-button flex items-center justify-center text-slate-300 hover:text-red-400">
                <i class="fa-solid fa-rotate-left text-lg"></i>
            </button>
        </div>
    </header>

    <!-- Floating Canvas Controls & Quests Sidebar Overlay -->
    <div class="absolute top-24 left-4 z-10 flex flex-col gap-3 pointer-events-auto w-64 max-w-[calc(100vw-2rem)]">
        <!-- Compact Collapsible Quest Panel -->
        <div id="questToggleHeader" class="glass-panel px-3.5 py-2 rounded-xl border border-purple-500/30 cursor-pointer flex items-center justify-between shadow-xl hover:border-purple-400/60 transition">
            <div class="flex items-center gap-2.5">
                <i class="fa-solid fa-scroll text-purple-400 text-sm"></i>
                <span class="font-orbitron font-bold text-xs text-purple-200 tracking-wider">QUESTS</span>
                <span id="questActiveBadge" class="text-[10px] font-mono px-1.5 py-0.2 rounded bg-purple-500/20 text-purple-300 border border-purple-500/30">0 Aktiv</span>
            </div>
            <i id="questToggleIcon" class="fa-solid fa-chevron-down text-xs text-slate-400 transition-transform duration-300"></i>
        </div>
        
        <!-- Quest Drawer Body -->
        <div id="questDrawer" class="quest-drawer w-full">
            <div class="glass-panel p-3.5 rounded-2xl border border-purple-500/30 space-y-2.5 shadow-xl">
                <div id="questList" class="space-y-2 text-xs font-orbitron max-h-80 overflow-y-auto pr-1">
                    <!-- Quests dynamically injected here -->
                </div>
            </div>
        </div>
    </div>

    <div class="absolute top-20 right-4 z-10 flex flex-col gap-2 pointer-events-auto">
        <button id="btnZoomIn" class="w-9 h-9 rounded-lg glass-button flex items-center justify-center text-slate-300 hover:text-purple-300"><i class="fa-solid fa-plus"></i></button>
        <button id="btnZoomOut" class="w-9 h-9 rounded-lg glass-button flex items-center justify-center text-slate-300 hover:text-purple-300"><i class="fa-solid fa-minus"></i></button>
        <button id="btnResetView" class="w-9 h-9 rounded-lg glass-button flex items-center justify-center text-slate-300 hover:text-purple-300" title="Kamera zentrieren"><i class="fa-solid fa-crosshairs"></i></button>
    </div>

    <!-- Center Interactive Notification / Event Log -->
    <div id="gameNotification" class="absolute top-24 left-1/2 -translate-x-1/2 z-20 pointer-events-none transition-all duration-500 opacity-0 transform -translate-y-4">
        <div class="glass-panel px-6 py-2.5 rounded-full border border-cyan-500/40 text-cyan-200 font-orbitron text-sm flex items-center gap-2 shadow-lg shadow-cyan-500/10">
            <i class="fa-solid fa-sparkles text-cyan-400"></i>
            <span id="notificationText">Ein neues Sternensystem ist entstanden!</span>
        </div>
    </div>

    <!-- Bottom Collapsible Action Bar & Dashboard -->
    <footer id="gameFooter" class="relative z-10 p-3 sm:p-5 flex flex-col max-w-5xl mx-auto w-full pointer-events-none transition-opacity duration-500 opacity-0 items-center">
        
        <!-- Ultra Compact Inventory Toggle Button / Pill -->
        <div id="inventoryToggleHeader" class="glass-panel px-4 py-2 rounded-full border border-purple-500/40 pointer-events-auto cursor-pointer flex items-center gap-3 shadow-2xl hover:border-purple-400/80 hover:bg-slate-900/90 transition">
            <i class="fa-solid fa-box-open text-purple-400 text-xs"></i>
            <span class="font-orbitron font-bold text-xs text-purple-200 tracking-wider">INVENTAR & AKTIONEN</span>
            <span class="text-[10px] text-slate-400 font-mono px-2 py-0.5 rounded-full bg-purple-950/60 border border-purple-500/20">
                <b id="compactObjectCount" class="text-cyan-300">0</b> Objekte
            </span>
            <i id="toggleIcon" class="fa-solid fa-chevron-up text-xs text-slate-400 transition-transform duration-300 ml-1"></i>
        </div>

        <!-- Expandable Inventory Drawer -->
        <div id="inventoryDrawer" class="inventory-drawer w-full">
            <div class="flex flex-col md:flex-row justify-between items-stretch md:items-end gap-4">
                
                <!-- Left Panel: Cosmic Object Counters -->
                <div class="glass-panel p-4 rounded-2xl border border-slate-700/50 flex flex-wrap gap-4 text-xs font-orbitron">
                    <div class="flex items-center gap-2.5">
                        <div class="w-3 h-3 rounded-full bg-yellow-400 shadow-[0_0_8px_rgba(250,204,21,0.8)]"></div>
                        <span class="text-slate-400">Sonne: <b id="countYellow" class="text-white">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <div class="w-3 h-3 rounded-full bg-red-500 shadow-[0_0_8px_rgba(239,68,68,0.8)]"></div>
                        <span class="text-slate-400">Roter Riese: <b id="countRed" class="text-white">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <div class="w-3 h-3 rounded-full bg-cyan-400 shadow-[0_0_8px_rgba(34,211,238,0.8)]"></div>
                        <span class="text-slate-400">Blauer Zwerg: <b id="countBlue" class="text-white">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <div class="w-3 h-3 rounded-full bg-slate-100 shadow-[0_0_8px_rgba(255,255,255,0.9)] animate-pulse"></div>
                        <span class="text-slate-400">Neutronenstern: <b id="countNeutron" class="text-white">0</b></span>
                    </div>
                    <div class="w-full h-px bg-slate-800 my-0.5"></div>
                    <div class="flex items-center gap-2.5">
                        <i class="fa-solid fa-dharmachakra text-purple-400 text-sm"></i>
                        <span class="text-slate-400">Galaxien: <b id="countGalaxies" class="text-purple-300">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <i class="fa-solid fa-globe text-emerald-400 text-sm"></i>
                        <span class="text-slate-400">Planeten: <b id="countPlanets" class="text-emerald-300">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <i class="fa-solid fa-circle text-purple-900 border border-purple-400 rounded-full text-xs"></i>
                        <span class="text-slate-400">Schwarze Löcher: <b id="countBlackHoles" class="text-pink-400">0</b></span>
                    </div>
                    <div class="flex items-center gap-2.5">
                        <i class="fa-solid fa-meteor text-amber-500 text-sm"></i>
                        <span class="text-slate-400">Supernovas: <b id="countSupernovas" class="text-amber-400">0</b></span>
                    </div>
                </div>

                <!-- Right Panel: Main Creation Action Buttons -->
                <div class="flex flex-wrap sm:flex-nowrap gap-3 justify-end flex-1">
                    
                    <!-- 1. Button: Create Star (Level 1) -->
                    <div class="flex-1 sm:flex-none flex flex-col items-center">
                        <button id="btnCreateStar" class="glass-button w-full sm:w-36 py-3 px-3 rounded-2xl font-orbitron font-bold text-xs text-yellow-200 border-yellow-500/30 flex items-center justify-center gap-2 group">
                            <i class="fa-solid fa-sun text-yellow-400 group-hover:rotate-45 transition-transform duration-300"></i>
                            <span>STERN</span>
                        </button>
                        <div id="cooldownStarContainer" class="w-full mt-1.5 hidden">
                            <div class="flex justify-between text-[9px] font-mono text-slate-400 mb-0.5 px-1">
                                <span>CD</span><span id="timerStar">60s</span>
                            </div>
                            <div class="w-full h-1 bg-slate-900 rounded-full overflow-hidden">
                                <div id="barStar" class="h-full bg-yellow-400 transition-all duration-300 w-0"></div>
                            </div>
                        </div>
                    </div>

                    <!-- 2. Button: Create Galaxy (Level 2) -->
                    <div class="flex-1 sm:flex-none flex flex-col items-center">
                        <button id="btnCreateGalaxy" disabled class="glass-button w-full sm:w-36 py-3 px-3 rounded-2xl font-orbitron font-bold text-xs text-purple-200 border-purple-500/30 flex items-center justify-center gap-2 group">
                            <i class="fa-solid fa-dharmachakra text-purple-400 group-hover:rotate-180 transition-transform duration-700"></i>
                            <span>GALAXIE</span>
                        </button>
                        <div id="cooldownGalaxyContainer" class="w-full mt-1.5 hidden">
                            <div class="flex justify-between text-[9px] font-mono text-slate-400 mb-0.5 px-1">
                                <span>CD</span><span id="timerGalaxy">10:00</span>
                            </div>
                            <div class="w-full h-1 bg-slate-900 rounded-full overflow-hidden">
                                <div id="barGalaxy" class="h-full bg-purple-500 transition-all duration-300 w-0"></div>
                            </div>
                        </div>
                    </div>

                    <!-- 3. Button: Create Planet (Level 3) -->
                    <div class="flex-1 sm:flex-none flex flex-col items-center">
                        <button id="btnCreatePlanet" disabled class="glass-button w-full sm:w-36 py-3 px-3 rounded-2xl font-orbitron font-bold text-xs text-emerald-200 border-emerald-500/30 flex items-center justify-center gap-2 group">
                            <i class="fa-solid fa-globe text-emerald-400 group-hover:rotate-180 transition-transform duration-700"></i>
                            <span>PLANET</span>
                        </button>
                        <div id="cooldownPlanetContainer" class="w-full mt-1.5 hidden">
                            <div class="flex justify-between text-[9px] font-mono text-slate-400 mb-0.5 px-1">
                                <span>CD</span><span id="timerPlanet">3:00</span>
                            </div>
                            <div class="w-full h-1 bg-slate-900 rounded-full overflow-hidden">
                                <div id="barPlanet" class="h-full bg-emerald-500 transition-all duration-300 w-0"></div>
                            </div>
                        </div>
                        <span id="planetReqText" class="text-[9px] font-mono text-slate-500 mt-1">Lvl 3</span>
                    </div>

                    <!-- 4. Button: Collapse Black Hole (Level 4) -->
                    <div class="flex-1 sm:flex-none flex flex-col items-center">
                        <button id="btnCollapseBlackHole" disabled class="glass-button w-full sm:w-40 py-3 px-3 rounded-2xl font-orbitron font-bold text-xs text-pink-200 border-pink-500/30 flex items-center justify-center gap-2 group">
                            <i class="fa-solid fa-circle-radiation text-pink-400 group-hover:scale-110 transition-transform"></i>
                            <span>SCHWARZES LOCH</span>
                        </button>
                        <div id="cooldownBhContainer" class="w-full mt-1.5 hidden">
                            <div class="flex justify-between text-[9px] font-mono text-slate-400 mb-0.5 px-1">
                                <span>CD</span><span id="timerBh">60:00</span>
                            </div>
                            <div class="w-full h-1 bg-slate-900 rounded-full overflow-hidden">
                                <div id="barBh" class="h-full bg-pink-500 transition-all duration-300 w-0"></div>
                            </div>
                        </div>
                        <span id="redGiantReqText" class="text-[9px] font-mono text-slate-500 mt-1">Ab Lvl 4</span>
                    </div>

                    <!-- 5. Button: Create Supernova (Level 5) -->
                    <div class="flex-1 sm:flex-none flex flex-col items-center">
                        <button id="btnCreateSupernova" disabled class="glass-button w-full sm:w-36 py-3 px-3 rounded-2xl font-orbitron font-bold text-xs text-amber-200 border-amber-500/30 flex items-center justify-center gap-2 group">
                            <i class="fa-solid fa-meteor text-amber-400 group-hover:scale-125 transition-transform"></i>
                            <span>SUPERNOVA</span>
                        </button>
                        <div id="cooldownSnContainer" class="w-full mt-1.5 hidden">
                            <div class="flex justify-between text-[9px] font-mono text-slate-400 mb-0.5 px-1">
                                <span>CD</span><span id="timerSn">300:00</span>
                            </div>
                            <div class="w-full h-1 bg-slate-900 rounded-full overflow-hidden">
                                <div id="barSn" class="h-full bg-amber-500 transition-all duration-300 w-0"></div>
                            </div>
                        </div>
                        <span id="snReqText" class="text-[9px] font-mono text-slate-500 mt-1">Ab Lvl 5</span>
                    </div>

                </div>
            </div>
        </div>
    </footer>

    <!-- Custom Modal Dialog -->
    <div id="customModal" class="absolute inset-0 z-50 flex items-center justify-center p-4 bg-slate-950/80 backdrop-blur-sm hidden">
        <div class="glass-panel max-w-md w-full p-6 rounded-2xl border border-slate-700 shadow-2xl text-center space-y-4">
            <h3 id="modalTitle" class="text-xl font-orbitron font-bold text-purple-300">Bestätigung</h3>
            <p id="modalMessage" class="text-slate-300 text-sm">Möchtest du diese Aktion wirklich ausführen?</p>
            <div class="flex justify-center gap-3 pt-2">
                <button id="btnModalCancel" class="px-4 py-2 rounded-xl glass-button text-xs font-orbitron text-slate-400 hover:text-white">Abbrechen</button>
                <button id="btnModalConfirm" class="px-5 py-2 rounded-xl bg-purple-600 hover:bg-purple-500 text-xs font-orbitron font-bold text-white shadow-lg shadow-purple-600/30">Bestätigen</button>
            </div>
        </div>
    </div>

    <!-- Main Game Logic Script -->
    <script>
        const state = {
            universeName: "",
            level: 1,
            xp: 0,
            starCooldownEnd: 0,
            galaxyCooldownEnd: 0,
            planetCooldownEnd: 0,
            bhCooldownEnd: 0,
            snCooldownEnd: 0,
            quests: {
                autoStars: false,
                autoGalaxies: false,
                autoBlackholes: false,
                autoSupernovas: false
            },
            objects: [],
            bigBangDone: false,
            audioEnabled: false,
            lastSavedTimestamp: Date.now()
        };

        const STAR_COOLDOWN_MS = 60 * 1000;         // 1 minute
        const GALAXY_COOLDOWN_MS = 10 * 60 * 1000;   // 10 minutes
        const PLANET_COOLDOWN_MS = 3 * 60 * 1000;    // 3 minutes
        const BH_COOLDOWN_MS = 60 * 60 * 1000;       // 60 minutes
        const SN_COOLDOWN_MS = 300 * 60 * 1000;      // 5 hours

        const LEVEL_THRESHOLDS = {
            1: 100,
            2: 1000,
            3: 50000,
            4: 100000,
            5: 1000000
        };

        const camera = { x: 0, y: 0, zoom: 1, isDragging: false, startX: 0, startY: 0 };

        const canvas = document.getElementById('universeCanvas');
        const ctx = canvas.getContext('2d');
        const bigBangScreen = document.getElementById('bigBangScreen');
        const universeNameInput = document.getElementById('universeNameInput');
        const nameError = document.getElementById('nameError');
        const btnBigBang = document.getElementById('btnBigBang');

        const gameHeader = document.getElementById('gameHeader');
        const gameFooter = document.getElementById('gameFooter');
        const displayUniverseName = document.getElementById('displayUniverseName');
        const displayLevelTag = document.getElementById('displayLevelTag');
        const displayXpText = document.getElementById('displayXpText');
        const xpProgressBar = document.getElementById('xpProgressBar');
        const xpPercent = document.getElementById('xpPercent');
        const displayXpRate = document.getElementById('displayXpRate');
        const questList = document.getElementById('questList');

        const inventoryToggleHeader = document.getElementById('inventoryToggleHeader');
        const inventoryDrawer = document.getElementById('inventoryDrawer');
        const toggleIcon = document.getElementById('toggleIcon');
        const toggleStateLabel = document.getElementById('toggleStateLabel');
        const compactObjectCount = document.getElementById('compactObjectCount');

        const countYellow = document.getElementById('countYellow');
        const countRed = document.getElementById('countRed');
        const countBlue = document.getElementById('countBlue');
        const countNeutron = document.getElementById('countNeutron');
        const countPlanets = document.getElementById('countPlanets');
        const countGalaxies = document.getElementById('countGalaxies');
        const countBlackHoles = document.getElementById('countBlackHoles');
        const countSupernovas = document.getElementById('countSupernovas');

        const btnCreateStar = document.getElementById('btnCreateStar');
        const cooldownStarContainer = document.getElementById('cooldownStarContainer');
        const timerStar = document.getElementById('timerStar');
        const barStar = document.getElementById('barStar');

        const btnCreateGalaxy = document.getElementById('btnCreateGalaxy');
        const cooldownGalaxyContainer = document.getElementById('cooldownGalaxyContainer');
        const timerGalaxy = document.getElementById('timerGalaxy');
        const barGalaxy = document.getElementById('barGalaxy');

        const btnCreatePlanet = document.getElementById('btnCreatePlanet');
        const cooldownPlanetContainer = document.getElementById('cooldownPlanetContainer');
        const timerPlanet = document.getElementById('timerPlanet');
        const barPlanet = document.getElementById('barPlanet');
        const planetReqText = document.getElementById('planetReqText');

        const btnCollapseBlackHole = document.getElementById('btnCollapseBlackHole');
        const cooldownBhContainer = document.getElementById('cooldownBhContainer');
        const timerBh = document.getElementById('timerBh');
        const barBh = document.getElementById('barBh');
        const redGiantReqText = document.getElementById('redGiantReqText');

        const btnCreateSupernova = document.getElementById('btnCreateSupernova');
        const cooldownSnContainer = document.getElementById('cooldownSnContainer');
        const timerSn = document.getElementById('timerSn');
        const barSn = document.getElementById('barSn');
        const snReqText = document.getElementById('snReqText');

        const btnAudioToggle = document.getElementById('btnAudioToggle');
        const btnResetGame = document.getElementById('btnResetGame');

        const customModal = document.getElementById('customModal');
        const modalTitle = document.getElementById('modalTitle');
        const modalMessage = document.getElementById('modalMessage');
        const btnModalCancel = document.getElementById('btnModalCancel');
        const btnModalConfirm = document.getElementById('btnModalConfirm');

        let modalConfirmCallback = null;
        let audioCtx = null;
        let isInventoryOpen = false;
        let isQuestOpen = false;

        // Toggle Inventory Drawer logic
        inventoryToggleHeader.addEventListener('click', () => {
            isInventoryOpen = !isInventoryOpen;
            if (isInventoryOpen) {
                inventoryDrawer.classList.add('open');
                toggleIcon.style.transform = 'rotate(180deg)';
            } else {
                inventoryDrawer.classList.remove('open');
                toggleIcon.style.transform = 'rotate(0deg)';
            }
        });

        // Toggle Quest Drawer logic
        const questToggleHeader = document.getElementById('questToggleHeader');
        const questDrawer = document.getElementById('questDrawer');
        const questToggleIcon = document.getElementById('questToggleIcon');
        const questActiveBadge = document.getElementById('questActiveBadge');

        questToggleHeader.addEventListener('click', () => {
            isQuestOpen = !isQuestOpen;
            if (isQuestOpen) {
                questDrawer.classList.add('open');
                questToggleIcon.style.transform = 'rotate(180deg)';
            } else {
                questDrawer.classList.remove('open');
                questToggleIcon.style.transform = 'rotate(0deg)';
            }
        });

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        function playCosmicSound(type) {
            if (!state.audioEnabled || !audioCtx) return;
            try {
                const now = audioCtx.currentTime;
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                
                if (type === 'bigbang') {
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(40, now);
                    osc.frequency.exponentialRampToValueAtTime(320, now + 1.2);
                    gain.gain.setValueAtTime(0.6, now);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + 2.5);
                    osc.start(now); osc.stop(now + 2.5);
                } else if (type === 'star') {
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(440, now);
                    osc.frequency.exponentialRampToValueAtTime(880, now + 0.2);
                    gain.gain.setValueAtTime(0.2, now);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + 0.3);
                    osc.start(now); osc.stop(now + 0.3);
                } else if (type === 'supernova') {
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(120, now);
                    osc.frequency.exponentialRampToValueAtTime(40, now + 1.5);
                    gain.gain.setValueAtTime(0.7, now);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + 2.0);
                    osc.start(now); osc.stop(now + 2.0);
                }
            } catch(e) {}
        }

        let particles = [];
        function spawnParticles(x, y, count, colorList, speedMax=12) {
            for (let i = 0; i < count; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = Math.random() * speedMax + 1;
                particles.push({
                    x, y,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed,
                    radius: Math.random() * 4 + 1,
                    color: colorList[Math.floor(Math.random() * colorList.length)],
                    alpha: 1,
                    decay: Math.random() * 0.02 + 0.005
                });
            }
        }

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function saveGame() {
            state.lastSavedTimestamp = Date.now();
            localStorage.setItem('cuniverse_save_v6', JSON.stringify(state));
        }

        function loadGame() {
            const saved = localStorage.getItem('cuniverse_save_v6');
            if (saved) {
                try {
                    const parsed = JSON.parse(saved);
                    Object.assign(state, parsed);
                    
                    const stars = state.objects.filter(o => o.kind === 'star');
                    state.objects.forEach(o => {
                        if (o.kind === 'planet' && typeof o.hostIndex === 'number' && stars[o.hostIndex]) {
                            o.host = stars[o.hostIndex];
                        }
                    });

                    if (state.bigBangDone && state.lastSavedTimestamp) {
                        const now = Date.now();
                        const elapsedSec = Math.max(0, (now - state.lastSavedTimestamp) / 1000);
                        const rate = calculateXpPerMinute();
                        if (elapsedSec > 5 && rate > 0) {
                            const earned = (rate / 60) * elapsedSec;
                            state.xp += earned;
                            checkLevelProgression();
                            setTimeout(() => showNotification(`Willkommen zurück! ${Math.floor(earned)} XP generiert.`), 1000);
                        }
                    }
                    return true;
                } catch(e) {}
            }
            return false;
        }

        let notifTimeout = null;
        function showNotification(text) {
            const el = document.getElementById('gameNotification');
            document.getElementById('notificationText').textContent = text;
            el.classList.remove('opacity-0', '-translate-y-4');
            el.classList.add('opacity-100', 'translate-y-0');
            clearTimeout(notifTimeout);
            notifTimeout = setTimeout(() => {
                el.classList.remove('opacity-100', 'translate-y-0');
                el.classList.add('opacity-0', '-translate-y-4');
            }, 3500);
        }

        function showModal(title, msg, onConfirm) {
            modalTitle.textContent = title;
            modalMessage.textContent = msg;
            modalConfirmCallback = onConfirm;
            customModal.classList.remove('hidden');
        }

        btnModalCancel.addEventListener('click', () => { customModal.classList.add('hidden'); modalConfirmCallback = null; });
        btnModalConfirm.addEventListener('click', () => {
            customModal.classList.add('hidden');
            if (modalConfirmCallback) modalConfirmCallback();
            modalConfirmCallback = null;
        });

        btnBigBang.addEventListener('click', () => {
            const name = universeNameInput.value.trim();
            if (!name) { nameError.classList.remove('hidden'); return; }
            nameError.classList.add('hidden');
            state.universeName = name;
            state.level = 1;
            state.xp = 0;
            state.bigBangDone = true;
            initAudio();
            playCosmicSound('bigbang');
            spawnParticles(0, 0, 300, ['#a855f7', '#ec4899', '#38bdf8', '#facc15', '#ffffff']);

            bigBangScreen.classList.add('transition-opacity', 'duration-700', 'opacity-0');
            setTimeout(() => {
                bigBangScreen.classList.add('hidden');
                gameHeader.classList.remove('opacity-0');
                gameFooter.classList.remove('opacity-0');
                updateUI();
                saveGame();
                showNotification(`Universum "${state.universeName}" erschaffen!`);
            }, 700);
        });

        function createStarAction(silent = false) {
            const rand = Math.random();
            let type = 'yellow';
            let name = 'Sonnähnlicher Stern';
            if (rand >= 0.25 && rand < 0.5) { type = 'red'; name = 'Roter Riese'; }
            else if (rand >= 0.5 && rand < 0.75) { type = 'blue'; name = 'Blauer Zwerg'; }
            else if (rand >= 0.75) { type = 'neutron'; name = 'Neutronenstern'; }

            const dist = Math.random() * 400 + 50;
            const angle = Math.random() * Math.PI * 2;

            state.objects.push({
                kind: 'star', type,
                x: Math.cos(angle) * dist, y: Math.sin(angle) * dist,
                orbitRadius: dist, orbitAngle: angle,
                orbitSpeed: (Math.random() * 0.002 + 0.0005) * (Math.random() > 0.5 ? 1 : -1),
                pulse: Math.random() * Math.PI
            });

            state.xp += 1;
            checkLevelProgression();
            if (!silent) {
                initAudio(); playCosmicSound('star');
                showNotification(`${name} erstellt! (+1 XP)`);
            }
            updateUI(); saveGame();
        }

        function createGalaxyAction(silent = false) {
            if (state.level < 2) return;
            const dist = Math.random() * 600 + 300;
            const angle = Math.random() * Math.PI * 2;

            state.objects.push({
                kind: 'galaxy',
                x: Math.cos(angle) * dist, y: Math.sin(angle) * dist,
                rotation: Math.random() * Math.PI * 2,
                rotSpeed: (Math.random() * 0.003 + 0.001) * (Math.random() > 0.5 ? 1 : -1),
                arms: Math.floor(Math.random() * 3) + 2,
                radius: Math.random() * 50 + 70,
                color: ['#c084fc', '#f472b6', '#38bdf8'][Math.floor(Math.random() * 3)]
            });

            state.xp += 250;
            checkLevelProgression();
            if (!silent) {
                showNotification("Neue Galaxie geformt! (+250 XP)");
            }
            updateUI(); saveGame();
        }

        function createBlackHoleAction(silent = false) {
            if (state.level < 4) return;
            const redGiants = state.objects.filter(o => o.kind === 'star' && o.type === 'red');
            if (redGiants.length === 0) return;

            const target = redGiants[0];
            spawnParticles(target.x, target.y, 70, ['#ec4899', '#8b5cf6']);
            target.kind = 'blackhole';
            target.type = 'blackhole';
            target.pulse = 0;

            state.xp += 1000;
            checkLevelProgression();
            if (!silent) {
                showNotification("Schwarzes Loch aus rotem Riesen geformt! (+1000 XP)");
            }
            updateUI(); saveGame();
        }

        function createSupernovaAction(silent = false) {
            if (state.level < 5) return;
            const dist = Math.random() * 500 + 100;
            const angle = Math.random() * Math.PI * 2;
            const x = Math.cos(angle) * dist;
            const y = Math.sin(angle) * dist;

            spawnParticles(x, y, 200, ['#f59e0b', '#ef4444', '#ffffff', '#38bdf8'], 18);

            state.objects.push({
                kind: 'supernova',
                x, y,
                radius: 40,
                alpha: 1
            });

            state.xp += 10000;
            checkLevelProgression();
            if (!silent) {
                initAudio(); playCosmicSound('supernova');
                showNotification("GIGANTISCHE SUPERNOVA EXPLODIERT! (+10.000 XP)");
            }
            updateUI(); saveGame();
        }

        btnCreateStar.addEventListener('click', () => {
            const now = Date.now();
            if (now < state.starCooldownEnd) return;
            createStarAction();
            state.starCooldownEnd = now + STAR_COOLDOWN_MS;
        });

        btnCreateGalaxy.addEventListener('click', () => {
            if (state.level < 2) return;
            const now = Date.now();
            if (now < state.galaxyCooldownEnd) return;
            createGalaxyAction();
            state.galaxyCooldownEnd = now + GALAXY_COOLDOWN_MS;
        });

        btnCreatePlanet.addEventListener('click', () => {
            if (state.level < 3) return;
            const now = Date.now();
            if (now < state.planetCooldownEnd) return;

            const suns = state.objects.filter(o => o.kind === 'star' && o.type === 'yellow');
            if (suns.length === 0) {
                showNotification("Du brauchst mindestens einen Sonnähnlichen Stern für Planeten!");
                return;
            }

            let availableSun = null;
            let sunIdx = -1;
            const stars = state.objects.filter(o => o.kind === 'star');

            for (let sun of suns) {
                const planetCount = state.objects.filter(o => o.kind === 'planet' && o.host === sun).length;
                if (planetCount < 5) { 
                    availableSun = sun; 
                    sunIdx = stars.indexOf(sun);
                    break; 
                }
            }

            if (!availableSun) {
                showNotification("Alle Sonnen haben bereits das Maximum von 5 Planeten erreicht!");
                return;
            }
            
            state.objects.push({
                kind: 'planet',
                host: availableSun,
                hostIndex: sunIdx,
                orbitDist: Math.random() * 30 + 35,
                orbitAngle: Math.random() * Math.PI * 2,
                orbitSpeed: Math.random() * 0.015 + 0.008,
                size: Math.random() * 2 + 2,
                color: ['#38bdf8', '#4ade80', '#fb923c', '#c084fc'][Math.floor(Math.random() * 4)]
            });

            state.xp += 50;
            checkLevelProgression();
            state.planetCooldownEnd = now + PLANET_COOLDOWN_MS;
            showNotification("Planet um einen Sonnenstern platziert! (+50 XP)");
            updateUI(); saveGame();
        });

        btnCollapseBlackHole.addEventListener('click', () => {
            if (state.level < 4) return;
            const now = Date.now();
            if (now < state.bhCooldownEnd) return;

            const redGiants = state.objects.filter(o => o.kind === 'star' && o.type === 'red');
            if (redGiants.length === 0) {
                showNotification("Keine Roten Riesen zum Kollabieren vorhanden!");
                return;
            }

            createBlackHoleAction();
            state.bhCooldownEnd = now + BH_COOLDOWN_MS;
        });

        btnCreateSupernova.addEventListener('click', () => {
            if (state.level < 5) return;
            const now = Date.now();
            if (now < state.snCooldownEnd) return;

            createSupernovaAction();
            state.snCooldownEnd = now + SN_COOLDOWN_MS;
        });

        function checkLevelProgression() {
            let newLvl = state.level;
            if (state.xp >= LEVEL_THRESHOLDS[5]) newLvl = 6;
            else if (state.xp >= LEVEL_THRESHOLDS[4]) newLvl = 5;
            else if (state.xp >= LEVEL_THRESHOLDS[3]) newLvl = 4;
            else if (state.xp >= LEVEL_THRESHOLDS[2]) newLvl = 3;
            else if (state.xp >= LEVEL_THRESHOLDS[1]) newLvl = 2;

            if (newLvl > state.level) {
                state.level = newLvl;
                showNotification(`LEVEL UP! Du hast Level ${state.level} erreicht!`);
            }
        }

        function calculateXpPerMinute() {
            let total = 0;
            state.objects.forEach(o => {
                if (o.kind === 'star') {
                    if (o.type === 'blue') total += 1;
                    else if (o.type === 'yellow') total += 2;
                    else if (o.type === 'red' || o.type === 'neutron') total += 3;
                } else if (o.kind === 'planet') {
                    total += 5;
                } else if (o.kind === 'galaxy') {
                    total += 50;
                } else if (o.kind === 'blackhole') {
                    total += 200;
                } else if (o.kind === 'supernova') {
                    total += 500;
                }
            });
            return total;
        }

        setInterval(() => {
            if (!state.bigBangDone) return;
            const now = Date.now();

            const totalStars = state.objects.filter(o => o.kind === 'star').length;
            if (state.quests.autoStars && totalStars >= 50 && now >= state.starCooldownEnd) {
                createStarAction(true);
                state.starCooldownEnd = now + STAR_COOLDOWN_MS;
            }

            const totalGalaxies = state.objects.filter(o => o.kind === 'galaxy').length;
            if (state.quests.autoGalaxies && totalGalaxies >= 50 && now >= state.galaxyCooldownEnd) {
                createGalaxyAction(true);
                state.galaxyCooldownEnd = now + GALAXY_COOLDOWN_MS;
            }

            const totalBH = state.objects.filter(o => o.kind === 'blackhole').length;
            const redGiants = state.objects.filter(o => o.kind === 'star' && o.type === 'red');
            if (state.quests.autoBlackholes && totalBH >= 50 && redGiants.length > 0 && now >= state.bhCooldownEnd) {
                createBlackHoleAction(true);
                state.bhCooldownEnd = now + BH_COOLDOWN_MS;
            }

            const totalSN = state.objects.filter(o => o.kind === 'supernova').length;
            if (state.quests.autoSupernovas && totalSN >= 50 && now >= state.snCooldownEnd) {
                createSupernovaAction(true);
                state.snCooldownEnd = now + SN_COOLDOWN_MS;
            }

            const rate = calculateXpPerMinute();
            if (rate > 0) {
                state.xp += rate / 60;
                checkLevelProgression();
                updateUI();
                saveGame();
            }
        }, 1000);

        window.addEventListener('beforeunload', () => { if (state.bigBangDone) saveGame(); });

        btnResetGame.addEventListener('click', () => {
            showModal("Universum neustarten?", "Alles wird gelöscht!", () => {
                localStorage.removeItem('cuniverse_save_v6');
                location.reload();
            });
        });

        btnAudioToggle.addEventListener('click', () => {
            state.audioEnabled = !state.audioEnabled;
            if (state.audioEnabled) initAudio();
            btnAudioToggle.innerHTML = state.audioEnabled ? '<i class="fa-solid fa-volume-high text-cyan-400"></i>' : '<i class="fa-solid fa-volume-xmark text-slate-400"></i>';
        });

        function updateQuestsUI(counts) {
            const questsData = [
                { id: 'autoStars', name: 'Stellarer Automatismus', desc: 'Auto-Sterne (ab 50 Sonnen)', count: counts.yellow + counts.red + counts.blue + counts.neutron, req: 50 },
                { id: 'autoGalaxies', name: 'Galaktische Expansion', desc: 'Auto-Galaxien (ab 50 Galaxien)', count: counts.galaxies, req: 50 },
                { id: 'autoBlackholes', name: 'Ereignishorizont-Fließband', desc: 'Auto-Schwarze Löcher (ab 50)', count: counts.blackholes, req: 50 },
                { id: 'autoSupernovas', name: 'Kosmische Kettenreaktion', desc: 'Auto-Supernovas (ab 50 Supernovas)', count: counts.supernovas, req: 50 }
            ];

            let activeCount = 0;
            questList.innerHTML = '';
            questsData.forEach(q => {
                const unlocked = q.count >= q.req;
                const active = state.quests[q.id];
                if (active) activeCount++;

                const item = document.createElement('div');
                item.className = `p-2 rounded-xl border flex flex-col gap-1 transition ${active ? 'bg-purple-900/40 border-purple-400/50' : (unlocked ? 'bg-slate-900/60 border-slate-700 cursor-pointer hover:border-purple-500/50' : 'bg-slate-950/40 border-slate-800 opacity-50')}`;
                
                item.innerHTML = `
                    <div class="flex justify-between items-center">
                        <span class="font-bold text-slate-200 text-xs">${q.name}</span>
                        <span class="text-[9px] font-mono ${active ? 'text-emerald-400' : (unlocked ? 'text-amber-400' : 'text-slate-500')}">${active ? 'AKTIV' : (unlocked ? 'FREI' : `${q.count}/${q.req}`)}</span>
                    </div>
                    <p class="text-[10px] text-slate-400">${q.desc}</p>
                `;

                if (unlocked && !active) {
                    item.addEventListener('click', () => {
                        state.quests[q.id] = true;
                        showNotification(`Quest "${q.name}" aktiviert!`);
                        updateUI();
                        saveGame();
                    });
                }
                questList.appendChild(item);
            });

            if (questActiveBadge) {
                questActiveBadge.textContent = `${activeCount} Aktiv`;
            }
        }

        function updateUI() {
            displayUniverseName.textContent = state.universeName || "Kosmos";
            const currentXp = Math.floor(state.xp);
            displayXpRate.textContent = `(+${calculateXpPerMinute()} XP/min)`;

            let nextMax = LEVEL_THRESHOLDS[state.level] || LEVEL_THRESHOLDS[5];
            let prevMax = state.level === 1 ? 0 : LEVEL_THRESHOLDS[state.level - 1];
            
            if (state.level >= 6) {
                displayLevelTag.textContent = `LVL ${state.level}: GOTTESMODE`;
                displayXpText.textContent = `${currentXp} XP (MAX)`;
                xpProgressBar.style.width = `100%`;
                xpPercent.textContent = `100%`;
            } else {
                displayLevelTag.textContent = `LVL ${state.level}`;
                displayXpText.textContent = `${currentXp} / ${nextMax} XP`;
                const pct = Math.min(100, Math.max(0, Math.floor(((state.xp - prevMax) / (nextMax - prevMax)) * 100)));
                xpProgressBar.style.width = `${pct}%`;
                xpPercent.textContent = `${pct}%`;
            }

            const counts = { yellow: 0, red: 0, blue: 0, neutron: 0, planets: 0, galaxies: 0, blackholes: 0, supernovas: 0 };
            state.objects.forEach(o => {
                if (o.kind === 'star') counts[o.type]++;
                else if (o.kind === 'planet') counts.planets++;
                else if (o.kind === 'galaxy') counts.galaxies++;
                else if (o.kind === 'blackhole') counts.blackholes++;
                else if (o.kind === 'supernova') counts.supernovas++;
            });

            const totalObj = state.objects.length;
            compactObjectCount.textContent = totalObj;

            countYellow.textContent = counts.yellow;
            countRed.textContent = counts.red;
            countBlue.textContent = counts.blue;
            countNeutron.textContent = counts.neutron;
            countPlanets.textContent = counts.planets;
            countGalaxies.textContent = counts.galaxies;
            countBlackHoles.textContent = counts.blackholes;
            countSupernovas.textContent = counts.supernovas;

            updateQuestsUI(counts);

            const suns = state.objects.filter(o => o.kind === 'star' && o.type === 'yellow');
            let hasSunSpace = false;
            for (let sun of suns) {
                const pCount = state.objects.filter(o => o.kind === 'planet' && o.host === sun).length;
                if (pCount < 5) { hasSunSpace = true; break; }
            }

            btnCreateGalaxy.disabled = (state.level < 2);
            btnCreatePlanet.disabled = (state.level < 3 || suns.length === 0 || !hasSunSpace);
            planetReqText.textContent = state.level < 3 ? "Ab Lvl 3" : (suns.length === 0 ? "Sonne nötig" : (!hasSunSpace ? "Sonne voll (max 5)" : "Bereit"));
            
            btnCollapseBlackHole.disabled = (state.level < 4 || counts.red === 0);
            redGiantReqText.textContent = state.level < 4 ? "Ab Lvl 4" : (counts.red === 0 ? "Roter Riese nötig" : "Bereit");

            btnCreateSupernova.disabled = (state.level < 5);
            snReqText.textContent = state.level < 5 ? "Ab Lvl 5" : "Bereit";
        }

        function tickCooldowns() {
            const now = Date.now();
            if (now < state.starCooldownEnd) {
                btnCreateStar.disabled = true;
                cooldownStarContainer.classList.remove('hidden');
                timerStar.textContent = `${Math.ceil((state.starCooldownEnd - now)/1000)}s`;
                barStar.style.width = `${((STAR_COOLDOWN_MS - (state.starCooldownEnd - now))/STAR_COOLDOWN_MS)*100}%`;
            } else { btnCreateStar.disabled = false; cooldownStarContainer.classList.add('hidden'); }

            if (state.level >= 2) {
                if (now < state.galaxyCooldownEnd) {
                    btnCreateGalaxy.disabled = true;
                    cooldownGalaxyContainer.classList.remove('hidden');
                    const rem = Math.ceil((state.galaxyCooldownEnd - now)/1000);
                    timerGalaxy.textContent = `${Math.floor(rem/60)}:${('0'+(rem%60)).slice(-2)}`;
                    barGalaxy.style.width = `${((GALAXY_COOLDOWN_MS - (state.galaxyCooldownEnd - now))/GALAXY_COOLDOWN_MS)*100}%`;
                } else { btnCreateGalaxy.disabled = false; cooldownGalaxyContainer.classList.add('hidden'); }
            }

            if (state.level >= 3) {
                if (now < state.planetCooldownEnd) {
                    btnCreatePlanet.disabled = true;
                    cooldownPlanetContainer.classList.remove('hidden');
                    const rem = Math.ceil((state.planetCooldownEnd - now)/1000);
                    timerPlanet.textContent = `${Math.floor(rem/60)}:${('0'+(rem%60)).slice(-2)}`;
                    barPlanet.style.width = `${((PLANET_COOLDOWN_MS - (state.planetCooldownEnd - now))/PLANET_COOLDOWN_MS)*100}%`;
                } else { cooldownPlanetContainer.classList.add('hidden'); }
            }

            if (state.level >= 4) {
                if (now < state.bhCooldownEnd) {
                    btnCollapseBlackHole.disabled = true;
                    cooldownBhContainer.classList.remove('hidden');
                    const rem = Math.ceil((state.bhCooldownEnd - now)/1000);
                    timerBh.textContent = `${Math.floor(rem/60)}:${('0'+(rem%60)).slice(-2)}`;
                    barBh.style.width = `${((BH_COOLDOWN_MS - (state.bhCooldownEnd - now))/BH_COOLDOWN_MS)*100}%`;
                } else { cooldownBhContainer.classList.add('hidden'); }
            }

            if (state.level >= 5) {
                if (now < state.snCooldownEnd) {
                    btnCreateSupernova.disabled = true;
                    cooldownSnContainer.classList.remove('hidden');
                    const rem = Math.ceil((state.snCooldownEnd - now)/1000);
                    const hours = Math.floor(rem / 3600);
                    const mins = Math.floor((rem % 3600) / 60);
                    const secs = rem % 60;
                    timerSn.textContent = hours > 0 ? `${hours}h ${mins}m` : `${mins}:${('0'+secs).slice(-2)}`;
                    barSn.style.width = `${((SN_COOLDOWN_MS - (state.snCooldownEnd - now))/SN_COOLDOWN_MS)*100}%`;
                } else { btnCreateSupernova.disabled = false; cooldownSnContainer.classList.add('hidden'); }
            }
        }
        setInterval(tickCooldowns, 250);

        canvas.addEventListener('mousedown', e => { camera.isDragging = true; camera.startX = e.clientX - camera.x; camera.startY = e.clientY - camera.y; });
        window.addEventListener('mousemove', e => { if (camera.isDragging) { camera.x = e.clientX - camera.startX; camera.y = e.clientY - camera.startY; } });
        window.addEventListener('mouseup', () => { camera.isDragging = false; });

        canvas.addEventListener('touchstart', e => {
            if (e.touches.length === 1) {
                camera.isDragging = true;
                camera.startX = e.touches[0].clientX - camera.x;
                camera.startY = e.touches[0].clientY - camera.y;
            }
        });
        window.addEventListener('touchmove', e => {
            if (camera.isDragging && e.touches.length === 1) {
                camera.x = e.touches[0].clientX - camera.startX;
                camera.y = e.touches[0].clientY - camera.startY;
            }
        });
        window.addEventListener('touchend', () => { camera.isDragging = false; });

        canvas.addEventListener('wheel', e => {
            e.preventDefault();
            const z = e.deltaY < 0 ? 1.1 : 0.9;
            if (camera.zoom * z >= 0.2 && camera.zoom * z <= 3.5) camera.zoom *= z;
        }, { passive: false });

        document.getElementById('btnZoomIn').addEventListener('click', () => { if (camera.zoom < 3.5) camera.zoom *= 1.25; });
        document.getElementById('btnZoomOut').addEventListener('click', () => { if (camera.zoom > 0.2) camera.zoom /= 1.25; });
        document.getElementById('btnResetView').addEventListener('click', () => { camera.x = 0; camera.y = 0; camera.zoom = 1; });

        const ambientStars = Array.from({ length: 400 }, () => ({
            x: (Math.random() - 0.5) * 3000, y: (Math.random() - 0.5) * 3000, size: Math.random() * 1.8 + 0.3, alpha: Math.random() * 0.8 + 0.2
        }));

        function render() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#030014';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.save();
            ctx.translate(canvas.width / 2 + camera.x, canvas.height / 2 + camera.y);
            ctx.scale(camera.zoom, camera.zoom);

            ambientStars.forEach(s => {
                ctx.fillStyle = `rgba(255, 255, 255, ${s.alpha})`;
                ctx.beginPath(); ctx.arc(s.x, s.y, s.size, 0, Math.PI * 2); ctx.fill();
            });

            const blackHoles = state.objects.filter(o => o.kind === 'blackhole');

            state.objects.forEach(obj => {
                if (obj.kind === 'star' && obj.orbitAngle !== undefined) {
                    obj.orbitAngle += obj.orbitSpeed || 0.001;
                    obj.x = Math.cos(obj.orbitAngle) * obj.orbitRadius;
                    obj.y = Math.sin(obj.orbitAngle) * obj.orbitRadius;
                } else if (obj.kind === 'planet' && obj.host) {
                    obj.orbitAngle += obj.orbitSpeed;
                    obj.x = obj.host.x + Math.cos(obj.orbitAngle) * obj.orbitDist;
                    obj.y = obj.host.y + Math.sin(obj.orbitAngle) * obj.orbitDist;
                }

                blackHoles.forEach(bh => {
                    if (bh !== obj) {
                        const dx = bh.x - obj.x, dy = bh.y - obj.y, dist = Math.sqrt(dx*dx + dy*dy);
                        if (dist > 20 && dist < 400) { obj.x += (dx/dist)*0.15; obj.y += (dy/dist)*0.15; }
                    }
                });

                if (obj.kind === 'star') drawStar(obj);
                else if (obj.kind === 'planet') drawPlanet(obj);
                else if (obj.kind === 'galaxy') drawGalaxy(obj);
                else if (obj.kind === 'blackhole') drawBlackHole(obj);
                else if (obj.kind === 'supernova') drawSupernova(obj);
            });

            for (let i = particles.length - 1; i >= 0; i--) {
                const p = particles[i];
                p.x += p.vx; p.y += p.vy; p.alpha -= p.decay;
                if (p.alpha <= 0) { particles.splice(i, 1); continue; }
                ctx.save(); ctx.globalAlpha = p.alpha; ctx.fillStyle = p.color; ctx.shadowColor = p.color; ctx.shadowBlur = 8;
                ctx.beginPath(); ctx.arc(p.x, p.y, p.radius, 0, Math.PI * 2); ctx.fill(); ctx.restore();
            }

            ctx.restore();
            requestAnimationFrame(render);
        }

        function drawStar(star) {
            star.pulse = (star.pulse || 0) + 0.03;
            const pf = Math.sin(star.pulse) * 0.15 + 1;
            ctx.save(); ctx.translate(star.x, star.y);
            if (star.type === 'yellow') {
                const r = 9 * pf;
                const grad = ctx.createRadialGradient(0,0,0,0,0,r*3);
                grad.addColorStop(0, '#ffffff'); grad.addColorStop(0.3, '#facc15'); grad.addColorStop(1, 'rgba(234, 179, 8, 0)');
                ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,r*3,0,Math.PI*2); ctx.fill();
            } else if (star.type === 'red') {
                const r = 18 * pf;
                const grad = ctx.createRadialGradient(0,0,0,0,0,r*2.5);
                grad.addColorStop(0, '#fef08a'); grad.addColorStop(0.2, '#ef4444'); grad.addColorStop(1, 'rgba(185, 28, 28, 0)');
                ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,r*2.5,0,Math.PI*2); ctx.fill();
            } else if (star.type === 'blue') {
                const r = 5 * pf;
                const grad = ctx.createRadialGradient(0,0,0,0,0,r*4);
                grad.addColorStop(0, '#ffffff'); grad.addColorStop(0.4, '#38bdf8'); grad.addColorStop(1, 'rgba(3, 105, 161, 0)');
                ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,r*4,0,Math.PI*2); ctx.fill();
            } else if (star.type === 'neutron') {
                ctx.rotate(star.pulse * 2);
                ctx.fillStyle = 'rgba(56, 189, 248, 0.4)'; ctx.fillRect(-1.5, -35, 3, 70);
                const grad = ctx.createRadialGradient(0,0,0,0,0,14);
                grad.addColorStop(0, '#ffffff'); grad.addColorStop(0.3, '#22d3ee'); grad.addColorStop(1, 'rgba(34, 211, 238, 0)');
                ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,14,0,Math.PI*2); ctx.fill();
            }
            ctx.restore();
        }

        function drawPlanet(p) {
            ctx.save(); ctx.translate(p.x, p.y);
            ctx.fillStyle = p.color;
            ctx.shadowBlur = 6; ctx.shadowColor = p.color;
            ctx.beginPath(); ctx.arc(0,0,p.size,0,Math.PI*2); ctx.fill();
            ctx.restore();
        }

        function drawGalaxy(g) {
            g.rotation += g.rotSpeed || 0.002;
            ctx.save(); ctx.translate(g.x, g.y); ctx.rotate(g.rotation);
            ctx.fillStyle = g.color || '#c084fc';
            for (let i = 0; i < g.arms; i++) {
                const ao = (i * 2 * Math.PI) / g.arms;
                for (let j = 0; j < 40; j++) {
                    const f = j/40, a = ao + f * Math.PI * 2.5, dist = f * g.radius;
                    ctx.globalAlpha = (1 - f) * 0.7 + 0.2;
                    ctx.beginPath(); ctx.arc(Math.cos(a)*dist, Math.sin(a)*dist, (1-f)*2.5+0.5, 0, Math.PI*2); ctx.fill();
                }
            }
            ctx.restore();
        }

        function drawBlackHole(bh) {
            bh.pulse = (bh.pulse || 0) + 0.04;
            ctx.save(); ctx.translate(bh.x, bh.y); ctx.rotate(bh.pulse * 0.5);
            const grad = ctx.createRadialGradient(0,0,10,0,0,45);
            grad.addColorStop(0, '#000'); grad.addColorStop(0.3, '#ec4899'); grad.addColorStop(1, 'rgba(139,92,246,0)');
            ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,45,0,Math.PI*2); ctx.fill();
            ctx.fillStyle = '#000'; ctx.beginPath(); ctx.arc(0,0,15,0,Math.PI*2); ctx.fill();
            ctx.strokeStyle = '#fff'; ctx.lineWidth = 1; ctx.globalAlpha = 0.8;
            ctx.beginPath(); ctx.arc(0,0,15,0,Math.PI*2); ctx.stroke();
            ctx.restore();
        }

        function drawSupernova(sn) {
            sn.radius += 0.5;
            sn.alpha = Math.max(0, sn.alpha - 0.003);
            ctx.save(); ctx.translate(sn.x, sn.y);
            ctx.globalAlpha = sn.alpha;
            const grad = ctx.createRadialGradient(0,0,0,0,0,sn.radius);
            grad.addColorStop(0, '#ffffff'); grad.addColorStop(0.2, '#fde047'); grad.addColorStop(0.6, '#ef4444'); grad.addColorStop(1, 'rgba(239, 68, 68, 0)');
            ctx.fillStyle = grad; ctx.beginPath(); ctx.arc(0,0,sn.radius,0,Math.PI*2); ctx.fill();
            ctx.restore();
        }

        window.onload = function() {
            const hasSave = loadGame();
            if (hasSave && state.bigBangDone) {
                bigBangScreen.classList.add('hidden');
                gameHeader.classList.remove('opacity-0');
                gameFooter.classList.remove('opacity-0');
                updateUI();
                showNotification(`Willkommen zurück in "${state.universeName}"!`);
            } else {
                bigBangScreen.classList.remove('hidden');
            }
            render();
        };
    </script>
</body>
</html>
