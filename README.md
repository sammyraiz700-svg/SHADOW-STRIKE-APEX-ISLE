# SHADOW-STRIKE-APEX-ISLE
Welcome to SHADOW STRIKE – APEX ISLE, an action-packed 3D open-world Battle Royale experience built for both Android and Windows PC! ​Deploy from the C-130 dropship onto a massive 1200m × 1200m island filled with tactical POIs, high-tier loot, off-road vehicles, and relentless enemy combatants. Outsmart your opponents, stay ahead of the shrinking 
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>SHADOW STRIKE – APEX ISLE | 3D Open-World Battle Royale</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Chakra+Petch:wght@400;600;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-user-select: none;
      touch-action: none;
    }
    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      background-color: #07090c;
      font-family: 'Inter', sans-serif;
      color: #e5e7eb;
    }
    .font-tactical {
      font-family: 'Chakra Petch', sans-serif;
      letter-spacing: 0.04em;
    }
    #game-canvas {
      position: fixed;
      inset: 0;
      width: 100%;
      height: 100%;
      display: block;
      z-index: 1;
    }
    .hud-glass {
      background: linear-gradient(135deg, rgba(13, 18, 24, 0.88), rgba(8, 11, 15, 0.94));
      border: 1px solid rgba(56, 189, 248, 0.25);
      box-shadow: 0 8px 32px rgba(0, 0, 0, 0.65);
      backdrop-filter: blur(8px);
    }
    .tac-btn {
      background: linear-gradient(180deg, rgba(30, 41, 59, 0.9), rgba(15, 23, 42, 0.95));
      border: 1px solid rgba(56, 189, 248, 0.35);
      transition: all 0.14s ease;
    }
    .tac-btn:hover {
      border-color: #38bdf8;
      box-shadow: 0 0 14px rgba(56, 189, 248, 0.35);
      transform: translateY(-1px);
    }
    .tac-btn:active {
      transform: scale(0.96);
    }
    .tac-btn-orange {
      background: linear-gradient(180deg, #f97316, #c2410c);
      border: 1px solid #fdba74;
      color: #fff;
      box-shadow: 0 0 20px rgba(249, 115, 22, 0.45);
    }
    .tac-btn-orange:hover {
      background: linear-gradient(180deg, #fb923c, #ea580c);
      box-shadow: 0 0 28px rgba(249, 115, 22, 0.7);
    }
    .rarity-LEGENDARY { border-color: #facc15 !important; box-shadow: inset 0 0 12px rgba(250, 204, 21, 0.25); }
    .rarity-EPIC { border-color: #c084fc !important; box-shadow: inset 0 0 12px rgba(192, 132, 252, 0.25); }
    .rarity-RARE { border-color: #38bdf8 !important; box-shadow: inset 0 0 12px rgba(56, 189, 248, 0.25); }
    .rarity-UNCOMMON { border-color: #4ade80 !important; box-shadow: inset 0 0 12px rgba(74, 222, 128, 0.2); }

    /* Sniper Scope Overlay */
    #sniper-scope {
      background: radial-gradient(circle at center, transparent 34%, rgba(0, 0, 0, 0.97) 36%);
    }
    /* Storm Screen Vignette */
    #storm-vignette {
      box-shadow: inset 0 0 90px rgba(14, 165, 233, 0.65);
      transition: opacity 0.25s ease;
    }
    /* Damage Flash */
    #damage-vignette {
      box-shadow: inset 0 0 85px rgba(239, 68, 68, 0.75);
      transition: opacity 0.12s ease;
    }
    /* Mobile Touch Controls */
    .touch-zone {
      position: fixed;
      z-index: 35;
    }
    .mobile-action-btn {
      background: rgba(15, 23, 42, 0.78);
      border: 1.5px solid rgba(56, 189, 248, 0.5);
      color: #f8fafc;
      border-radius: 9999px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Chakra Petch', sans-serif;
      font-weight: 700;
      box-shadow: 0 4px 14px rgba(0,0,0,0.6);
    }
    .mobile-action-btn:active {
      background: rgba(56, 189, 248, 0.45);
      transform: scale(0.92);
    }
    @keyframes pulseGlow {
      0%, 100% { opacity: 0.75; }
      50% { opacity: 1; }
    }
    .animate-pulse-fast {
      animation: pulseGlow 1.1s infinite;
    }
  </style>
</head>
<body>

  <!-- 3D WebGL Canvas -->
  <canvas id="game-canvas"></canvas>

  <!-- Screen Effects Overlays -->
  <div id="storm-vignette" class="fixed inset-0 pointer-events-none opacity-0 z-10"></div>
  <div id="damage-vignette" class="fixed inset-0 pointer-events-none opacity-0 z-10"></div>

  <!-- Sniper 4x Scope Overlay -->
  <div id="sniper-scope" class="fixed inset-0 pointer-events-none hidden z-20 flex items-center justify-center">
    <div class="w-full h-[1px] bg-cyan-400/60"></div>
    <div class="h-full w-[1px] bg-cyan-400/60 absolute"></div>
    <div class="w-64 h-64 rounded-full border border-cyan-400/50 absolute flex items-center justify-center">
      <div class="w-2 h-2 rounded-full bg-orange-500"></div>
    </div>
    <div class="absolute bottom-12 text-xs font-tactical text-cyan-300 tracking-widest bg-black/70 px-3 py-1 rounded">VIPER .50 // 4.0X BALLISTIC OPTIC</div>
  </div>

  <!-- =========================================================
       MAIN MENU / LOBBY OVERLAY
  ========================================================== -->
  <div id="main-menu" class="fixed inset-0 z-40 flex flex-col justify-between p-4 md:p-8 bg-gradient-to-r from-black/90 via-slate-950/75 to-black/60 overflow-y-auto">
    <!-- Top Header Bar -->
    <div class="flex flex-wrap items-center justify-between gap-4 border-b border-cyan-500/25 pb-4">
      <div class="flex items-center gap-3">
        <div class="w-11 h-11 rounded-lg bg-gradient-to-br from-orange-500 to-amber-700 flex items-center justify-center font-tactical font-bold text-xl text-white shadow-lg shadow-orange-500/30">
          SS
        </div>
        <div>
          <div class="flex items-center gap-2">
            <h1 class="font-tactical text-2xl md:text-3xl font-bold tracking-wider text-white">SHADOW STRIKE</h1>
            <span class="px-2 py-0.5 text-[10px] font-tactical font-bold bg-cyan-500/20 text-cyan-300 border border-cyan-400/40 rounded">APEX ISLE</span>
          </div>
          <p class="text-xs text-slate-400">GOOGLE ANTIGRAVITY AI // TACTICAL 3D BATTLE ROYALE ENGINE</p>
        </div>
      </div>

      <!-- Callsign & Quick Controls -->
      <div class="flex items-center gap-3">
        <div class="hud-glass px-3 py-1.5 rounded-lg flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
          <input id="player-name-input" type="text" value="STRIKER-01" maxlength="14"
            class="bg-transparent text-xs font-tactical font-bold text-white w-28 focus:outline-none uppercase" />
        </div>
        <button id="btn-open-settings" class="tac-btn px-3.5 py-2 rounded-lg text-xs font-tactical font-bold text-cyan-300 flex items-center gap-1.5">
          ⚙ SETTINGS
        </button>
        <button id="btn-open-controls-help" class="tac-btn px-3.5 py-2 rounded-lg text-xs font-tactical font-bold text-slate-200">
          ? CONTROLS
        </button>
      </div>
    </div>

    <!-- Center Lobby Content -->
    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 my-auto py-4">
      <!-- Left Column: Operator & Skin Selection -->
      <div class="lg:col-span-4 hud-glass rounded-xl p-5 flex flex-col justify-between">
        <div>
          <div class="flex items-center justify-between mb-3">
            <span class="text-xs font-tactical font-bold text-cyan-400 tracking-widest">01 // SELECT OPERATOR</span>
            <span id="operator-perk-badge" class="text-[11px] px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-500/40 font-tactical">STEALTH RECON</span>
          </div>

          <div class="grid grid-cols-2 gap-3 mb-4">
            <button id="op-spectre" class="op-card p-3 rounded-lg border-2 border-cyan-400 bg-slate-900/90 text-left transition">
              <div class="text-xs font-tactical text-cyan-400">ASSAULT // 01</div>
              <div class="font-tactical text-lg font-bold text-white">SPECTRE</div>
              <div class="text-[11px] text-slate-400 mt-1">Tactical Shadow Ops Armor. +5% Sprint Speed.</div>
            </button>
            <button id="op-valkyrie" class="op-card p-3 rounded-lg border border-slate-700 bg-slate-900/60 text-left transition">
              <div class="text-xs font-tactical text-orange-400">RECON // 02</div>
              <div class="font-tactical text-lg font-bold text-white">VALKYRIE</div>
              <div class="text-[11px] text-slate-400 mt-1">Cyber-Vanguard Exo Suit. +15 Starting Shield.</div>
            </button>
          </div>

          <!-- Tactical Camo Skin Selector -->
          <div class="mb-4">
            <label class="block text-xs font-tactical text-slate-400 mb-2">COMBAT CAMO SKIN</label>
            <div class="grid grid-cols-4 gap-2">
              <button data-skin="0" class="skin-btn p-2 rounded border-2 border-orange-400 bg-slate-800 text-[10px] font-tactical font-bold text-center">
                <span class="block w-full h-3 rounded bg-emerald-800 mb-1"></span>FOREST
              </button>
              <button data-skin="1" class="skin-btn p-2 rounded border border-slate-700 bg-slate-800 text-[10px] font-tactical font-bold text-center">
                <span class="block w-full h-3 rounded bg-slate-900 border border-cyan-400 mb-1"></span>CYBER
              </button>
              <button data-skin="2" class="skin-btn p-2 rounded border border-slate-700 bg-slate-800 text-[10px] font-tactical font-bold text-center">
                <span class="block w-full h-3 rounded bg-amber-700 mb-1"></span>DESERT
              </button>
              <button data-skin="3" class="skin-btn p-2 rounded border border-slate-700 bg-slate-800 text-[10px] font-tactical font-bold text-center">
                <span class="block w-full h-3 rounded bg-orange-600 mb-1"></span>APEX
              </button>
            </div>
          </div>
        </div>

        <!-- Environment & Weather Preset -->
        <div>
          <label class="block text-xs font-tactical text-cyan-400 mb-2">02 // WEATHER & TIME CYCLE</label>
          <div class="grid grid-cols-2 gap-2">
            <button data-weather="CLEAR" class="weather-btn px-3 py-2 rounded border-2 border-cyan-400 bg-slate-900 text-xs font-tactical font-bold text-left">
              ☀ CLEAR DAYLIGHT
            </button>
            <button data-weather="SUNSET" class="weather-btn px-3 py-2 rounded border border-slate-700 bg-slate-900/70 text-xs font-tactical font-bold text-left">
              🌅 SUNSET AMBER
            </button>
            <button data-weather="NIGHT" class="weather-btn px-3 py-2 rounded border border-slate-700 bg-slate-900/70 text-xs font-tactical font-bold text-left">
              🌙 NIGHT OPS
            </button>
            <button data-weather="STORM" class="weather-btn px-3 py-2 rounded border border-slate-700 bg-slate-900/70 text-xs font-tactical font-bold text-left">
              ⛈ STORM TEMPEST
            </button>
          </div>
        </div>
      </div>

      <!-- Center Column: Live 3D Operator Preview Space + Island Telemetry -->
      <div class="lg:col-span-4 flex flex-col justify-between items-center text-center pointer-events-none py-4">
        <div class="hud-glass px-4 py-2 rounded-full pointer-events-auto">
          <span class="text-xs font-tactical text-cyan-300">MAP: 1200m × 1200m APEX ISLE // 5 POIs // VEHICLES ACTIVE</span>
        </div>
        <div class="my-auto">
          <div class="text-[11px] font-tactical tracking-widest text-orange-400 mb-1">READY FOR DEPLOYMENT</div>
          <div id="lobby-operator-title" class="font-tactical text-3xl font-bold text-white drop-shadow">OPERATOR: SPECTRE</div>
        </div>
        <div class="hud-glass p-3 rounded-xl w-full max-w-sm text-left pointer-events-auto">
          <div class="text-[11px] font-tactical text-slate-400 mb-1">TACTICAL BRIEFING</div>
          <p class="text-xs text-slate-300 leading-relaxed">
            Deploy from the C-130 Dropship over <span class="text-cyan-300">Shadow Military Base</span>, <span class="text-cyan-300">Apex Port</span>, or <span class="text-cyan-300">Haven Springs</span>. Scavenge Gold/Epic weapons, drive off-road vehicles, and survive the 5-phase electric storm.
          </p>
        </div>
      </div>

      <!-- Right Column: Game Mode Selection & Multiplayer Server Panel -->
      <div class="lg:col-span-4 hud-glass rounded-xl p-5 flex flex-col justify-between">
        <div>
          <span class="block text-xs font-tactical font-bold text-cyan-400 tracking-widest mb-3">03 // SELECT COMBAT MODE</span>
          <div class="space-y-2.5 mb-4">
            <button data-mode="BR_OFFLINE" class="mode-btn w-full p-3.5 rounded-lg border-2 border-orange-500 bg-slate-900/95 text-left transition">
              <div class="flex items-center justify-between">
                <span class="font-tactical font-bold text-base text-white">OFFLINE SURVIVAL (BATTLE ROYALE)</span>
                <span class="text-[10px] font-tactical px-2 py-0.5 rounded bg-orange-500/20 text-orange-300 border border-orange-400/40">SOLO + 19 BOTS</span>
              </div>
              <p class="text-xs text-slate-400 mt-1">Full dropship deployment, looting, vehicles, AI squads & shrinking storm.</p>
            </button>

            <button data-mode="TRAINING" class="mode-btn w-full p-3.5 rounded-lg border border-slate-700 bg-slate-900/70 text-left transition">
              <div class="flex items-center justify-between">
                <span class="font-tactical font-bold text-base text-cyan-300">TACTICAL TRAINING RANGE</span>
                <span class="text-[10px] font-tactical px-2 py-0.5 rounded bg-cyan-500/20 text-cyan-300">SANDBOX</span>
              </div>
              <p class="text-xs text-slate-400 mt-1">All 5 weapons unlocked, moving ballistic targets, recoil testing & vehicle track.</p>
            </button>

            <button data-mode="MULTIPLAYER" class="mode-btn w-full p-3.5 rounded-lg border border-slate-700 bg-slate-900/70 text-left transition">
              <div class="flex items-center justify-between">
                <span class="font-tactical font-bold text-base text-emerald-300">ONLINE MULTIPLAYER LOBBY</span>
                <span id="mp-status-pill" class="text-[10px] font-tactical px-2 py-0.5 rounded bg-slate-800 text-slate-300 border border-slate-600">WS READY</span>
              </div>
              <p class="text-xs text-slate-400 mt-1">Connect to dedicated WebSocket server, create or join room codes.</p>
            </button>
          </div>

          <!-- Dedicated Server Room Panel (Visible when Multiplayer selected) -->
          <div id="mp-lobby-panel" class="hidden p-3 rounded-lg bg-slate-950/90 border border-emerald-500/30 space-y-2.5 mb-3">
            <div class="flex items-center justify-between text-xs">
              <span class="font-tactical text-emerald-400">AUTHORITATIVE SERVER URL</span>
              <span id="ws-conn-state" class="text-[11px] font-tactical text-amber-400">DISCONNECTED</span>
            </div>
            <div class="flex gap-2">
              <input id="ws-server-url" type="text" value="ws://localhost:8080"
                class="flex-1 bg-slate-900 border border-slate-700 rounded px-2.5 py-1.5 text-xs text-white font-mono focus:outline-none focus:border-cyan-400" />
              <button id="btn-ws-connect" class="tac-btn px-3 py-1.5 rounded text-xs font-tactical font-bold text-emerald-300">CONNECT</button>
            </div>
            <div class="grid grid-cols-2 gap-2">
              <button id="btn-create-room" class="tac-btn py-1.5 rounded text-xs font-tactical font-bold text-cyan-300">CREATE ROOM</button>
              <div class="flex gap-1">
                <input id="room-code-input" type="text" placeholder="CODE" maxlength="6"
                  class="w-full bg-slate-900 border border-slate-700 rounded px-2 py-1 text-xs text-center font-tactical uppercase text-white" />
                <button id="btn-join-room" class="tac-btn px-2.5 py-1 rounded text-xs font-tactical font-bold text-orange-300">JOIN</button>
              </div>
            </div>
            <div id="mp-room-info" class="text-[11px] text-slate-400 font-tactical">
              Run Node.js server or click DEPLOY to launch local hybrid session if offline.
            </div>
          </div>
        </div>

        <!-- Launch Button -->
        <button id="btn-deploy-match" class="tac-btn-orange w-full py-4 rounded-xl font-tactical font-bold text-xl tracking-wider uppercase cursor-pointer">
          ▶ DEPLOY TO APEX ISLE
        </button>
      </div>
    </div>

    <!-- Footer Specs -->
    <div class="flex flex-wrap items-center justify-between text-[11px] text-slate-400 border-t border-slate-800 pt-3">
      <div>CONTROLS: WASD Move • Mouse Aim • LMB Fire • RMB ADS • Space Jump/Chute • C Crouch • Z Prone • R Reload • 1-3 Weapons • F Loot • E Vehicle • V FPS/TPS • M Map</div>
      <div class="font-tactical text-cyan-400">BUILD v1.0.4 // WEBGL + WEBAUDIO</div>
    </div>
  </div>

  <!-- =========================================================
       IN-GAME HUD OVERLAY
  ========================================================== -->
  <div id="in-game-hud" class="fixed inset-0 pointer-events-none hidden z-20">
    <!-- Top Left: Mini-Map & Coordinates -->
    <div class="fixed top-3 left-3 flex items-start gap-3 pointer-events-auto">
      <div class="relative">
        <canvas id="minimap-canvas" width="144" height="144" class="rounded-full border-2 border-cyan-400/70 bg-slate-950/90 shadow-lg"></canvas>
        <button id="hud-btn-map" class="absolute -bottom-2 left-1/2 -translate-x-1/2 px-2.5 py-0.5 rounded bg-slate-900 border border-cyan-400/60 text-[10px] font-tactical font-bold text-cyan-300">
          MAP [M]
        </button>
      </div>
      <div class="hud-glass px-3 py-2 rounded-lg hidden sm:block">
        <div id="hud-poi-name" class="font-tactical text-xs font-bold text-cyan-300">APEX ISLE AIRSPACE</div>
        <div id="hud-zone-timer" class="font-tactical text-sm font-bold text-white mt-0.5">ZONE 1: 01:30</div>
        <div id="hud-weather-label" class="text-[10px] font-tactical text-slate-400 mt-0.5">CLEAR DAYLIGHT</div>
      </div>
    </div>

    <!-- Top Center: Dropship / Parachute Prompt & Compass -->
    <div class="fixed top-3 left-1/2 -translate-x-1/2 flex flex-col items-center gap-2">
      <div class="hud-glass px-4 py-1 rounded-full flex items-center gap-4 text-xs font-tactical">
        <span id="hud-heading" class="text-cyan-300 font-bold">N 000°</span>
        <span class="text-slate-500">|</span>
        <span id="hud-altitude" class="text-slate-200">ALT: 0m</span>
      </div>

      <!-- Dropship / Skydiving Banner -->
      <div id="dropship-banner" class="hidden hud-glass px-5 py-2.5 rounded-xl border-orange-400/70 text-center pointer-events-auto">
        <div id="dropship-title" class="font-tactical font-bold text-sm text-orange-400 animate-pulse-fast">C-130 DROPSHIP IN FLIGHT</div>
        <div id="dropship-sub" class="text-xs text-white mt-0.5">Press <span class="text-cyan-300 font-bold">[SPACE]</span> or tap JUMP to deploy</div>
      </div>
    </div>

    <!-- Top Right: Alive, Kills, FPS & Pause -->
    <div class="fixed top-3 right-3 flex items-center gap-2 pointer-events-auto">
      <div class="hud-glass px-3 py-1.5 rounded-lg flex items-center gap-3 font-tactical">
        <div class="text-center">
          <div class="text-[10px] text-slate-400">ALIVE</div>
          <div id="hud-alive-count" class="text-base font-bold text-emerald-400">20</div>
        </div>
        <div class="h-6 w-[1px] bg-slate-700"></div>
        <div class="text-center">
          <div class="text-[10px] text-slate-400">KILLS</div>
          <div id="hud-kills-count" class="text-base font-bold text-orange-400">0</div>
        </div>
        <div class="h-6 w-[1px] bg-slate-700 hidden sm:block"></div>
        <div class="text-center hidden sm:block">
          <div class="text-[10px] text-slate-400">FPS</div>
          <div id="hud-fps" class="text-xs font-bold text-cyan-300">60</div>
        </div>
      </div>
      <button id="hud-btn-pause" class="hud-glass w-10 h-10 rounded-lg flex items-center justify-center font-tactical font-bold text-white hover:border-cyan-400">
        ❚❚
      </button>
    </div>

    <!-- Kill Feed (Top Right below counters) -->
    <div id="kill-feed" class="fixed top-16 right-3 flex flex-col items-end gap-1 pointer-events-none"></div>

    <!-- Center Crosshair & Hitmarker -->
    <div id="crosshair-container" class="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 pointer-events-none flex items-center justify-center">
      <!-- Dynamic Crosshair Lines -->
      <div id="ch-top" class="w-[2px] h-3 bg-cyan-300 absolute -top-4 transition-all duration-75"></div>
      <div id="ch-bottom" class="w-[2px] h-3 bg-cyan-300 absolute -bottom-4 transition-all duration-75"></div>
      <div id="ch-left" class="h-[2px] w-3 bg-cyan-300 absolute -left-4 transition-all duration-75"></div>
      <div id="ch-right" class="h-[2px] w-3 bg-cyan-300 absolute -right-4 transition-all duration-75"></div>
      <div class="w-1 h-1 rounded-full bg-white"></div>

      <!-- Hitmarker X -->
      <div id="hitmarker" class="hidden absolute w-7 h-7">
        <div class="w-full h-[2px] bg-white rotate-45 absolute top-1/2"></div>
        <div class="w-full h-[2px] bg-white -rotate-45 absolute top-1/2"></div>
      </div>
    </div>

    <!-- Context Interaction Prompt (Loot / Vehicle) -->
    <div id="interact-prompt" class="hidden fixed top-[58%] left-1/2 -translate-x-1/2 hud-glass px-4 py-2 rounded-lg border-cyan-400 pointer-events-auto">
      <div id="interact-text" class="font-tactical text-xs font-bold text-white">[F] PICK UP PHANTOM AR-7</div>
    </div>

    <!-- Reload Progress Bar -->
    <div id="reload-indicator" class="hidden fixed top-[55%] left-1/2 -translate-x-1/2 w-36 bg-slate-900/90 border border-cyan-400/50 rounded-full p-1">
      <div id="reload-bar" class="h-1.5 bg-orange-400 rounded-full w-0"></div>
    </div>

    <!-- Vehicle Telemetry Bar (When driving) -->
    <div id="vehicle-hud" class="hidden fixed bottom-36 left-1/2 -translate-x-1/2 hud-glass px-5 py-2 rounded-xl flex items-center gap-5">
      <div>
        <div id="veh-name" class="text-[10px] font-tactical text-cyan-400">SHADOW RAIDER BUGGY</div>
        <div id="veh-speed" class="font-tactical text-xl font-bold text-white">0 <span class="text-xs text-slate-400">KM/H</span></div>
      </div>
      <div class="w-28">
        <div class="text-[10px] font-tactical text-slate-400 mb-1">CHASSIS INTEGRITY</div>
        <div class="w-full h-2 bg-slate-800 rounded-full overflow-hidden">
          <div id="veh-hp-bar" class="h-full bg-emerald-400 w-full"></div>
        </div>
      </div>
    </div>

    <!-- Bottom Left: Health, Shield/Armor & Quick Heal -->
    <div class="fixed bottom-4 left-4 flex flex-col gap-2 pointer-events-auto">
      <div class="flex items-center gap-2">
        <button id="hud-btn-heal" class="hud-glass px-3 py-1.5 rounded-lg text-xs font-tactical font-bold text-emerald-300 border-emerald-500/40 hover:border-emerald-400">
          ✚ MEDKIT [7] (<span id="hud-medkit-count">2</span>)
        </button>
        <button id="hud-btn-bag" class="hud-glass px-3 py-1.5 rounded-lg text-xs font-tactical font-bold text-cyan-300">
          🎒 BAG [TAB]
        </button>
        <button id="hud-btn-cam" class="hud-glass px-2.5 py-1.5 rounded-lg text-xs font-tactical font-bold text-slate-200">
          👁 <span id="hud-cam-mode">TPS</span> [V]
        </button>
      </div>

      <div class="hud-glass p-3 rounded-xl w-64 sm:w-72">
        <!-- Armor Bar -->
        <div class="flex justify-between text-[11px] font-tactical mb-1">
          <span class="text-cyan-300">ARMOR SHIELD</span>
          <span id="hud-armor-text" class="text-cyan-300 font-bold">50 / 100</span>
        </div>
        <div class="w-full h-2 bg-slate-800 rounded-full overflow-hidden mb-2.5">
          <div id="hud-armor-bar" class="h-full bg-gradient-to-r from-cyan-500 to-blue-400 transition-all duration-150" style="width: 50%"></div>
        </div>

        <!-- Health Bar -->
        <div class="flex justify-between text-[11px] font-tactical mb-1">
          <span class="text-emerald-300">VITALITY HP</span>
          <span id="hud-hp-text" class="text-white font-bold">100 / 100</span>
        </div>
        <div class="w-full h-3 bg-slate-800 rounded-full overflow-hidden">
          <div id="hud-hp-bar" class="h-full bg-gradient-to-r from-emerald-500 to-lime-400 transition-all duration-150" style="width: 100%"></div>
        </div>
      </div>
    </div>

    <!-- Bottom Right: Weapon Slots & Ammo Counter -->
    <div class="fixed bottom-4 right-4 flex flex-col items-end gap-2 pointer-events-auto">
      <!-- Current Ammo Display -->
      <div class="hud-glass px-4 py-2 rounded-xl flex items-baseline gap-2">
        <span id="hud-weapon-name" class="font-tactical text-xs font-bold text-orange-400 mr-2">STRIKER-45</span>
        <span id="hud-ammo-mag" class="font-tactical text-2xl font-bold text-white">15</span>
        <span class="text-slate-500 font-tactical">/</span>
        <span id="hud-ammo-reserve" class="font-tactical text-sm text-slate-300">60</span>
      </div>

      <!-- 3 Weapon Slots -->
      <div class="flex gap-2">
        <button id="slot-0" class="weapon-slot hud-glass w-24 sm:w-28 p-2 rounded-lg border-2 border-cyan-400 text-left">
          <div class="text-[10px] font-tactical text-slate-400">[1] PRIMARY</div>
          <div id="slot-0-name" class="text-xs font-tactical font-bold text-white truncate">STRIKER-45</div>
        </button>
        <button id="slot-1" class="weapon-slot hud-glass w-24 sm:w-28 p-2 rounded-lg border border-slate-700 text-left opacity-75">
          <div class="text-[10px] font-tactical text-slate-400">[2] SECONDARY</div>
          <div id="slot-1-name" class="text-xs font-tactical font-bold text-slate-400 truncate">EMPTY</div>
        </button>
        <button id="slot-2" class="weapon-slot hud-glass w-24 sm:w-28 p-2 rounded-lg border border-slate-700 text-left opacity-75">
          <div class="text-[10px] font-tactical text-slate-400">[3] SPECIAL</div>
          <div id="slot-2-name" class="text-xs font-tactical font-bold text-slate-400 truncate">EMPTY</div>
        </button>
      </div>
    </div>
  </div>

  <!-- =========================================================
       MOBILE TOUCH CONTROLS HUD (Auto-enabled on touch or Settings)
  ========================================================== -->
  <div id="mobile-hud" class="fixed inset-0 pointer-events-none hidden z-30">
    <!-- Left Virtual Joystick Zone -->
    <div id="joy-left-zone" class="fixed bottom-28 left-6 w-32 h-32 rounded-full bg-slate-900/45 border border-cyan-400/35 pointer-events-auto flex items-center justify-center">
      <div id="joy-left-knob" class="w-12 h-12 rounded-full bg-cyan-400/50 border-2 border-cyan-300 shadow-lg"></div>
    </div>

    <!-- Right Camera Drag Zone -->
    <div id="touch-look-zone" class="fixed top-16 right-0 w-1/2 h-2/3 pointer-events-auto"></div>

    <!-- Mobile Tactical Action Buttons -->
    <div class="fixed bottom-24 right-5 flex flex-col items-end gap-2.5 pointer-events-auto">
      <div class="flex gap-2.5">
        <button id="m-btn-veh" class="mobile-action-btn w-12 h-12 text-xs hidden">CAR</button>
        <button id="m-btn-loot" class="mobile-action-btn w-12 h-12 text-xs hidden border-orange-400 text-orange-300">LOOT</button>
        <button id="m-btn-ads" class="mobile-action-btn w-13 h-13 px-3 py-3 text-xs">ADS</button>
        <button id="m-btn-reload" class="mobile-action-btn w-12 h-12 text-xs">RLD</button>
      </div>
      <div class="flex items-center gap-3">
        <button id="m-btn-prone" class="mobile-action-btn w-11 h-11 text-[10px]">PRONE</button>
        <button id="m-btn-crouch" class="mobile-action-btn w-12 h-12 text-[11px]">CROUCH</button>
        <button id="m-btn-jump" class="mobile-action-btn w-14 h-14 text-xs border-emerald-400 text-emerald-300">JUMP</button>
        <button id="m-btn-fire" class="mobile-action-btn w-18 h-18 w-16 h-16 text-sm bg-orange-600/85 border-orange-300 text-white">FIRE</button>
      </div>
    </div>
  </div>

  <!-- =========================================================
       FULL TACTICAL MAP MODAL [M]
  ========================================================== -->
  <div id="full-map-modal" class="fixed inset-0 z-40 bg-black/85 hidden flex items-center justify-center p-4">
    <div class="hud-glass rounded-2xl p-4 max-w-2xl w-full flex flex-col items-center">
      <div class="w-full flex items-center justify-between mb-3">
        <div>
          <h2 class="font-tactical text-lg font-bold text-cyan-300">APEX ISLE TACTICAL SATELLITE MAP</h2>
          <p class="text-xs text-slate-400">White Circle: Next Safe Zone • Blue Ring: Electric Storm Boundary</p>
        </div>
        <button id="btn-close-map" class="tac-btn px-3 py-1.5 rounded text-xs font-tactical font-bold text-white">CLOSE [M]</button>
      </div>
      <canvas id="fullmap-canvas" width="520" height="520" class="w-full max-w-[480px] aspect-square rounded-xl border border-cyan-400/50 bg-slate-950"></canvas>
    </div>
  </div>

  <!-- =========================================================
       INVENTORY MODAL [TAB]
  ========================================================== -->
  <div id="inventory-modal" class="fixed inset-0 z-40 bg-black/80 hidden flex items-center justify-center p-4">
    <div class="hud-glass rounded-2xl p-5 max-w-xl w-full">
      <div class="flex items-center justify-between border-b border-slate-700 pb-3 mb-4">
        <h2 class="font-tactical text-lg font-bold text-orange-400">TACTICAL BACKPACK & LOADOUT</h2>
        <button id="btn-close-inv" class="tac-btn px-3 py-1 rounded text-xs font-tactical font-bold">CLOSE [TAB]</button>
      </div>
      <div id="inventory-grid" class="grid grid-cols-1 sm:grid-cols-2 gap-3 mb-4"></div>
      <div class="flex justify-between items-center text-xs text-slate-400 pt-2 border-t border-slate-800">
        <span>Click any weapon to equip • Use Medkits to restore +50 HP</span>
        <button id="inv-use-medkit" class="tac-btn px-3 py-1.5 rounded font-tactical font-bold text-emerald-300">USE MEDKIT</button>
      </div>
    </div>
  </div>

  <!-- =========================================================
       SETTINGS & CONTROLS MODAL
  ========================================================== -->
  <div id="settings-modal" class="fixed inset-0 z-50 bg-black/85 hidden flex items-center justify-center p-4">
    <div class="hud-glass rounded-2xl p-6 max-w-md w-full space-y-4">
      <div class="flex items-center justify-between border-b border-slate-700 pb-3">
        <h2 class="font-tactical text-lg font-bold text-cyan-300">SYSTEM & COMBAT SETTINGS</h2>
        <button id="btn-close-settings" class="tac-btn px-3 py-1 rounded text-xs font-tactical font-bold">DONE</button>
      </div>

      <div>
        <label class="block text-xs font-tactical text-slate-300 mb-1">GRAPHICS QUALITY</label>
        <select id="set-graphics" class="w-full bg-slate-900 border border-slate-700 rounded-lg p-2 text-xs text-white font-tactical">
          <option value="LOW">LOW (Battery Saver / High FPS Mobile)</option>
          <option value="MEDIUM" selected>MEDIUM (Balanced Shadows & Foliage)</option>
          <option value="HIGH">HIGH (Full Shadows, Particles & Draw Distance)</option>
        </select>
      </div>

      <div>
        <label class="block text-xs font-tactical text-slate-300 mb-1">AIM SENSITIVITY: <span id="sens-val" class="text-cyan-300">1.0</span></label>
        <input id="set-sensitivity" type="range" min="0.3" max="2.5" step="0.1" value="1.0" class="w-full accent-cyan-400" />
      </div>

      <div>
        <label class="block text-xs font-tactical text-slate-300 mb-1">MASTER AUDIO VOLUME: <span id="vol-val" class="text-cyan-300">80%</span></label>
        <input id="set-volume" type="range" min="0" max="100" step="5" value="80" class="w-full accent-orange-500" />
      </div>

      <div class="flex items-center justify-between pt-1">
        <span class="text-xs font-tactical text-slate-300">FORCE MOBILE TOUCH HUD</span>
        <input id="set-mobile-hud" type="checkbox" class="w-4 h-4 accent-cyan-400" />
      </div>
    </div>
  </div>

  <!-- =========================================================
       PAUSE MENU MODAL [ESC]
  ========================================================== -->
  <div id="pause-modal" class="fixed inset-0 z-50 bg-black/80 hidden flex items-center justify-center p-4">
    <div class="hud-glass rounded-2xl p-6 max-w-sm w-full text-center space-y-3">
      <h2 class="font-tactical text-2xl font-bold text-white">MATCH PAUSED</h2>
      <p class="text-xs text-slate-400">SHADOW STRIKE – APEX ISLE</p>
      <button id="btn-resume-game" class="tac-btn-orange w-full py-3 rounded-xl font-tactical font-bold text-sm">RESUME COMBAT</button>
      <button id="btn-pause-settings" class="tac-btn w-full py-2.5 rounded-xl font-tactical font-bold text-sm text-cyan-300">SETTINGS</button>
      <button id="btn-quit-lobby" class="tac-btn w-full py-2.5 rounded-xl font-tactical font-bold text-sm text-red-400 border-red-500/40">RETURN TO LOBBY</button>
    </div>
  </div>

  <!-- =========================================================
       END OF MATCH (ELIMINATION / SPECTATOR / VICTORY ROYALE)
  ========================================================== -->
  <div id="end-match-modal" class="fixed inset-0 z-50 bg-black/85 hidden flex items-center justify-center p-4">
    <div class="hud-glass rounded-2xl p-6 max-w-md w-full text-center space-y-4 border-2 border-orange-400/60">
      <div id="end-match-badge" class="inline-block px-3 py-1 rounded-full text-xs font-tactical font-bold bg-orange-500/20 text-orange-300 border border-orange-400">
        MATCH SUMMARY
      </div>
      <h2 id="end-match-title" class="font-tactical text-3xl font-bold text-white">VICTORY ROYALE</h2>
      <p id="end-match-sub" class="text-xs text-slate-300">You are the last operator standing on Apex Isle.</p>

      <div class="grid grid-cols-3 gap-2 bg-slate-950/90 p-3 rounded-xl border border-slate-800">
        <div>
          <div class="text-[10px] font-tactical text-slate-400">PLACEMENT</div>
          <div id="stat-rank" class="font-tactical text-xl font-bold text-orange-400">#1</div>
        </div>
        <div>
          <div class="text-[10px] font-tactical text-slate-400">ELIMINATIONS</div>
          <div id="stat-kills" class="font-tactical text-xl font-bold text-cyan-300">0</div>
        </div>
        <div>
          <div class="text-[10px] font-tactical text-slate-400">SURVIVAL</div>
          <div id="stat-time" class="font-tactical text-xl font-bold text-emerald-400">00:00</div>
        </div>
      </div>

      <div class="flex gap-3 pt-2">
        <button id="btn-spectate" class="tac-btn flex-1 py-3 rounded-xl font-tactical font-bold text-xs text-cyan-300">SPECTATE BOTS</button>
        <button id="btn-end-lobby" class="tac-btn-orange flex-1 py-3 rounded-xl font-tactical font-bold text-xs">RETURN TO LOBBY</button>
      </div>
    </div>
  </div>

  <!-- =========================================================
       CORE GAME ENGINE SCRIPT (MODULAR ARCHITECTURE)
  ========================================================== -->
  <script>
  (() => {
    'use strict';

    /* =========================================================
       MODULE 1: PROCEDURAL WEBAUDIO ENGINE
    ========================================================== */
    class SoundEngine {
      constructor() {
        this.ctx = null;
        this.masterGain = null;
        this.volume = 0.8;
      }

      init() {
        if (!this.ctx) {
          const AudioCtx = window.AudioContext || window.webkitAudioContext;
          if (AudioCtx) {
            this.ctx = new AudioCtx();
            this.masterGain = this.ctx.createGain();
            this.masterGain.gain.value = this.volume;
            this.masterGain.connect(this.ctx.destination);
          }
        }
        if (this.ctx && this.ctx.state === 'suspended') {
          this.ctx.resume();
        }
      }

      setVolume(v) {
        this.volume = v;
        if (this.masterGain) this.masterGain.gain.value = v;
      }

      playNoiseBurst(duration, filterFreq, filterType = 'lowpass', gainVal = 0.4) {
        if (!this.ctx) return;
        const bufferSize = this.ctx.sampleRate * duration;
        const buffer = this.ctx.createBuffer(1, bufferSize, this.ctx.sampleRate);
        const data = buffer.getChannelData(0);
        for (let i = 0; i < bufferSize; i++) {
          data[i] = (Math.random() * 2 - 1) * Math.exp(-i / (bufferSize * 0.35));
        }
        const noise = this.ctx.createBufferSource();
        noise.buffer = buffer;

        const filter = this.ctx.createBiquadFilter();
        filter.type = filterType;
        filter.frequency.value = filterFreq;

        const gain = this.ctx.createGain();
        gain.gain.setValueAtTime(gainVal, this.ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

        noise.connect(filter);
        filter.connect(gain);
        gain.connect(this.masterGain);
        noise.start();
      }

      playTone(freqStart, freqEnd, duration, type = 'sawtooth', gainVal = 0.25) {
        if (!this.ctx) return;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();
        osc.type = type;
        osc.frequency.setValueAtTime(freqStart, this.ctx.currentTime);
        osc.frequency.exponentialRampToValueAtTime(Math.max(20, freqEnd), this.ctx.currentTime + duration);

        gain.gain.setValueAtTime(gainVal, this.ctx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + duration);

        osc.connect(gain);
        gain.connect(this.masterGain);
        osc.start();
        osc.stop(this.ctx.currentTime + duration);
      }

      playWeaponShot(weaponId) {
        this.init();
        if (weaponId === 'VIPER50') {
          this.playNoiseBurst(0.45, 1600, 'lowpass', 0.65);
          this.playTone(190, 42, 0.32, 'sawtooth', 0.45);
        } else if (weaponId === 'THUNDER12') {
          this.playNoiseBurst(0.38, 1100, 'lowpass', 0.7);
          this.playTone(140, 35, 0.25, 'square', 0.4);
        } else if (weaponId === 'SHADOW9') {
          this.playNoiseBurst(0.11, 2400, 'bandpass', 0.32);
          this.playTone(320, 90, 0.08, 'triangle', 0.2);
        } else if (weaponId === 'STRIKER45') {
          this.playNoiseBurst(0.14, 2100, 'lowpass', 0.35);
          this.playTone(280, 80, 0.1, 'sawtooth', 0.22);
        } else {
          // PHANTOM AR-7
          this.playNoiseBurst(0.18, 1950, 'lowpass', 0.45);
          this.playTone(240, 65, 0.14, 'sawtooth', 0.3);
        }
      }

      playHitmarker(isHeadshot = false) {
        this.init();
        if (isHeadshot) {
          this.playTone(1350, 1850, 0.12, 'sine', 0.35);
          setTimeout(() => this.playTone(1900, 2200, 0.12, 'sine', 0.3), 50);
        } else {
          this.playTone(880, 520, 0.07, 'triangle', 0.25);
        }
      }

      playReload() {
        this.init();
        this.playTone(420, 680, 0.1, 'square', 0.15);
        setTimeout(() => this.playTone(620, 920, 0.12, 'square', 0.18), 260);
      }

      playFootstep() {
        this.playNoiseBurst(0.06, 480, 'lowpass', 0.08);
      }

      playThunder() {
        this.playNoiseBurst(1.4, 340, 'lowpass', 0.55);
      }

      playStormSiren() {
        this.init();
        this.playTone(440, 660, 0.6, 'sawtooth', 0.18);
      }

      playVictoryFanfare() {
        this.init();
        const notes = [523.25, 659.25, 783.99, 1046.5];
        notes.forEach((n, idx) => {
          setTimeout(() => this.playTone(n, n * 1.02, 0.45, 'triangle', 0.3), idx * 160);
        });
      }
    }

    const sound = new SoundEngine();

    /* =========================================================
       MODULE 2: GAME DATA, WEAPONS, RARITIES & POIS
    ========================================================== */
    const WEAPON_CATALOG = {
      STRIKER45: {
        id: 'STRIKER45',
        name: 'STRIKER-45',
        type: 'PISTOL',
        rarity: 'UNCOMMON',
        damage: 24,
        fireRate: 0.24,
        magSize: 15,
        reloadTime: 1.2,
        spread: 0.018,
        adsZoom: 1.2,
        pellets: 1,
        auto: false,
        color: 0x94a3b8
      },
      SHADOW9: {
        id: 'SHADOW9',
        name: 'SHADOW-9',
        type: 'SMG',
        rarity: 'RARE',
        damage: 21,
        fireRate: 0.085,
        magSize: 35,
        reloadTime: 1.5,
        spread: 0.028,
        adsZoom: 1.25,
        pellets: 1,
        auto: true,
        color: 0x38bdf8
      },
      PHANTOM_AR7: {
        id: 'PHANTOM_AR7',
        name: 'PHANTOM AR-7',
        type: 'ASSAULT RIFLE',
        rarity: 'EPIC',
        damage: 33,
        fireRate: 0.115,
        magSize: 30,
        reloadTime: 1.8,
        spread: 0.014,
        adsZoom: 1.45,
        pellets: 1,
        auto: true,
        color: 0xc084fc
      },
      THUNDER12: {
        id: 'THUNDER12',
        name: 'THUNDER-12',
        type: 'SHOTGUN',
        rarity: 'RARE',
        damage: 17,
        fireRate: 0.68,
        magSize: 8,
        reloadTime: 2.0,
        spread: 0.085,
        adsZoom: 1.15,
        pellets: 7,
        auto: false,
        color: 0xf97316
      },
      VIPER50: {
        id: 'VIPER50',
        name: 'VIPER .50',
        type: 'SNIPER',
        rarity: 'LEGENDARY',
        damage: 95,
        fireRate: 1.15,
        magSize: 5,
        reloadTime: 2.3,
        spread: 0.002,
        adsZoom: 3.6,
        pellets: 1,
        auto: false,
        color: 0xfacc15
      }
    };

    const RARITY_COLORS = {
      LEGENDARY: 0xfacc15,
      EPIC: 0xc084fc,
      RARE: 0x38bdf8,
      UNCOMMON: 0x4ade80
    };

    const POIS = [
      { name: 'SHADOW MILITARY BASE', x: -240, z: -220, radius: 110, color: '#38bdf8' },
      { name: 'APEX PORT', x: 270, z: 210, radius: 115, color: '#f97316' },
      { name: 'HAVEN SPRINGS VILLAGE', x: -20, z: 20, radius: 95, color: '#4ade80' },
      { name: "SNIPER'S PEAK", x: 220, z: -260, radius: 90, color: '#c084fc' },
      { name: 'WHISPERING PINES', x: -250, z: 230, radius: 110, color: '#22c55e' }
    ];

    const ISLAND_SIZE = 1200;
    const HALF_ISLAND = ISLAND_SIZE / 2;

    /* =========================================================
       MODULE 3: DETERMINISTIC PROCEDURAL TERRAIN & COLLISION
    ========================================================== */
    function getTerrainHeight(x, z) {
      const distFromCenter = Math.sqrt(x * x + z * z);
      // Island radial falloff into ocean around radius 520m
      const islandMask = Math.max(0, 1 - Math.pow(distFromCenter / 550, 3.2));
      if (islandMask <= 0.01) return -4.5;

      // Rolling hills + Sniper's Peak mountain in NE quadrant (+220, -260)
      const dxPeak = x - 220;
      const dzPeak = z + 260;
      const peakDist = Math.sqrt(dxPeak * dxPeak + dzPeak * dzPeak);
      const sniperMountain = Math.max(0, 44 * Math.exp(-(peakDist * peakDist) / (155 * 155)));

      // Flatten Military Base & Port pads for clean tactical structures
      const distBase = Math.hypot(x + 240, z + 220);
      const distPort = Math.hypot(x - 270, z - 210);
      const distVillage = Math.hypot(x + 20, z - 20);

      let flatFactor = 1.0;
      if (distBase < 95) flatFactor = Math.max(0.08, (distBase - 65) / 30);
      if (distPort < 100) flatFactor = Math.min(flatFactor, Math.max(0.05, (distPort - 70) / 30));
      if (distVillage < 85) flatFactor = Math.min(flatFactor, Math.max(0.12, (distVillage - 55) / 30));

      const wave1 = Math.sin(x * 0.012) * Math.cos(z * 0.012) * 11;
      const wave2 = Math.sin(x * 0.031 + 1.4) * Math.sin(z * 0.029) * 3.8;

      const rawHeight = (4.5 + (wave1 + wave2) * flatFactor + sniperMountain) * islandMask - (1 - islandMask) * 4.5;
      return rawHeight;
    }

    /* =========================================================
       MODULE 4: GLOBAL GAME STATE
    ========================================================== */
    const state = {
      screen: 'MENU', // MENU, DROPSHIP, SKYDIVING, PLAYING, PAUSED, ENDED
      mode: 'BR_OFFLINE',
      operator: 'SPECTRE',
      skinIndex: 0,
      weather: 'CLEAR',
      graphics: 'MEDIUM',
      sensitivity: 1.0,
      forceMobileHud: false,
      playerName: 'STRIKER-01',

      // Match timers & Zone
      matchTime: 0,
      zonePhase: 1,
      zoneTimer: 65,
      currentZone: { x: 0, z: 0, radius: 560 },
      nextZone: { x: 35, z: -25, radius: 360 },
      zoneShrinking: false,

      // Player Stats
      player: {
        pos: new THREE.Vector3(0, 220, 0),
        vel: new THREE.Vector3(),
        yaw: 0,
        pitch: 0,
        hp: 100,
        maxHp: 100,
        armor: 50,
        maxArmor: 100,
        speedMult: 1.0,
        stance: 'STAND', // STAND, CROUCH, PRONE
        grounded: false,
        cameraMode: 'TPS', // TPS or FPS
        ads: false,
        kills: 0,
        medkits: 2,
        weapons: [
          { ...WEAPON_CATALOG.STRIKER45, mag: 15, reserve: 75 },
          null,
          null
        ],
        activeSlot: 0,
        fireCooldown: 0,
        reloading: false,
        reloadProgress: 0,
        inVehicle: null,
        parachuteOpen: false,
        stepTimer: 0
      },

      // Dropship state
      dropship: {
        start: new THREE.Vector3(-520, 195, -320),
        end: new THREE.Vector3(520, 195, 340),
        progress: 0
      },

      // Entities
      bots: [],
      lootItems: [],
      vehicles: [],
      bullets: [],
      colliders: [], // { minX, maxX, minZ, maxZ, maxY }
      targets: [],   // Training range moving targets

      // Multiplayer WebSocket Client
      ws: null,
      wsConnected: false,
      roomCode: null,
      remotePlayers: new Map(),

      // Input state
      keys: {},
      mouseDown: false,
      pointerLocked: false,
      joyMove: { x: 0, y: 0 },
      mobileFiring: false
    };

    /* =========================================================
       MODULE 5: THREE.JS RENDERER, SCENE, LIGHTING & WEATHER
    ========================================================== */
    const canvas = document.getElementById('game-canvas');
    const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, powerPreference: 'high-performance' });
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.shadowMap.enabled = true;
    renderer.shadowMap.type = THREE.PCFSoftShadowMap;

    const scene = new THREE.Scene();
    scene.background = new THREE.Color(0x7dd3fc);
    scene.fog = new THREE.FogExp2(0x93c5fd, 0.0014);

    const camera = new THREE.PerspectiveCamera(68, window.innerWidth / window.innerHeight, 0.15, 1450);

    // Lighting
    const hemiLight = new THREE.HemisphereLight(0xe0f2fe, 0x3f6212, 0.85);
    scene.add(hemiLight);

    const sunLight = new THREE.DirectionalLight(0xfffbeb, 1.35);
    sunLight.position.set(260, 420, 180);
    sunLight.castShadow = true;
    sunLight.shadow.mapSize.width = 2048;
    sunLight.shadow.mapSize.height = 2048;
    sunLight.shadow.camera.near = 20;
    sunLight.shadow.camera.far = 1100;
    const dShadow = 360;
    sunLight.shadow.camera.left = -dShadow;
    sunLight.shadow.camera.right = dShadow;
    sunLight.shadow.camera.top = dShadow;
    sunLight.shadow.camera.bottom = -dShadow;
    scene.add(sunLight);

    // Tactical Flashlight for Night Ops
    const playerFlashlight = new THREE.SpotLight(0xe0f2fe, 0, 65, Math.PI / 5, 0.45, 1.2);
    scene.add(playerFlashlight);
    scene.add(playerFlashlight.target);

    // Rain Particle System for Storm Tempest
    const rainCount = 1600;
    const rainGeo = new THREE.BufferGeometry();
    const rainPositions = new Float32Array(rainCount * 3);
    for (let i = 0; i < rainCount; i++) {
      rainPositions[i * 3] = (Math.random() - 0.5) * 140;
      rainPositions[i * 3 + 1] = Math.random() * 65;
      rainPositions[i * 3 + 2] = (Math.random() - 0.5) * 140;
    }
    rainGeo.setAttribute('position', new THREE.BufferAttribute(rainPositions, 3));
    const rainMat = new THREE.PointsMaterial({ color: 0xbae6fd, size: 0.35, transparent: true, opacity: 0.65 });
    const rainSystem = new THREE.Points(rainGeo, rainMat);
    rainSystem.visible = false;
    scene.add(rainSystem);

    function applyWeatherPreset(preset) {
      state.weather = preset;
      const label = document.getElementById('hud-weather-label');
      if (label) label.textContent = preset;
      rainSystem.visible = false;
      playerFlashlight.intensity = 0;

      if (preset === 'CLEAR') {
        scene.background.setHex(0x60a5fa);
        scene.fog.color.setHex(0x93c5fd);
        scene.fog.density = 0.0012;
        sunLight.color.setHex(0xfffbeb);
        sunLight.intensity = 1.4;
        hemiLight.intensity = 0.85;
      } else if (preset === 'SUNSET') {
        scene.background.setHex(0xfb923c);
        scene.fog.color.setHex(0xfdba74);
        scene.fog.density = 0.0016;
        sunLight.color.setHex(0xf97316);
        sunLight.intensity = 1.5;
        sunLight.position.set(-340, 130, 210);
        hemiLight.intensity = 0.7;
      } else if (preset === 'NIGHT') {
        scene.background.setHex(0x050914);
        scene.fog.color.setHex(0x091124);
        scene.fog.density = 0.0024;
        sunLight.color.setHex(0x60a5fa);
        sunLight.intensity = 0.38;
        hemiLight.intensity = 0.32;
        playerFlashlight.intensity = 2.2;
      } else if (preset === 'STORM') {
        scene.background.setHex(0x1e293b);
        scene.fog.color.setHex(0x334155);
        scene.fog.density = 0.0032;
        sunLight.color.setHex(0x94a3b8);
        sunLight.intensity = 0.55;
        hemiLight.intensity = 0.45;
        rainSystem.visible = true;
      }
    }

    /* =========================================================
       MODULE 6: BUILD 1200x1200 APEX ISLE WORLD & 5 POIS
    ========================================================== */
    const worldGroup = new THREE.Group();
    scene.add(worldGroup);

    function buildApexIsleWorld() {
      // 1. Ocean Water Plane
      const oceanGeo = new THREE.PlaneGeometry(3200, 3200);
      const oceanMat = new THREE.MeshStandardMaterial({
        color: 0x0284c7,
        roughness: 0.25,
        metalness: 0.45
      });
      const ocean = new THREE.Mesh(oceanGeo, oceanMat);
      ocean.rotation.x = -Math.PI / 2;
      ocean.position.y = -0.6;
      worldGroup.add(ocean);

      // 2. Procedural Island Terrain Mesh with Vertex Colors
      const segs = 140;
      const terrainGeo = new THREE.PlaneGeometry(ISLAND_SIZE, ISLAND_SIZE, segs, segs);
      terrainGeo.rotateX(-Math.PI / 2);
      const posAttr = terrainGeo.attributes.position;
      const colors = [];

      const cSand = new THREE.Color(0xe7d59c);
      const cGrass = new THREE.Color(0x3f6212);
      const cForest = new THREE.Color(0x1e3a1e);
      const cRock = new THREE.Color(0x57534e);
      const cRoad = new THREE.Color(0x334155);

      for (let i = 0; i < posAttr.count; i++) {
        const vx = posAttr.getX(i);
        const vz = posAttr.getZ(i);
        const vy = getTerrainHeight(vx, vz);
        posAttr.setY(i, vy);

        // Tactical Road network cross
        const onRoad = (Math.abs(vx + vz * 0.2) < 6.5 || Math.abs(vz - vx * 0.15) < 6.5) && vy > 1.2 && vy < 18;
        let col = cGrass;
        if (vy < 1.35) col = cSand;
        else if (onRoad) col = cRoad;
        else if (vy > 22) col = cRock;
        else if (vx < -90 && vz > 90) col = cForest;
        colors.push(col.r, col.g, col.b);
      }

      terrainGeo.setAttribute('color', new THREE.Float32BufferAttribute(colors, 3));
      terrainGeo.computeVertexNormals();

      const terrainMat = new THREE.MeshStandardMaterial({
        vertexColors: true,
        roughness: 0.88,
        metalness: 0.05
      });
      const terrainMesh = new THREE.Mesh(terrainGeo, terrainMat);
      terrainMesh.receiveShadow = true;
      worldGroup.add(terrainMesh);

      // Helper to add solid box structures with automatic AABB collision
      const addStructure = (x, z, w, h, d, colorHex, solid = true, yOffset = 0) => {
        const groundY = getTerrainHeight(x, z);
        const geo = new THREE.BoxGeometry(w, h, d);
        const mat = new THREE.MeshStandardMaterial({ color: colorHex, roughness: 0.75, metalness: 0.2 });
        const mesh = new THREE.Mesh(geo, mat);
        mesh.position.set(x, groundY + h / 2 + yOffset, z);
        mesh.castShadow = true;
        mesh.receiveShadow = true;
        worldGroup.add(mesh);

        if (solid) {
          state.colliders.push({
            minX: x - w / 2 - 0.45,
            maxX: x + w / 2 + 0.45,
            minZ: z - d / 2 - 0.45,
            maxZ: z + d / 2 + 0.45,
            maxY: groundY + h + yOffset
          });
        }
        return mesh;
      };

      // POI 1: SHADOW MILITARY BASE (-240, -220)
      addStructure(-240, -260, 34, 11, 22, 0x334155); // Main Hangar 1
      addStructure(-195, -260, 34, 11, 22, 0x3f4e3c); // Main Hangar 2
      addStructure(-240, -205, 85, 0.35, 16, 0x1e293b, false); // Aircraft Runway
      addStructure(-290, -180, 7, 18, 7, 0x475569); // Watchtower 1
      addStructure(-175, -180, 7, 18, 7, 0x475569); // Watchtower 2
      addStructure(-225, -175, 14, 4.5, 10, 0x4d5d43); // Bunker

      // POI 2: APEX PORT (270, 210)
      const containerColors = [0xdc2626, 0x2563eb, 0x16a34a, 0xea580c];
      for (let i = 0; i < 14; i++) {
        const cx = 230 + (i % 5) * 19;
        const cz = 175 + Math.floor(i / 5) * 24;
        const col = containerColors[i % containerColors.length];
        addStructure(cx, cz, 6.5, 5.2, 14, col);
        if (i % 3 === 0) addStructure(cx, cz, 6.5, 5.2, 14, containerColors[(i + 1) % 4], true, 5.2);
      }
      addStructure(315, 220, 26, 12, 34, 0x334155); // Port Warehouse
      addStructure(265, 265, 5, 28, 5, 0xf97316);   // Cargo Crane Mast
      addStructure(265, 250, 4, 3, 36, 0xf97316, false, 26); // Crane Boom

      // POI 3: HAVEN SPRINGS VILLAGE (-20, 20) — Enterable Multi-Room Houses
      const buildEnterableHouse = (hx, hz, wallColor) => {
        const gy = getTerrainHeight(hx, hz);
        // Floor slab
        addStructure(hx, hz, 14, 0.4, 14, 0x78716c, false);
        // Back wall & Side walls (leaving front doorway open)
        addStructure(hx, hz - 6.6, 14, 6.5, 0.6, wallColor);
        addStructure(hx - 6.6, hz, 0.6, 6.5, 14, wallColor);
        addStructure(hx + 6.6, hz, 0.6, 6.5, 14, wallColor);
        // Front wall segments around 3.2m doorway
        addStructure(hx - 4.3, hz + 6.6, 5.2, 6.5, 0.6, wallColor);
        addStructure(hx + 4.3, hz + 6.6, 5.2, 6.5, 0.6, wallColor);
        // Second-story roof deck
        addStructure(hx, hz, 15, 0.6, 15, 0x44403c, false, 6.5);
        // Exterior stairs/ramp to roof
        const rampGeo = new THREE.BoxGeometry(2.6, 0.4, 9.5);
        const rampMat = new THREE.MeshStandardMaterial({ color: 0x92400e });
        const ramp = new THREE.Mesh(rampGeo, rampMat);
        ramp.position.set(hx + 8.3, gy + 3.4, hz);
        ramp.rotation.x = 0.68;
        worldGroup.add(ramp);
      };

      buildEnterableHouse(-45, 5, 0xd6d3d1);
      buildEnterableHouse(-5, 35, 0xfef3c7);
      buildEnterableHouse(30, -10, 0xcbd5e1);
      buildEnterableHouse(-25, -40, 0xe2e8f0);

      // POI 4: SNIPER'S PEAK (220, -260)
      addStructure(220, -260, 6, 36, 6, 0x94a3b8); // Tall Comm Tower
      addStructure(220, -260, 12, 1.2, 12, 0xef4444, false, 34); // Radar Dish
      addStructure(205, -248, 8, 3.5, 4, 0x475569); // Sniper Nest Cover

      // POI 5: WHISPERING PINES & ISLAND FOLIAGE (Instanced / Shared Geometries for 60 FPS)
      const trunkGeo = new THREE.CylinderGeometry(0.45, 0.7, 5.5, 6);
      const trunkMat = new THREE.MeshStandardMaterial({ color: 0x451a03, roughness: 0.9 });
      const leavesGeo = new THREE.ConeGeometry(3.4, 9.5, 7);
      const leavesMat = new THREE.MeshStandardMaterial({ color: 0x14532d, roughness: 0.85 });

      for (let t = 0; t < 230; t++) {
        const angle = (t * 137.5) * (Math.PI / 180);
        const radius = 55 + ((t * 47) % 430);
        const tx = Math.cos(angle) * radius;
        const tz = Math.sin(angle) * radius;
        const ty = getTerrainHeight(tx, tz);
        if (ty < 2.2 || ty > 28) continue;

        const trunk = new THREE.Mesh(trunkGeo, trunkMat);
        trunk.position.set(tx, ty + 2.75, tz);
        worldGroup.add(trunk);

        const leaves = new THREE.Mesh(leavesGeo, leavesMat);
        leaves.position.set(tx, ty + 8.5, tz);
        leaves.castShadow = state.graphics !== 'LOW';
        worldGroup.add(leaves);

        state.colliders.push({
          minX: tx - 0.85,
          maxX: tx + 0.85,
          minZ: tz - 0.85,
          maxZ: tz + 0.85,
          maxY: ty + 11
        });
      }

      // Tactical Boulders
      const rockGeo = new THREE.DodecahedronGeometry(2.6, 0);
      const rockMat = new THREE.MeshStandardMaterial({ color: 0x64748b, roughness: 0.9 });
      for (let r = 0; r < 65; r++) {
        const rx = Math.sin(r * 73.1) * 420;
        const rz = Math.cos(r * 41.7) * 420;
        const ry = getTerrainHeight(rx, rz);
        if (ry < 1.8) continue;
        const rock = new THREE.Mesh(rockGeo, rockMat);
        rock.position.set(rx, ry + 1.4, rz);
        rock.rotation.set(r, r * 0.5, 0);
        worldGroup.add(rock);
        state.colliders.push({
          minX: rx - 2.3,
          maxX: rx + 2.3,
          minZ: rz - 2.3,
          maxZ: rz + 2.3,
          maxY: ry + 3.5
        });
      }
    }

    buildApexIsleWorld();

    /* =========================================================
       MODULE 7: SAFE ZONE ELECTRIC BLUE CYLINDER FORCEFIELD
    ========================================================== */
    const stormGeo = new THREE.CylinderGeometry(1, 1, 240, 48, 1, true);
    const stormMat = new THREE.MeshBasicMaterial({
      color: 0x0ea5e9,
      transparent: true,
      opacity: 0.24,
      side: THREE.DoubleSide,
      depthWrite: false
    });
    const stormCylinder = new THREE.Mesh(stormGeo, stormMat);
    stormCylinder.position.y = 90;
    scene.add(stormCylinder);

    /* =========================================================
       MODULE 8: OPERATOR 3D CHARACTER MODEL (LOBBY + IN-GAME)
    ========================================================== */
    const SKIN_PALETTES = [
      { primary: 0x2f4f3e, accent: 0x4ade80, visor: 0x38bdf8 }, // Forest
      { primary: 0x0f172a, accent: 0x38bdf8, visor: 0xf97316 }, // Cyber
      { primary: 0x92400e, accent: 0xfbbf24, visor: 0x38bdf8 }, // Desert
      { primary: 0x7c2d12, accent: 0xf97316, visor: 0xfacc15 }  // Apex
    ];

    function createSoldierModel(operatorType = 'SPECTRE', skinIdx = 0) {
      const group = new THREE.Group();
      const pal = SKIN_PALETTES[skinIdx % SKIN_PALETTES.length];
      const primaryColor = operatorType === 'VALKYRIE' ? 0x1e293b : pal.primary;

      const bodyMat = new THREE.MeshStandardMaterial({ color: primaryColor, roughness: 0.55 });
      const armorMat = new THREE.MeshStandardMaterial({ color: pal.accent, roughness: 0.35, metalness: 0.4 });
      const visorMat = new THREE.MeshBasicMaterial({ color: pal.visor });

      // Torso
      const torso = new THREE.Mesh(new THREE.BoxGeometry(0.68, 0.85, 0.38), bodyMat);
      torso.position.y = 1.15;
      torso.castShadow = true;
      group.add(torso);

      // Tactical Chest Plate
      const chest = new THREE.Mesh(new THREE.BoxGeometry(0.72, 0.48, 0.44), armorMat);
      chest.position.y = 1.25;
      group.add(chest);

      // Head & Helmet
      const head = new THREE.Mesh(new THREE.BoxGeometry(0.42, 0.44, 0.44), bodyMat);
      head.position.y = 1.82;
      head.castShadow = true;
      group.add(head);

      const visor = new THREE.Mesh(new THREE.BoxGeometry(0.36, 0.14, 0.06), visorMat);
      visor.position.set(0, 1.84, 0.21);
      group.add(visor);

      // Limbs
      const limbGeo = new THREE.BoxGeometry(0.22, 0.72, 0.22);
      const leftLeg = new THREE.Mesh(limbGeo, bodyMat);
      leftLeg.position.set(-0.2, 0.38, 0);
      group.add(leftLeg);

      const rightLeg = new THREE.Mesh(limbGeo, bodyMat);
      rightLeg.position.set(0.2, 0.38, 0);
      group.add(rightLeg);

      const leftArm = new THREE.Mesh(limbGeo, bodyMat);
      leftArm.position.set(-0.48, 1.15, 0);
      group.add(leftArm);

      const rightArm = new THREE.Mesh(limbGeo, bodyMat);
      rightArm.position.set(0.48, 1.15, 0.15);
      rightArm.rotation.x = -0.5;
      group.add(rightArm);

      // Held Weapon Mesh
      const gunMesh = new THREE.Mesh(
        new THREE.BoxGeometry(0.1, 0.14, 0.68),
        new THREE.MeshStandardMaterial({ color: 0x0f172a, metalness: 0.7, roughness: 0.25 })
      );
      gunMesh.position.set(0.48, 1.12, 0.48);
      group.add(gunMesh);

      // Parachute Canopy (hidden unless skydiving with chute deployed)
      const chuteGroup = new THREE.Group();
      const canopy = new THREE.Mesh(
        new THREE.SphereGeometry(2.6, 14, 8, 0, Math.PI * 2, 0, Math.PI / 2),
        new THREE.MeshStandardMaterial({ color: 0xf97316, side: THREE.DoubleSide })
      );
      canopy.position.y = 4.6;
      chuteGroup.add(canopy);
      chuteGroup.visible = false;
      group.add(chuteGroup);

      group.userData = { leftLeg, rightLeg, leftArm, rightArm, gunMesh, chuteGroup, torso, head };
      return group;
    }

    let playerMesh = createSoldierModel(state.operator, state.skinIndex);
    scene.add(playerMesh);

    function rebuildPlayerAvatar() {
      const oldPos = playerMesh.position.clone();
      scene.remove(playerMesh);
      playerMesh = createSoldierModel(state.operator, state.skinIndex);
      playerMesh.position.copy(oldPos);
      scene.add(playerMesh);
    }

    // C-130 Dropship Mesh
    const dropshipMesh = new THREE.Group();
    const fuselage = new THREE.Mesh(
      new THREE.BoxGeometry(6.5, 5.2, 24),
      new THREE.MeshStandardMaterial({ color: 0x334155, metalness: 0.5, roughness: 0.4 })
    );
    const wings = new THREE.Mesh(
      new THREE.BoxGeometry(34, 0.6, 5.5),
      new THREE.MeshStandardMaterial({ color: 0x475569 })
    );
    wings.position.y = 1.8;
    dropshipMesh.add(fuselage, wings);
    dropshipMesh.visible = false;
    scene.add(dropshipMesh);

    /* =========================================================
       MODULE 9: LOOT SYSTEM, VEHICLES & TRAINING TARGETS
    ========================================================== */
    const lootGroup = new THREE.Group();
    scene.add(lootGroup);

    function spawnLootItem(x, z, kind, weaponKey = null) {
      const y = getTerrainHeight(x, z) + 0.55;
      const item = {
        id: Math.random().toString(36).slice(2),
        x, y, z,
        kind, // 'WEAPON', 'MEDKIT', 'ARMOR', 'AMMO'
        weaponKey,
        collected: false
      };

      const rarity = weaponKey ? WEAPON_CATALOG[weaponKey].rarity : (kind === 'ARMOR' ? 'RARE' : 'UNCOMMON');
      const col = RARITY_COLORS[rarity];

      const mesh = new THREE.Group();
      const box = new THREE.Mesh(
        new THREE.BoxGeometry(kind === 'WEAPON' ? 1.1 : 0.65, 0.35, 0.45),
        new THREE.MeshStandardMaterial({ color: col, emissive: col, emissiveIntensity: 0.35 })
      );
      const beacon = new THREE.Mesh(
        new THREE.CylinderGeometry(0.12, 0.28, 3.6, 8),
        new THREE.MeshBasicMaterial({ color: col, transparent: true, opacity: 0.25 })
      );
      beacon.position.y = 1.8;
      mesh.add(box, beacon);
      mesh.position.set(x, y, z);
      lootGroup.add(mesh);

      item.mesh = mesh;
      item.label = kind === 'WEAPON'
        ? `${WEAPON_CATALOG[weaponKey].name} [${rarity}]`
        : (kind === 'MEDKIT' ? 'TACTICAL MEDKIT (+50 HP)' : (kind === 'ARMOR' ? 'KEVLAR ARMOR (+50)' : 'AMMO CRATE'));
      state.lootItems.push(item);
    }

    function populateIslandLoot(isTraining = false) {
      // Clear previous loot
      while (lootGroup.children.length) lootGroup.remove(lootGroup.children[0]);
      state.lootItems = [];

      if (isTraining) {
        // Training Range weapon rack near Haven Springs
        const keys = Object.keys(WEAPON_CATALOG);
        keys.forEach((k, idx) => {
          spawnLootItem(-18 + idx * 5, 18, 'WEAPON', k);
        });
        spawnLootItem(-18, 24, 'MEDKIT');
        spawnLootItem(-12, 24, 'ARMOR');
        return;
      }

      const weaponKeys = ['SHADOW9', 'PHANTOM_AR7', 'THUNDER12', 'VIPER50', 'STRIKER45'];
      // Spawn dense loot in all 5 POIs + scattered crates
      POIS.forEach(poi => {
        for (let i = 0; i < 9; i++) {
          const lx = poi.x + (Math.random() - 0.5) * poi.radius * 1.2;
          const lz = poi.z + (Math.random() - 0.5) * poi.radius * 1.2;
          if (getTerrainHeight(lx, lz) < 1.2) continue;
          const roll = Math.random();
          if (roll < 0.52) {
            const wKey = weaponKeys[Math.floor(Math.random() * weaponKeys.length)];
            spawnLootItem(lx, lz, 'WEAPON', wKey);
          } else if (roll < 0.76) {
            spawnLootItem(lx, lz, 'MEDKIT');
          } else if (roll < 0.92) {
            spawnLootItem(lx, lz, 'ARMOR');
          } else {
            spawnLootItem(lx, lz, 'AMMO');
          }
        }
      });
    }

    // Drivable Vehicles (Shadow Raider Buggy & Tactical Armored Jeep)
    const vehicleGroup = new THREE.Group();
    scene.add(vehicleGroup);

    function spawnVehicle(x, z, type = 'BUGGY') {
      const y = getTerrainHeight(x, z) + 0.85;
      const mesh = new THREE.Group();
      const isJeep = type === 'JEEP';

      const chassis = new THREE.Mesh(
        new THREE.BoxGeometry(isJeep ? 2.5 : 2.1, isJeep ? 1.25 : 0.85, isJeep ? 4.6 : 3.9),
        new THREE.MeshStandardMaterial({
          color: isJeep ? 0x365314 : 0x1e293b,
          roughness: 0.45,
          metalness: 0.5
        })
      );
      chassis.position.y = 0.6;
      chassis.castShadow = true;
      mesh.add(chassis);

      // Roll cage / Cabin
      const cabin = new THREE.Mesh(
        new THREE.BoxGeometry(isJeep ? 2.2 : 1.65, 0.85, isJeep ? 2.4 : 1.7),
        new THREE.MeshStandardMaterial({ color: isJeep ? 0x3f6212 : 0xf97316, wireframe: !isJeep })
      );
      cabin.position.set(0, 1.45, -0.2);
      mesh.add(cabin);

      // 4 Wheels
      const wheelGeo = new THREE.CylinderGeometry(0.55, 0.55, 0.4, 12);
      wheelGeo.rotateZ(Math.PI / 2);
      const wheelMat = new THREE.MeshStandardMaterial({ color: 0x0f172a });
      const offsets = [
        [-1.15, 0.45, 1.35], [1.15, 0.45, 1.35],
        [-1.15, 0.45, -1.35], [1.15, 0.45, -1.35]
      ];
      offsets.forEach(([wx, wy, wz]) => {
        const w = new THREE.Mesh(wheelGeo, wheelMat);
        w.position.set(wx, wy, wz);
        mesh.add(w);
      });

      mesh.position.set(x, y, z);
      vehicleGroup.add(mesh);

      state.vehicles.push({
        id: Math.random().toString(36).slice(2),
        name: isJeep ? 'TACTICAL ARMORED JEEP' : 'SHADOW RAIDER BUGGY',
        type,
        mesh,
        pos: new THREE.Vector3(x, y, z),
        yaw: Math.random() * Math.PI * 2,
        speed: 0,
        maxSpeed: isJeep ? 24 : 32, // 32 m/s ~ 115 km/h
        hp: isJeep ? 450 : 280,
        maxHp: isJeep ? 450 : 280
      });
    }

    function spawnAllVehicles() {
      while (vehicleGroup.children.length) vehicleGroup.remove(vehicleGroup.children[0]);
      state.vehicles = [];
      spawnVehicle(-210, -195, 'JEEP');
      spawnVehicle(225, 185, 'BUGGY');
      spawnVehicle(-15, 55, 'BUGGY');
      spawnVehicle(165, -180, 'JEEP');
      spawnVehicle(-190, 190, 'BUGGY');
    }

    // Training Range Moving Targets
    const targetGroup = new THREE.Group();
    scene.add(targetGroup);

    function spawnTrainingTargets() {
      while (targetGroup.children.length) targetGroup.remove(targetGroup.children[0]);
      state.targets = [];
      for (let i = 0; i < 6; i++) {
        const tx = -30 + i * 11;
        const tz = -15 - (i % 3) * 14;
        const ty = getTerrainHeight(tx, tz) + 1.3;
        const mesh = new THREE.Mesh(
          new THREE.BoxGeometry(1.1, 2.1, 0.35),
          new THREE.MeshStandardMaterial({ color: 0xf97316, emissive: 0x7c2d12 })
        );
        mesh.position.set(tx, ty, tz);
        targetGroup.add(mesh);
        state.targets.push({
          mesh,
          baseX: tx,
          z: tz,
          speed: (i % 2 === 0 ? 1 : -1) * (3 + i * 0.7),
          hp: 100
        });
      }
    }

    /* =========================================================
       MODULE 10: ENEMY AI BOTS
    ========================================================== */
    const botGroup = new THREE.Group();
    scene.add(botGroup);

    const BOT_NAMES = [
      'VIPER_X', 'GHOST_ACTUAL', 'KRAKEN_99', 'REAPER_07', 'APEX_HUNTER',
      'NOVA_STRIKE', 'COBRA_UNIT', 'SHADOW_WOLF', 'TITAN_OPS', 'ZERO_DARK',
      'PHANTOM_V', 'BLITZ_KRIEG', 'FALCON_EYE', 'IRON_CLAD', 'NIGHT_HAWK',
      'STORM_BRINGER', 'OMEGA_SIX', 'DELTA_FORCE', 'ROGUE_ONE'
    ];

    function spawnAIBots(count = 19) {
      while (botGroup.children.length) botGroup.remove(botGroup.children[0]);
      state.bots = [];

      const botWeapons = ['PHANTOM_AR7', 'SHADOW9', 'THUNDER12', 'STRIKER45'];

      for (let i = 0; i < count; i++) {
        const poi = POIS[i % POIS.length];
        const bx = poi.x + (Math.random() - 0.5) * 160;
        const bz = poi.z + (Math.random() - 0.5) * 160;
        const by = getTerrainHeight(bx, bz);

        const mesh = createSoldierModel(i % 2 === 0 ? 'SPECTRE' : 'VALKYRIE', (i + 1) % 4);
        mesh.position.set(bx, by, bz);
        botGroup.add(mesh);

        state.bots.push({
          id: 'bot_' + i,
          name: BOT_NAMES[i % BOT_NAMES.length],
          mesh,
          pos: new THREE.Vector3(bx, by, bz),
          yaw: Math.random() * Math.PI * 2,
          hp: 100,
          alive: true,
          weapon: WEAPON_CATALOG[botWeapons[i % botWeapons.length]],
          fireCooldown: 0.5 + Math.random() * 1.5,
          moveTarget: new THREE.Vector3(bx, by, bz),
          retargetTimer: Math.random() * 3
        });
      }
    }

    /* =========================================================
       MODULE 11: BALLISTICS, TRACERS & HIT DETECTION
    ========================================================== */
    const tracerGroup = new THREE.Group();
    scene.add(tracerGroup);
    const tracerGeo = new THREE.BoxGeometry(0.08, 0.08, 2.2);

    function spawnBulletTracer(origin, dir, colorHex = 0xfacc15) {
      const mat = new THREE.MeshBasicMaterial({ color: colorHex });
      const mesh = new THREE.Mesh(tracerGeo, mat);
      mesh.position.copy(origin);
      mesh.lookAt(origin.clone().add(dir));
      tracerGroup.add(mesh);
      state.bullets.push({
        mesh,
        vel: dir.clone().multiplyScalar(195),
        life: 0.45
      });
    }

    function addKillFeedMessage(killer, victim, weaponName, isHeadshot = false) {
      const feed = document.getElementById('kill-feed');
      const div = document.createElement('div');
      div.className = 'hud-glass px-3 py-1 rounded text-[11px] font-tactical flex items-center gap-1.5';
      div.innerHTML = `<span class="text-cyan-300 font-bold">${killer}</span>
        <span class="text-orange-400">[${weaponName}${isHeadshot ? ' 🎯' : ''}]</span>
        <span class="text-slate-200">${victim}</span>`;
      feed.prepend(div);
      if (feed.children.length > 5) feed.removeChild(feed.lastChild);
      setTimeout(() => { if (div.parentNode) div.remove(); }, 4500);
    }

    function showHitmarker(isHeadshot) {
      const hm = document.getElementById('hitmarker');
      hm.classList.remove('hidden');
      hm.querySelectorAll('div').forEach(d => {
        d.style.backgroundColor = isHeadshot ? '#facc15' : '#ffffff';
      });
      sound.playHitmarker(isHeadshot);
      setTimeout(() => hm.classList.add('hidden'), 110);
    }

    function firePlayerWeapon() {
      const p = state.player;
      if (state.screen !== 'PLAYING' || p.inVehicle || p.reloading || p.fireCooldown > 0) return;
      const w = p.weapons[p.activeSlot];
      if (!w) return;

      if (w.mag <= 0) {
        startReload();
        return;
      }

      w.mag--;
      p.fireCooldown = w.fireRate;
      sound.playWeaponShot(w.id);

      // Recoil kick
      p.pitch = Math.min(Math.PI / 2.2, p.pitch + (w.id === 'VIPER50' ? 0.045 : 0.014));

      // Raycast from camera center
      const raycaster = new THREE.Raycaster();
      const spreadScale = p.ads ? w.spread * 0.3 : w.spread;

      for (let pellet = 0; pellet < w.pellets; pellet++) {
        const sx = (Math.random() - 0.5) * spreadScale;
        const sy = (Math.random() - 0.5) * spreadScale;
        raycaster.setFromCamera(new THREE.Vector2(sx, sy), camera);

        const origin = raycaster.ray.origin.clone().addScaledVector(raycaster.ray.direction, 1.4);
        spawnBulletTracer(origin, raycaster.ray.direction, w.color);

        // Check Bot hits
        let hitSomething = false;
        for (const bot of state.bots) {
          if (!bot.alive) continue;
          const botCenter = bot.pos.clone().add(new THREE.Vector3(0, 1.1, 0));
          const toBot = botCenter.clone().sub(raycaster.ray.origin);
          const proj = toBot.dot(raycaster.ray.direction);
          if (proj > 0 && proj < 320) {
            const closestPoint = raycaster.ray.origin.clone().addScaledVector(raycaster.ray.direction, proj);
            const distXZ = Math.hypot(closestPoint.x - bot.pos.x, closestPoint.z - bot.pos.z);
            const dy = closestPoint.y - bot.pos.y;
            if (distXZ < 0.65 && dy >= 0 && dy <= 2.1) {
              const isHeadshot = dy > 1.55;
              const dmg = Math.round(w.damage * (isHeadshot ? 2.2 : 1.0));
              bot.hp -= dmg;
              showHitmarker(isHeadshot);
              hitSomething = true;

              if (bot.hp <= 0) {
                bot.alive = false;
                bot.mesh.rotation.x = Math.PI / 2;
                p.kills++;
                addKillFeedMessage(state.playerName, bot.name, w.name, isHeadshot);
                // Drop loot crate upon elimination
                spawnLootItem(bot.pos.x, bot.pos.z, 'WEAPON', bot.weapon.id);
                spawnLootItem(bot.pos.x + 1.1, bot.pos.z, 'MEDKIT');
              }
              break;
            }
          }
        }

        // Check Training Range Targets
        if (!hitSomething && state.mode === 'TRAINING') {
          for (const t of state.targets) {
            const toT = t.mesh.position.clone().sub(raycaster.ray.origin);
            const proj = toT.dot(raycaster.ray.direction);
            if (proj > 0 && proj < 220) {
              const cp = raycaster.ray.origin.clone().addScaledVector(raycaster.ray.direction, proj);
              if (cp.distanceTo(t.mesh.position) < 1.2) {
                showHitmarker(cp.y > t.mesh.position.y + 0.45);
                t.mesh.material.color.setHex(0x4ade80);
                setTimeout(() => t.mesh.material.color.setHex(0xf97316), 180);
              }
            }
          }
        }
      }

      // Broadcast shoot event if connected to Multiplayer Server
      if (state.wsConnected && state.ws) {
        state.ws.send(JSON.stringify({
          type: 'SHOOT',
          weapon: w.id,
          pos: p.pos,
          yaw: p.yaw,
          pitch: p.pitch
        }));
      }

      updateHUD();
    }

    function startReload() {
      const p = state.player;
      const w = p.weapons[p.activeSlot];
      if (!w || p.reloading || w.mag >= w.magSize || w.reserve <= 0) return;
      p.reloading = true;
      p.reloadProgress = 0;
      sound.playReload();
      document.getElementById('reload-indicator').classList.remove('hidden');
    }

    function useMedkit() {
      const p = state.player;
      if (p.medkits <= 0 || p.hp >= p.maxHp) return;
      p.medkits--;
      p.hp = Math.min(p.maxHp, p.hp + 50);
      sound.playTone(520, 880, 0.25, 'sine', 0.25);
      updateHUD();
    }

    function damagePlayer(amount, attackerName = 'ENEMY') {
      const p = state.player;
      if (state.screen !== 'PLAYING') return;

      // Shield absorbs 65% of incoming combat damage
      if (p.armor > 0 && attackerName !== 'ELECTRIC STORM') {
        const absorbed = Math.min(p.armor, amount * 0.65);
        p.armor -= absorbed;
        amount -= absorbed;
      }
      p.hp = Math.max(0, p.hp - amount);

      const dmgVig = document.getElementById('damage-vignette');
      dmgVig.style.opacity = '1';
      setTimeout(() => { dmgVig.style.opacity = '0'; }, 160);

      if (p.hp <= 0) {
        endMatch(false, attackerName);
      }
      updateHUD();
    }

    /* =========================================================
       MODULE 12: MATCH LIFECYCLE (DROPSHIP -> SKYDIVING -> COMBAT)
    ========================================================== */
    function startMatch() {
      sound.init();
      state.playerName = document.getElementById('player-name-input').value.trim() || 'STRIKER-01';
      applyWeatherPreset(state.weather);

      const isTraining = state.mode === 'TRAINING';
      populateIslandLoot(isTraining);
      spawnAllVehicles();

      const p = state.player;
      p.hp = 100;
      p.armor = state.operator === 'VALKYRIE' ? 65 : 50;
      p.speedMult = state.operator === 'SPECTRE' ? 1.06 : 1.0;
      p.kills = 0;
      p.medkits = 2;
      p.inVehicle = null;
      p.parachuteOpen = false;
      p.reloading = false;
      p.ads = false;
      p.weapons = [
        { ...WEAPON_CATALOG.STRIKER45, mag: 15, reserve: 90 },
        null,
        null
      ];
      p.activeSlot = 0;

      state.matchTime = 0;
      state.zonePhase = 1;
      state.zoneTimer = 60;
      state.currentZone = { x: 0, z: 0, radius: 560 };
      state.nextZone = { x: (Math.random() - 0.5) * 160, z: (Math.random() - 0.5) * 160, radius: 340 };

      document.getElementById('main-menu').classList.add('hidden');
      document.getElementById('end-match-modal').classList.add('hidden');
      document.getElementById('in-game-hud').classList.remove('hidden');

      if (isTraining) {
        spawnAIBots(0);
        spawnTrainingTargets();
        const startY = getTerrainHeight(-15, 28);
        p.pos.set(-15, startY, 28);
        p.yaw = 0;
        p.pitch = 0;
        dropshipMesh.visible = false;
        document.getElementById('dropship-banner').classList.add('hidden');
        state.screen = 'PLAYING';
      } else {
        spawnAIBots(19);
        state.dropship.progress = 0;
        p.pos.copy(state.dropship.start);
        dropshipMesh.visible = true;
        document.getElementById('dropship-banner').classList.remove('hidden');
        document.getElementById('dropship-title').textContent = 'C-130 DROPSHIP OVER APEX ISLE';
        document.getElementById('dropship-sub').innerHTML = 'Press <span class="text-cyan-300 font-bold">[SPACE]</span> or tap JUMP to skydive';
        state.screen = 'DROPSHIP';
      }

      // Auto-enable mobile touch controls on touch-capable screens
      if ('ontouchstart' in window || navigator.maxTouchPoints > 0 || state.forceMobileHud) {
        document.getElementById('mobile-hud').classList.remove('hidden');
      }

      if (canvas.requestPointerLock && !('ontouchstart' in window)) {
        try { canvas.requestPointerLock(); } catch (e) {}
      }
      updateHUD();
    }

    function jumpFromDropship() {
      if (state.screen === 'DROPSHIP') {
        state.screen = 'SKYDIVING';
        dropshipMesh.visible = false;
        state.player.vel.set(0, -14, 0);
        document.getElementById('dropship-title').textContent = 'FREEFALL SKYDIVING';
        document.getElementById('dropship-sub').innerHTML = 'Press <span class="text-cyan-300 font-bold">[SPACE]</span> to deploy Tactical Parachute';
      } else if (state.screen === 'SKYDIVING' && !state.player.parachuteOpen) {
        state.player.parachuteOpen = true;
        playerMesh.userData.chuteGroup.visible = true;
        sound.playNoiseBurst(0.35, 650, 'lowpass', 0.3);
        document.getElementById('dropship-title').textContent = 'PARACHUTE DEPLOYED';
        document.getElementById('dropship-sub').textContent = 'Steer with WASD / Left Stick toward Landing Zone';
      }
    }

    function endMatch(victory, killerName = '') {
      state.screen = 'ENDED';
      if (document.exitPointerLock) {
        try { document.exitPointerLock(); } catch (e) {}
      }
      document.getElementById('sniper-scope').classList.add('hidden');
      const modal = document.getElementById('end-match-modal');
      modal.classList.remove('hidden');

      const aliveBots = state.bots.filter(b => b.alive).length;
      const rank = victory ? 1 : aliveBots + 1;

      document.getElementById('end-match-title').textContent = victory ? 'VICTORY ROYALE' : 'ELIMINATED IN ACTION';
      document.getElementById('end-match-title').className = victory
        ? 'font-tactical text-3xl font-bold text-orange-400'
        : 'font-tactical text-3xl font-bold text-red-400';
      document.getElementById('end-match-sub').textContent = victory
        ? 'Apex Isle Secured! Tactical supremacy achieved.'
        : `Eliminated by ${killerName}`;

      document.getElementById('stat-rank').textContent = '#' + rank;
      document.getElementById('stat-kills').textContent = state.player.kills;
      const mins = String(Math.floor(state.matchTime / 60)).padStart(2, '0');
      const secs = String(Math.floor(state.matchTime % 60)).padStart(2, '0');
      document.getElementById('stat-time').textContent = `${mins}:${secs}`;

      if (victory) sound.playVictoryFanfare();
    }

    /* =========================================================
       MODULE 13: COLLISION & MOVEMENT PHYSICS
    ========================================================== */
    function resolveHorizontalCollisions(pos, radius = 0.45) {
      for (let i = 0; i < state.colliders.length; i++) {
        const c = state.colliders[i];
        if (pos.y > c.maxY - 0.3) continue; // Allow walking on roofs/platforms
        if (pos.x + radius > c.minX && pos.x - radius < c.maxX &&
            pos.z + radius > c.minZ && pos.z - radius < c.maxZ) {
          const overlapLeft = (pos.x + radius) - c.minX;
          const overlapRight = c.maxX - (pos.x - radius);
          const overlapTop = (pos.z + radius) - c.minZ;
          const overlapBottom = c.maxZ - (pos.z - radius);
          const minOverlap = Math.min(overlapLeft, overlapRight, overlapTop, overlapBottom);
          if (minOverlap === overlapLeft) pos.x = c.minX - radius;
          else if (minOverlap === overlapRight) pos.x = c.maxX + radius;
          else if (minOverlap === overlapTop) pos.z = c.minZ - radius;
          else pos.z = c.maxZ + radius;
        }
      }
      // Keep inside island boundary
      pos.x = Math.max(-540, Math.min(540, pos.x));
      pos.z = Math.max(-540, Math.min(540, pos.z));
    }

    function getSupportHeight(x, z, currentY) {
      let floorY = getTerrainHeight(x, z);
      for (let i = 0; i < state.colliders.length; i++) {
        const c = state.colliders[i];
        if (x > c.minX && x < c.maxX && z > c.minZ && z < c.maxZ) {
          if (currentY >= c.maxY - 0.65 && c.maxY > floorY) {
            floorY = c.maxY;
          }
        }
      }
      return floorY;
    }

    /* =========================================================
       MODULE 14: MULTIPLAYER WEBSOCKET CLIENT HOOKS
    ========================================================== */
    function connectWebSocket() {
      const url = document.getElementById('ws-server-url').value.trim();
      const statusEl = document.getElementById('ws-conn-state');
      const pillEl = document.getElementById('mp-status-pill');
      statusEl.textContent = 'CONNECTING...';

      try {
        const ws = new WebSocket(url);
        state.ws = ws;

        ws.onopen = () => {
          state.wsConnected = true;
          statusEl.textContent = 'ONLINE (CONNECTED)';
          statusEl.className = 'text-[11px] font-tactical text-emerald-400';
          pillEl.textContent = 'SERVER ONLINE';
          pillEl.className = 'text-[10px] font-tactical px-2 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-400';
          ws.send(JSON.stringify({ type: 'AUTH', name: document.getElementById('player-name-input').value }));
        };

        ws.onmessage = (ev) => {
          try {
            const msg = JSON.parse(ev.data);
            if (msg.type === 'ROOM_JOINED') {
              state.roomCode = msg.code;
              document.getElementById('mp-room-info').textContent = `ACTIVE ROOM: ${msg.code} (${msg.players} Operators)`;
            } else if (msg.type === 'STATE_SNAPSHOT' && Array.isArray(msg.players)) {
              // Sync remote players
              msg.players.forEach(rp => {
                if (rp.id === msg.selfId) return;
                let remote = state.remotePlayers.get(rp.id);
                if (!remote) {
                  const mesh = createSoldierModel('VALKYRIE', 1);
                  scene.add(mesh);
                  remote = { mesh };
                  state.remotePlayers.set(rp.id, remote);
                }
                remote.mesh.position.set(rp.x, rp.y, rp.z);
                remote.mesh.rotation.y = rp.yaw || 0;
              });
            }
          } catch (e) {}
        };

        ws.onerror = () => {
          state.wsConnected = false;
          statusEl.textContent = 'SERVER UNREACHABLE (HYBRID READY)';
          statusEl.className = 'text-[11px] font-tactical text-amber-400';
        };

        ws.onclose = () => {
          state.wsConnected = false;
          statusEl.textContent = 'DISCONNECTED';
        };
      } catch (err) {
        statusEl.textContent = 'INVALID WS URL';
      }
    }

    /* =========================================================
       MODULE 15: MINI-MAP & FULL TACTICAL MAP RENDERERS
    ========================================================== */
    const miniCanvas = document.getElementById('minimap-canvas');
    const miniCtx = miniCanvas.getContext('2d');
    const fullCanvas = document.getElementById('fullmap-canvas');
    const fullCtx = fullCanvas.getContext('2d');

    function renderRadarMap(ctx, size, isFullMap = false) {
      const p = state.player;
      ctx.clearRect(0, 0, size, size);

      // Ocean background
      ctx.fillStyle = '#041224';
      ctx.fillRect(0, 0, size, size);

      ctx.save();
      if (!isFullMap) {
        // Rotate minimap around player heading
        ctx.translate(size / 2, size / 2);
        ctx.rotate(p.yaw);
        ctx.scale(0.32, 0.32);
        ctx.translate(-p.pos.x, -p.pos.z);
      } else {
        const scale = size / ISLAND_SIZE;
        ctx.scale(scale, scale);
        ctx.translate(HALF_ISLAND, HALF_ISLAND);
      }

      // Island landmass silhouette
      ctx.fillStyle = '#1e3a29';
      ctx.beginPath();
      ctx.arc(0, 0, 525, 0, Math.PI * 2);
      ctx.fill();

      // Sandy beach rim
      ctx.strokeStyle = '#d6c289';
      ctx.lineWidth = 18;
      ctx.stroke();

      // Roads
      ctx.strokeStyle = '#475569';
      ctx.lineWidth = 8;
      ctx.beginPath();
      ctx.moveTo(-420, 84);
      ctx.lineTo(420, -84);
      ctx.moveTo(-65, -420);
      ctx.lineTo(65, 420);
      ctx.stroke();

      // POI Markers
      POIS.forEach(poi => {
        ctx.fillStyle = poi.color;
        ctx.beginPath();
        ctx.arc(poi.x, poi.z, 12, 0, Math.PI * 2);
        ctx.fill();
        if (isFullMap) {
          ctx.font = 'bold 26px Chakra Petch, sans-serif';
          ctx.fillStyle = '#f8fafc';
          ctx.fillText(poi.name, poi.x - 60, poi.z - 18);
        }
      });

      // Vehicles
      state.vehicles.forEach(v => {
        ctx.fillStyle = '#facc15';
        ctx.fillRect(v.pos.x - 9, v.pos.z - 9, 18, 18);
      });

      // Next Safe Zone (White Circle)
      ctx.strokeStyle = '#ffffff';
      ctx.lineWidth = 6;
      ctx.beginPath();
      ctx.arc(state.nextZone.x, state.nextZone.z, state.nextZone.radius, 0, Math.PI * 2);
      ctx.stroke();

      // Current Electric Storm Circle (Neon Blue)
      ctx.strokeStyle = '#0ea5e9';
      ctx.lineWidth = 9;
      ctx.beginPath();
      ctx.arc(state.currentZone.x, state.currentZone.z, state.currentZone.radius, 0, Math.PI * 2);
      ctx.stroke();

      // Player Arrow
      ctx.save();
      ctx.translate(p.pos.x, p.pos.z);
      ctx.rotate(-p.yaw);
      ctx.fillStyle = '#f97316';
      ctx.beginPath();
      ctx.moveTo(0, -18);
      ctx.lineTo(13, 14);
      ctx.lineTo(0, 7);
      ctx.lineTo(-13, 14);
      ctx.closePath();
      ctx.fill();
      ctx.restore();

      ctx.restore();
    }

    /* =========================================================
       MODULE 16: HUD SYNCHRONIZATION
    ========================================================== */
    function updateHUD() {
      const p = state.player;
      document.getElementById('hud-hp-bar').style.width = `${(p.hp / p.maxHp) * 100}%`;
      document.getElementById('hud-hp-text').textContent = `${Math.ceil(p.hp)} / ${p.maxHp}`;
      document.getElementById('hud-armor-bar').style.width = `${(p.armor / p.maxArmor) * 100}%`;
      document.getElementById('hud-armor-text').textContent = `${Math.ceil(p.armor)} / ${p.maxArmor}`;
      document.getElementById('hud-medkit-count').textContent = p.medkits;
      document.getElementById('hud-kills-count').textContent = p.kills;

      const aliveBots = state.bots.filter(b => b.alive).length;
      document.getElementById('hud-alive-count').textContent = aliveBots + (p.hp > 0 ? 1 : 0);

      // Active weapon & slots
      const curW = p.weapons[p.activeSlot];
      document.getElementById('hud-weapon-name').textContent = curW ? curW.name : 'UNARMED';
      document.getElementById('hud-ammo-mag').textContent = curW ? curW.mag : '--';
      document.getElementById('hud-ammo-reserve').textContent = curW ? curW.reserve : '--';

      for (let i = 0; i < 3; i++) {
        const slotEl = document.getElementById(`slot-${i}`);
        const nameEl = document.getElementById(`slot-${i}-name`);
        const w = p.weapons[i];
        slotEl.className = `weapon-slot hud-glass w-24 sm:w-28 p-2 rounded-lg text-left transition ${
          i === p.activeSlot ? 'border-2 border-cyan-400 opacity-100' : 'border border-slate-700 opacity-70'
        } ${w ? 'rarity-' + w.rarity : ''}`;
        nameEl.textContent = w ? w.name : 'EMPTY';
        nameEl.className = w ? 'text-xs font-tactical font-bold text-white truncate' : 'text-xs font-tactical font-bold text-slate-500 truncate';
      }

      // Compass & POI proximity
      const deg = (((-p.yaw * 180) / Math.PI) % 360 + 360) % 360;
      document.getElementById('hud-heading').textContent = `HDG ${String(Math.round(deg)).padStart(3, '0')}°`;
      document.getElementById('hud-altitude').textContent = `ALT ${Math.max(0, Math.round(p.pos.y))}m`;

      let nearestPOI = 'APEX ISLE WILDERNESS';
      for (const poi of POIS) {
        if (Math.hypot(p.pos.x - poi.x, p.pos.z - poi.z) < poi.radius) {
          nearestPOI = poi.name;
          break;
        }
      }
      document.getElementById('hud-poi-name').textContent = nearestPOI;

      // Zone timer
      const zMin = String(Math.floor(state.zoneTimer / 60)).padStart(2, '0');
      const zSec = String(Math.floor(state.zoneTimer % 60)).padStart(2, '0');
      document.getElementById('hud-zone-timer').textContent = state.zoneShrinking
        ? `⚡ STORM CLOSING (PHASE ${state.zonePhase})`
        : `ZONE ${state.zonePhase}: ${zMin}:${zSec}`;
    }

    /* =========================================================
       MODULE 17: MAIN SIMULATION LOOP (60 FPS)
    ========================================================== */
    let lastTime = performance.now();
    let frameCounter = 0;
    let fpsTimer = 0;

    function updateSimulation(dt) {
      if (state.screen === 'MENU') {
        // Rotate camera smoothly around operator in Lobby
        const t = performance.now() * 0.0005;
        playerMesh.position.set(0, getTerrainHeight(0, 0), 0);
        playerMesh.rotation.y = Math.sin(t) * 0.6;
        camera.position.set(Math.sin(t * 0.4) * 3.4, playerMesh.position.y + 1.85, Math.cos(t * 0.4) * 3.4);
        camera.lookAt(0, playerMesh.position.y + 1.25, 0);
        return;
      }

      if (state.screen === 'PAUSED' || state.screen === 'ENDED') return;

      state.matchTime += dt;
      const p = state.player;

      // 1. Update Safe Zone & Storm Damage
      if (state.mode !== 'TRAINING') {
        state.zoneTimer -= dt;
        if (state.zoneTimer <= 0) {
          if (!state.zoneShrinking) {
            state.zoneShrinking = true;
            state.zoneTimer = 28;
            sound.playStormSiren();
          } else {
            state.zoneShrinking = false;
            state.zonePhase = Math.min(5, state.zonePhase + 1);
            state.zoneTimer = 50;
            state.currentZone = { ...state.nextZone };
            const nextRad = Math.max(28, state.currentZone.radius * 0.56);
            state.nextZone = {
              x: state.currentZone.x + (Math.random() - 0.5) * nextRad * 0.6,
              z: state.currentZone.z + (Math.random() - 0.5) * nextRad * 0.6,
              radius: nextRad
            };
          }
        }
        if (state.zoneShrinking) {
          const lerpSpeed = dt * 0.035;
          state.currentZone.x += (state.nextZone.x - state.currentZone.x) * lerpSpeed;
          state.currentZone.z += (state.nextZone.z - state.currentZone.z) * lerpSpeed;
          state.currentZone.radius += (state.nextZone.radius - state.currentZone.radius) * lerpSpeed;
        }
      }

      stormCylinder.position.set(state.currentZone.x, 90, state.currentZone.z);
      stormCylinder.scale.set(state.currentZone.radius, 1, state.currentZone.radius);

      // Storm damage if player outside electric circle
      const distToZone = Math.hypot(p.pos.x - state.currentZone.x, p.pos.z - state.currentZone.z);
      const stormVig = document.getElementById('storm-vignette');
      if (distToZone > state.currentZone.radius && state.screen === 'PLAYING') {
        stormVig.style.opacity = '1';
        damagePlayer((2.5 + state.zonePhase * 1.6) * dt, 'ELECTRIC STORM');
      } else {
        stormVig.style.opacity = '0';
      }

      // 2. Dropship Phase
      if (state.screen === 'DROPSHIP') {
        state.dropship.progress += dt * 0.042;
        dropshipMesh.position.lerpVectors(state.dropship.start, state.dropship.end, state.dropship.progress);
        dropshipMesh.lookAt(state.dropship.end);
        p.pos.copy(dropshipMesh.position);
        playerMesh.visible = false;

        camera.position.set(p.pos.x - Math.sin(p.yaw) * 24, p.pos.y + 11, p.pos.z - Math.cos(p.yaw) * 24);
        camera.lookAt(p.pos);

        if (state.dropship.progress >= 0.95) jumpFromDropship();
        renderRadarMap(miniCtx, 144, false);
        updateHUD();
        return;
      }

      // 3. Skydiving & Parachute Phase
      if (state.screen === 'SKYDIVING') {
        playerMesh.visible = true;
        const moveForward = (state.keys['KeyW'] ? 1 : 0) - (state.keys['KeyS'] ? 1 : 0) - state.joyMove.y;
        const moveStrafe = (state.keys['KeyD'] ? 1 : 0) - (state.keys['KeyA'] ? 1 : 0) + state.joyMove.x;

        const forwardVec = new THREE.Vector3(-Math.sin(p.yaw), 0, -Math.cos(p.yaw));
        const rightVec = new THREE.Vector3(Math.cos(p.yaw), 0, -Math.sin(p.yaw));

        const glideSpeed = p.parachuteOpen ? 19 : 26;
        p.pos.addScaledVector(forwardVec, moveForward * glideSpeed * dt);
        p.pos.addScaledVector(rightVec, moveStrafe * glideSpeed * 0.75 * dt);

        const groundY = getSupportHeight(p.pos.x, p.pos.z, p.pos.y);
        // Auto-deploy parachute at 32m above terrain
        if (!p.parachuteOpen && p.pos.y - groundY < 32) {
          p.parachuteOpen = true;
          playerMesh.userData.chuteGroup.visible = true;
        }

        const fallRate = p.parachuteOpen ? 8.5 : 28;
        p.pos.y -= fallRate * dt;

        playerMesh.position.copy(p.pos);
        playerMesh.rotation.y = p.yaw + Math.PI;

        camera.position.set(
          p.pos.x + Math.sin(p.yaw) * 6.5,
          p.pos.y + 3.2,
          p.pos.z + Math.cos(p.yaw) * 6.5
        );
        camera.lookAt(p.pos.x, p.pos.y + 1.2, p.pos.z);

        if (p.pos.y <= groundY + 0.1) {
          p.pos.y = groundY;
          p.parachuteOpen = false;
          playerMesh.userData.chuteGroup.visible = false;
          document.getElementById('dropship-banner').classList.add('hidden');
          state.screen = 'PLAYING';
        }
        renderRadarMap(miniCtx, 144, false);
        updateHUD();
        return;
      }

      // 4. Active Ground / Vehicle Combat Movement
      if (p.fireCooldown > 0) p.fireCooldown -= dt;
      if ((state.mouseDown || state.mobileFiring) && p.weapons[p.activeSlot]?.auto) {
        firePlayerWeapon();
      }

      // Reload progress
      if (p.reloading) {
        const w = p.weapons[p.activeSlot];
        p.reloadProgress += dt / (w ? w.reloadTime : 1.5);
        document.getElementById('reload-bar').style.width = `${Math.min(100, p.reloadProgress * 100)}%`;
        if (p.reloadProgress >= 1 && w) {
          const needed = w.magSize - w.mag;
          const take = Math.min(needed, w.reserve);
          w.mag += take;
          w.reserve -= take;
          p.reloading = false;
          document.getElementById('reload-indicator').classList.add('hidden');
        }
      }

      const forwardInput = (state.keys['KeyW'] ? 1 : 0) - (state.keys['KeyS'] ? 1 : 0) - state.joyMove.y;
      const strafeInput = (state.keys['KeyD'] ? 1 : 0) - (state.keys['KeyA'] ? 1 : 0) + state.joyMove.x;

      if (p.inVehicle) {
        // Vehicle Driving Physics
        const v = p.inVehicle;
        playerMesh.visible = false;
        document.getElementById('vehicle-hud').classList.remove('hidden');

        if (forwardInput !== 0) {
          v.speed += forwardInput * 22 * dt;
        } else {
          v.speed *= Math.pow(0.25, dt); // Natural rolling friction
        }
        if (state.keys['Space']) v.speed *= Math.pow(0.05, dt); // Handbrake

        v.speed = Math.max(-v.maxSpeed * 0.45, Math.min(v.maxSpeed, v.speed));
        if (Math.abs(v.speed) > 0.5) {
          v.yaw -= strafeInput * 1.9 * dt * Math.sign(v.speed);
        }

        v.pos.x += Math.sin(v.yaw) * v.speed * dt;
        v.pos.z += Math.cos(v.yaw) * v.speed * dt;
        resolveHorizontalCollisions(v.pos, 1.8);
        v.pos.y = getTerrainHeight(v.pos.x, v.pos.z) + 0.2;

        v.mesh.position.copy(v.pos);
        v.mesh.rotation.y = v.yaw;
        p.pos.copy(v.pos);

        document.getElementById('veh-name').textContent = v.name;
        document.getElementById('veh-speed').innerHTML = `${Math.round(Math.abs(v.speed) * 3.6)} <span class="text-xs text-slate-400">KM/H</span>`;
        document.getElementById('veh-hp-bar').style.width = `${(v.hp / v.maxHp) * 100}%`;

        // Chase camera behind vehicle
        const camDist = 8.2;
        camera.position.set(
          v.pos.x - Math.sin(p.yaw) * camDist,
          v.pos.y + 3.4 - Math.sin(p.pitch) * 3.5,
          v.pos.z - Math.cos(p.yaw) * camDist
        );
        camera.lookAt(v.pos.x, v.pos.y + 1.4, v.pos.z);
      } else {
        // On-Foot Tactical Movement
        document.getElementById('vehicle-hud').classList.add('hidden');
        playerMesh.visible = p.cameraMode === 'TPS' && !p.ads;

        const isSprinting = state.keys['ShiftLeft'] && forwardInput > 0.2 && p.stance === 'STAND' && !p.ads;
        let baseSpeed = 7.4 * p.speedMult;
        if (isSprinting) baseSpeed *= 1.42;
        if (p.stance === 'CROUCH') baseSpeed *= 0.58;
        if (p.stance === 'PRONE') baseSpeed *= 0.34;
        if (p.ads) baseSpeed *= 0.65;

        const forwardVec = new THREE.Vector3(-Math.sin(p.yaw), 0, -Math.cos(p.yaw));
        const rightVec = new THREE.Vector3(Math.cos(p.yaw), 0, -Math.sin(p.yaw));
        const wishDir = new THREE.Vector3()
          .addScaledVector(forwardVec, forwardInput)
          .addScaledVector(rightVec, strafeInput);
        if (wishDir.lengthSq() > 1) wishDir.normalize();

        p.pos.x += wishDir.x * baseSpeed * dt;
        p.pos.z += wishDir.z * baseSpeed * dt;
        resolveHorizontalCollisions(p.pos, 0.45);

        // Gravity & Jumping
        const supportY = getSupportHeight(p.pos.x, p.pos.z, p.pos.y);
        if (p.pos.y <= supportY + 0.05 && p.vel.y <= 0) {
          p.pos.y = supportY;
          p.vel.y = 0;
          p.grounded = true;
        } else {
          p.vel.y -= 24 * dt;
          p.pos.y += p.vel.y * dt;
          p.grounded = false;
        }

        // Stance & Limb Animation
        playerMesh.position.copy(p.pos);
        playerMesh.rotation.y = p.yaw + Math.PI;
        playerMesh.scale.y = p.stance === 'CROUCH' ? 0.72 : (p.stance === 'PRONE' ? 0.38 : 1.0);

        const movingMag = wishDir.length();
        if (movingMag > 0.1 && p.grounded) {
          const swing = Math.sin(state.matchTime * (isSprinting ? 14 : 9)) * 0.55;
          playerMesh.userData.leftLeg.rotation.x = swing;
          playerMesh.userData.rightLeg.rotation.x = -swing;
          p.stepTimer -= dt;
          if (p.stepTimer <= 0) {
            sound.playFootstep();
            p.stepTimer = isSprinting ? 0.32 : 0.48;
          }
        } else {
          playerMesh.userData.leftLeg.rotation.x = 0;
          playerMesh.userData.rightLeg.rotation.x = 0;
        }

        // Camera Positioning (FPS vs TPS + ADS Zoom)
        const eyeHeight = p.stance === 'CROUCH' ? 1.15 : (p.stance === 'PRONE' ? 0.55 : 1.65);
        const activeW = p.weapons[p.activeSlot];
        const targetFov = p.ads && activeW ? 68 / activeW.adsZoom : (isSprinting ? 74 : 68);
        camera.fov += (targetFov - camera.fov) * Math.min(1, dt * 12);
        camera.updateProjectionMatrix();

        // Sniper 4x scope overlay
        const scopeEl = document.getElementById('sniper-scope');
        if (p.ads && activeW?.id === 'VIPER50') {
          scopeEl.classList.remove('hidden');
          playerMesh.visible = false;
        } else {
          scopeEl.classList.add('hidden');
        }

        if (p.cameraMode === 'FPS' || p.ads) {
          camera.position.set(p.pos.x, p.pos.y + eyeHeight, p.pos.z);
          const lookTarget = new THREE.Vector3(
            p.pos.x - Math.sin(p.yaw) * Math.cos(p.pitch) * 20,
            p.pos.y + eyeHeight + Math.sin(p.pitch) * 20,
            p.pos.z - Math.cos(p.yaw) * Math.cos(p.pitch) * 20
          );
          camera.lookAt(lookTarget);
        } else {
          // Over-the-shoulder Tactical Third-Person Camera
          const shoulderRight = new THREE.Vector3(Math.cos(p.yaw) * 0.65, 0, -Math.sin(p.yaw) * 0.65);
          const camBack = 3.6;
          camera.position.set(
            p.pos.x + Math.sin(p.yaw) * camBack + shoulderRight.x,
            p.pos.y + eyeHeight + 0.45 - Math.sin(p.pitch) * 2.1,
            p.pos.z + Math.cos(p.yaw) * camBack + shoulderRight.z
          );
          const aimPoint = new THREE.Vector3(
            p.pos.x - Math.sin(p.yaw) * 25 + shoulderRight.x,
            p.pos.y + eyeHeight + Math.sin(p.pitch) * 25,
            p.pos.z - Math.cos(p.yaw) * 25 + shoulderRight.z
          );
          camera.lookAt(aimPoint);
        }

        // Update Night Ops Flashlight
        if (state.weather === 'NIGHT') {
          playerFlashlight.position.copy(camera.position);
          const fDir = new THREE.Vector3();
          camera.getWorldDirection(fDir);
          playerFlashlight.target.position.copy(camera.position).addScaledVector(fDir, 15);
        }
      }

      // 5. Loot & Vehicle Proximity Interaction Prompt
      checkNearbyInteractions();

      // 6. Update Bullet Tracers
      for (let i = state.bullets.length - 1; i >= 0; i--) {
        const b = state.bullets[i];
        b.mesh.position.addScaledVector(b.vel, dt);
        b.life -= dt;
        if (b.life <= 0) {
          tracerGroup.remove(b.mesh);
          state.bullets.splice(i, 1);
        }
      }

      // 7. Update Training Range Moving Targets
      if (state.mode === 'TRAINING') {
        state.targets.forEach(t => {
          t.mesh.position.x += t.speed * dt;
          if (Math.abs(t.mesh.position.x - t.baseX) > 10) t.speed *= -1;
        });
      }

      // 8. Update Intelligent Enemy AI Bots
      updateAIBots(dt);

      // 9. Storm Tempest Rain & Lightning FX
      if (state.weather === 'STORM') {
        rainSystem.position.set(p.pos.x, p.pos.y, p.pos.z);
        const rPos = rainSystem.geometry.attributes.position;
        for (let i = 0; i < rainCount; i++) {
          let ry = rPos.getY(i) - 42 * dt;
          if (ry < -5) ry = 55;
          rPos.setY(i, ry);
        }
        rPos.needsUpdate = true;

        if (Math.random() < dt * 0.08) {
          sunLight.intensity = 4.5;
          sound.playThunder();
          setTimeout(() => { if (state.weather === 'STORM') sunLight.intensity = 0.55; }, 120);
        }
      }

      // 10. Broadcast Multiplayer Position Update
      if (state.wsConnected && state.ws && frameCounter % 3 === 0) {
        state.ws.send(JSON.stringify({
          type: 'MOVE',
          x: p.pos.x,
          y: p.pos.y,
          z: p.pos.z,
          yaw: p.yaw,
          hp: p.hp
        }));
      }

      renderRadarMap(miniCtx, 144, false);
      updateHUD();
    }

    function updateAIBots(dt) {
      const p = state.player;
      let aliveCount = 0;

      for (const bot of state.bots) {
        if (!bot.alive) continue;
        aliveCount++;

        // Storm damage on bots
        if (Math.hypot(bot.pos.x - state.currentZone.x, bot.pos.z - state.currentZone.z) > state.currentZone.radius) {
          bot.hp -= 3.5 * dt;
          if (bot.hp <= 0) {
            bot.alive = false;
            bot.mesh.rotation.x = Math.PI / 2;
            addKillFeedMessage('ELECTRIC STORM', bot.name, 'ZONE', false);
            continue;
          }
        }

        // Check distance to player or nearest other bot
        const distToPlayer = bot.pos.distanceTo(p.pos);
        let targetPos = null;
        let targetIsPlayer = false;

        if (distToPlayer < 88 && p.hp > 0) {
          targetPos = p.pos;
          targetIsPlayer = true;
        } else {
          // Look for nearby rival AI bot
          for (const other of state.bots) {
            if (other !== bot && other.alive && bot.pos.distanceTo(other.pos) < 65) {
              targetPos = other.pos;
              break;
            }
          }
        }

        if (targetPos) {
          // Face target & strafe tactically
          const dx = targetPos.x - bot.pos.x;
          const dz = targetPos.z - bot.pos.z;
          bot.yaw = Math.atan2(dx, dz);
          bot.mesh.rotation.y = bot.yaw;

          if (bot.pos.distanceTo(targetPos) > 22) {
            bot.pos.x += Math.sin(bot.yaw) * 4.5 * dt;
            bot.pos.z += Math.cos(bot.yaw) * 4.5 * dt;
          }

          bot.fireCooldown -= dt;
          if (bot.fireCooldown <= 0) {
            bot.fireCooldown = 0.65 + Math.random() * 0.9;
            const origin = bot.pos.clone().add(new THREE.Vector3(0, 1.35, 0));
            const aimDir = targetPos.clone().add(new THREE.Vector3(0, 1.1, 0)).sub(origin).normalize();
            spawnBulletTracer(origin, aimDir, 0xf97316);

            if (targetIsPlayer && Math.random() < 0.28) {
              damagePlayer(9 + Math.random() * 7, bot.name);
            } else if (!targetIsPlayer && Math.random() < 0.35) {
              // Bot-vs-Bot combat
              for (const other of state.bots) {
                if (other.pos === targetPos && other.alive) {
                  other.hp -= 24;
                  if (other.hp <= 0) {
                    other.alive = false;
                    other.mesh.rotation.x = Math.PI / 2;
                    addKillFeedMessage(bot.name, other.name, bot.weapon.name, false);
                  }
                  break;
                }
              }
            }
          }
        } else {
          // Patrol toward safe zone center
          bot.retargetTimer -= dt;
          if (bot.retargetTimer <= 0) {
            bot.retargetTimer = 3.5 + Math.random() * 3;
            bot.moveTarget.set(
              state.nextZone.x + (Math.random() - 0.5) * state.nextZone.radius * 0.85,
              0,
              state.nextZone.z + (Math.random() - 0.5) * state.nextZone.radius * 0.85
            );
          }
          const dx = bot.moveTarget.x - bot.pos.x;
          const dz = bot.moveTarget.z - bot.pos.z;
          const dist = Math.hypot(dx, dz);
          if (dist > 3) {
            bot.yaw = Math.atan2(dx, dz);
            bot.pos.x += Math.sin(bot.yaw) * 4.8 * dt;
            bot.pos.z += Math.cos(bot.yaw) * 4.8 * dt;
            bot.mesh.rotation.y = bot.yaw;
          }
        }

        resolveHorizontalCollisions(bot.pos, 0.45);
        bot.pos.y = getTerrainHeight(bot.pos.x, bot.pos.z);
        bot.mesh.position.copy(bot.pos);
        const swing = Math.sin(state.matchTime * 8 + bot.pos.x) * 0.45;
        bot.mesh.userData.leftLeg.rotation.x = swing;
        bot.mesh.userData.rightLeg.rotation.x = -swing;
      }

      // Check Victory Royale condition
      if (state.mode !== 'TRAINING' && state.screen === 'PLAYING' && aliveCount === 0 && p.hp > 0) {
        endMatch(true);
      }
    }

    let nearestLoot = null;
    let nearestVehicle = null;

    function checkNearbyInteractions() {
      const p = state.player;
      nearestLoot = null;
      nearestVehicle = null;

      const promptBox = document.getElementById('interact-prompt');
      const promptText = document.getElementById('interact-text');
      const mLootBtn = document.getElementById('m-btn-loot');
      const mVehBtn = document.getElementById('m-btn-veh');

      // Rotate loot items gently
      for (const item of state.lootItems) {
        if (item.collected) continue;
        item.mesh.rotation.y += 0.025;
        if (Math.hypot(p.pos.x - item.x, p.pos.z - item.z) < 2.8) {
          nearestLoot = item;
        }
      }

      for (const v of state.vehicles) {
        if (Math.hypot(p.pos.x - v.pos.x, p.pos.z - v.pos.z) < 4.2) {
          nearestVehicle = v;
        }
      }

      if (p.inVehicle) {
        promptBox.classList.remove('hidden');
        promptText.textContent = '[E] EXIT VEHICLE';
        mVehBtn.classList.remove('hidden');
      } else if (nearestLoot) {
        promptBox.classList.remove('hidden');
        promptText.textContent = `[F] PICK UP ${nearestLoot.label}`;
        mLootBtn.classList.remove('hidden');
      } else if (nearestVehicle) {
        promptBox.classList.remove('hidden');
        promptText.textContent = `[E] DRIVE ${nearestVehicle.name}`;
        mVehBtn.classList.remove('hidden');
        mLootBtn.classList.add('hidden');
      } else {
        promptBox.classList.add('hidden');
        mLootBtn.classList.add('hidden');
        mVehBtn.classList.add('hidden');
      }
    }

    function pickupNearestLoot() {
      if (!nearestLoot || nearestLoot.collected) return;
      const p = state.player;
      nearestLoot.collected = true;
      lootGroup.remove(nearestLoot.mesh);
      sound.playTone(620, 940, 0.12, 'sine', 0.25);

      if (nearestLoot.kind === 'MEDKIT') {
        p.medkits++;
      } else if (nearestLoot.kind === 'ARMOR') {
        p.armor = Math.min(p.maxArmor, p.armor + 50);
      } else if (nearestLoot.kind === 'AMMO') {
        p.weapons.forEach(w => { if (w) w.reserve += 45; });
      } else if (nearestLoot.kind === 'WEAPON') {
        const wTemplate = WEAPON_CATALOG[nearestLoot.weaponKey];
        const newWeapon = { ...wTemplate, mag: wTemplate.magSize, reserve: wTemplate.magSize * 3 };
        // Place in empty slot or replace active slot
        const emptyIdx = p.weapons.findIndex(slot => slot === null);
        if (emptyIdx !== -1) {
          p.weapons[emptyIdx] = newWeapon;
          p.activeSlot = emptyIdx;
        } else {
          p.weapons[p.activeSlot] = newWeapon;
        }
      }
      updateHUD();
    }

    function toggleVehicle() {
      const p = state.player;
      if (p.inVehicle) {
        p.pos.x += Math.cos(p.inVehicle.yaw) * 2.5;
        p.pos.z -= Math.sin(p.inVehicle.yaw) * 2.5;
        p.inVehicle = null;
      } else if (nearestVehicle) {
        p.inVehicle = nearestVehicle;
        p.ads = false;
      }
      updateHUD();
    }

    /* =========================================================
       MODULE 18: INPUT HANDLERS (WINDOWS PC + ANDROID TOUCH)
    ========================================================== */
    window.addEventListener('keydown', (e) => {
      state.keys[e.code] = true;

      if (e.code === 'Space') {
        if (state.screen === 'DROPSHIP' || state.screen === 'SKYDIVING') {
          jumpFromDropship();
        } else if (state.screen === 'PLAYING' && state.player.grounded && !state.player.inVehicle) {
          state.player.vel.y = 9.8;
          state.player.grounded = false;
          state.player.stance = 'STAND';
        }
      } else if (e.code === 'KeyC' && state.screen === 'PLAYING') {
        state.player.stance = state.player.stance === 'CROUCH' ? 'STAND' : 'CROUCH';
      } else if (e.code === 'KeyZ' && state.screen === 'PLAYING') {
        state.player.stance = state.player.stance === 'PRONE' ? 'STAND' : 'PRONE';
      } else if (e.code === 'KeyR') {
        startReload();
      } else if (e.code === 'Digit1') {
        state.player.activeSlot = 0; state.player.reloading = false; updateHUD();
      } else if (e.code === 'Digit2') {
        state.player.activeSlot = 1; state.player.reloading = false; updateHUD();
      } else if (e.code === 'Digit3') {
        state.player.activeSlot = 2; state.player.reloading = false; updateHUD();
      } else if (e.code === 'KeyF') {
        pickupNearestLoot();
      } else if (e.code === 'KeyE') {
        toggleVehicle();
      } else if (e.code === 'KeyV') {
        state.player.cameraMode = state.player.cameraMode === 'TPS' ? 'FPS' : 'TPS';
        document.getElementById('hud-cam-mode').textContent = state.player.cameraMode;
      } else if (e.code === 'Digit7') {
        useMedkit();
      } else if (e.code === 'KeyM') {
        toggleFullMap();
      } else if (e.code === 'Tab') {
        e.preventDefault();
        toggleInventoryModal();
      } else if (e.code === 'Escape') {
        togglePauseMenu();
      }
    });

    window.addEventListener('keyup', (e) => {
      state.keys[e.code] = false;
    });

    // Mouse Look & Fire
    canvas.addEventListener('mousedown', (e) => {
      if (state.screen !== 'PLAYING' && state.screen !== 'SKYDIVING' && state.screen !== 'DROPSHIP') return;
      if (document.pointerLockElement !== canvas && canvas.requestPointerLock) {
        try { canvas.requestPointerLock(); } catch (err) {}
      }
      if (e.button === 0) {
        state.mouseDown = true;
        firePlayerWeapon();
      } else if (e.button === 2) {
        state.player.ads = true;
      }
    });

    window.addEventListener('mouseup', (e) => {
      if (e.button === 0) state.mouseDown = false;
      if (e.button === 2) state.player.ads = false;
    });

    window.addEventListener('contextmenu', (e) => e.preventDefault());

    window.addEventListener('mousemove', (e) => {
      if (document.pointerLockElement === canvas) {
        const sens = 0.0022 * state.sensitivity;
        state.player.yaw -= e.movementX * sens;
        state.player.pitch = Math.max(-1.25, Math.min(1.25, state.player.pitch - e.movementY * sens));
      }
    });

    // Mobile Virtual Joystick & Touch Aiming
    const joyZone = document.getElementById('joy-left-zone');
    const joyKnob = document.getElementById('joy-left-knob');
    let joyTouchId = null;

    joyZone.addEventListener('touchstart', (e) => {
      e.preventDefault();
      const t = e.changedTouches[0];
      joyTouchId = t.identifier;
      updateJoystick(t);
    }, { passive: false });

    joyZone.addEventListener('touchmove', (e) => {
      e.preventDefault();
      for (const t of e.changedTouches) {
        if (t.identifier === joyTouchId) updateJoystick(t);
      }
    }, { passive: false });

    const resetJoystick = (e) => {
      for (const t of e.changedTouches) {
        if (t.identifier === joyTouchId) {
          joyTouchId = null;
          state.joyMove = { x: 0, y: 0 };
          joyKnob.style.transform = 'translate(0px, 0px)';
        }
      }
    };
    joyZone.addEventListener('touchend', resetJoystick);
    joyZone.addEventListener('touchcancel', resetJoystick);

    function updateJoystick(touch) {
      const rect = joyZone.getBoundingClientRect();
      const cx = rect.left + rect.width / 2;
      const cy = rect.top + rect.height / 2;
      const maxR = rect.width / 2 - 16;
      let dx = touch.clientX - cx;
      let dy = touch.clientY - cy;
      const dist = Math.hypot(dx, dy);
      if (dist > maxR) {
        dx = (dx / dist) * maxR;
        dy = (dy / dist) * maxR;
      }
      joyKnob.style.transform = `translate(${dx}px, ${dy}px)`;
      state.joyMove.x = dx / maxR;
      state.joyMove.y = dy / maxR;
    }

    // Touch Camera Look Zone
    const lookZone = document.getElementById('touch-look-zone');
    let lookTouchId = null;
    let lastLookX = 0;
    let lastLookY = 0;

    lookZone.addEventListener('touchstart', (e) => {
      const t = e.changedTouches[0];
      lookTouchId = t.identifier;
      lastLookX = t.clientX;
      lastLookY = t.clientY;
    }, { passive: true });

    lookZone.addEventListener('touchmove', (e) => {
      for (const t of e.changedTouches) {
        if (t.identifier === lookTouchId) {
          const dx = t.clientX - lastLookX;
          const dy = t.clientY - lastLookY;
          lastLookX = t.clientX;
          lastLookY = t.clientY;
          const sens = 0.0055 * state.sensitivity;
          state.player.yaw -= dx * sens;
          state.player.pitch = Math.max(-1.25, Math.min(1.25, state.player.pitch - dy * sens));
        }
      }
    }, { passive: true });

    lookZone.addEventListener('touchend', (e) => {
      for (const t of e.changedTouches) {
        if (t.identifier === lookTouchId) lookTouchId = null;
      }
    });

    // Mobile HUD Buttons
    const fireBtn = document.getElementById('m-btn-fire');
    fireBtn.addEventListener('touchstart', (e) => { e.preventDefault(); state.mobileFiring = true; firePlayerWeapon(); });
    fireBtn.addEventListener('touchend', () => { state.mobileFiring = false; });

    document.getElementById('m-btn-ads').addEventListener('click', () => { state.player.ads = !state.player.ads; });
    document.getElementById('m-btn-reload').addEventListener('click', startReload);
    document.getElementById('m-btn-loot').addEventListener('click', pickupNearestLoot);
    document.getElementById('m-btn-veh').addEventListener('click', toggleVehicle);
    document.getElementById('m-btn-crouch').addEventListener('click', () => {
      state.player.stance = state.player.stance === 'CROUCH' ? 'STAND' : 'CROUCH';
    });
    document.getElementById('m-btn-prone').addEventListener('click', () => {
      state.player.stance = state.player.stance === 'PRONE' ? 'STAND' : 'PRONE';
    });
    document.getElementById('m-btn-jump').addEventListener('click', () => {
      if (state.screen === 'DROPSHIP' || state.screen === 'SKYDIVING') jumpFromDropship();
      else if (state.player.grounded) { state.player.vel.y = 9.8; state.player.grounded = false; }
    });

    /* =========================================================
       MODULE 19: UI BUTTON BINDINGS & MODAL CONTROLS
    ========================================================== */
    // Operator Selection
    document.getElementById('op-spectre').addEventListener('click', () => {
      state.operator = 'SPECTRE';
      document.getElementById('op-spectre').className = 'op-card p-3 rounded-lg border-2 border-cyan-400 bg-slate-900/90 text-left transition';
      document.getElementById('op-valkyrie').className = 'op-card p-3 rounded-lg border border-slate-700 bg-slate-900/60 text-left transition';
      document.getElementById('operator-perk-badge').textContent = 'STEALTH RECON';
      document.getElementById('lobby-operator-title').textContent = 'OPERATOR: SPECTRE';
      rebuildPlayerAvatar();
    });

    document.getElementById('op-valkyrie').addEventListener('click', () => {
      state.operator = 'VALKYRIE';
      document.getElementById('op-valkyrie').className = 'op-card p-3 rounded-lg border-2 border-orange-400 bg-slate-900/90 text-left transition';
      document.getElementById('op-spectre').className = 'op-card p-3 rounded-lg border border-slate-700 bg-slate-900/60 text-left transition';
      document.getElementById('operator-perk-badge').textContent = 'CYBER ARMOR +15';
      document.getElementById('lobby-operator-title').textContent = 'OPERATOR: VALKYRIE';
      rebuildPlayerAvatar();
    });

    // Skins
    document.querySelectorAll('.skin-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.skin-btn').forEach(b => b.classList.remove('border-2', 'border-orange-400'));
        btn.classList.add('border-2', 'border-orange-400');
        state.skinIndex = parseInt(btn.dataset.skin, 10);
        rebuildPlayerAvatar();
      });
    });

    // Weather
    document.querySelectorAll('.weather-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.weather-btn').forEach(b => b.classList.remove('border-2', 'border-cyan-400'));
        btn.classList.add('border-2', 'border-cyan-400');
        applyWeatherPreset(btn.dataset.weather);
      });
    });

    // Game Modes
    document.querySelectorAll('.mode-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        document.querySelectorAll('.mode-btn').forEach(b => {
          b.classList.remove('border-2', 'border-orange-500');
          b.classList.add('border', 'border-slate-700');
        });
        btn.classList.remove('border', 'border-slate-700');
        btn.classList.add('border-2', 'border-orange-500');
        state.mode = btn.dataset.mode;

        const mpPanel = document.getElementById('mp-lobby-panel');
        if (state.mode === 'MULTIPLAYER') mpPanel.classList.remove('hidden');
        else mpPanel.classList.add('hidden');
      });
    });

    // Multiplayer WS buttons
    document.getElementById('btn-ws-connect').addEventListener('click', connectWebSocket);
    document.getElementById('btn-create-room').addEventListener('click', () => {
      const code = Math.random().toString(36).substring(2, 7).toUpperCase();
      state.roomCode = code;
      document.getElementById('room-code-input').value = code;
      document.getElementById('mp-room-info').textContent = `ROOM CREATED: ${code} — Share code with squad!`;
      if (state.wsConnected && state.ws) state.ws.send(JSON.stringify({ type: 'CREATE_ROOM', code }));
    });
    document.getElementById('btn-join-room').addEventListener('click', () => {
      const code = document.getElementById('room-code-input').value.trim().toUpperCase();
      if (!code) return;
      state.roomCode = code;
      document.getElementById('mp-room-info').textContent = `JOINED ROOM: ${code} — Ready to Deploy!`;
      if (state.wsConnected && state.ws) state.ws.send(JSON.stringify({ type: 'JOIN_ROOM', code }));
    });

    document.getElementById('btn-deploy-match').addEventListener('click', startMatch);

    // HUD Quick Buttons
    document.getElementById('hud-btn-heal').addEventListener('click', useMedkit);
    document.getElementById('hud-btn-cam').addEventListener('click', () => {
      state.player.cameraMode = state.player.cameraMode === 'TPS' ? 'FPS' : 'TPS';
      document.getElementById('hud-cam-mode').textContent = state.player.cameraMode;
    });
    [0, 1, 2].forEach(idx => {
      document.getElementById(`slot-${idx}`).addEventListener('click', () => {
        state.player.activeSlot = idx;
        updateHUD();
      });
    });

    function toggleFullMap() {
      const m = document.getElementById('full-map-modal');
      m.classList.toggle('hidden');
      if (!m.classList.contains('hidden')) {
        if (document.exitPointerLock) try { document.exitPointerLock(); } catch (e) {}
        renderRadarMap(fullCtx, 520, true);
      }
    }
    document.getElementById('hud-btn-map').addEventListener('click', toggleFullMap);
    document.getElementById('btn-close-map').addEventListener('click', toggleFullMap);

    function toggleInventoryModal() {
      const inv = document.getElementById('inventory-modal');
      inv.classList.toggle('hidden');
      if (!inv.classList.contains('hidden')) {
        if (document.exitPointerLock) try { document.exitPointerLock(); } catch (e) {}
        const grid = document.getElementById('inventory-grid');
        grid.innerHTML = '';
        state.player.weapons.forEach((w, idx) => {
          const card = document.createElement('div');
          card.className = `p-3 rounded-xl bg-slate-900 border ${w ? 'rarity-' + w.rarity : 'border-slate-700'} cursor-pointer`;
          card.innerHTML = w
            ? `<div class="text-[10px] font-tactical text-cyan-400">SLOT ${idx + 1} // ${w.rarity}</div>
               <div class="font-tactical font-bold text-white">${w.name}</div>
               <div class="text-xs text-slate-400">DMG: ${w.damage} • AMMO: ${w.mag}/${w.reserve}</div>`
            : `<div class="text-xs font-tactical text-slate-500">SLOT ${idx + 1} — EMPTY</div>`;
          card.addEventListener('click', () => {
            state.player.activeSlot = idx;
            updateHUD();
            toggleInventoryModal();
          });
          grid.appendChild(card);
        });
      }
    }
    document.getElementById('hud-btn-bag').addEventListener('click', toggleInventoryModal);
    document.getElementById('btn-close-inv').addEventListener('click', toggleInventoryModal);
    document.getElementById('inv-use-medkit').addEventListener('click', useMedkit);

    // Settings Modal
    const openSettings = () => document.getElementById('settings-modal').classList.remove('hidden');
    document.getElementById('btn-open-settings').addEventListener('click', openSettings);
    document.getElementById('btn-pause-settings').addEventListener('click', openSettings);
    document.getElementById('btn-close-settings').addEventListener('click', () => {
      document.getElementById('settings-modal').classList.add('hidden');
    });

    document.getElementById('set-graphics').addEventListener('change', (e) => {
      state.graphics = e.target.value;
      renderer.shadowMap.enabled = state.graphics !== 'LOW';
      renderer.setPixelRatio(state.graphics === 'LOW' ? 1 : Math.min(window.devicePixelRatio, 2));
    });
    document.getElementById('set-sensitivity').addEventListener('input', (e) => {
      state.sensitivity = parseFloat(e.target.value);
      document.getElementById('sens-val').textContent = state.sensitivity.toFixed(1);
    });
    document.getElementById('set-volume').addEventListener('input', (e) => {
      const pct = parseInt(e.target.value, 10);
      document.getElementById('vol-val').textContent = pct + '%';
      sound.setVolume(pct / 100);
    });
    document.getElementById('set-mobile-hud').addEventListener('change', (e) => {
      state.forceMobileHud = e.target.checked;
      document.getElementById('mobile-hud').classList.toggle('hidden', !e.target.checked);
    });

    document.getElementById('btn-open-controls-help').addEventListener('click', openSettings);

    // Pause & Return to Lobby
    function togglePauseMenu() {
      if (state.screen === 'PLAYING') {
        state.screen = 'PAUSED';
        if (document.exitPointerLock) try { document.exitPointerLock(); } catch (e) {}
        document.getElementById('pause-modal').classList.remove('hidden');
      } else if (state.screen === 'PAUSED') {
        state.screen = 'PLAYING';
        document.getElementById('pause-modal').classList.add('hidden');
      }
    }
    document.getElementById('hud-btn-pause').addEventListener('click', togglePauseMenu);
    document.getElementById('btn-resume-game').addEventListener('click', togglePauseMenu);

    const returnToLobby = () => {
      state.screen = 'MENU';
      document.getElementById('pause-modal').classList.add('hidden');
      document.getElementById('end-match-modal').classList.add('hidden');
      document.getElementById('in-game-hud').classList.add('hidden');
      document.getElementById('mobile-hud').classList.add('hidden');
      document.getElementById('sniper-scope').classList.add('hidden');
      document.getElementById('main-menu').classList.remove('hidden');
      playerMesh.visible = true;
    };
    document.getElementById('btn-quit-lobby').addEventListener('click', returnToLobby);
    document.getElementById('btn-end-lobby').addEventListener('click', returnToLobby);

    document.getElementById('btn-spectate').addEventListener('click', () => {
      document.getElementById('end-match-modal').classList.add('hidden');
      const liveBot = state.bots.find(b => b.alive);
      if (liveBot) {
        state.player.pos.copy(liveBot.pos).add(new THREE.Vector3(0, 12, 12));
      }
    });

    // Resize Handling
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });

    /* =========================================================
       MODULE 20: MAIN ANIMATION LOOP
    ========================================================== */
    function animate(now) {
      requestAnimationFrame(animate);
      const dt = Math.min(0.05, (now - lastTime) / 1000);
      lastTime = now;

      frameCounter++;
      fpsTimer += dt;
      if (fpsTimer >= 0.5) {
        document.getElementById('hud-fps').textContent = Math.round(frameCounter / fpsTimer);
        frameCounter = 0;
        fpsTimer = 0;
      }

      updateSimulation(dt);
      renderer.render(scene, camera);
    }

    window.onload = () => {
      applyWeatherPreset('CLEAR');
      requestAnimationFrame(animate);
    };
  })();
  </script>
</body>
</html>
```
