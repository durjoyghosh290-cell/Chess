
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Chess: head-to-head Stellar Conflict</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { margin: 0; overflow: hidden; background-color: #010103; font-family: 'Orbitron', sans-serif; color: #fff; user-select: none; }
        
        /* --- SCREENS: LOBBY & GAME OVER --- */
        .overlay-screen {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: radial-gradient(circle at center, #050a15 0%, #000000 100%);
            display: flex; flex-direction: column; align-items: center; justify-content: center;
            z-index: 20;
        }
        
        .overlay-screen::before {
            content: ""; position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
            background-size: 100% 4px, 3px 100%; z-index: -1; pointer-events: none;
        }
        
        .screen-title { font-size: 52px; margin-bottom: 5px; font-weight: 900; letter-spacing: 10px; color: #0ff; text-shadow: 0 0 10px #0ff, 0 0 20px #0ff; }
        .screen-subtitle { font-size: 16px; color: #08f; margin-bottom: 50px; letter-spacing: 5px; text-transform: uppercase; }
        
        .btn-container { display: flex; gap: 50px; }
        
        .cyber-btn {
            padding: 20px 40px; font-size: 18px; font-family: 'Orbitron', sans-serif; font-weight: 700; border-radius: 0;
            cursor: pointer; transition: all 0.2s; color: #fff; text-transform: uppercase; letter-spacing: 3px;
            background: rgba(0, 20, 40, 0.6); position: relative;
            clip-path: polygon(10% 0, 100% 0, 100% 70%, 90% 100%, 0 100%, 0 30%);
        }
        
        #btn-durjay { border: 2px solid #0ff; box-shadow: 0 0 15px rgba(0, 255, 255, 0.3) inset; color: #0ff; }
        #btn-durjay:hover { background: rgba(0, 255, 255, 0.2); box-shadow: 0 0 30px rgba(0, 255, 255, 0.8) inset; }
        
        #btn-yash { border: 2px solid #f0f; box-shadow: 0 0 15px rgba(255, 0, 255, 0.3) inset; color: #f0f; }
        #btn-yash:hover { background: rgba(255, 0, 255, 0.2); box-shadow: 0 0 30px rgba(255, 0, 255, 0.8) inset; }

        #btn-restart { border: 2px solid #fff; box-shadow: 0 0 15px rgba(255, 255, 255, 0.3) inset; color: #fff; margin-top: 20px;}
        #btn-restart:hover { background: rgba(255, 255, 255, 0.2); box-shadow: 0 0 30px rgba(255, 255, 255, 0.8) inset; }

        /* Game Over Specifics */
        #game-over-screen { display: none; background: rgba(0, 0, 0, 0.85); backdrop-filter: blur(10px); z-index: 30;}

        /* --- IN-GAME HUD --- */
        #game-ui {
            position: absolute; top: 0; left: 0; width: 100%; height: 100%;
            pointer-events: none; z-index: 10; display: none; flex-direction: column; justify-content: space-between; padding: 30px; box-sizing: border-box;
        }
        
        .header { text-align: center; background: rgba(0, 10, 20, 0.8); padding: 20px 40px; border: 1px solid #0ff; box-shadow: 0 0 15px rgba(0, 255, 255, 0.2); margin: 0 auto; width: fit-content; pointer-events: auto; backdrop-filter: blur(5px); clip-path: polygon(5% 0, 95% 0, 100% 50%, 95% 100%, 5% 100%, 0 50%);}
        h1 { margin: 0 0 10px 0; font-size: 24px; letter-spacing: 6px; color: #fff; text-shadow: 0 0 8px #fff;}
        
        #turn-indicator { font-size: 16px; font-weight: 700; padding: 8px 25px; display: inline-block; transition: 0.3s; letter-spacing: 4px; }
        .durjay-turn { background-color: rgba(0, 255, 255, 0.1); color: #0ff; border: 1px solid #0ff; box-shadow: 0 0 10px #0ff; }
        .yash-turn { background-color: rgba(255, 0, 255, 0.1); color: #f0f; border: 1px solid #f0f; box-shadow: 0 0 10px #f0f; }
        .ai-thinking { background-color: rgba(255, 165, 0, 0.1); color: #ffa500; border: 1px solid #ffa500; animation: glitch 0.5s infinite; }
        
        @keyframes glitch { 0% { transform: translate(0) } 20% { transform: translate(-2px, 1px) } 40% { transform: translate(-1px, -1px) } 60% { transform: translate(2px, 1px) } 80% { transform: translate(1px, -1px) } 100% { transform: translate(0) } }

        @keyframes victoryPulse {
            0%   { box-shadow: 0 0 0px rgba(0,255,255,0); }
            25%  { box-shadow: 0 0 80px rgba(0,255,255,0.8), inset 0 0 80px rgba(0,255,255,0.2); }
            50%  { box-shadow: 0 0 20px rgba(0,255,255,0.3); }
            75%  { box-shadow: 0 0 100px rgba(0,255,255,1), inset 0 0 100px rgba(0,255,255,0.3); }
            100% { box-shadow: 0 0 40px rgba(0,255,255,0.5); }
        }
        @keyframes defeatPulse {
            0%   { box-shadow: 0 0 0px rgba(255,0,255,0); }
            25%  { box-shadow: 0 0 80px rgba(255,100,0,0.9), inset 0 0 80px rgba(255,60,0,0.4); }
            50%  { box-shadow: 0 0 20px rgba(255,80,0,0.4); }
            75%  { box-shadow: 0 0 120px rgba(255,200,0,1), inset 0 0 100px rgba(255,80,0,0.5); }
            100% { box-shadow: 0 0 40px rgba(255,60,0,0.5); }
        }
        @keyframes titleZoomIn {
            0%   { transform: scale(0.2) rotate(-10deg); opacity: 0; }
            60%  { transform: scale(1.15) rotate(2deg); opacity: 1; }
            100% { transform: scale(1) rotate(0deg); }
        }
        .victory-flash { animation: victoryPulse 1s ease-in-out 3; }
        .defeat-flash  { animation: defeatPulse 0.8s ease-in-out 4; }
        #game-over-title { animation: titleZoomIn 0.7s cubic-bezier(0.175,0.885,0.32,1.275) forwards; }

        .instructions { position: absolute; bottom: 30px; left: 30px; background: rgba(0, 10, 20, 0.8); padding: 15px 25px; font-size: 12px; color: #0ff; border-left: 4px solid #0ff; line-height: 2; pointer-events: auto; text-transform: uppercase; letter-spacing: 1px;}
        .instructions strong { color: #fff; text-shadow: 0 0 5px #0ff; font-size: 14px;}
        
        #watermark { position: absolute; bottom: 30px; right: 30px; font-family: 'Orbitron', sans-serif; font-weight: 900; color: #0ff; font-size: 18px; letter-spacing: 6px; pointer-events: none; text-shadow: 0 0 10px rgba(0,255,255,0.5); border-right: 4px solid #0ff; padding-right: 15px; }

        /* --- MODE SELECTOR --- */
        .mode-selector { display:flex; gap:20px; margin-bottom:30px; }
        .mode-btn { padding:14px 30px; font-size:14px; }
        .active-mode { background: rgba(0,255,255,0.15) !important; }

        /* --- ARENA INTRO ANIMATION --- */
        #arena-intro {
            position: absolute; top:0; left:0; width:100%; height:100%;
            pointer-events:none; z-index:25; display:none;
            background: radial-gradient(circle at center, #020a18 0%, #000000 100%);
        }
        #arena-intro-canvas {
            position:absolute; top:0; left:0; width:100%; height:100%;
        }
        #arena-intro-text {
            position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
            font-family:'Orbitron',sans-serif; font-weight:900; font-size:72px;
            color:#0ff; text-align:center; letter-spacing:16px;
            text-shadow:0 0 20px #0ff,0 0 60px #0ff,0 0 120px #0ff;
            opacity:0; white-space:nowrap;
        }
        #arena-intro-sub {
            position:absolute; top:calc(50% + 70px); left:50%; transform:translateX(-50%);
            font-family:'Orbitron',sans-serif; font-size:18px; letter-spacing:8px;
            color:#08f; opacity:0; white-space:nowrap;
        }
        @keyframes introTextIn {
            0%   { opacity:0; transform:translate(-50%,-50%) scale(2.5) skewX(-10deg); filter:blur(20px); }
            40%  { opacity:1; transform:translate(-50%,-50%) scale(0.95) skewX(0deg); filter:blur(0px); }
            70%  { opacity:1; transform:translate(-50%,-50%) scale(1.04); }
            100% { opacity:1; transform:translate(-50%,-50%) scale(1); }
        }
        @keyframes introSubIn {
            0%   { opacity:0; letter-spacing:30px; }
            100% { opacity:1; letter-spacing:8px; }
        }
        @keyframes introFadeOut {
            0%   { opacity:1; }
            100% { opacity:0; }
        }
        .intro-scanline {
            position:absolute; left:0; width:100%; height:3px;
            background:linear-gradient(90deg,transparent,#0ff,transparent);
            animation:scanSweep 1.2s ease-in-out forwards;
        }
        @keyframes scanSweep {
            0%   { top:-3px; opacity:1; }
            100% { top:100%; opacity:0.2; }
        }

        /* --- MULTIPLAYER TURN INDICATOR --- */
        .p1-turn { background-color: rgba(0,255,255,0.1); color:#0ff; border:1px solid #0ff; box-shadow:0 0 10px #0ff; }
        .p2-turn { background-color: rgba(255,0,255,0.1); color:#f0f; border:1px solid #f0f; box-shadow:0 0 10px #f0f; }
        
        #multiplayer-label {
            position:absolute; top:30px; right:30px;
            font-family:'Orbitron',sans-serif; font-size:11px; letter-spacing:3px;
            color:rgba(170,255,170,0.7); padding:6px 14px;
            border:1px solid rgba(170,255,170,0.4); pointer-events:none;
            display:none;
        }

        /* --- ONLINE MULTIPLAYER UI --- */
        #online-panel {
            display:none; flex-direction:column; align-items:center; gap:18px;
            background:rgba(0,10,20,0.85); border:1px solid #0f8; padding:28px 40px;
            margin-bottom:20px; min-width:340px;
        }
        #online-panel.show { display:flex; }
        .online-label { font-size:11px; letter-spacing:3px; color:#0f8; text-transform:uppercase; }
        #room-code-display {
            font-size:36px; letter-spacing:12px; color:#0ff; font-weight:900;
            text-shadow:0 0 15px #0ff; background:rgba(0,20,40,0.6);
            padding:12px 28px; border:2px solid #0ff; cursor:pointer;
            user-select:all; min-width:200px; text-align:center;
        }
        #room-code-display:hover { background:rgba(0,255,255,0.1); }
        #join-input {
            font-family:'Orbitron',sans-serif; font-size:22px; letter-spacing:8px;
            color:#0ff; background:rgba(0,20,40,0.8); border:2px solid #0ff;
            padding:10px 20px; text-align:center; text-transform:uppercase;
            outline:none; width:200px;
        }
        #join-input::placeholder { color:rgba(0,255,255,0.3); letter-spacing:4px; }
        #online-status {
            font-size:12px; letter-spacing:3px; color:#0f8;
            text-align:center; min-height:20px; text-transform:uppercase;
        }
        #online-status.error { color:#f44; }
        #online-status.success { color:#0f8; }
        .online-btn-row { display:flex; gap:16px; margin-top:4px; }
        #btn-copy-code {
            font-family:'Orbitron',sans-serif; font-size:11px; letter-spacing:2px;
            color:#0f8; border:1px solid #0f8; background:transparent;
            padding:6px 16px; cursor:pointer; text-transform:uppercase;
        }
        #btn-copy-code:hover { background:rgba(0,255,136,0.1); }

        /* Online HUD indicator */
        #online-label {
            position:absolute; top:30px; right:30px;
            font-family:'Orbitron',sans-serif; font-size:11px; letter-spacing:3px;
            color:rgba(0,255,136,0.8); padding:6px 14px;
            border:1px solid rgba(0,255,136,0.4); pointer-events:none;
            display:none; text-transform:uppercase;
        }
        #connection-dot {
            display:inline-block; width:8px; height:8px; border-radius:50%;
            background:#0f8; box-shadow:0 0 6px #0f8; margin-right:8px;
            animation:blink 1.5s infinite;
        }
        @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }

        /* --- PASS DEVICE MODAL --- */
        #pass-device-modal {
            display:none; position:absolute; top:0; left:0; width:100%; height:100%;
            background:rgba(0,0,0,0.92); z-index:22; backdrop-filter:blur(12px);
            flex-direction:column; align-items:center; justify-content:center;
            pointer-events:none;
        }
        #pass-device-modal.show { display:flex; pointer-events:all; }
        #pass-device-title {
            font-family:'Orbitron',sans-serif; font-weight:900; font-size:36px;
            letter-spacing:10px; margin-bottom:10px;
        }
        #pass-device-sub {
            font-family:'Orbitron',sans-serif; font-size:13px; letter-spacing:4px;
            color:#888; margin-bottom:40px; text-transform:uppercase;
        }
        #pass-device-info {
            font-family:'Orbitron',sans-serif; font-size:11px; letter-spacing:3px;
            color:#555; margin-bottom:50px; text-transform:uppercase;
        }
        #btn-pass-confirm {
            border: 2px solid #afa; box-shadow: 0 0 15px rgba(170,255,170,0.3) inset;
            color:#afa;
        }
        #btn-pass-confirm:hover { background:rgba(170,255,170,0.15); box-shadow:0 0 30px rgba(170,255,170,0.7) inset; }
    </style>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/peerjs/1.5.2/peerjs.min.js"></script>
    <script type="importmap">
        {
            "imports": {
                "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
                "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
            }
        }
    </script>
</head>
<body>

    <div id="arena-intro">
        <canvas id="arena-intro-canvas"></canvas>
        <div id="arena-intro-text">ORBITAL ARENA</div>
        <div id="arena-intro-sub">INITIATING STELLAR CONFLICT</div>
        <div class="intro-scanline" id="intro-scanline" style="display:none;"></div>
    </div>
    <div id="multiplayer-label">👥 LOCAL MULTIPLAYER</div>
    <div id="online-label"><span id="connection-dot"></span>ONLINE MATCH</div>

    <!-- Pass Device Modal (multiplayer) -->
    <div id="pass-device-modal">
        <div id="pass-device-title">HAND OFF</div>
        <div id="pass-device-sub" id="pass-device-sub">PASS THE DEVICE</div>
        <div id="pass-device-info">▸ COVER YOUR SCREEN BEFORE HANDING OVER ◂</div>
        <button id="btn-pass-confirm" class="cyber-btn">READY — CONFIRM TURN</button>
    </div>

    <div id="lobby-screen" class="overlay-screen">
        <h1 class="screen-title">STELLAR CONFLICT</h1>
        <p class="screen-subtitle">SELECT BATTLE MODE</p>
        <div class="mode-selector" id="mode-selector">
            <button id="btn-mode-ai" class="cyber-btn mode-btn active-mode">⚡ VS AI</button>
            <button id="btn-mode-multi" class="cyber-btn mode-btn" style="border-color:#afa;color:#afa;box-shadow:0 0 15px rgba(170,255,170,0.3) inset;">👥 2 PLAYERS</button>
            <button id="btn-mode-online" class="cyber-btn mode-btn" style="border-color:#0f8;color:#0f8;box-shadow:0 0 15px rgba(0,255,136,0.3) inset;">🌐 ONLINE</button>
        </div>
        <p id="lobby-desc" class="screen-subtitle" style="margin-top:-20px;margin-bottom:20px;font-size:13px;">Choose your faction — enemy AI controls the other side</p>

        <!-- Online Panel -->
        <div id="online-panel">
            <div id="online-host-section">
                <div class="online-label">YOUR ROOM CODE — SHARE WITH FRIEND</div>
                <div id="room-code-display">···</div>
                <button id="btn-copy-code" class="cyber-btn">📋 COPY CODE</button>
                <div class="online-label" style="margin-top:8px;color:#555;">— OR JOIN A ROOM —</div>
            </div>
            <div>
                <div class="online-label" style="margin-bottom:8px;">ENTER FRIEND'S CODE</div>
                <input id="join-input" placeholder="CODE" maxlength="6" autocomplete="off" spellcheck="false"/>
            </div>
            <div class="online-btn-row">
                <button id="btn-join-room" class="cyber-btn" style="border-color:#0f8;color:#0f8;box-shadow:0 0 10px rgba(0,255,136,0.2) inset;">JOIN FRIEND</button>
            </div>
            <div id="online-status">CONNECTING TO NETWORK...</div>
        </div>
        <div class="btn-container" id="faction-btns">
            <button id="btn-durjay" class="cyber-btn">TEAM DURJAY [CYAN]</button>
            <button id="btn-yash" class="cyber-btn">TEAM YASH [MAGENTA]</button>
        </div>
        <button id="btn-start-multi" class="cyber-btn" style="display:none;border-color:#afa;color:#afa;box-shadow:0 0 15px rgba(170,255,170,0.3) inset;margin-top:10px;" >LAUNCH ARENA</button>
    </div>

    <div id="game-over-screen" class="overlay-screen">
        <h1 id="game-over-title" class="screen-title">VICTORY</h1>
        <p id="game-over-subtitle" class="screen-subtitle">ENEMY SYSTEM DESTROYED</p>
        <button id="btn-restart" class="cyber-btn">REBOOT SYSTEM</button>
    </div>

    <div id="game-ui">
        <div class="header">
            <h1>ORBITAL ARENA</h1>
            <div id="turn-indicator" class="durjay-turn">AWAITING INPUT</div>
        </div>
        <div class="instructions" id="instructions-panel">
            <strong>COMMAND PROTOCOLS:</strong><br>
            > Eradicate Enemy Sun (King).<br>
            > <span style="color:#ffaa00">■</span> Gold tile = Castling available.<br>
            > AI Threat Evaluation: ONLINE.<br>
            > Scroll: Zoom | Drag: Orbit.
        </div>
        <div id="watermark">MADE BY GHOSH</div>
    </div>

    <script type="module">
        import * as THREE from 'three';
        import { OrbitControls } from 'three/addons/controls/OrbitControls.js';

        // --- 1. GAME STATE ---
        let humanTeam = null;
        let aiTeam = null;
        let gameActive = false;
        let currentTurn = 'durjay';
        let isMultiplayer = false; // NEW: multiplayer flag

        // --- ONLINE MULTIPLAYER STATE ---
        let isOnline = false;
        let myTeam = null;       // 'durjay' (host) or 'yash' (joiner)
        let peer = null;
        let conn = null;
        let isHost = false;

        const lobbyScreen = document.getElementById('lobby-screen');
        const gameOverScreen = document.getElementById('game-over-screen');
        const gameUI = document.getElementById('game-ui');
        const turnIndicator = document.getElementById('turn-indicator');
        const multiplayerLabel = document.getElementById('multiplayer-label');
        
        const gameOverTitle = document.getElementById('game-over-title');
        const gameOverSubtitle = document.getElementById('game-over-subtitle');

        // --- MODE SELECTOR LOGIC ---
        const btnModeAI = document.getElementById('btn-mode-ai');
        const btnModeMulti = document.getElementById('btn-mode-multi');
        const btnModeOnline = document.getElementById('btn-mode-online');
        const factionBtns = document.getElementById('faction-btns');
        const btnStartMulti = document.getElementById('btn-start-multi');
        const lobbyDesc = document.getElementById('lobby-desc');
        const onlinePanel = document.getElementById('online-panel');
        const roomCodeDisplay = document.getElementById('room-code-display');
        const joinInput = document.getElementById('join-input');
        const onlineStatus = document.getElementById('online-status');
        const onlineLabel = document.getElementById('online-label');

        btnModeAI.addEventListener('click', () => {
            isMultiplayer = false; isOnline = false;
            btnModeAI.classList.add('active-mode');
            btnModeMulti.classList.remove('active-mode');
            btnModeOnline.classList.remove('active-mode');
            factionBtns.style.display = 'flex';
            btnStartMulti.style.display = 'none';
            onlinePanel.classList.remove('show');
            lobbyDesc.textContent = 'Choose your faction — enemy AI controls the other side';
        });
        btnModeMulti.addEventListener('click', () => {
            isMultiplayer = true; isOnline = false;
            btnModeMulti.classList.add('active-mode');
            btnModeAI.classList.remove('active-mode');
            btnModeOnline.classList.remove('active-mode');
            factionBtns.style.display = 'none';
            btnStartMulti.style.display = 'block';
            onlinePanel.classList.remove('show');
            lobbyDesc.textContent = 'Two players share this screen — take turns commanding your fleet';
        });
        btnModeOnline.addEventListener('click', () => {
            isOnline = true; isMultiplayer = false;
            btnModeOnline.classList.add('active-mode');
            btnModeAI.classList.remove('active-mode');
            btnModeMulti.classList.remove('active-mode');
            factionBtns.style.display = 'none';
            btnStartMulti.style.display = 'none';
            onlinePanel.classList.add('show');
            lobbyDesc.textContent = 'Play with a friend anywhere in the world — share your room code!';
            initPeer();
        });

        document.getElementById('btn-durjay').addEventListener('click', () => startGame('durjay', 'yash'));
        document.getElementById('btn-yash').addEventListener('click', () => startGame('yash', 'durjay'));
        document.getElementById('btn-start-multi').addEventListener('click', () => startGame('durjay', null));
        document.getElementById('btn-restart').addEventListener('click', restartGame);

        // =====================================================================
        // --- ONLINE MULTIPLAYER (PeerJS WebRTC) ---
        // =====================================================================
        function genCode() {
            return Math.random().toString(36).substring(2, 8).toUpperCase();
        }

        function setOnlineStatus(msg, type = '') {
            onlineStatus.textContent = msg;
            onlineStatus.className = type ? `${type}` : '';
        }

        function initPeer() {
            if (peer) { peer.destroy(); peer = null; }
            const code = genCode();
            isHost = true;
            myTeam = 'durjay'; // host is always cyan/durjay
            roomCodeDisplay.textContent = code;
            setOnlineStatus('WAITING FOR FRIEND TO JOIN...');

            peer = new Peer(code);

            peer.on('open', (id) => {
                setOnlineStatus('ROOM READY — SHARE YOUR CODE', 'success');
            });

            peer.on('connection', (connection) => {
                conn = connection;
                setupConnection();
                setOnlineStatus('FRIEND CONNECTED! LAUNCHING...', 'success');
                // Tell joiner their team, then start
                setTimeout(() => {
                    sendMsg({ type: 'start', hostTeam: 'durjay', joinTeam: 'yash' });
                    startOnlineGame();
                }, 800);
            });

            peer.on('error', (err) => {
                setOnlineStatus('CONNECTION ERROR: ' + err.type, 'error');
            });
        }

        function joinRoom(code) {
            if (!code || code.length < 4) { setOnlineStatus('ENTER A VALID CODE', 'error'); return; }
            if (peer) { peer.destroy(); peer = null; }
            isHost = false;
            myTeam = 'yash'; // joiner is always magenta/yash
            setOnlineStatus('CONNECTING TO ' + code + '...');

            peer = new Peer();
            peer.on('open', () => {
                conn = peer.connect(code);
                setupConnection();
            });
            peer.on('error', (err) => {
                setOnlineStatus('FAILED TO CONNECT. CHECK CODE.', 'error');
            });
        }

        function setupConnection() {
            conn.on('open', () => {
                if (!isHost) {
                    setOnlineStatus('CONNECTED! WAITING FOR HOST...', 'success');
                }
            });

            conn.on('data', (data) => {
                handleNetworkMsg(data);
            });

            conn.on('close', () => {
                if (gameActive) {
                    setOnlineStatus('OPPONENT DISCONNECTED', 'error');
                    gameActive = false;
                    alert('Your opponent has disconnected.');
                }
            });

            conn.on('error', (err) => {
                setOnlineStatus('CONNECTION LOST', 'error');
            });
        }

        function sendMsg(data) {
            if (conn && conn.open) {
                conn.send(data);
            }
        }

        function handleNetworkMsg(data) {
            if (data.type === 'start') {
                // Joiner receives start signal
                myTeam = data.joinTeam;
                startOnlineGame();
            } else if (data.type === 'move') {
                applyRemoteMove(data);
            } else if (data.type === 'restart') {
                restartGame(true);
            }
        }

        // Called on both host and joiner
        function startOnlineGame() {
            isOnline = true;
            isMultiplayer = false;
            humanTeam = myTeam;
            aiTeam = null;
            lobbyScreen.style.display = 'none';

            playArenaIntro(() => {
                gameUI.style.display = 'flex';
                gameActive = true;
                onlineLabel.style.display = 'block';
                multiplayerLabel.style.display = 'none';
                if (bossAvatar) bossAvatar.visible = false;
                if (playerAvatar) playerAvatar.visible = false;

                // Orient camera for each player's perspective
                if (myTeam === 'durjay') {
                    camera.position.set(0, 10, 12);
                } else {
                    camera.position.set(0, 10, -12);
                }
                camera.lookAt(0, 0, 0);

                const instrPanel = document.getElementById('instructions-panel');
                instrPanel.innerHTML = `<strong>COMMAND PROTOCOLS:</strong><br>
                    > You are <span style="color:${myTeam==='durjay'?'#0ff':'#f0f'}">${myTeam==='durjay'?'CYAN [DURJAY]':'MAGENTA [YASH]'}</span><br>
                    > Capture the enemy Sun (King).<br>
                    > <span style="color:#ffaa00">■</span> Gold tile = Castling.<br>
                    > Scroll: Zoom | Drag: Orbit.`;

                updateTurnUI();
            });
        }

        function applyRemoteMove(data) {
            // Find piece at source position
            const piece = pieces.find(p =>
                p.userData.gridX === data.fromX && p.userData.gridY === data.fromY
            );
            if (!piece) return;

            // Find the target tile
            const tile = tiles.find(t => t.userData.gridX === data.toX && t.userData.gridY === data.toY);
            if (!tile) return;

            // Simulate selecting the piece then executing the move
            selectedPiece = piece;
            // Highlight the target tile temporarily so executeMove works
            tile.userData.castling = data.castling || null;
            validMoveTiles = [tile];
            executeMove(tile);
        }

        // Join button
        document.getElementById('btn-join-room').addEventListener('click', () => {
            const code = joinInput.value.trim().toUpperCase();
            joinRoom(code);
        });
        joinInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') {
                const code = joinInput.value.trim().toUpperCase();
                joinRoom(code);
            }
        });

        // Copy code button
        document.getElementById('btn-copy-code').addEventListener('click', () => {
            const code = roomCodeDisplay.textContent;
            if (code && code !== '···') {
                navigator.clipboard.writeText(code).then(() => {
                    document.getElementById('btn-copy-code').textContent = '✅ COPIED!';
                    setTimeout(() => document.getElementById('btn-copy-code').textContent = '📋 COPY CODE', 2000);
                });
            }
        });
        // =====================================================================

        // --- ARENA INTRO ANIMATION ---
        function playArenaIntro(callback) {
            const introEl = document.getElementById('arena-intro');
            const introText = document.getElementById('arena-intro-text');
            const introSub = document.getElementById('arena-intro-sub');
            const scanline = document.getElementById('intro-scanline');
            const introCanvas = document.getElementById('arena-intro-canvas');

            // Reset state
            introEl.style.display = 'block';
            introEl.style.opacity = '1';
            introEl.style.animation = '';
            introText.style.animation = 'none';
            introText.style.opacity = '0';
            introSub.style.animation = 'none';
            introSub.style.opacity = '0';

            // Set up particle canvas
            introCanvas.width = window.innerWidth;
            introCanvas.height = window.innerHeight;
            const ctx2d = introCanvas.getContext('2d');

            // Particle system
            const particles = [];
            const cx = introCanvas.width / 2, cy = introCanvas.height / 2;
            for (let i = 0; i < 180; i++) {
                const angle = Math.random() * Math.PI * 2;
                const speed = 1.5 + Math.random() * 5;
                const hue = Math.random() < 0.6 ? 180 : 280; // cyan or purple
                particles.push({
                    x: cx, y: cy,
                    vx: Math.cos(angle) * speed,
                    vy: Math.sin(angle) * speed,
                    life: 1.0,
                    decay: 0.012 + Math.random() * 0.018,
                    size: 1.5 + Math.random() * 3,
                    hue,
                    trail: []
                });
            }

            let rafId;
            let particleStart = performance.now();
            function drawParticles(ts) {
                const elapsed = (ts - particleStart) / 1000;
                ctx2d.clearRect(0, 0, introCanvas.width, introCanvas.height);

                let alive = 0;
                particles.forEach(p => {
                    if (p.life <= 0) return;
                    alive++;
                    p.trail.push({x: p.x, y: p.y});
                    if (p.trail.length > 8) p.trail.shift();
                    p.x += p.vx;
                    p.y += p.vy;
                    p.vx *= 0.97;
                    p.vy *= 0.97;
                    p.life -= p.decay;

                    // Draw trail
                    for (let t = 0; t < p.trail.length - 1; t++) {
                        const alpha = (t / p.trail.length) * p.life * 0.6;
                        ctx2d.strokeStyle = `hsla(${p.hue},100%,70%,${alpha})`;
                        ctx2d.lineWidth = p.size * (t / p.trail.length);
                        ctx2d.beginPath();
                        ctx2d.moveTo(p.trail[t].x, p.trail[t].y);
                        ctx2d.lineTo(p.trail[t+1].x, p.trail[t+1].y);
                        ctx2d.stroke();
                    }

                    // Draw head glow
                    const grd = ctx2d.createRadialGradient(p.x, p.y, 0, p.x, p.y, p.size * 3);
                    grd.addColorStop(0, `hsla(${p.hue},100%,90%,${p.life})`);
                    grd.addColorStop(1, `hsla(${p.hue},100%,60%,0)`);
                    ctx2d.fillStyle = grd;
                    ctx2d.beginPath();
                    ctx2d.arc(p.x, p.y, p.size * 3, 0, Math.PI * 2);
                    ctx2d.fill();
                });

                // Draw central burst ring
                if (elapsed < 0.5) {
                    const r = elapsed * 400;
                    const alpha = Math.max(0, 1 - elapsed * 3);
                    ctx2d.strokeStyle = `rgba(0,255,255,${alpha})`;
                    ctx2d.lineWidth = 3;
                    ctx2d.beginPath();
                    ctx2d.arc(cx, cy, r, 0, Math.PI * 2);
                    ctx2d.stroke();
                    // Second ring
                    ctx2d.strokeStyle = `rgba(180,0,255,${alpha * 0.6})`;
                    ctx2d.beginPath();
                    ctx2d.arc(cx, cy, r * 0.7, 0, Math.PI * 2);
                    ctx2d.stroke();
                }

                if (alive > 0 || elapsed < 0.6) {
                    rafId = requestAnimationFrame(drawParticles);
                } else {
                    ctx2d.clearRect(0, 0, introCanvas.width, introCanvas.height);
                }
            }
            rafId = requestAnimationFrame(drawParticles);

            // Phase 1: scanline sweeps down
            scanline.style.display = 'block';
            scanline.style.animation = 'none';
            scanline.offsetHeight;
            scanline.style.animation = 'scanSweep 0.9s ease-in-out forwards';

            // Phase 2: title crashes in
            setTimeout(() => {
                introText.style.animation = 'introTextIn 0.8s cubic-bezier(0.175,0.885,0.32,1.275) forwards';
            }, 300);

            // Phase 3: subtitle types in
            setTimeout(() => {
                introSub.style.animation = 'introSubIn 0.6s ease-out forwards';
            }, 900);

            // Phase 4: hold then fade out everything
            setTimeout(() => {
                cancelAnimationFrame(rafId);
                ctx2d.clearRect(0, 0, introCanvas.width, introCanvas.height);
                introEl.style.animation = 'introFadeOut 0.7s ease-in forwards';
                setTimeout(() => {
                    introEl.style.display = 'none';
                    introEl.style.animation = '';
                    introText.style.animation = '';
                    introSub.style.animation = '';
                    introText.style.opacity = '0';
                    introSub.style.opacity = '0';
                    scanline.style.display = 'none';
                    callback();
                }, 700);
            }, 2400);
        }

        // --- PASS DEVICE MODAL (multiplayer) ---
        const passModal = document.getElementById('pass-device-modal');
        const passTitle = document.getElementById('pass-device-title');
        const passSub = document.getElementById('pass-device-sub');
        document.getElementById('btn-pass-confirm').addEventListener('click', () => {
            passModal.classList.remove('show');
        });

        function showPassDeviceModal(nextTeam) {
            const name = nextTeam === 'durjay' ? 'DURJAY [CYAN]' : 'YASH [MAGENTA]';
            const color = nextTeam === 'durjay' ? '#0ff' : '#f0f';
            passTitle.textContent = name;
            passTitle.style.color = color;
            passTitle.style.textShadow = `0 0 10px ${color}, 0 0 30px ${color}`;
            passSub.textContent = "IT'S YOUR TURN — PASS THE DEVICE";
            passModal.classList.add('show');
        }

        function startGame(playerSelection, aiSelection) {
            humanTeam = playerSelection;
            aiTeam = isMultiplayer ? null : aiSelection;
            
            lobbyScreen.style.display = 'none';

            // Play intro animation THEN show game
            playArenaIntro(() => {
                gameUI.style.display = 'flex';
                gameActive = true;

                const instrPanel = document.getElementById('instructions-panel');
                if (isMultiplayer) {
                    multiplayerLabel.style.display = 'block';
                    if (bossAvatar) bossAvatar.visible = false;
                    if (playerAvatar) playerAvatar.visible = false;
                    camera.position.z = 0;
                    camera.position.y = 14;
                    camera.position.x = 0;
                    instrPanel.innerHTML = `<strong>COMMAND PROTOCOLS:</strong><br>
                        > Eradicate Enemy Sun (King).<br>
                        > <span style="color:#ffaa00">■</span> Gold tile = Castling available.<br>
                        > <span style="color:#0ff">CYAN</span> vs <span style="color:#f0f">MAGENTA</span> — local duel.<br>
                        > Scroll: Zoom | Drag: Orbit.`;
                    // Show first pass-device for Durjay (first mover)
                    setTimeout(() => showPassDeviceModal('durjay'), 300);
                } else {
                    multiplayerLabel.style.display = 'none';
                    if (bossAvatar) bossAvatar.visible = true;
                    if (playerAvatar) playerAvatar.visible = true;
                    camera.position.z = humanTeam === 'durjay' ? 12 : -12;
                    camera.position.y = 8;
                    instrPanel.innerHTML = `<strong>COMMAND PROTOCOLS:</strong><br>
                        > Eradicate Enemy Sun (King).<br>
                        > <span style="color:#ffaa00">■</span> Gold tile = Castling available.<br>
                        > AI Threat Evaluation: ONLINE.<br>
                        > Scroll: Zoom | Drag: Orbit.`;
                }
                camera.lookAt(0,0,0);
                updateTurnUI();
                
                if (!isMultiplayer && currentTurn === aiTeam) setTimeout(makeAIMove, 1000);
            });
        }

        // --- 2. AUDIO SYNTHESIZER ---
        function playEliminationSound() {
            const AudioContext = window.AudioContext || window.webkitAudioContext;
            if (!AudioContext) return;
            const ctx = new AudioContext();
            const t = ctx.currentTime;
            
            const osc = ctx.createOscillator();
            const gain = ctx.createGain();
            osc.type = 'sawtooth';
            osc.frequency.setValueAtTime(2000, t);
            osc.frequency.exponentialRampToValueAtTime(10, t + 0.3);
            gain.gain.setValueAtTime(0.3, t);
            gain.gain.exponentialRampToValueAtTime(0.01, t + 0.3);
            osc.connect(gain).connect(ctx.destination);
            
            osc.start(t); osc.stop(t + 0.3);
        }

        function playGameOverSound(win) {
            const AudioContext = window.AudioContext || window.webkitAudioContext;
            if (!AudioContext) return;
            const ctx = new AudioContext();
            const t = ctx.currentTime;
            if (win) {
                // Ascending victory fanfare
                [523, 659, 784, 1047].forEach((freq, i) => {
                    const osc = ctx.createOscillator(); const g = ctx.createGain();
                    osc.type = 'sine'; osc.frequency.value = freq;
                    g.gain.setValueAtTime(0, t + i*0.12);
                    g.gain.linearRampToValueAtTime(0.25, t + i*0.12 + 0.05);
                    g.gain.exponentialRampToValueAtTime(0.01, t + i*0.12 + 0.4);
                    osc.connect(g).connect(ctx.destination);
                    osc.start(t + i*0.12); osc.stop(t + i*0.12 + 0.5);
                });
            } else {
                // Descending defeat tone
                [400, 280, 180, 100].forEach((freq, i) => {
                    const osc = ctx.createOscillator(); const g = ctx.createGain();
                    osc.type = 'sawtooth'; osc.frequency.value = freq;
                    g.gain.setValueAtTime(0, t + i*0.18);
                    g.gain.linearRampToValueAtTime(0.2, t + i*0.18 + 0.05);
                    g.gain.exponentialRampToValueAtTime(0.01, t + i*0.18 + 0.5);
                    osc.connect(g).connect(ctx.destination);
                    osc.start(t + i*0.18); osc.stop(t + i*0.18 + 0.6);
                });
            }
        }

        // --- 3. SCENE & LIGHTING ---
        const scene = new THREE.Scene();
        scene.fog = new THREE.FogExp2(0x010103, 0.02); 

        const camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.body.appendChild(renderer.domElement);

        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        controls.maxPolarAngle = Math.PI / 2.1; 

        const ambientLight = new THREE.AmbientLight(0xffffff, 0.8); 
        scene.add(ambientLight);
        
        const dirLight = new THREE.DirectionalLight(0xffffff, 1);
        dirLight.position.set(10, 20, 10);
        scene.add(dirLight);

        // --- 4. COSMIC ENVIRONMENT ---
        const starGeo = new THREE.BufferGeometry();
        const starCount = 3000;
        const posArray = new Float32Array(starCount * 3);
        for(let i=0; i<starCount*3; i++) posArray[i] = (Math.random() - 0.5) * 200;
        starGeo.setAttribute('position', new THREE.BufferAttribute(posArray, 3));
        const starMat = new THREE.PointsMaterial({color: 0xffffff, size: 0.15, transparent: true, opacity: 0.8});
        const stars = new THREE.Points(starGeo, starMat);
        scene.add(stars);

        const gridHelper = new THREE.GridHelper(60, 60, 0x00ffff, 0x002244);
        gridHelper.position.y = -2;
        scene.add(gridHelper);

        // --- 5. HEAD-TO-HEAD PLAYER AVATARS ---
        function createPlayerAvatar(teamColor, titleText) {
            const canvas = document.createElement('canvas');
            canvas.width = 512; canvas.height = 512;
            const ctx = canvas.getContext('2d');
            
            ctx.fillStyle = '#050a15'; ctx.strokeStyle = teamColor; ctx.lineWidth = 4;
            ctx.lineJoin = 'round';
            ctx.beginPath();
            ctx.ellipse(256, 180, 70, 90, 0, 0, Math.PI * 2);
            ctx.moveTo(210, 240); 
            ctx.bezierCurveTo(150, 260, 50, 300, 20, 450); 
            ctx.lineTo(20, 512); ctx.lineTo(492, 512); ctx.lineTo(492, 450); 
            ctx.bezierCurveTo(462, 300, 362, 260, 302, 240); 
            ctx.closePath();
            ctx.fill(); ctx.stroke();
            
            ctx.fillStyle = teamColor; ctx.shadowColor = teamColor; ctx.shadowBlur = 20;
            ctx.fillRect(206, 160, 100, 20); 
            
            ctx.shadowBlur = 0; ctx.font = 'bold 22px "Orbitron", sans-serif';
            ctx.fillStyle = teamColor; ctx.textAlign = 'center';
            ctx.fillText(titleText, 256, 80);

            const tex = new THREE.CanvasTexture(canvas);
            const mat = new THREE.MeshBasicMaterial({map: tex, transparent: true, opacity: 0.85, side: THREE.DoubleSide});
            return new THREE.Mesh(new THREE.PlaneGeometry(25, 25), mat);
        }

        const bossAvatar = createPlayerAvatar('#ff00ff', "AI OVERLORD :: ACTIVE"); 
        bossAvatar.position.set(0, 10, -20);
        scene.add(bossAvatar);

        const playerAvatar = createPlayerAvatar('#00ffff', "SYSTEM OPERATOR :: ONLINE"); 
        playerAvatar.position.set(0, 10, 20); 
        scene.add(playerAvatar);

        // --- 6. HOLOGRAPHIC BOARD ---
        const boardSize = 8;
        const tileSize = 1.1;

        let selectedPiece = null;
        let validMoveTiles = [];
        let pendingTurnSwitch = false;
        const tiles = [];
        const pieces = [];
        const backRowLayout = ['rook', 'knight', 'bishop', 'queen', 'king', 'bishop', 'knight', 'rook'];

        function gridToWorld(x, y) {
            return { x: (x - boardSize / 2) * tileSize + (tileSize / 2), z: (y - boardSize / 2) * tileSize + (tileSize / 2) };
        }

        const tileGeo = new THREE.BoxGeometry(tileSize * 0.95, 0.05, tileSize * 0.95);
        const darkMat = new THREE.MeshBasicMaterial({ color: 0x001122, transparent: true, opacity: 0.6, wireframe: true });
        const lightMat = new THREE.MeshBasicMaterial({ color: 0x002244, transparent: true, opacity: 0.8 });
        const highlightMat = new THREE.MeshBasicMaterial({ color: 0x00ff00, transparent: true, opacity: 0.5, wireframe: true });
        const castleMat = new THREE.MeshBasicMaterial({ color: 0xffaa00, transparent: true, opacity: 0.7, wireframe: true });

        // --- PARTICLE SYSTEM FOR WIN/LOSE ANIMATION ---
        let winParticles = null;
        let winAnimActive = false;
        let winAnimTimer = 0;

        function createWinParticles(win) {
            if (winParticles) { scene.remove(winParticles); winParticles = null; }
            const count = 600;
            const geo = new THREE.BufferGeometry();
            const pos = new Float32Array(count * 3);
            const vel = new Float32Array(count * 3);
            const col = new Float32Array(count * 3);
            for (let i = 0; i < count; i++) {
                pos[i*3] = (Math.random()-0.5)*2; pos[i*3+1] = 0; pos[i*3+2] = (Math.random()-0.5)*2;
                const angle = Math.random() * Math.PI * 2;
                const speed = 0.05 + Math.random()*0.15;
                vel[i*3] = Math.cos(angle)*speed; vel[i*3+1] = 0.08+Math.random()*0.2; vel[i*3+2] = Math.sin(angle)*speed;
                if (win) { col[i*3]=0; col[i*3+1]=1; col[i*3+2]=1; }
                else { col[i*3]=1; col[i*3+1]=0; col[i*3+2]=1; }
            }
            geo.setAttribute('position', new THREE.BufferAttribute(pos, 3));
            geo.setAttribute('color', new THREE.BufferAttribute(col, 3));
            geo.userData.vel = vel;
            const mat = new THREE.PointsMaterial({ size: 0.2, vertexColors: true, transparent: true, opacity: 1 });
            winParticles = new THREE.Points(geo, mat);
            scene.add(winParticles);
            winAnimActive = true;
            winAnimTimer = 0;
        }

        function updateWinParticles(delta) {
            if (!winParticles || !winAnimActive) return;
            winAnimTimer += delta;
            const pos = winParticles.geometry.attributes.position.array;
            const vel = winParticles.geometry.userData.vel;
            for (let i = 0; i < pos.length/3; i++) {
                pos[i*3]   += vel[i*3];
                pos[i*3+1] += vel[i*3+1];
                pos[i*3+2] += vel[i*3+2];
                vel[i*3+1] -= 0.004; // gravity
            }
            winParticles.geometry.attributes.position.needsUpdate = true;
            winParticles.material.opacity = Math.max(0, 1 - winAnimTimer/3);
            if (winAnimTimer > 3) { scene.remove(winParticles); winParticles = null; winAnimActive = false; }
        }

        for (let x = 0; x < boardSize; x++) {
            for (let y = 0; y < boardSize; y++) {
                const isLight = (x + y) % 2 === 0;
                const tile = new THREE.Mesh(tileGeo, isLight ? lightMat : darkMat);
                const pos = gridToWorld(x, y);
                tile.position.set(pos.x, -0.025, pos.z);
                tile.userData = { gridX: x, gridY: y, originalMat: isLight ? lightMat : darkMat };
                scene.add(tile);
                tiles.push(tile);
            }
        }

        // --- 7. PROCEDURAL PLANET GENERATORS ---
        function createPlanetTexture(type) {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 256;
            const ctx = canvas.getContext('2d');

            if (type === 'king') { // SUN
                let grad = ctx.createRadialGradient(128,128,0,128,128,128);
                grad.addColorStop(0, '#ffffff'); grad.addColorStop(0.3, '#ffff00'); grad.addColorStop(1, '#ff3300');
                ctx.fillStyle = grad; ctx.fillRect(0,0,256,256);
            } else if (type === 'queen') { // JUPITER
                for(let y=0; y<256; y+=8) {
                    ctx.fillStyle = ['#c88b3a', '#e0cda9', '#a66a26', '#d39c7e'][Math.floor(Math.random()*4)];
                    ctx.fillRect(0, y, 256, 8 + Math.random()*10);
                }
                ctx.fillStyle = '#9e3b1c'; ctx.beginPath(); ctx.ellipse(150, 150, 40, 20, 0, 0, Math.PI*2); ctx.fill(); 
            } else if (type === 'rook') { // EARTH
                ctx.fillStyle = '#1e90ff'; ctx.fillRect(0,0,256,256); 
                ctx.fillStyle = '#228b22'; 
                for(let i=0; i<20; i++) {
                    ctx.beginPath(); ctx.arc(Math.random()*256, Math.random()*256, 20+Math.random()*30, 0, Math.PI*2); ctx.fill();
                }
            } else if (type === 'bishop') { // MARS
                ctx.fillStyle = '#c1440e'; ctx.fillRect(0,0,256,256);
                ctx.fillStyle = '#8b2500'; 
                for(let i=0; i<30; i++) {
                    ctx.beginPath(); ctx.arc(Math.random()*256, Math.random()*256, 5+Math.random()*15, 0, Math.PI*2); ctx.fill();
                }
            } else if (type === 'knight') { // SATURN
                ctx.fillStyle = '#eaddb6'; ctx.fillRect(0,0,256,256);
                ctx.fillStyle = '#c5ab6e'; ctx.fillRect(0, 100, 256, 60);
            } else if (type === 'pawn') { // MOON
                ctx.fillStyle = '#888888'; ctx.fillRect(0,0,256,256);
                ctx.fillStyle = '#555555'; 
                for(let i=0; i<40; i++) {
                    ctx.beginPath(); ctx.arc(Math.random()*256, Math.random()*256, 4+Math.random()*8, 0, Math.PI*2); ctx.fill();
                }
            }
            return new THREE.CanvasTexture(canvas);
        }

        function createTextSprite(message, hexColor) {
            const canvas = document.createElement('canvas');
            canvas.width = 512; canvas.height = 128;
            const context = canvas.getContext('2d');
            context.fillStyle = 'rgba(0, 10, 20, 0.7)'; context.strokeStyle = hexColor;
            context.lineWidth = 4; context.fillRect(64, 32, 384, 64); context.strokeRect(64, 32, 384, 64);
            context.font = 'bold 32px "Orbitron", sans-serif'; context.textAlign = 'center'; context.textBaseline = 'middle';
            context.fillStyle = hexColor; context.fillText(message.toUpperCase(), 256, 64);
            const spriteMaterial = new THREE.SpriteMaterial({ map: new THREE.CanvasTexture(canvas), transparent: true });
            const sprite = new THREE.Sprite(spriteMaterial);
            sprite.scale.set(1.5, 0.375, 1);
            return sprite;
        }

        const planetConfig = {
            'king':   { name: 'SUN [KING]', size: 0.45 }, 'queen':  { name: 'JUPITER [QUEEN]', size: 0.4 },
            'rook':   { name: 'EARTH [ROOK]', size: 0.35 }, 'bishop': { name: 'MARS [BISHOP]', size: 0.3 },
            'knight': { name: 'SATURN [KNIGHT]', size: 0.35, hasRing: true }, 'pawn':   { name: 'MOON [PAWN]', size: 0.2 }
        };

        const colors = { 'durjay': 0x00ffff, 'yash': 0xff00ff };
        const stringColors = { 'durjay': '#00ffff', 'yash': '#ff00ff' };

        const planetTextures = {
            'king': createPlanetTexture('king'), 'queen': createPlanetTexture('queen'), 'rook': createPlanetTexture('rook'),
            'bishop': createPlanetTexture('bishop'), 'knight': createPlanetTexture('knight'), 'pawn': createPlanetTexture('pawn')
        };

        function createPiece(type, x, y, team) {
            const pieceGroup = new THREE.Group();
            pieceGroup.userData = { type: type, gridX: x, gridY: y, team: team, isMoving: false, hasMoved: false };
            const teamColor = colors[team];
            const config = planetConfig[type];
            let h = 0.5;

            const geo = new THREE.SphereGeometry(config.size, 32, 32);
            const mat = type === 'king' ? new THREE.MeshBasicMaterial({ map: planetTextures[type] }) : new THREE.MeshStandardMaterial({ map: planetTextures[type], roughness: 0.8 });
            const planet = new THREE.Mesh(geo, mat);
            planet.position.y = h;
            pieceGroup.add(planet);
            pieceGroup.userData.planetMesh = planet; 

            if (config.hasRing) {
                const saturnRing = new THREE.Mesh(new THREE.RingGeometry(config.size + 0.1, config.size + 0.2, 32), new THREE.MeshBasicMaterial({ color: 0xddccaa, side: THREE.DoubleSide, transparent: true, opacity: 0.8 }));
                saturnRing.rotation.x = Math.PI / 2.5; saturnRing.position.y = h;
                pieceGroup.add(saturnRing);
                pieceGroup.userData.saturnRing = saturnRing;
            }

            const atmosphere = new THREE.Mesh(new THREE.SphereGeometry(config.size + 0.05, 16, 16), new THREE.MeshBasicMaterial({ color: teamColor, transparent: true, opacity: 0.2, wireframe: true }));
            atmosphere.position.y = h; pieceGroup.add(atmosphere); pieceGroup.userData.atmosphere = atmosphere;

            const baseRing = new THREE.Mesh(new THREE.RingGeometry(0.35, 0.45, 32), new THREE.MeshBasicMaterial({ color: teamColor, side: THREE.DoubleSide, transparent: true, opacity: 0.6 }));
            baseRing.rotation.x = Math.PI / 2; baseRing.position.y = 0.05; pieceGroup.add(baseRing);

            const hitbox = new THREE.Mesh(new THREE.CylinderGeometry(0.5, 0.5, 1.5, 16), new THREE.MeshBasicMaterial({ visible: false }));
            hitbox.position.y = h; pieceGroup.add(hitbox);

            const nameTag = createTextSprite(config.name, stringColors[team]);
            nameTag.position.y = h + config.size + 0.3; pieceGroup.add(nameTag);

            const pos = gridToWorld(x, y);
            pieceGroup.position.set(pos.x, 0, pos.z);
            scene.add(pieceGroup);
            pieces.push(pieceGroup); 
        }

        function spawnAllPieces() {
            for (let i = 0; i < 8; i++) {
                createPiece(backRowLayout[i], i, 0, 'yash');
                createPiece('pawn', i, 1, 'yash');
                createPiece('pawn', i, 6, 'durjay');
                createPiece(backRowLayout[i], i, 7, 'durjay');
            }
        }
        
        spawnAllPieces();

        // --- 8. RESTART GAME LOGIC ---
        function restartGame(fromNetwork) {
            gameOverScreen.style.display = 'none';
            
            // In online mode, broadcast restart to opponent
            if (isOnline && !fromNetwork) {
                sendMsg({ type: 'restart' });
            }
            
            // Clean up black hole if active
            if (blackHole) { scene.remove(blackHole); blackHole = null; }
            if (bhAccretionDisk) { scene.remove(bhAccretionDisk); bhAccretionDisk = null; }
            if (bhLensRing) { scene.remove(bhLensRing); bhLensRing = null; }
            blackHoleRings.forEach(r => scene.remove(r)); blackHoleRings = [];
            blackHoleActive = false; blackHoleTimer = 0;
            stars.scale.set(1, 1, 1);

            // Clean up supernova if active
            if (supernovaGlow) { scene.remove(supernovaGlow); supernovaGlow = null; }
            if (supernovaCore) { scene.remove(supernovaCore); supernovaCore = null; }
            if (supernovaDebris) { scene.remove(supernovaDebris); supernovaDebris = null; }
            supernovaShockwaves.forEach(r => scene.remove(r)); supernovaShockwaves = [];
            supernovaActive = false; supernovaTimer = 0; supernovaPhase = 'swell';
            supernovaKingRef = null; camera.position.x = 0;

            // Clean up old pieces
            pieces.forEach(p => scene.remove(p));
            pieces.length = 0;
            
            currentTurn = 'durjay';
            gameActive = false;
            selectedPiece = null;
            pendingTurnSwitch = false;
            clearHighlights();

            spawnAllPieces();

            // Play intro animation again, then resume game
            playArenaIntro(() => {
                gameActive = true;
                updateTurnUI();
                const instrPanel = document.getElementById('instructions-panel');
                if (isOnline) {
                    if (bossAvatar) bossAvatar.visible = false;
                    if (playerAvatar) playerAvatar.visible = false;
                    if (myTeam === 'durjay') { camera.position.set(0, 10, 12); }
                    else { camera.position.set(0, 10, -12); }
                    instrPanel.innerHTML = `<strong>COMMAND PROTOCOLS:</strong><br>
                        > You are <span style="color:${myTeam==='durjay'?'#0ff':'#f0f'}">${myTeam==='durjay'?'CYAN [DURJAY]':'MAGENTA [YASH]'}</span><br>
                        > Capture the enemy Sun (King).<br>
                        > <span style="color:#ffaa00">■</span> Gold tile = Castling.<br>
                        > Scroll: Zoom | Drag: Orbit.`;
                } else if (isMultiplayer) {
                    if (bossAvatar) bossAvatar.visible = false;
                    if (playerAvatar) playerAvatar.visible = false;
                    camera.position.set(0, 14, 0);
                    instrPanel.innerHTML = `<strong>COMMAND PROTOCOLS:</strong><br>
                        > Eradicate Enemy Sun (King).<br>
                        > <span style="color:#ffaa00">■</span> Gold tile = Castling available.<br>
                        > <span style="color:#0ff">CYAN</span> vs <span style="color:#f0f">MAGENTA</span> — local duel.<br>
                        > Scroll: Zoom | Drag: Orbit.`;
                    setTimeout(() => showPassDeviceModal('durjay'), 300);
                } else {
                    if (bossAvatar) bossAvatar.visible = true;
                    if (playerAvatar) playerAvatar.visible = true;
                    camera.position.z = humanTeam === 'durjay' ? 12 : -12;
                    camera.position.y = 8;
                }
                camera.lookAt(0,0,0);
                if (!isMultiplayer && currentTurn === aiTeam) setTimeout(makeAIMove, 1000);
            });
        }

        // --- 9. CHESS LOGIC ENGINE ---
        function clearHighlights() {
            tiles.forEach(t => { t.material = t.userData.originalMat; t.userData.castling = null; });
            validMoveTiles = [];
        }

        function getPieceAt(x, y) {
            return pieces.find(p => p.userData.gridX === x && p.userData.gridY === y);
        }

        function getPossibleMoves(piece, attackOnly = false) {
            let validCoords = [];
            const type = piece.userData.type;
            const x = piece.userData.gridX;
            const y = piece.userData.gridY;
            const team = piece.userData.team;

            const orthoDirs = [{x:0, y:1}, {x:0, y:-1}, {x:1, y:0}, {x:-1, y:0}];
            const diagDirs = [{x:1, y:1}, {x:1, y:-1}, {x:-1, y:1}, {x:-1, y:-1}];

            function checkSlide(dirs) {
                dirs.forEach(dir => {
                    for (let step = 1; step < 8; step++) {
                        const tx = x + (dir.x * step); const ty = y + (dir.y * step);
                        if (tx < 0 || tx > 7 || ty < 0 || ty > 7) break; 
                        const occ = getPieceAt(tx, ty);
                        if (!occ) { validCoords.push({x: tx, y: ty}); } 
                        else {
                            if (occ.userData.team !== team) validCoords.push({x: tx, y: ty});
                            break; 
                        }
                    }
                });
            }

            if (type === 'rook') checkSlide(orthoDirs);
            if (type === 'bishop') checkSlide(diagDirs);
            if (type === 'queen') checkSlide([...orthoDirs, ...diagDirs]);
            if (type === 'knight') {
                [{x:1,y:2},{x:2,y:1},{x:2,y:-1},{x:1,y:-2},{x:-1,y:-2},{x:-2,y:-1},{x:-2,y:1},{x:-1,y:2}].forEach(m => {
                    const tx = x + m.x; const ty = y + m.y;
                    if(tx>=0 && tx<8 && ty>=0 && ty<8) {
                        const occ = getPieceAt(tx, ty);
                        if (!occ || occ.userData.team !== team) validCoords.push({x: tx, y: ty});
                    }
                });
            }
            if (type === 'king') {
                [...orthoDirs, ...diagDirs].forEach(m => {
                    const tx = x + m.x; const ty = y + m.y;
                    if(tx>=0 && tx<8 && ty>=0 && ty<8) {
                        const occ = getPieceAt(tx, ty);
                        if (!occ || occ.userData.team !== team) validCoords.push({x: tx, y: ty});
                    }
                });
                // --- CASTLING ---
                if (!piece.userData.hasMoved && !attackOnly) {
                    const backRank = team === 'durjay' ? 7 : 0;
                    if (y === backRank) {
                        // Kingside castling (rook at x=7)
                        const kRook = getPieceAt(7, backRank);
                        if (kRook && kRook.userData.type === 'rook' && kRook.userData.team === team && !kRook.userData.hasMoved) {
                            if (!getPieceAt(5, backRank) && !getPieceAt(6, backRank)) {
                                validCoords.push({x: 6, y: backRank, castling: 'kingside'});
                            }
                        }
                        // Queenside castling (rook at x=0)
                        const qRook = getPieceAt(0, backRank);
                        if (qRook && qRook.userData.type === 'rook' && qRook.userData.team === team && !qRook.userData.hasMoved) {
                            if (!getPieceAt(1, backRank) && !getPieceAt(2, backRank) && !getPieceAt(3, backRank)) {
                                validCoords.push({x: 2, y: backRank, castling: 'queenside'});
                            }
                        }
                    }
                }
            }
            if (type === 'pawn') {
                const forward = team === 'durjay' ? -1 : 1; 
                const startRow = team === 'durjay' ? 6 : 1;
                
                if (!attackOnly && y + forward >= 0 && y + forward < 8 && !getPieceAt(x, y + forward)) {
                    validCoords.push({x: x, y: y + forward});
                    if (y === startRow && !getPieceAt(x, y + (forward * 2))) {
                        validCoords.push({x: x, y: y + (forward * 2)});
                    }
                }
                const diagRight = getPieceAt(x + 1, y + forward);
                const diagLeft = getPieceAt(x - 1, y + forward);
                if (attackOnly) { 
                    if(x+1<8 && y+forward<8 && y+forward>=0) validCoords.push({x: x+1, y: y+forward});
                    if(x-1>=0 && y+forward<8 && y+forward>=0) validCoords.push({x: x-1, y: y+forward});
                } else {
                    if (diagRight && diagRight.userData.team !== team) validCoords.push({x: x+1, y: y+forward});
                    if (diagLeft && diagLeft.userData.team !== team) validCoords.push({x: x-1, y: y+forward});
                }
            }
            return validCoords;
        }

        // --- 10. TACTICAL AI BRAIN ---
        function isPathClear(x1, y1, x2, y2) {
            let dx = Math.sign(x2 - x1); let dy = Math.sign(y2 - y1);
            let cx = x1 + dx; let cy = y1 + dy;
            while(cx !== x2 || cy !== y2) {
                if(getPieceAt(cx, cy)) return false;
                cx += dx; cy += dy;
            }
            return true;
        }

        function isAttacked(x, y, attackerTeam) {
            for(let p of pieces) {
                if(p.userData.team !== attackerTeam) continue;
                let px = p.userData.gridX; let py = p.userData.gridY; let type = p.userData.type;
                let dx = Math.abs(px - x); let dy = Math.abs(py - y);

                if(type === 'pawn') {
                    let forward = attackerTeam === 'durjay' ? -1 : 1;
                    if(py + forward === y && dx === 1) return true;
                }
                if(type === 'knight' && ((dx===1&&dy===2)||(dx===2&&dy===1))) return true;
                if(type === 'king' && dx<=1 && dy<=1) return true;
                if(type === 'rook' || type === 'queen') {
                    if((dx===0 || dy===0) && isPathClear(px, py, x, y)) return true;
                }
                if(type === 'bishop' || type === 'queen') {
                    if(dx===dy && dx>0 && isPathClear(px, py, x, y)) return true;
                }
            }
            return false;
        }

        function makeAIMove() {
            if (!gameActive) return;

            let allPossibleMoves = [];
            const pieceValues = { 'pawn': 10, 'knight': 30, 'bishop': 30, 'rook': 50, 'queen': 90, 'king': 1000 };

            pieces.forEach(p => {
                if (p.userData.team === aiTeam) {
                    let moves = getPossibleMoves(p);
                    let pType = p.userData.type;
                    let pVal = pieceValues[pType];
                    
                    let currentlyAttacked = isAttacked(p.userData.gridX, p.userData.gridY, humanTeam);

                    moves.forEach(m => {
                        let score = Math.random() * 2; 
                        
                        let centerDist = Math.abs(3.5 - m.x) + Math.abs(3.5 - m.y);
                        score += (10 - centerDist); 

                        const enemy = getPieceAt(m.x, m.y);
                        if (enemy) score += pieceValues[enemy.userData.type] * 10;

                        let targetAttacked = isAttacked(m.x, m.y, humanTeam);
                        if (targetAttacked) score -= (pVal * 5); 

                        if (currentlyAttacked && !targetAttacked) score += (pVal * 5);

                        allPossibleMoves.push({ piece: p, targetX: m.x, targetY: m.y, score: score });
                    });
                }
            });

            if (allPossibleMoves.length > 0) {
                allPossibleMoves.sort((a, b) => b.score - a.score); 
                let bestMove = allPossibleMoves[0];
                
                const tile = tiles.find(t => t.userData.gridX === bestMove.targetX && t.userData.gridY === bestMove.targetY);
                
                selectedPiece = bestMove.piece;
                executeMove(tile);
            }
        }

        // --- 11. EXECUTION & GAME OVER HANDLING ---

        // BLACK HOLE STATE
        let blackHole = null;
        let blackHoleActive = false;
        let blackHoleTimer = 0;
        let blackHoleRings = [];
        let bhAccretionDisk = null;
        let bhLensRing = null;

        // SUPERNOVA STATE
        let supernovaActive = false;
        let supernovaTimer = 0;
        let supernovaPhase = 'swell'; // swell → flash → explode → debris
        let supernovaCore = null;
        let supernovaShockwaves = [];
        let supernovaDebris = null;
        let supernovaGlow = null;
        let supernovaKingRef = null; // reference to player's king piece

        function spawnSupernova() {
            supernovaActive = true;
            supernovaTimer = 0;
            supernovaPhase = 'swell';

            // Find the player's king
            supernovaKingRef = pieces.find(p => p.userData.type === 'king' && p.userData.team === humanTeam) || null;

            // Outer glow sphere
            const glowGeo = new THREE.SphereGeometry(0.5, 32, 32);
            const glowMat = new THREE.MeshBasicMaterial({ color: 0xff4400, transparent: true, opacity: 0.4 });
            supernovaGlow = new THREE.Mesh(glowGeo, glowMat);
            if (supernovaKingRef) {
                supernovaGlow.position.copy(supernovaKingRef.position);
                supernovaGlow.position.y = 0.5;
            } else {
                supernovaGlow.position.set(0, 0.5, 0);
            }
            scene.add(supernovaGlow);
        }

        function triggerSupernovaExplosion() {
            const origin = supernovaGlow ? supernovaGlow.position.clone() : new THREE.Vector3(0, 0.5, 0);

            // Remove king visually
            if (supernovaKingRef) {
                scene.remove(supernovaKingRef);
                const idx = pieces.indexOf(supernovaKingRef);
                if (idx > -1) pieces.splice(idx, 1);
                supernovaKingRef = null;
            }

            // Replace glow with blazing white core flash
            if (supernovaGlow) {
                supernovaGlow.material.color.setHex(0xffffff);
                supernovaGlow.material.opacity = 1.0;
                supernovaGlow.scale.set(6, 6, 6);
            }

            // Screen flash via a full-scene overlay sphere
            const flashGeo = new THREE.SphereGeometry(50, 16, 16);
            const flashMat = new THREE.MeshBasicMaterial({ color: 0xffffff, transparent: true, opacity: 0.95, side: THREE.BackSide });
            supernovaCore = new THREE.Mesh(flashGeo, flashMat);
            supernovaCore.position.copy(origin);
            scene.add(supernovaCore);

            // Create 4 expanding shockwave rings
            for (let i = 0; i < 4; i++) {
                const ringGeo = new THREE.RingGeometry(0.05, 0.25, 64);
                const ringMat = new THREE.MeshBasicMaterial({
                    color: [0xffffff, 0xff8800, 0xff3300, 0xffcc00][i],
                    side: THREE.DoubleSide, transparent: true, opacity: 0.9
                });
                const ring = new THREE.Mesh(ringGeo, ringMat);
                ring.rotation.x = Math.PI / 2;
                ring.position.copy(origin);
                ring.userData.delay = i * 0.18;
                ring.userData.speed = 1.8 + i * 0.6;
                ring.scale.set(0.01, 0.01, 0.01);
                scene.add(ring);
                supernovaShockwaves.push(ring);
            }

            // Debris: 300 fire particles launched outward
            const debrisCount = 300;
            const debrisGeo = new THREE.BufferGeometry();
            const dPos = new Float32Array(debrisCount * 3);
            const dVel = new Float32Array(debrisCount * 3);
            const dCol = new Float32Array(debrisCount * 3);
            for (let i = 0; i < debrisCount; i++) {
                dPos[i*3]   = origin.x; dPos[i*3+1] = origin.y; dPos[i*3+2] = origin.z;
                const phi = Math.random() * Math.PI * 2;
                const theta = Math.random() * Math.PI;
                const speed = 0.15 + Math.random() * 0.35;
                dVel[i*3]   = Math.sin(theta) * Math.cos(phi) * speed;
                dVel[i*3+1] = Math.cos(theta) * speed * 0.6;
                dVel[i*3+2] = Math.sin(theta) * Math.sin(phi) * speed;
                // Fire palette: red, orange, yellow, white
                const heat = Math.random();
                dCol[i*3]   = 1;
                dCol[i*3+1] = heat * 0.7;
                dCol[i*3+2] = heat > 0.8 ? heat : 0;
            }
            debrisGeo.setAttribute('position', new THREE.BufferAttribute(dPos, 3));
            debrisGeo.setAttribute('color',    new THREE.BufferAttribute(dCol, 3));
            debrisGeo.userData.vel = dVel;
            const debrisMat = new THREE.PointsMaterial({ size: 0.22, vertexColors: true, transparent: true, opacity: 1 });
            supernovaDebris = new THREE.Points(debrisGeo, debrisMat);
            scene.add(supernovaDebris);

            // Blast all remaining pieces outward from explosion origin
            pieces.forEach(piece => {
                const dx = piece.position.x - origin.x;
                const dz = piece.position.z - origin.z;
                const dist = Math.sqrt(dx*dx + dz*dz) || 1;
                const blastForce = 0.4 + Math.random() * 0.5;
                piece.userData.blastVX = (dx / dist) * blastForce;
                piece.userData.blastVZ = (dz / dist) * blastForce;
                piece.userData.blastVY = 0.12 + Math.random() * 0.2;
                piece.userData.blastSpin = (Math.random() - 0.5) * 0.3;
                piece.userData.isBlasted = true;
                piece.userData.isMoving = false;
            });
        }

        function updateSupernova(time) {
            if (!supernovaActive) return;
            supernovaTimer += 0.016;
            const t = supernovaTimer;

            if (supernovaPhase === 'swell') {
                // King swells and pulses orange-white over 1.5s
                if (supernovaGlow) {
                    const swellSize = 1 + t * 1.2 + Math.sin(t * 18) * 0.15;
                    supernovaGlow.scale.set(swellSize, swellSize, swellSize);
                    const r = Math.min(1, 0.6 + t * 0.4);
                    const g = Math.max(0, 0.3 - t * 0.1);
                    supernovaGlow.material.color.setRGB(r, g, 0);
                    supernovaGlow.material.opacity = 0.5 + Math.sin(t * 20) * 0.2;
                    // Also pulse the king itself
                    if (supernovaKingRef) {
                        supernovaKingRef.scale.set(swellSize * 0.6, swellSize * 0.6, swellSize * 0.6);
                    }
                }
                if (t > 1.8) {
                    supernovaPhase = 'explode';
                    supernovaTimer = 0;
                    triggerSupernovaExplosion();
                }

            } else if (supernovaPhase === 'explode') {
                // Fade the white flash
                if (supernovaCore) {
                    supernovaCore.material.opacity = Math.max(0, 0.95 - t * 2.5);
                    if (supernovaCore.material.opacity <= 0) {
                        scene.remove(supernovaCore); supernovaCore = null;
                    }
                }
                if (supernovaGlow) {
                    supernovaGlow.material.opacity = Math.max(0, 1 - t * 1.5);
                    const gs = 6 + t * 3;
                    supernovaGlow.scale.set(gs, gs, gs);
                }

                // Expand shockwave rings
                supernovaShockwaves.forEach((ring, i) => {
                    const lt = Math.max(0, t - ring.userData.delay);
                    const s = lt * ring.userData.speed * 5;
                    ring.scale.set(s, s, s);
                    ring.material.opacity = Math.max(0, 0.9 - lt * 0.5);
                    // Tilt rings for 3D effect
                    ring.rotation.z = time * 0.3 + i;
                });

                // Update fire debris
                if (supernovaDebris) {
                    const pos = supernovaDebris.geometry.attributes.position.array;
                    const vel = supernovaDebris.geometry.userData.vel;
                    for (let i = 0; i < pos.length / 3; i++) {
                        pos[i*3]   += vel[i*3];
                        pos[i*3+1] += vel[i*3+1];
                        pos[i*3+2] += vel[i*3+2];
                        vel[i*3+1] -= 0.003; // gravity
                    }
                    supernovaDebris.geometry.attributes.position.needsUpdate = true;
                    supernovaDebris.material.opacity = Math.max(0, 1 - t * 0.28);
                }

                // Blast pieces flying
                [...pieces].forEach(piece => {
                    if (piece.userData.isBlasted) {
                        piece.position.x  += piece.userData.blastVX;
                        piece.position.y  += piece.userData.blastVY;
                        piece.position.z  += piece.userData.blastVZ;
                        piece.userData.blastVY -= 0.006; // gravity
                        piece.rotation.y  += piece.userData.blastSpin;
                        piece.rotation.x  += piece.userData.blastSpin * 0.5;
                        piece.scale.multiplyScalar(0.97);
                        // Remove when far or tiny
                        if (piece.position.y < -5 || piece.scale.x < 0.02) {
                            scene.remove(piece);
                            pieces.splice(pieces.indexOf(piece), 1);
                        }
                    }
                });

                // Shake camera slightly
                camera.position.x = Math.sin(t * 40) * Math.max(0, 0.3 - t * 0.15);

                // After 4s show defeat screen
                if (t > 4.0 && !supernovaPhase.done) {
                    supernovaPhase = 'done';
                    // Clean up
                    setTimeout(() => {
                        if (supernovaGlow) { scene.remove(supernovaGlow); supernovaGlow = null; }
                        if (supernovaCore) { scene.remove(supernovaCore); supernovaCore = null; }
                        if (supernovaDebris) { scene.remove(supernovaDebris); supernovaDebris = null; }
                        supernovaShockwaves.forEach(r => scene.remove(r)); supernovaShockwaves = [];
                        supernovaActive = false;
                        camera.position.x = 0;
                        showGameOverScreen(false);
                    }, 300);
                }
            }
        }

        function spawnBlackHole() {
            blackHoleActive = true;
            blackHoleTimer = 0;

            // Core: jet-black sphere
            const coreGeo = new THREE.SphereGeometry(0.01, 32, 32);
            const coreMat = new THREE.MeshBasicMaterial({ color: 0x000000 });
            blackHole = new THREE.Mesh(coreGeo, coreMat);
            blackHole.position.set(0, 0.5, 0);
            scene.add(blackHole);

            // Accretion disk (flat glowing torus)
            const diskGeo = new THREE.TorusGeometry(0.5, 0.12, 16, 80);
            const diskMat = new THREE.MeshBasicMaterial({ color: 0xff6600, transparent: true, opacity: 0.9 });
            bhAccretionDisk = new THREE.Mesh(diskGeo, diskMat);
            bhAccretionDisk.rotation.x = Math.PI / 2.2;
            bhAccretionDisk.position.set(0, 0.5, 0);
            bhAccretionDisk.scale.set(0.01, 0.01, 0.01);
            scene.add(bhAccretionDisk);

            // Gravitational lensing ring
            const lensGeo = new THREE.RingGeometry(0.01, 0.02, 64);
            const lensMat = new THREE.MeshBasicMaterial({ color: 0xffffff, side: THREE.DoubleSide, transparent: true, opacity: 0.8 });
            bhLensRing = new THREE.Mesh(lensGeo, lensMat);
            bhLensRing.rotation.x = Math.PI / 2;
            bhLensRing.position.set(0, 0.5, 0);
            scene.add(bhLensRing);

            // Spawn 5 orbiting energy rings
            for (let i = 0; i < 5; i++) {
                const r = new THREE.Mesh(
                    new THREE.RingGeometry(0.02, 0.04, 48),
                    new THREE.MeshBasicMaterial({ color: [0x00ffff, 0xff00ff, 0xffaa00, 0xff4444, 0x44ffaa][i], side: THREE.DoubleSide, transparent: true, opacity: 0.7 })
                );
                r.rotation.x = Math.PI / 2;
                r.rotation.z = (i / 5) * Math.PI;
                r.position.set(0, 0.5, 0);
                r.userData.ringPhase = (i / 5) * Math.PI * 2;
                r.userData.ringTilt = (i / 5) * Math.PI;
                r.scale.set(0.01, 0.01, 0.01);
                scene.add(r);
                blackHoleRings.push(r);
            }
        }

        function updateBlackHole(time) {
            if (!blackHoleActive || !blackHole) return;
            blackHoleTimer += 0.016;
            const t = blackHoleTimer;

            // Growth curve: fast then plateau
            const size = Math.min(t * 0.9, 2.2);
            blackHole.scale.set(size, size, size);

            // Accretion disk grows + spins
            const dSize = Math.min(t * 1.1, 2.8);
            bhAccretionDisk.scale.set(dSize, dSize, dSize);
            bhAccretionDisk.rotation.z = time * 3;
            // Color shift: orange → white-hot at peak
            const heat = Math.min(t / 3, 1);
            bhAccretionDisk.material.color.setRGB(1, 0.4 + heat*0.6, heat*0.3);

            // Lensing ring pulses and grows
            const lSize = Math.min(t * 1.4, 3.5);
            bhLensRing.scale.set(lSize, lSize, lSize);
            bhLensRing.material.opacity = 0.6 + Math.sin(time * 8) * 0.3;

            // Orbiting rings grow and spin
            blackHoleRings.forEach((r, i) => {
                const rSize = Math.min(t * (0.8 + i * 0.15), 3.0 + i * 0.4);
                r.scale.set(rSize, rSize, rSize);
                r.rotation.z = time * (1.5 + i * 0.4) + r.userData.ringPhase;
                r.rotation.y = time * (0.5 + i * 0.2);
                r.material.opacity = Math.max(0, 0.7 - t * 0.05);
            });

            // Suck all pieces toward black hole
            [...pieces].forEach(piece => {
                const px = piece.position.x;
                const pz = piece.position.z;
                const dist = Math.sqrt(px*px + pz*pz);
                const pullStrength = Math.min(0.04 + t * 0.025, 0.3);

                // Spiral inward: pull + angular momentum
                const angle = Math.atan2(pz, px) + 0.08;
                piece.position.x += (Math.cos(angle) * dist * 0.96 - px) * pullStrength + (0 - px) * pullStrength * 0.5;
                piece.position.z += (Math.sin(angle) * dist * 0.96 - pz) * pullStrength + (0 - pz) * pullStrength * 0.5;
                piece.position.y = Math.max(piece.position.y - 0.01 * t, 0);

                // Spin wildly as they're consumed
                piece.rotation.y += 0.15 + t * 0.05;
                piece.rotation.x += 0.03;

                // Scale down when close
                if (dist < size * 0.6) {
                    piece.scale.multiplyScalar(0.92);
                }

                // Remove when swallowed
                if (dist < 0.15 || piece.scale.x < 0.01) {
                    scene.remove(piece);
                    pieces.splice(pieces.indexOf(piece), 1);
                }
            });

            // Also suck stars slightly
            stars.rotation.y = time * (0.02 + t * 0.03);
            if (t > 0.5) stars.scale.set(Math.max(1 - t*0.05, 0.5), Math.max(1 - t*0.05, 0.5), Math.max(1 - t*0.05, 0.5));

            // After 4s everything consumed, flash and show screen
            if (t > 4.5 && !blackHole.userData.done) {
                blackHole.userData.done = true;
                // Collapse flash
                bhAccretionDisk.material.opacity = 0;
                bhLensRing.material.opacity = 0;
                blackHoleRings.forEach(r => r.material.opacity = 0);
                setTimeout(() => {
                    scene.remove(blackHole);
                    scene.remove(bhAccretionDisk);
                    scene.remove(bhLensRing);
                    blackHoleRings.forEach(r => scene.remove(r));
                    blackHole = null; bhAccretionDisk = null; bhLensRing = null; blackHoleRings = [];
                    blackHoleActive = false;
                    showGameOverScreen(true);
                }, 400);
            }
        }

        function showGameOverScreen(playerWon) {
            playGameOverSound(playerWon);
            if (isOnline) {
                const winner = currentTurn;
                const winnerName = winner === myTeam ? 'YOU WIN' : 'OPPONENT WINS';
                const youWon = winner === myTeam;
                gameOverTitle.innerText = youWon ? "VICTORY" : "DEFEAT";
                gameOverTitle.style.color = youWon ? "#0ff" : "#f0f";
                gameOverTitle.style.textShadow = youWon
                    ? "0 0 10px #0ff, 0 0 30px #0ff, 0 0 80px #0ff"
                    : "0 0 10px #f0f, 0 0 30px #f0f, 0 0 80px #f0f";
                gameOverSubtitle.innerText = youWon ? `${winnerName} — ENEMY SUN CONSUMED` : `${winnerName} — YOUR SUN WAS DESTROYED`;
                gameOverScreen.classList.remove(youWon ? 'defeat-flash' : 'victory-flash');
                gameOverScreen.classList.add(youWon ? 'victory-flash' : 'defeat-flash');
            } else if (isMultiplayer) {
                // In multiplayer, playerWon means the team whose king was captured LOST
                // currentTurn at game-over is the team that made the kill move
                const winner = currentTurn; // the team that just moved (captured the king)
                const winnerName = winner === 'durjay' ? 'DURJAY [CYAN]' : 'YASH [MAGENTA]';
                gameOverTitle.innerText = "VICTORY";
                gameOverTitle.style.color = winner === 'durjay' ? "#0ff" : "#f0f";
                gameOverTitle.style.textShadow = winner === 'durjay'
                    ? "0 0 10px #0ff, 0 0 30px #0ff, 0 0 80px #0ff"
                    : "0 0 10px #f0f, 0 0 30px #f0f, 0 0 80px #f0f";
                gameOverSubtitle.innerText = `${winnerName} DESTROYS THE ENEMY SUN`;
                gameOverScreen.classList.remove('defeat-flash');
                gameOverScreen.classList.add('victory-flash');
            } else if (playerWon) {
                gameOverTitle.innerText = "SINGULARITY";
                gameOverTitle.style.color = "#0ff";
                gameOverTitle.style.textShadow = "0 0 10px #0ff, 0 0 30px #0ff, 0 0 80px #0ff";
                gameOverSubtitle.innerText = "ENEMY SYSTEM CONSUMED BY BLACK HOLE";
                gameOverScreen.classList.remove('defeat-flash');
                gameOverScreen.classList.add('victory-flash');
            } else {
                gameOverTitle.innerText = "SUPERNOVA";
                gameOverTitle.style.color = "#ff4400";
                gameOverTitle.style.textShadow = "0 0 10px #ff4400, 0 0 30px #ff8800, 0 0 80px #ffcc00";
                gameOverSubtitle.innerText = "YOUR SUN WENT SUPERNOVA — SYSTEM OBLITERATED";
                gameOverScreen.classList.remove('victory-flash');
                gameOverScreen.classList.add('defeat-flash');
            }
            gameOverScreen.style.display = 'flex';
        }

        function showGameOver(playerWon) {
            playEliminationSound();
            if (isOnline || isMultiplayer) {
                // Always spawn black hole
                setTimeout(() => spawnBlackHole(), 400);
            } else if (playerWon) {
                // Spawn black hole — delay game-over screen until it finishes
                setTimeout(() => spawnBlackHole(), 400);
            } else {
                // Spawn supernova on player's king — delay game-over screen until done
                setTimeout(() => spawnSupernova(), 400);
            }
        }

        function executeMove(tile) {
            const targetX = tile.userData.gridX; const targetY = tile.userData.gridY;
            const castlingType = tile.userData.castling || null;
            const enemy = getPieceAt(targetX, targetY);

            // Broadcast move to opponent if online and it's our turn
            if (isOnline && gameActive && currentTurn === myTeam) {
                sendMsg({
                    type: 'move',
                    fromX: selectedPiece.userData.gridX,
                    fromY: selectedPiece.userData.gridY,
                    toX: targetX,
                    toY: targetY,
                    castling: castlingType
                });
            }
            
            if (enemy) {
                scene.remove(enemy);
                pieces.splice(pieces.indexOf(enemy), 1);
                playEliminationSound(); 
                
                if (enemy.userData.type === 'king') {
                    gameActive = false;
                    // In multiplayer: currentTurn = the attacker (winner). playerWon = true always triggers black hole
                    // In AI mode: playerWon = human captured AI king
                    const playerWon = isMultiplayer ? true : (currentTurn === humanTeam);
                    setTimeout(() => showGameOver(playerWon), 600);
                }
            }

            selectedPiece.userData.hasMoved = true;
            selectedPiece.userData.gridX = targetX;
            selectedPiece.userData.gridY = targetY;
            const targetPos = gridToWorld(targetX, targetY);
            selectedPiece.userData.targetX = targetPos.x;
            selectedPiece.userData.targetZ = targetPos.z;
            selectedPiece.userData.isMoving = true;

            // --- CASTLING: also move the rook ---
            if (castlingType && selectedPiece.userData.type === 'king') {
                const backRank = targetY;
                let rookFromX, rookToX;
                if (castlingType === 'kingside') { rookFromX = 7; rookToX = 5; }
                else { rookFromX = 0; rookToX = 3; }
                const rook = getPieceAt(rookFromX, backRank);
                if (rook) {
                    rook.userData.hasMoved = true;
                    rook.userData.gridX = rookToX;
                    const rookPos = gridToWorld(rookToX, backRank);
                    rook.userData.targetX = rookPos.x;
                    rook.userData.targetZ = rookPos.z;
                    rook.userData.isMoving = true;
                }
            }

            clearHighlights();
            selectedPiece = null;
            pendingTurnSwitch = true;
        }

        function updateTurnUI() {
            if (isOnline) {
                if (currentTurn === myTeam) {
                    turnIndicator.innerText = `YOUR TURN — MAKE YOUR MOVE`;
                    turnIndicator.className = myTeam === 'durjay' ? 'p1-turn' : 'p2-turn';
                } else {
                    turnIndicator.innerText = `OPPONENT'S TURN — WAITING...`;
                    turnIndicator.className = currentTurn === 'durjay' ? 'p1-turn' : 'p2-turn';
                }
            } else if (isMultiplayer) {
                if (currentTurn === 'durjay') {
                    turnIndicator.innerText = `DURJAY [CYAN] — YOUR MOVE`;
                    turnIndicator.className = `p1-turn`;
                } else {
                    turnIndicator.innerText = `YASH [MAGENTA] — YOUR MOVE`;
                    turnIndicator.className = `p2-turn`;
                }
            } else {
                if (currentTurn === humanTeam) {
                    turnIndicator.innerText = `INPUT REQUIRED`;
                    turnIndicator.className = `${humanTeam}-turn`;
                } else {
                    turnIndicator.innerText = `PROCESSING...`;
                    turnIndicator.className = `ai-thinking`;
                }
            }
        }

        function switchTurn() {
            if (!gameActive) return;
            currentTurn = currentTurn === 'durjay' ? 'yash' : 'durjay';
            updateTurnUI();
            
            if (isOnline) {
                // Nothing extra — moves come via network
            } else if (isMultiplayer) {
                // Show pass-device modal so next player can take over privately
                setTimeout(() => showPassDeviceModal(currentTurn), 400);
            } else if (currentTurn === aiTeam) {
                setTimeout(makeAIMove, 1000);
            }
        }

        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        window.addEventListener('mousedown', (event) => {
            if (!gameActive || pieces.some(p => p.userData.isMoving)) return;
            // Block input: in AI mode during AI turn, in online mode during opponent's turn
            if (!isMultiplayer && !isOnline && currentTurn === aiTeam) return;
            if (isOnline && currentTurn !== myTeam) return;

            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
            raycaster.setFromCamera(mouse, camera);

            const pieceHits = raycaster.intersectObjects(pieces, true); 
            if (pieceHits.length > 0) {
                let clickedPiece = pieceHits[0].object;
                while (!clickedPiece.userData.team && clickedPiece.parent) {
                    clickedPiece = clickedPiece.parent;
                }

                if (clickedPiece.userData && clickedPiece.userData.team === (isOnline ? myTeam : isMultiplayer ? currentTurn : humanTeam)) { 
                    if (selectedPiece) selectedPiece.position.y = 0; 
                    selectedPiece = clickedPiece;
                    
                    const moves = getPossibleMoves(selectedPiece);
                    moves.forEach(m => {
                        const tile = tiles.find(t => t.userData.gridX === m.x && t.userData.gridY === m.y);
                        if (m.castling) {
                            tile.material = castleMat;
                            tile.userData.castling = m.castling;
                        } else {
                            tile.material = highlightMat;
                            tile.userData.castling = null;
                        }
                        validMoveTiles.push(tile);
                    });

                    selectedPiece.position.y = 0.5; 
                    return;
                }
            }

            if (selectedPiece) {
                const tileHits = raycaster.intersectObjects(validMoveTiles);
                if (tileHits.length > 0) {
                    executeMove(tileHits[0].object);
                } else {
                    selectedPiece.position.y = 0; 
                    selectedPiece = null;
                    clearHighlights();
                }
            }
        });

        // --- 12. ANIMATION LOOP ---
        const clock = new THREE.Clock();

        function animate() {
            requestAnimationFrame(animate);
            controls.update();
            const time = clock.getElapsedTime();
            const delta = clock.getDelta ? clock.getDelta() : 0.016;

            stars.rotation.y = time * 0.02;

            if (bossAvatar) bossAvatar.position.y = 10 + Math.sin(time * 1.5) * 0.5;
            if (playerAvatar) playerAvatar.position.y = 10 + Math.sin(time * 1.5 + Math.PI) * 0.5;

            updateWinParticles(0.016);
            updateBlackHole(time);
            updateSupernova(time);

            pieces.forEach(piece => {
                
                if (piece.userData.planetMesh) {
                    piece.userData.planetMesh.rotation.y = time * 0.5;
                }
                
                if (piece.userData.atmosphere) {
                    piece.userData.atmosphere.rotation.y = -time * 0.3;
                    piece.userData.atmosphere.rotation.x = time * 0.2;
                }

                if (piece.userData.saturnRing) {
                    piece.userData.saturnRing.rotation.z = time * 0.8;
                }

                if (piece.userData.isMoving) {
                    piece.position.x += (piece.userData.targetX - piece.position.x) * 0.3;
                    piece.position.z += (piece.userData.targetZ - piece.position.z) * 0.3;

                    const dx = Math.abs(piece.position.x - piece.userData.targetX);
                    const dz = Math.abs(piece.position.z - piece.userData.targetZ);
                    
                    if (dx < 0.01 && dz < 0.01) {
                        piece.position.x = piece.userData.targetX;
                        piece.position.z = piece.userData.targetZ;
                        piece.position.y = 0; 
                        piece.userData.isMoving = false;
                    }
                }
            });

            // Switch turn only when ALL pieces have finished moving
            if (gameActive && pieces.length > 0 && !pieces.some(p => p.userData.isMoving)) {
                if (pendingTurnSwitch) { pendingTurnSwitch = false; switchTurn(); }
            }

            renderer.render(scene, camera);
        }

        animate();

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
