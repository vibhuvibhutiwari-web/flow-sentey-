# flow-sentey-
It is a website which me and my team developed for the problem of water leakage 

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Water Sentry - Smart Leak Detection & Hydrodynamics</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Phosphor Icons for intuitive universal visual cues -->
  <script src="https://unpkg.com/@phosphor-icons/web"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            safe: '#10b981',
            warning: '#f59e0b',
            critical: '#f97316',
            danger: '#ef4444'
          },
          animation: {
            'pulse-fast': 'pulse 0.8s cubic-bezier(0.4, 0, 0.6, 1) infinite',
            'bounce-gentle': 'bounce 2s infinite',
          }
        }
      }
    }
  </script>
  <style>
    /* Wave animation for animated water tank */
    @keyframes waveMotion {
      0% { transform: translateX(0) translateZ(0) scaleY(1); }
      50% { transform: translateX(-25%) translateZ(0) scaleY(0.85); }
      100% { transform: translateX(-50%) translateZ(0) scaleY(1); }
    }
    .wave-shape {
      animation: waveMotion 4s linear infinite;
    }
    /* Flashing beacon for Zone D emergency */
    @keyframes sirenFlash {
      0%, 100% { background-color: rgba(239, 68, 68, 0.15); box-shadow: inset 0 0 40px rgba(239, 68, 68, 0.6); }
      50% { background-color: rgba(239, 68, 68, 0.45); box-shadow: inset 0 0 90px rgba(239, 68, 68, 0.95); }
    }
    .emergency-siren-active {
      animation: sirenFlash 0.7s infinite alternate;
    }
    /* Extra large accessible touch targets */
    .touch-target {
      min-height: 56px;
      min-width: 56px;
    }
  </style>
</head>
<body class="bg-slate-50 dark:bg-slate-950 text-slate-900 dark:text-slate-100 min-h-screen transition-colors duration-300 font-sans antialiased pb-24 sm:pb-16 selection:bg-cyan-500 selection:text-white">

  <!-- Top Navigation & Accessibility Bar -->
  <header class="sticky top-0 z-50 bg-white/90 dark:bg-slate-900/90 backdrop-blur-md border-b border-slate-200 dark:border-slate-800 px-4 py-3 shadow-sm">
    <div class="max-w-6xl mx-auto flex flex-wrap items-center justify-between gap-2">
      
      <!-- Brand Logo / Identity -->
      <div class="flex items-center gap-3">
        <div class="w-12 h-12 rounded-2xl bg-gradient-to-br from-cyan-500 to-blue-600 flex items-center justify-center text-white shadow-lg shadow-cyan-500/30">
          <i class="ph-fill ph-drop-half-bottom text-2xl"></i>
        </div>
        <div>
          <h1 class="text-xl sm:text-2xl font-black tracking-tight flex items-center gap-2">
            <span id="brand-title">WATER SENTRY</span>
            <span class="text-xs font-bold uppercase px-2 py-0.5 rounded-full bg-cyan-100 dark:bg-cyan-950 text-cyan-700 dark:text-cyan-300 border border-cyan-300 dark:border-cyan-800">AI Guard</span>
          </h1>
          <p id="sub-title" class="text-xs text-slate-500 dark:text-slate-400 font-medium">Smart Leak & Hydrodynamic Guard</p>
        </div>
      </div>

      <!-- Accessibility, Language & Audio Controls -->
      <div class="flex items-center gap-2">
        <!-- Voice Read Out Loud Current Status Button -->
        <button id="speak-status-btn" title="Read Aloud Status" class="touch-target p-3 bg-amber-100 dark:bg-amber-950/80 text-amber-800 dark:text-amber-300 rounded-2xl flex items-center gap-2 border border-amber-300 dark:border-amber-800 font-bold active:scale-95 transition-transform">
          <i class="ph-fill ph-speaker-high text-2xl"></i>
          <span class="hidden md:inline text-sm" id="speak-btn-text">Hear Status</span>
        </button>

        <!-- Language Selector for Low Literacy / Multilingual users -->
        <div class="relative inline-block text-left">
          <select id="lang-select" aria-label="Select Language" class="touch-target px-3 py-2 text-sm font-bold bg-slate-100 dark:bg-slate-800 border-2 border-slate-300 dark:border-slate-700 rounded-2xl focus:outline-none focus:ring-2 focus:ring-cyan-500 cursor-pointer">
            <option value="en">English (Text & Voice)</option>
            <option value="hi">हिंदी (Hindi Voice & Text)</option>
            <option value="symbol">🖼️ Icons Only (ಚಿತ್ರ ಮೋಡ್)</option>
          </select>
        </div>

        <!-- Dark / Light Mode Toggle -->
        <button id="theme-toggle" title="Toggle Theme" class="touch-target p-3 bg-slate-100 dark:bg-slate-800 rounded-2xl text-slate-700 dark:text-slate-200 border border-slate-300 dark:border-slate-700 active:scale-95 transition-transform">
          <i id="theme-icon" class="ph-fill ph-moon text-xl"></i>
        </button>
      </div>

    </div>
  </header>

  <!-- Emergency Siren / Banner Area -->
  <div id="emergency-banner" class="hidden w-full bg-red-600 text-white px-4 py-3 font-extrabold text-center text-lg sm:text-xl flex items-center justify-center gap-3 animate-pulse-fast shadow-xl">
    <i class="ph-fill ph-warning-octagon text-3xl"></i>
    <span id="emergency-text">DANGER! CRITICAL LEAKAGE DETECTED! SHUT OFF VALVE IMMEDIATELY!</span>
    <i class="ph-fill ph-siren text-3xl"></i>
  </div>

  <main class="max-w-6xl mx-auto px-4 py-6 space-y-6">

    <!-- Top Master Status Card: Clear Visual Symbol for Low Literacy -->
    <div id="master-status-card" class="p-6 rounded-3xl border-4 transition-all duration-500 shadow-xl bg-white dark:bg-slate-900 border-emerald-500 flex flex-col md:flex-row items-center justify-between gap-6">
      
      <!-- Big Visual Indicator -->
      <div class="flex items-center gap-5 w-full md:w-auto">
        <div id="zone-avatar" class="w-24 h-24 sm:w-28 sm:h-28 rounded-3xl bg-emerald-100 dark:bg-emerald-950 text-emerald-600 dark:text-emerald-400 flex items-center justify-center text-5xl sm:text-6xl shadow-inner border-2 border-emerald-400 shrink-0">
          <i id="zone-icon" class="ph-fill ph-shield-check"></i>
        </div>
        <div>
          <div class="flex items-center gap-2">
            <span id="zone-badge" class="px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-emerald-500 text-white">Zone A - Safe</span>
            <span id="vibration-badge" class="hidden text-xs bg-slate-200 dark:bg-slate-800 px-2 py-0.5 rounded-md font-semibold text-slate-600 dark:text-slate-300">
              <i class="ph-fill ph-vibrate"></i> Vibrating
            </span>
          </div>
          <h2 id="zone-headline" class="text-2xl sm:text-3xl font-black mt-1 text-slate-900 dark:text-white">Water System Normal</h2>
          <p id="zone-simple-advice" class="text-sm sm:text-base font-semibold text-slate-600 dark:text-slate-300 mt-1">
            No leaks detected. Pipes and flow are completely secure.
          </p>
        </div>
      </div>

      <!-- Quick Action Buttons for Low Literacy Users -->
      <div class="flex flex-wrap items-center gap-3 w-full md:w-auto justify-end">
        <!-- Emergency Master Valve Button -->
        <button id="valve-btn" class="touch-target flex-1 md:flex-initial px-6 py-4 rounded-2xl bg-red-600 hover:bg-red-700 active:scale-95 text-white font-black text-lg flex items-center justify-center gap-3 shadow-lg shadow-red-600/30 transition-all border-2 border-red-400">
          <i id="valve-icon" class="ph-fill ph-lock-key text-2xl"></i>
          <span id="valve-btn-label">CLOSE VALVE</span>
        </button>
        <!-- Test Alarm Sound -->
        <button id="test-audio-btn" class="touch-target px-4 py-4 rounded-2xl bg-slate-200 dark:bg-slate-800 hover:bg-slate-300 dark:hover:bg-slate-700 active:scale-95 text-slate-800 dark:text-slate-100 font-bold text-sm flex items-center gap-2 border border-slate-300 dark:border-slate-700">
          <i class="ph-fill ph-bell-ringing text-xl text-cyan-600 dark:text-cyan-400"></i>
          <span id="test-sound-text">Test Siren</span>
        </button>
      </div>

    </div>

    <!-- 4-Zone Status Strip: Color coded with clear progression -->
    <div class="grid grid-cols-2 lg:grid-cols-4 gap-3">
      <!-- Zone A Card -->
      <div id="strip-zone-a" class="p-4 rounded-2xl border-2 transition-all cursor-pointer bg-white dark:bg-slate-900 border-emerald-500 shadow-sm" onclick="setZone('A')">
        <div class="flex items-center justify-between">
          <span class="text-xs font-black px-2 py-0.5 rounded bg-emerald-100 dark:bg-emerald-950 text-emerald-700 dark:text-emerald-300">ZONE A</span>
          <i class="ph-fill ph-check-circle text-emerald-500 text-xl"></i>
        </div>
        <p class="font-extrabold text-slate-800 dark:text-slate-100 mt-2 text-base">SAFE</p>
        <p class="text-xs text-slate-500 dark:text-slate-400">Normal Flow & Pressure</p>
      </div>

      <!-- Zone B Card -->
      <div id="strip-zone-b" class="p-4 rounded-2xl border-2 transition-all cursor-pointer bg-white dark:bg-slate-900 border-transparent hover:border-amber-400 shadow-sm opacity-60" onclick="setZone('B')">
        <div class="flex items-center justify-between">
          <span class="text-xs font-black px-2 py-0.5 rounded bg-amber-100 dark:bg-amber-950 text-amber-700 dark:text-amber-300">ZONE B</span>
          <i class="ph-fill ph-warning text-amber-500 text-xl"></i>
        </div>
        <p class="font-extrabold text-slate-800 dark:text-slate-100 mt-2 text-base">WARNING</p>
        <p class="text-xs text-slate-500 dark:text-slate-400">Slow concealed seepage</p>
      </div>

      <!-- Zone C Card -->
      <div id="strip-zone-c" class="p-4 rounded-2xl border-2 transition-all cursor-pointer bg-white dark:bg-slate-900 border-transparent hover:border-orange-500 shadow-sm opacity-60" onclick="setZone('C')">
        <div class="flex items-center justify-between">
          <span class="text-xs font-black px-2 py-0.5 rounded bg-orange-100 dark:bg-orange-950 text-orange-700 dark:text-orange-300">ZONE C</span>
          <i class="ph-fill ph-warning-diamond text-orange-600 text-xl"></i>
        </div>
        <p class="font-extrabold text-slate-800 dark:text-slate-100 mt-2 text-base">CRITICAL</p>
        <p class="text-xs text-slate-500 dark:text-slate-400">High friction / stuck valve</p>
      </div>

      <!-- Zone D Card -->
      <div id="strip-zone-d" class="p-4 rounded-2xl border-2 transition-all cursor-pointer bg-white dark:bg-slate-900 border-transparent hover:border-red-500 shadow-sm opacity-60" onclick="setZone('D')">
        <div class="flex items-center justify-between">
          <span class="text-xs font-black px-2 py-0.5 rounded bg-red-100 dark:bg-red-950 text-red-700 dark:text-red-300">ZONE D</span>
          <i class="ph-fill ph-siren text-red-600 text-xl"></i>
        </div>
        <p class="font-extrabold text-slate-800 dark:text-slate-100 mt-2 text-base">EMERGENCY</p>
        <p class="text-xs text-slate-500 dark:text-slate-400">Pipe burst / massive flood</p>
      </div>
    </div>

    <!-- Hydrodynamics & Sensor Gauges Section -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">

      <!-- Tank Water Level (Fluid Visualizer) -->
      <div class="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-200 dark:border-slate-800 shadow-sm flex flex-col items-center">
        <div class="w-full flex items-center justify-between mb-2">
          <div class="flex items-center gap-2">
            <i class="ph-fill ph-cylinder text-2xl text-cyan-600"></i>
            <span class="font-extrabold text-sm uppercase tracking-wide">Main Storage Tank</span>
          </div>
          <span id="tank-percent-label" class="font-black text-xl text-cyan-600 dark:text-cyan-400">65%</span>
        </div>

        <!-- Custom Graphical Water Tank with SVG waves -->
        <div class="relative w-44 h-64 border-4 border-slate-400 dark:border-slate-600 rounded-3xl overflow-hidden bg-slate-100 dark:bg-slate-800 my-2 shadow-inner">
          <!-- Measurement ticks on tank side for instant visual estimation -->
          <div class="absolute left-2 top-0 bottom-0 flex flex-col justify-between py-4 z-20 pointer-events-none opacity-40">
            <span class="text-[10px] font-black">100%</span>
            <span class="text-[10px] font-black">75%</span>
            <span class="text-[10px] font-black">50%</span>
            <span class="text-[10px] font-black">25%</span>
            <span class="text-[10px] font-black">0%</span>
          </div>
          
          <!-- Animated fluid container -->
          <div id="tank-water-fluid" class="absolute bottom-0 left-0 right-0 transition-all duration-700 ease-out bg-gradient-to-t from-blue-700 to-cyan-400 overflow-hidden" style="height: 65%;">
            <!-- SVG Wave Top -->
            <svg class="absolute top-0 left-0 w-[200%] h-8 wave-shape opacity-80" viewBox="0 0 1200 120" preserveAspectRatio="none">
              <path d="M0,0 C150,90 350,-40 500,50 C650,140 900,10 1200,40 L1200,120 L0,120 Z" fill="#67e8f9"></path>
            </svg>
          </div>
        </div>

        <div class="w-full text-center mt-2">
          <span id="tank-status-caption" class="text-xs font-bold text-slate-500 dark:text-slate-400">Capacity: 3,250 / 5,000 Liters</span>
        </div>
      </div>

      <!-- Flow Rate Gauge (Liters/Minute) -->
      <div class="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-200 dark:border-slate-800 shadow-sm flex flex-col items-center justify-between">
        <div class="w-full flex items-center justify-between">
          <div class="flex items-center gap-2">
            <i class="ph-fill ph-gauge text-2xl text-blue-600"></i>
            <span class="font-extrabold text-sm uppercase tracking-wide">Flow Speed</span>
          </div>
          <span id="flow-status-pill" class="text-xs font-extrabold px-2 py-0.5 rounded-full bg-emerald-100 text-emerald-700 dark:bg-emerald-950 dark:text-emerald-300">Optimal</span>
        </div>

        <!-- Speedometer SVG Gauge -->
        <div class="relative w-56 h-36 flex items-center justify-center my-2">
          <svg class="w-56 h-36 overflow-visible" viewBox="0 0 200 120">
            <!-- Background Arc -->
            <path d="M 20 110 A 80 80 0 0 1 180 110" fill="none" stroke="#e2e8f0" stroke-width="20" stroke-linecap="round" class="dark:stroke-slate-800" />
            <!-- Colored Segments -->
            <path d="M 20 110 A 80 80 0 0 1 70 45" fill="none" stroke="#10b981" stroke-width="20" stroke-linecap="round" />
            <path d="M 70 45 A 80 80 0 0 1 130 45" fill="none" stroke="#f59e0b" stroke-width="20" />
            <path d="M 130 45 A 80 80 0 0 1 180 110" fill="none" stroke="#ef4444" stroke-width="20" stroke-linecap="round" />
            
            <!-- Needle -->
            <g id="flow-needle-group" transform="rotate(-90 100 110)" class="transition-transform duration-700 ease-out">
              <polygon points="96,110 104,110 101,25 99,25" fill="#1e293b" class="dark:fill-white" />
              <circle cx="100" cy="110" r="10" fill="#0284c7" />
            </g>
          </svg>
          <div class="absolute bottom-0 flex flex-col items-center">
            <span id="flow-rate-val" class="text-3xl font-black">28</span>
            <span class="text-[11px] font-bold text-slate-400">Liters / min</span>
          </div>
        </div>

        <div class="w-full text-center">
          <p class="text-xs text-slate-500 font-semibold" id="flow-hint">Ideal baseline is 15–35 L/min</p>
        </div>
      </div>

      <!-- Hydrodynamic Water Pressure Meter -->
      <div class="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-200 dark:border-slate-800 shadow-sm flex flex-col items-center justify-between">
        <div class="w-full flex items-center justify-between">
          <div class="flex items-center gap-2">
            <i class="ph-fill ph-arrows-in-cardinal text-2xl text-purple-600"></i>
            <span class="font-extrabold text-sm uppercase tracking-wide">Piping Pressure</span>
          </div>
          <span id="pressure-status-pill" class="text-xs font-extrabold px-2 py-0.5 rounded-full bg-emerald-100 text-emerald-700 dark:bg-emerald-950 dark:text-emerald-300">Safe Bar</span>
        </div>

        <!-- Pressure Dial -->
        <div class="relative w-44 h-44 rounded-full border-4 border-slate-200 dark:border-slate-700 flex items-center justify-center bg-slate-50 dark:bg-slate-800/50 my-2 shadow-inner">
          <!-- Circular Dial Face -->
          <div class="text-center z-10">
            <span id="pressure-val" class="text-4xl font-black text-slate-900 dark:text-white">45</span>
            <p class="text-xs font-bold text-slate-400 uppercase tracking-widest">PSI (3.1 Bar)</p>
          </div>
          <!-- Gauge border ring gradient indicator -->
          <div id="pressure-ring" class="absolute inset-1 rounded-full border-4 border-dashed border-emerald-500 transition-colors duration-500"></div>
        </div>

        <div class="w-full text-center">
          <p class="text-xs text-slate-500 font-semibold" id="pressure-hint">Threshold limits: 30 to 60 PSI</p>
        </div>
      </div>

    </div>

    <!-- AI Leak Probability & Anomaly Predictor Box -->
    <div class="bg-gradient-to-r from-slate-900 via-indigo-950 to-slate-900 text-white p-6 rounded-3xl shadow-xl border border-indigo-800/40">
      <div class="flex flex-col md:flex-row items-start md:items-center justify-between gap-4">
        <div class="flex items-center gap-4">
          <div class="w-14 h-14 rounded-2xl bg-indigo-500/20 border border-indigo-400 flex items-center justify-center text-3xl text-indigo-300 shrink-0">
            <i class="ph-fill ph-brain"></i>
          </div>
          <div>
            <div class="flex items-center gap-2">
              <span class="text-xs uppercase font-extrabold tracking-widest text-indigo-300">Hydro-AI Predictive Engine</span>
              <span class="w-2 h-2 rounded-full bg-emerald-400 animate-ping"></span>
            </div>
            <h3 id="ai-anomaly-title" class="text-lg sm:text-xl font-black">Pattern Analysis: Normal Consumption Profile</h3>
            <p id="ai-anomaly-detail" class="text-sm text-indigo-200 mt-0.5">Continuous night-flow telemetry matches typical residential benchmarks.</p>
          </div>
        </div>

        <!-- Leak probability score indicator -->
        <div class="flex items-center gap-3 bg-white/10 px-5 py-3 rounded-2xl border border-white/10 self-stretch md:self-auto justify-between">
          <div>
            <div class="text-[11px] font-bold text-indigo-200 uppercase">Leak Probability</div>
            <div id="leak-probability-num" class="text-2xl font-black text-emerald-400">4%</div>
          </div>
          <div id="leak-prob-icon" class="text-3xl text-emerald-400">
            <i class="ph-fill ph-shield-check"></i>
          </div>
        </div>
      </div>
    </div>

    <!-- Interactive Simulation Controls for Hackathon Demonstration -->
    <div class="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-200 dark:border-slate-800 shadow-sm space-y-4">
      <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 border-b border-slate-200 dark:border-slate-800 pb-3">
        <div>
          <h4 class="font-black text-lg flex items-center gap-2">
            <i class="ph-fill ph-sliders text-cyan-600"></i>
            <span id="sim-panel-title">Hackathon Demonstration & Anomaly Injection</span>
          </h4>
          <p class="text-xs text-slate-500 dark:text-slate-400" id="sim-panel-desc">Test real-time phone vibrations, voice alerts, sirens, and automatic shutoff.</p>
        </div>
        <button onclick="resetSimulation()" class="touch-target px-4 py-2 bg-slate-100 hover:bg-slate-200 dark:bg-slate-800 dark:hover:bg-slate-700 text-xs font-bold rounded-xl active:scale-95 transition-transform flex items-center gap-1.5 self-start sm:self-auto">
          <i class="ph-fill ph-arrow-counter-clockwise"></i> Reset System
        </button>
      </div>

      <!-- Scenario Trigger Buttons -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-3">
        <!-- Scenario 1: Normal -->
        <button onclick="setZone('A')" class="p-4 rounded-2xl border-2 border-emerald-300 bg-emerald-50 hover:bg-emerald-100 dark:bg-emerald-950/40 dark:border-emerald-800 text-left transition-all active:scale-98">
          <div class="flex items-center justify-between text-emerald-600 dark:text-emerald-400 font-black">
            <span>Scenario 1</span>
            <i class="ph-fill ph-house-line text-lg"></i>
          </div>
          <p class="font-bold text-slate-800 dark:text-slate-100 text-sm mt-1">Normal Everyday Flow</p>
          <span class="text-xs text-slate-500 dark:text-slate-400">Zone A: 24 L/min, 45 PSI</span>
        </button>

        <!-- Scenario 2: Slow Concealed Leak -->
        <button onclick="setZone('B')" class="p-4 rounded-2xl border-2 border-amber-300 bg-amber-50 hover:bg-amber-100 dark:bg-amber-950/40 dark:border-amber-800 text-left transition-all active:scale-98">
          <div class="flex items-center justify-between text-amber-600 dark:text-amber-400 font-black">
            <span>Scenario 2</span>
            <i class="ph-fill ph-drop text-lg"></i>
          </div>
          <p class="font-bold text-slate-800 dark:text-slate-100 text-sm mt-1">Slow Concealed Leak</p>
          <span class="text-xs text-slate-500 dark:text-slate-400">Zone B: Night Flow Anomaly</span>
        </button>

        <!-- Scenario 3: Stuck Valve / Pressure Spike -->
        <button onclick="setZone('C')" class="p-4 rounded-2xl border-2 border-orange-300 bg-orange-50 hover:bg-orange-100 dark:bg-orange-950/40 dark:border-orange-800 text-left transition-all active:scale-98">
          <div class="flex items-center justify-between text-orange-600 dark:text-orange-400 font-black">
            <span>Scenario 3</span>
            <i class="ph-fill ph-warning-diamond text-lg"></i>
          </div>
          <p class="font-bold text-slate-800 dark:text-slate-100 text-sm mt-1">Pressure Spike / Blockage</p>
          <span class="text-xs text-slate-500 dark:text-slate-400">Zone C: 88 PSI Critical Stress</span>
        </button>

        <!-- Scenario 4: Pipe Burst / Flooding -->
        <button onclick="setZone('D')" class="p-4 rounded-2xl border-2 border-red-400 bg-red-50 hover:bg-red-100 dark:bg-red-950/40 dark:border-red-800 text-left transition-all active:scale-98">
          <div class="flex items-center justify-between text-red-600 dark:text-red-400 font-black">
            <span>Scenario 4</span>
            <i class="ph-fill ph-lightning text-lg"></i>
          </div>
          <p class="font-bold text-slate-800 dark:text-slate-100 text-sm mt-1">Main Pipe Burst</p>
          <span class="text-xs text-slate-500 dark:text-slate-400">Zone D: Extreme 145 L/min!</span>
        </button>
      </div>
    </div>

    <!-- Live Hydrodynamic Event Log (Accessible, Clear Cards) -->
    <div class="bg-white dark:bg-slate-900 p-6 rounded-3xl border border-slate-200 dark:border-slate-800 shadow-sm">
      <div class="flex items-center justify-between mb-4">
        <h4 class="font-black text-base flex items-center gap-2">
          <i class="ph-fill ph-clock-counter-clockwise text-blue-600"></i>
          <span id="log-title">Recent System Events</span>
        </h4>
        <span class="text-xs text-slate-400">Real-time Telemetry</span>
      </div>
      <div id="events-log-container" class="space-y-2.5 max-h-48 overflow-y-auto pr-1">
        <!-- Log entries injected dynamically -->
      </div>
    </div>

  </main>

  <!-- Sticky Bottom Alert Ribbon for Mobile Touch Accessibility -->
  <nav class="fixed bottom-0 left-0 right-0 p-3 bg-white/95 dark:bg-slate-900/95 backdrop-blur-md border-t border-slate-200 dark:border-slate-800 z-40 sm:hidden flex items-center justify-around gap-2">
    <button onclick="speakCurrentStatus()" class="touch-target flex-1 py-3 px-2 rounded-2xl bg-amber-500 text-white font-black text-sm flex items-center justify-center gap-1.5 shadow-md">
      <i class="ph-fill ph-speaker-high text-xl"></i>
      <span>Listen</span>
    </button>
    <button id="mobile-valve-btn" class="touch-target flex-1 py-3 px-2 rounded-2xl bg-red-600 text-white font-black text-sm flex items-center justify-center gap-1.5 shadow-md">
      <i class="ph-fill ph-lock-key text-xl"></i>
      <span id="mobile-valve-text">Shut Valve</span>
    </button>
  </nav>

  <!-- Notification Toast Modal -->
  <div id="toast-modal" class="fixed bottom-20 sm:bottom-8 right-6 z-50 bg-slate-900 text-white px-5 py-3.5 rounded-2xl shadow-2xl flex items-center gap-3 border border-slate-700 transition-all duration-300 opacity-0 pointer-events-none translate-y-4">
    <i id="toast-icon" class="ph-fill ph-info text-2xl text-cyan-400"></i>
    <span id="toast-msg" class="text-sm font-bold">Action recorded</span>
  </div>

  <script>
    /* =========================================================================
       1. STATE MANAGEMENT & CONSTANTS
       ========================================================================= */
    const STATE = {
      currentZone: 'A', // 'A', 'B', 'C', 'D'
      tankLevel: 65,    // in percentage 0 - 100
      flowRate: 28,     // L/min
      pressure: 45,     // PSI
      leakProbability: 4, // 0 - 100%
      valveClosed: false,
      language: 'en',   // 'en', 'hi', 'symbol'
      isSirenPlaying: false
    };

    // Translations and low-literacy universal speech lines
    const DICTIONARY = {
      en: {
        title: "WATER SENTRY",
        subtitle: "Smart Leak & Hydrodynamic Guard",
        hearStatus: "Hear Status",
        closeValve: "CLOSE VALVE",
        openValve: "OPEN VALVE",
        valveClosedAlert: "Master valve closed. Water flow halted.",
        valveOpenAlert: "Master valve open. Water flow restored.",
        eventsTitle: "Recent System Events",
        zones: {
          A: {
            badge: "Zone A - Safe",
            headline: "Water System Normal",
            advice: "No leaks detected. Pipes and flow are completely secure.",
            speech: "Water system is normal. Zone A. Everything is safe."
          },
          B: {
            badge: "Zone B - Warning",
            headline: "Slow Leak Suspected",
            advice: "Continuous low flow during inactive hours. Check taps and joints.",
            speech: "Warning! Zone B! Possible slow water leak detected in pipes."
          },
          C: {
            badge: "Zone C - Critical Danger",
            headline: "High Pipe Stress & Pressure",
            advice: "Abnormal hydrodynamic resistance or valve bottleneck.",
            speech: "Alert! Zone C! Water pressure is critical. Risk of damage."
          },
          D: {
            badge: "Zone D - Emergency Burst!",
            headline: "EXTREME PIPE BURST & FLOODING",
            advice: "Massive volumetric discharge! Tap CLOSE VALVE immediately!",
            speech: "Danger! Extreme danger! Zone D! Pipe has burst! Shut off water now!"
          }
        }
      },
      hi: {
        title: "वाटर संतरी",
        subtitle: "पानी रिसाव रोकथाम व निगरानी प्रणाली",
        hearStatus: "स्थिति सुनें",
        closeValve: "नल बंद करें",
        openValve: "नल खोलें",
        valveClosedAlert: "मुख्य वाल्व बंद कर दिया गया है। पानी रुक गया है।",
        valveOpenAlert: "मुख्य वाल्व खोल दिया गया है। पानी चालू है।",
        eventsTitle: "हालिया सूचनाएं",
        zones: {
          A: {
            badge: "ज़ोन A - सुरक्षित (हरा)",
            headline: "पानी का स्तर और प्रवाह सुरक्षित है",
            advice: "कोई रिसाव नहीं मिला। सभी पाइप और दबाव पूरी तरह ठीक हैं।",
            speech: "पानी का सिस्टम ठीक है। ज़ोन ए। सब कुछ सुरक्षित है।"
          },
          B: {
            badge: "ज़ोन B - चेतावनी (पीला)",
            headline: "धीमा पानी रिसाव (लीक) की आशंका",
            advice: "रात में भी पानी बह रहा है। कृपया नल और पाइप जोड़ों की जांच करें।",
            speech: "चेतावनी! ज़ोन बी! पाइप में धीरे-धीरे पानी लीक हो रहा है।"
          },
          C: {
            badge: "ज़ोन C - गंभीर खतरा (नारंगी)",
            headline: "अत्यधिक दबाव और रुकावट",
            advice: "पानी का दबाव बहुत ज्यादा है। पाइप फटने का खतरा है।",
            speech: "सावधान! ज़ोन सी! पानी का दबाव बहुत ज्यादा है।"
          },
          D: {
            badge: "ज़ोन D - आपातकाल! भारी रिसाव (लाल)",
            headline: "पाइप फट गया है! तुरंत पानी बंद करें!",
            advice: "तेज़ बाढ़ जैसी स्थिति! तुरंत लाल बटन दबाकर वाल्व बंद करें!",
            speech: "खतरा! बहुत बड़ा खतरा! ज़ोन डी! तुरंत लाल बटन दबाकर नल बंद करें!"
          }
        }
      },
      symbol: {
        title: "💧 SENTRY",
        subtitle: "🛡️ 🚰 ⚠️ 🚨",
        hearStatus: "🔊 👂",
        closeValve: "🚫 🚰 CLOSE",
        openValve: "✅ 🚰 OPEN",
        valveClosedAlert: "🚫 🚰",
        valveOpenAlert: "✅ 🚰",
        eventsTitle: "📋 ⏱️",
        zones: {
          A: {
            badge: "🟢 ZONE A",
            headline: "✅ 🟢 SAFE",
            advice: "🛡️ 👍 🚰",
            speech: "Zone A. Safe."
          },
          B: {
            badge: "🟡 ZONE B",
            headline: "⚠️ 🟡 WARNING",
            advice: "👀 💧 🔍",
            speech: "Zone B. Warning leak."
          },
          C: {
            badge: "🟠 ZONE C",
            headline: "⚡ 🟠 CRITICAL",
            advice: "⚠️ 🛑 🌡️",
            speech: "Zone C. Critical pressure."
          },
          D: {
            badge: "🔴 ZONE D",
            headline: "🚨 🔴 DANGER!",
            advice: "🛑 🚫 🚰 💥",
            speech: "Zone D. Danger burst. Stop water."
          }
        }
      }
    };

    /* =========================================================================
       2. WEB AUDIO API SYNTHESIZER (NO EXTERNAL SOUND URLS)
       ========================================================================= */
    let audioCtx = null;

    function getAudioContext() {
      if (!audioCtx) {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      }
      if (audioCtx.state === 'suspended') {
        audioCtx.resume();
      }
      return audioCtx;
    }

    // Play Safe gentle chime
    function playSafeChime() {
      try {
        const ctx = getAudioContext();
        const now = ctx.currentTime;
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();

        osc.type = 'sine';
        osc.frequency.setValueAtTime(523.25, now); // C5
        osc.frequency.exponentialRampToValueAtTime(659.25, now + 0.15); // E5
        osc.frequency.exponentialRampToValueAtTime(783.99, now + 0.35); // G5

        gain.gain.setValueAtTime(0.15, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 0.6);

        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.start(now);
        osc.stop(now + 0.6);
      } catch (e) {
        console.warn('Audio unable to play:', e);
      }
    }

    // Play Warning double beep
    function playWarningBeep() {
      try {
        const ctx = getAudioContext();
        const now = ctx.currentTime;
        [0, 0.18].forEach(offset => {
          const osc = ctx.createOscillator();
          const gain = ctx.createGain();
          osc.type = 'triangle';
          osc.frequency.setValueAtTime(880, now + offset);
          gain.gain.setValueAtTime(0.2, now + offset);
          gain.gain.exponentialRampToValueAtTime(0.01, now + offset + 0.12);
          osc.connect(gain);
          gain.connect(ctx.destination);
          osc.start(now + offset);
          osc.stop(now + offset + 0.13);
        });
      } catch (e) {
        console.warn('Audio unable to play:', e);
      }
    }

    // Play Emergency High-Low Siren
    let sirenOsc = null;
    let sirenGain = null;
    let sirenInterval = null;

    function startEmergencySiren() {
      if (STATE.isSirenPlaying) return;
      try {
        const ctx = getAudioContext();
        sirenOsc = ctx.createOscillator();
        sirenGain = ctx.createGain();

        sirenOsc.type = 'sawtooth';
        sirenGain.gain.setValueAtTime(0.15, ctx.currentTime);

        const now = ctx.currentTime;
        sirenOsc.frequency.setValueAtTime(600, now);
        
        sirenOsc.connect(sirenGain);
        sirenGain.connect(ctx.destination);
        sirenOsc.start();
        STATE.isSirenPlaying = true;

        // Oscillate pitch like an emergency klaxon
        sirenInterval = setInterval(() => {
          if (!STATE.isSirenPlaying || !audioCtx || !sirenOsc) return;
          const t = audioCtx.currentTime;
          sirenOsc.frequency.setValueAtTime(850, t);
          sirenOsc.frequency.exponentialRampToValueAtTime(500, t + 0.35);
        }, 400);

      } catch (e) {
        console.warn('Siren audio error:', e);
      }
    }

    function stopEmergencySiren() {
      if (sirenInterval) {
        clearInterval(sirenInterval);
        sirenInterval = null;
      }
      if (sirenOsc) {
        try {
          const oscToStop = sirenOsc;
          sirenGain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + 0.1);
          setTimeout(() => {
            try {
              oscToStop.stop();
              oscToStop.disconnect();
            } catch (err) {}
          }, 120);
          sirenOsc = null;
        } catch (e) {}
      }
      STATE.isSirenPlaying = false;
    }

    /* =========================================================================
       3. HAPTIC FEEDBACK (VIBRATION API)
       ========================================================================= */
    function triggerVibration(zone) {
      if (!("vibrate" in navigator)) return;
      const vibBadge = document.getElementById('vibration-badge');
      if (vibBadge) vibBadge.classList.remove('hidden');

      try {
        if (zone === 'A') {
          navigator.vibrate(60); // Single gentle haptic tap
        } else if (zone === 'B') {
          navigator.vibrate([150, 100, 150]); // Double alert pulse
        } else if (zone === 'C') {
          navigator.vibrate([300, 150, 300, 150, 300]); // Triple urgent pulse
        } else if (zone === 'D') {
          // Continuous pulsing emergency pattern
          navigator.vibrate([500, 200, 500, 200, 500, 200, 800]);
        }
      } catch (err) {
        console.log('Vibration not permitted or unsupported', err);
      }

      setTimeout(() => {
        if (vibBadge) vibBadge.classList.add('hidden');
      }, 2500);
    }

    /* =========================================================================
       4. SPEECH SYNTHESIS (VOICE ANNOUNCER FOR LOW LITERACY)
       ========================================================================= */
    function speakCurrentStatus() {
      if (!('speechSynthesis' in window)) {
        showToast("Speech synthesis not supported on this device.");
        return;
      }
      window.speechSynthesis.cancel(); // Stop prior speeches

      const langObj = DICTIONARY[STATE.language] || DICTIONARY.en;
      const zoneData = langObj.zones[STATE.currentZone];
      let speechText = zoneData.speech;

      if (STATE.valveClosed) {
        speechText += " " + langObj.valveClosedAlert;
      }

      const utterance = new SpeechSynthesisUtterance(speechText);
      utterance.rate = 0.95;
      utterance.pitch = STATE.currentZone === 'D' ? 1.2 : 1.0;

      if (STATE.language === 'hi') {
        utterance.lang = 'hi-IN';
      } else {
        utterance.lang = 'en-US';
      }

      window.speechSynthesis.speak(utterance);
      showToast("🔊 " + speechText);
    }

    /* =========================================================================
       5. CORE ZONE TRANSITIONS & HYDRODYNAMICS
       ========================================================================= */
    function setZone(zone) {
      STATE.currentZone = zone;

      // Adjust physical parameters based on simulated hydrodynamic states
      if (zone === 'A') {
        stopEmergencySiren();
        playSafeChime();
        STATE.flowRate = 26;
        STATE.pressure = 45;
        STATE.tankLevel = 75;
        STATE.leakProbability = 4;
      } else if (zone === 'B') {
        stopEmergencySiren();
        playWarningBeep();
        STATE.flowRate = 42;
        STATE.pressure = 38;
        STATE.tankLevel = 58;
        STATE.leakProbability = 68;
      } else if (zone === 'C') {
        stopEmergencySiren();
        playWarningBeep();
        STATE.flowRate = 68;
        STATE.pressure = 84;
        STATE.tankLevel = 42;
        STATE.leakProbability = 89;
      } else if (zone === 'D') {
        startEmergencySiren();
        STATE.flowRate = 135;
        STATE.pressure = 18; // Pressure plummets during catastrophic rupture
        STATE.tankLevel = 22;
        STATE.leakProbability = 99;
      }

      // If valve was closed, shut down flow to 0
      if (STATE.valveClosed) {
        STATE.flowRate = 0;
        STATE.pressure = 10;
      }

      triggerVibration(zone);
      updateUI();
      logEvent(`Switched to Zone ${zone}`, zone);

      // Speak alert automatically if emergency or critical
      if (zone === 'D' || zone === 'C') {
        setTimeout(speakCurrentStatus, 400);
      }
    }

    /* =========================================================================
       6. UI RENDERING & COMPONENT SYNCHRONIZATION
       ========================================================================= */
    function updateUI() {
      const lang = DICTIONARY[STATE.language] || DICTIONARY.en;
      const zoneData = lang.zones[STATE.currentZone];

      // Update text labels
      document.getElementById('brand-title').textContent = lang.title;
      document.getElementById('speak-btn-text').textContent = lang.hearStatus;
      document.getElementById('zone-badge').textContent = zoneData.badge;
      document.getElementById('zone-headline').textContent = zoneData.headline;
      document.getElementById('zone-simple-advice').textContent = zoneData.advice;
      document.getElementById('sub-title').textContent = lang.subtitle;
      document.getElementById('valve-btn-label').textContent = STATE.valveClosed ? lang.openValve : lang.closeValve;
      document.getElementById('log-title').textContent = lang.eventsTitle;

      const mobileValveText = document.getElementById('mobile-valve-text');
      if (mobileValveText) {
        mobileValveText.textContent = STATE.valveClosed ? lang.openValve : lang.closeValve;
      }

      // Master Card Colors & Icons
      const masterCard = document.getElementById('master-status-card');
      const zoneAvatar = document.getElementById('zone-avatar');
      const zoneIcon = document.getElementById('zone-icon');
      const zoneBadge = document.getElementById('zone-badge');
      const emergencyBanner = document.getElementById('emergency-banner');

      // Reset dynamic borders
      masterCard.className = "p-6 rounded-3xl border-4 transition-all duration-500 shadow-xl bg-white dark:bg-slate-900 flex flex-col md:flex-row items-center justify-between gap-6 ";

      if (STATE.currentZone === 'A') {
        masterCard.classList.add('border-emerald-500');
        zoneAvatar.className = "w-24 h-24 sm:w-28 sm:h-28 rounded-3xl bg-emerald-100 dark:bg-emerald-950 text-emerald-600 dark:text-emerald-400 flex items-center justify-center text-5xl sm:text-6xl shadow-inner border-2 border-emerald-400 shrink-0";
        zoneIcon.className = "ph-fill ph-shield-check";
        zoneBadge.className = "px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-emerald-500 text-white";
        emergencyBanner.classList.add('hidden');
        document.body.classList.remove('emergency-siren-active');
      } else if (STATE.currentZone === 'B') {
        masterCard.classList.add('border-amber-500');
        zoneAvatar.className = "w-24 h-24 sm:w-28 sm:h-28 rounded-3xl bg-amber-100 dark:bg-amber-950 text-amber-600 dark:text-amber-400 flex items-center justify-center text-5xl sm:text-6xl shadow-inner border-2 border-amber-400 shrink-0";
        zoneIcon.className = "ph-fill ph-warning";
        zoneBadge.className = "px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-amber-500 text-white";
        emergencyBanner.classList.add('hidden');
        document.body.classList.remove('emergency-siren-active');
      } else if (STATE.currentZone === 'C') {
        masterCard.classList.add('border-orange-500');
        zoneAvatar.className = "w-24 h-24 sm:w-28 sm:h-28 rounded-3xl bg-orange-100 dark:bg-orange-950 text-orange-600 dark:text-orange-400 flex items-center justify-center text-5xl sm:text-6xl shadow-inner border-2 border-orange-400 shrink-0";
        zoneIcon.className = "ph-fill ph-warning-diamond";
        zoneBadge.className = "px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-orange-500 text-white";
        emergencyBanner.classList.add('hidden');
        document.body.classList.remove('emergency-siren-active');
      } else if (STATE.currentZone === 'D') {
        masterCard.classList.add('border-red-600', 'animate-pulse');
        zoneAvatar.className = "w-24 h-24 sm:w-28 sm:h-28 rounded-3xl bg-red-100 dark:bg-red-950 text-red-600 dark:text-red-400 flex items-center justify-center text-5xl sm:text-6xl shadow-inner border-2 border-red-500 shrink-0 animate-bounce-gentle";
        zoneIcon.className = "ph-fill ph-siren";
        zoneBadge.className = "px-3 py-1 rounded-full text-xs font-black uppercase tracking-wider bg-red-600 text-white animate-pulse";
        emergencyBanner.classList.remove('hidden');
        document.body.classList.add('emergency-siren-active');
      }

      // Update Zone Strips Opacity & active highlights
      ['a', 'b', 'c', 'd'].forEach(z => {
        const strip = document.getElementById(`strip-zone-${z}`);
        if (z.toUpperCase() === STATE.currentZone) {
          strip.classList.remove('opacity-60');
          strip.classList.add('ring-2', 'ring-cyan-500', 'shadow-md');
        } else {
          strip.classList.add('opacity-60');
          strip.classList.remove('ring-2', 'ring-cyan-500', 'shadow-md');
        }
      });

      // Update Water Tank Fluid
      document.getElementById('tank-water-fluid').style.height = `${STATE.tankLevel}%`;
      document.getElementById('tank-percent-label').textContent = `${STATE.tankLevel}%`;
      const liters = Math.round((STATE.tankLevel / 100) * 5000);
      document.getElementById('tank-status-caption').textContent = `Volume: ${liters.toLocaleString()} / 5,000 Liters`;

      // Update Flow Needle Rotation (maps 0 -> 160 L/min to -90 -> 90 degrees)
      const flowDegrees = Math.min(Math.max((STATE.flowRate / 160) * 180 - 90, -90), 90);
      document.getElementById('flow-needle-group').setAttribute('transform', `rotate(${flowDegrees} 100 110)`);
      document.getElementById('flow-rate-val').textContent = STATE.flowRate;
      
      const flowPill = document.getElementById('flow-status-pill');
      if (STATE.flowRate === 0) {
        flowPill.textContent = "Shut Off (0 L/min)";
        flowPill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-slate-200 text-slate-800 dark:bg-slate-700 dark:text-slate-200";
      } else if (STATE.flowRate > 100) {
        flowPill.textContent = "EXTREME FLOW";
        flowPill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-red-500 text-white animate-pulse";
      } else if (STATE.flowRate > 50) {
        flowPill.textContent = "High Velocity";
        flowPill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-orange-100 text-orange-800 dark:bg-orange-950 dark:text-orange-300";
      } else {
        flowPill.textContent = "Optimal Range";
        flowPill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-emerald-100 text-emerald-800 dark:bg-emerald-950 dark:text-emerald-300";
      }

      // Update Pressure Meter
      document.getElementById('pressure-val').textContent = STATE.pressure;
      const barVal = (STATE.pressure * 0.0689476).toFixed(1);
      document.querySelector('#pressure-val + p').textContent = `PSI (${barVal} Bar)`;
      const pressureRing = document.getElementById('pressure-ring');
      const pressurePill = document.getElementById('pressure-status-pill');

      if (STATE.pressure > 75) {
        pressureRing.className = "absolute inset-1 rounded-full border-4 border-dashed border-red-500";
        pressurePill.textContent = "Dangerous Pressure";
        pressurePill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-red-500 text-white";
      } else if (STATE.pressure < 25 && STATE.flowRate > 60) {
        pressureRing.className = "absolute inset-1 rounded-full border-4 border-dashed border-orange-500";
        pressurePill.textContent = "Rupture Drop";
        pressurePill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-orange-500 text-white";
      } else {
        pressureRing.className = "absolute inset-1 rounded-full border-4 border-dashed border-emerald-500";
        pressurePill.textContent = "Balanced PSI";
        pressurePill.className = "text-xs font-extrabold px-2 py-0.5 rounded-full bg-emerald-100 text-emerald-800 dark:bg-emerald-950 dark:text-emerald-300";
      }

      // AI Leak Prediction Status
      document.getElementById('leak-probability-num').textContent = `${STATE.leakProbability}%`;
      const leakProbIcon = document.getElementById('leak-prob-icon');
      const leakProbNum = document.getElementById('leak-probability-num');
      const aiTitle = document.getElementById('ai-anomaly-title');
      const aiDetail = document.getElementById('ai-anomaly-detail');

      if (STATE.leakProbability > 85) {
        leakProbNum.className = "text-2xl font-black text-red-400";
        leakProbIcon.innerHTML = '<i class="ph-fill ph-warning-octagon text-red-400 animate-pulse"></i>';
        aiTitle.textContent = "AI Alert: Critical Hydrodynamic Rupture Identified";
        aiDetail.textContent = "Massive delta between upstream transmitter and branch meters. High probability of severed supply conduit.";
      } else if (STATE.leakProbability > 50) {
        leakProbNum.className = "text-2xl font-black text-amber-400";
        leakProbIcon.innerHTML = '<i class="ph-fill ph-warning text-amber-400"></i>';
        aiTitle.textContent = "AI Alert: Micro-Seepage / Silent Creep Detected";
        aiDetail.textContent = "Nocturnal minimum flow exceeded 3 consecutive cycles. Inspect basement joints and cistern floaters.";
      } else {
        leakProbNum.className = "text-2xl font-black text-emerald-400";
        leakProbIcon.innerHTML = '<i class="ph-fill ph-shield-check text-emerald-400"></i>';
        aiTitle.textContent = "Pattern Analysis: Normal Consumption Profile";
        aiDetail.textContent = "Continuous telemetry matches baseline residential profile without pressure anomalies.";
      }

      // Update Valve Button visuals
      const valveBtn = document.getElementById('valve-btn');
      const valveIcon = document.getElementById('valve-icon');
      const mobileValveBtn = document.getElementById('mobile-valve-btn');

      if (STATE.valveClosed) {
        valveBtn.classList.remove('bg-red-600', 'hover:bg-red-700', 'border-red-400');
        valveBtn.classList.add('bg-emerald-600', 'hover:bg-emerald-700', 'border-emerald-400');
        valveIcon.className = "ph-fill ph-lock-key-open text-2xl";
        if (mobileValveBtn) {
          mobileValveBtn.classList.remove('bg-red-600');
          mobileValveBtn.classList.add('bg-emerald-600');
        }
      } else {
        valveBtn.classList.add('bg-red-600', 'hover:bg-red-700', 'border-red-400');
        valveBtn.classList.remove('bg-emerald-600', 'hover:bg-emerald-700', 'border-emerald-400');
        valveIcon.className = "ph-fill ph-lock-key text-2xl";
        if (mobileValveBtn) {
          mobileValveBtn.classList.add('bg-red-600');
          mobileValveBtn.classList.remove('bg-emerald-600');
        }
      }
    }

    /* =========================================================================
       7. EMERGENCY VALVE TOGGLE
       ========================================================================= */
    function toggleValve() {
      STATE.valveClosed = !STATE.valveClosed;
      const lang = DICTIONARY[STATE.language] || DICTIONARY.en;

      if (STATE.valveClosed) {
        stopEmergencySiren();
        playWarningBeep();
        STATE.flowRate = 0;
        STATE.pressure = 8;
        showToast(lang.valveClosedAlert);
        logEvent("Master valve CLOSED manually", "Action");
      } else {
        showToast(lang.valveOpenAlert);
        logEvent("Master valve OPENED", "Action");
        setZone(STATE.currentZone); // re-evaluate
      }

      triggerVibration(STATE.valveClosed ? 'B' : 'A');
      updateUI();
    }

    /* =========================================================================
       8. LOGGING & TOAST HELPERS
       ========================================================================= */
    function logEvent(message, type = 'Info') {
      const logContainer = document.getElementById('events-log-container');
      const time = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' });
      
      let badgeColor = 'bg-slate-200 text-slate-800 dark:bg-slate-800 dark:text-slate-200';
      if (type === 'D') badgeColor = 'bg-red-500 text-white font-bold';
      else if (type === 'C') badgeColor = 'bg-orange-500 text-white font-bold';
      else if (type === 'B') badgeColor = 'bg-amber-500 text-white font-bold';
      else if (type === 'A') badgeColor = 'bg-emerald-500 text-white font-bold';
      else if (type === 'Action') badgeColor = 'bg-blue-600 text-white font-bold';

      const entry = document.createElement('div');
      entry.className = "flex items-center justify-between p-3 rounded-2xl bg-slate-50 dark:bg-slate-800/60 border border-slate-200 dark:border-slate-800 text-xs";
      entry.innerHTML = `
        <div class="flex items-center gap-2.5">
          <span class="px-2 py-0.5 rounded-md ${badgeColor} text-[10px] uppercase">${type}</span>
          <span class="font-bold text-slate-800 dark:text-slate-200">${message}</span>
        </div>
        <span class="text-slate-400 font-mono text-[11px]">${time}</span>
      `;
      logContainer.prepend(entry);

      // Keep only recent 15 events
      if (logContainer.children.length > 15) {
        logContainer.removeChild(logContainer.lastChild);
      }
    }

    function showToast(msg) {
      const toast = document.getElementById('toast-modal');
      const toastMsg = document.getElementById('toast-msg');
      toastMsg.textContent = msg;
      toast.classList.remove('opacity-0', 'pointer-events-none', 'translate-y-4');
      toast.classList.add('opacity-100', 'translate-y-0');

      setTimeout(() => {
        toast.classList.add('opacity-0', 'pointer-events-none', 'translate-y-4');
        toast.classList.remove('opacity-100', 'translate-y-0');
      }, 3200);
    }

    function resetSimulation() {
      STATE.valveClosed = false;
      setZone('A');
      showToast("System reset to standard baseline state.");
      logEvent("Simulation reset to baseline", "Reset");
    }

    /* =========================================================================
       9. EVENT LISTENERS & INITIALIZATION
       ========================================================================= */
    window.addEventListener('DOMContentLoaded', () => {
      // Language selector
      const langSelect = document.getElementById('lang-select');
      langSelect.addEventListener('change', (e) => {
        STATE.language = e.target.value;
        updateUI();
        speakCurrentStatus();
      });

      // Valve triggers
      document.getElementById('valve-btn').addEventListener('click', toggleValve);
      document.getElementById('mobile-valve-btn')?.addEventListener('click', toggleValve);

      // Speak current status
      document.getElementById('speak-status-btn').addEventListener('click', () => {
        getAudioContext(); // User gesture unlocks Web Audio
        speakCurrentStatus();
      });

      // Siren test button
      document.getElementById('test-audio-btn').addEventListener('click', () => {
        getAudioContext();
        if (STATE.isSirenPlaying) {
          stopEmergencySiren();
          showToast("Siren stopped");
        } else {
          startEmergencySiren();
          showToast("Testing siren alert sound (Tap again to stop)");
          setTimeout(stopEmergencySiren, 4500);
        }
      });

      // Theme toggle (Dark / Light)
      const themeToggle = document.getElementById('theme-toggle');
      const themeIcon = document.getElementById('theme-icon');
      
      // Auto-detect system theme preference
      if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
        document.documentElement.classList.add('dark');
        themeIcon.className = "ph-fill ph-sun text-xl text-amber-400";
      }

      themeToggle.addEventListener('click', () => {
        const isDark = document.documentElement.classList.toggle('dark');
        themeIcon.className = isDark ? "ph-fill ph-sun text-xl text-amber-400" : "ph-fill ph-moon text-xl";
      });

      // Initial system logs and UI run
      logEvent("Water Sentry system booted successfully", "Info");
      logEvent("Pressure sensor node calibrated (45 PSI)", "A");
      logEvent("Continuous ultrasonic flowmeter operational", "A");
      setZone('A');

      // Subtle dynamic fluctuations to mimic live telemetry
      setInterval(() => {
        if (!STATE.valveClosed && STATE.currentZone === 'A') {
          const jitter = (Math.random() - 0.5) * 2;
          STATE.flowRate = Math.round(Math.max(22, Math.min(30, 26 + jitter)));
          STATE.pressure = Math.round(Math.max(43, Math.min(48, 45 + jitter)));
          updateUI();
        }
      }, 3500);
    });
  </script>
</body>
</html>
