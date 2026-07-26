<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Autismoware Web Radar</title>
    <!-- Include PeerJS for zero-IP P2P room streaming -->
    <script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700;800&display=swap');
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
        }

        :root {
            --bg-color: #08090d;
            --panel-bg: rgba(15, 18, 26, 0.95);
            --accent-color: #ccff00;
            --accent-glow: rgba(204, 255, 0, 0.35);
            --border-color: rgba(204, 255, 0, 0.2);
            --text-main: #ffffff;
            --text-dim: #8b92a5;
        }

        html, body {
            width: 100%;
            height: 100%;
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: var(--bg-color);
            color: var(--text-main);
            font-family: 'Outfit', sans-serif;
            display: flex;
            flex-direction: row;
        }

        /* Fixed Left Glassmorphism Sidebar */
        #sidebar {
            width: 320px;
            flex: 0 0 320px;
            height: 100vh;
            background: var(--panel-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-right: 1px solid var(--border-color);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            z-index: 100;
            overflow-y: auto;
            box-shadow: 10px 0 35px rgba(0, 0, 0, 0.7);
        }

        #sidebar::-webkit-scrollbar {
            width: 4px;
        }
        #sidebar::-webkit-scrollbar-thumb {
            background: var(--accent-color);
            border-radius: 4px;
        }

        .brand-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding-bottom: 8px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.08);
        }

        h1 {
            font-size: 20px;
            font-weight: 800;
            color: var(--accent-color);
            text-shadow: 0 0 16px var(--accent-glow);
            letter-spacing: 1.5px;
            text-transform: uppercase;
        }

        .version-tag {
            font-size: 10px;
            font-weight: 700;
            background: rgba(204, 255, 0, 0.12);
            color: var(--accent-color);
            padding: 3px 8px;
            border-radius: 6px;
            border: 1px solid var(--border-color);
        }

        .subtitle {
            font-size: 11px;
            color: var(--text-dim);
            text-transform: uppercase;
            letter-spacing: 2px;
            margin-top: 2px;
        }

        /* Lobby & Server Config Box */
        .lobby-box {
            background: rgba(255, 255, 255, 0.025);
            border: 1px solid var(--border-color);
            border-radius: 14px;
            padding: 14px;
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .lobby-row {
            display: flex;
            align-items: center;
            justify-content: space-between;
        }

        .lobby-label {
            font-size: 11px;
            color: var(--text-dim);
            text-transform: uppercase;
            letter-spacing: 1px;
            font-weight: 600;
        }

        .lobby-value {
            font-size: 22px;
            font-weight: 800;
            color: #ffffff;
            letter-spacing: 2px;
            font-family: monospace;
        }

        .copy-btn {
            background: rgba(204, 255, 0, 0.15);
            color: var(--accent-color);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 6px 12px;
            font-size: 11px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .copy-btn:hover {
            background: var(--accent-color);
            color: #0c0d12;
            box-shadow: 0 0 12px var(--accent-glow);
        }

        .status-indicator {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 12px;
            color: var(--text-dim);
            margin-top: 2px;
        }

        .status-dot {
            width: 9px;
            height: 9px;
            border-radius: 50%;
            background: #ff3333;
            box-shadow: 0 0 10px #ff3333;
            transition: all 0.3s ease;
        }

        .status-dot.connected {
            background: var(--accent-color);
            box-shadow: 0 0 12px var(--accent-color);
        }

        /* Feature Card Section */
        .controls-group {
            background: rgba(255, 255, 255, 0.015);
            border: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 14px;
            padding: 14px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .group-title {
            font-size: 11px;
            font-weight: 700;
            color: var(--accent-color);
            text-transform: uppercase;
            letter-spacing: 1.5px;
        }

        /* Custom Switch */
        .checkbox-container {
            display: flex;
            align-items: center;
            justify-content: space-between;
            cursor: pointer;
        }

        .checkbox-label {
            font-size: 12px;
            color: #d1d5db;
            font-weight: 500;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 38px;
            height: 20px;
        }

        .switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background-color: rgba(255, 255, 255, 0.1);
            transition: .3s ease;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 12px;
            width: 12px;
            left: 3px;
            bottom: 3px;
            background-color: #ffffff;
            transition: .3s ease;
            border-radius: 50%;
        }

        input:checked + .slider {
            background-color: var(--accent-color);
            border-color: var(--accent-color);
        }

        input:checked + .slider:before {
            transform: translateX(18px);
            background-color: #0c0d12;
        }

        /* Sliders */
        .range-container {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .range-header {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: #d1d5db;
        }

        .range-value {
            color: var(--accent-color);
            font-weight: 700;
        }

        .range-input {
            width: 100%;
            accent-color: var(--accent-color);
            background: rgba(255, 255, 255, 0.1);
            border-radius: 6px;
            height: 6px;
            outline: none;
            -webkit-appearance: none;
            cursor: pointer;
        }

        /* Right Canvas Viewport */
        #viewport {
            flex: 1;
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            background: radial-gradient(circle at center, #151824 0%, #08090d 100%);
            overflow: hidden;
        }

        canvas {
            background: #0b0d14;
            border: 1.5px solid var(--border-color);
            border-radius: 32px;
            box-shadow: 0 30px 70px rgba(0, 0, 0, 0.85), 0 0 35px rgba(204, 255, 0, 0.08);
        }
    </style>
</head>
<body>
    <!-- LEFT SIDEBAR -->
    <div id="sidebar">
        <div>
            <div class="brand-header">
                <h1>Autismoware</h1>
                <span class="version-tag">v1.3.0</span>
            </div>
            <div class="subtitle">Web Radar System</div>
        </div>

        <div class="lobby-box">
            <div class="lobby-row">
                <span class="lobby-label">Lobby Code</span>
                <button class="copy-btn" id="copy-btn">COPY LINK</button>
            </div>
            <span id="lobby-id" class="lobby-value">LOADING</span>

            <div class="status-indicator">
                <div id="status-dot" class="status-dot"></div>
                <span id="status-text">Connecting...</span>
            </div>
        </div>

        <div class="controls-group">
            <span class="group-title">Radar Visuals</span>
            
            <label class="checkbox-container">
                <span class="checkbox-label">Terrain Map Overview</span>
                <span class="switch">
                    <input type="checkbox" id="show-terrain" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Compass (N,S,E,W)</span>
                <span class="switch">
                    <input type="checkbox" id="show-compass" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Distance Rings</span>
                <span class="switch">
                    <input type="checkbox" id="show-rings" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">FOV View Cone</span>
                <span class="switch">
                    <input type="checkbox" id="show-fov" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Local Player</span>
                <span class="switch">
                    <input type="checkbox" id="show-local" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Friends / Teammates</span>
                <span class="switch">
                    <input type="checkbox" id="show-friends" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Enemies</span>
                <span class="switch">
                    <input type="checkbox" id="show-enemies" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Player Names & Tags</span>
                <span class="switch">
                    <input type="checkbox" id="show-names" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Box Shape Container</span>
                <span class="switch">
                    <input type="checkbox" id="shape-box">
                    <span class="slider"></span>
                </span>
            </label>
        </div>

        <div class="controls-group">
            <span class="group-title">Configuration</span>
            
            <div class="range-container">
                <div class="range-header">
                    <span>Radar Zoom / Scale</span>
                    <span id="scale-val" class="range-value">1.0x</span>
                </div>
                <input type="range" id="scale-slider" class="range-input" min="0.1" max="5.0" step="0.1" value="1.0">
            </div>

            <div class="range-container">
                <div class="range-header">
                    <span>Terrain Opacity</span>
                    <span id="opacity-val" class="range-value">100%</span>
                </div>
                <input type="range" id="opacity-slider" class="range-input" min="0.1" max="1.0" step="0.05" value="1.0">
            </div>
        </div>
    </div>

    <!-- RIGHT RADAR VIEWPORT -->
    <div id="viewport">
        <canvas id="radarCanvas"></canvas>
    </div>

    <script>
        // Parse Lobby ID e.g. ?lobby=AF7SRW
        const urlParams = new URLSearchParams(window.location.search);
        let lobbyId = urlParams.get('lobby') || 'AF7SRW';
        lobbyId = lobbyId.toUpperCase();
        document.getElementById('lobby-id').innerText = lobbyId;

        // Copy Share Link Button
        document.getElementById('copy-btn').addEventListener('click', () => {
            const shareUrl = `${window.location.origin}${window.location.pathname}?lobby=${lobbyId}`;
            navigator.clipboard.writeText(shareUrl).then(() => {
                const btn = document.getElementById('copy-btn');
                btn.innerText = 'COPIED!';
                setTimeout(() => btn.innerText = 'COPY LINK', 2000);
            });
        });

        // UI Controls elements
        const showTerrainToggle = document.getElementById('show-terrain');
        const showCompassToggle = document.getElementById('show-compass');
        const showRingsToggle = document.getElementById('show-rings');
        const showFovToggle = document.getElementById('show-fov');
        const showLocalToggle = document.getElementById('show-local');
        const showFriendsToggle = document.getElementById('show-friends');
        const showEnemiesToggle = document.getElementById('show-enemies');
        const showNamesToggle = document.getElementById('show-names');
        const shapeBoxToggle = document.getElementById('shape-box');

        const scaleSlider = document.getElementById('scale-slider');
        const scaleValLabel = document.getElementById('scale-val');
        const opacitySlider = document.getElementById('opacity-slider');
        const opacityValLabel = document.getElementById('opacity-val');

        let radarScale = parseFloat(scaleSlider.value);
        scaleSlider.addEventListener('input', (e) => {
            radarScale = parseFloat(e.target.value);
            scaleValLabel.innerText = radarScale.toFixed(1) + 'x';
        });

        let terrainOpacity = parseFloat(opacitySlider.value);
        opacitySlider.addEventListener('input', (e) => {
            terrainOpacity = parseFloat(e.target.value);
            opacityValLabel.innerText = Math.round(terrainOpacity * 100) + '%';
        });

        // Setup Responsive Canvas
        const canvas = document.getElementById('radarCanvas');
        const ctx = canvas.getContext('2d');
        const center = { x: 0, y: 0 };
        let radius = 0;

        function resizeCanvas() {
            const viewport = document.getElementById('viewport');
            const size = Math.min(viewport.clientWidth, viewport.clientHeight) - 30;
            canvas.width = size;
            canvas.height = size;
            center.x = canvas.width / 2;
            center.y = canvas.height / 2;
            radius = (canvas.width / 2) - 35;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Shared Player state & Terrain Map
        let localPlayer = { x: 0, y: 0, z: 0, yaw: 0, name: 'Local' };
        let remotePlayers = {}; // name -> { x, y, z, yaw, isFriend, lastUpdate }
        let mapBlocks = []; // Array of [dx, dz, colorHex]

        // Status Indicator Helper
        const statusDot = document.getElementById('status-dot');
        const statusText = document.getElementById('status-text');

        function setStatus(status, text) {
            statusText.innerText = text;
            if (status === 'connected') {
                statusDot.className = 'status-dot connected';
            } else {
                statusDot.className = 'status-dot';
            }
        }

        // Dual Connection Strategy:
        // 1. PeerJS Cloud P2P Room (Zero IP configuration, works on Phone/4G/5G/GitHub Pages)
        // 2. Direct Local WS Server (ws://localhost:8080/ws for local PC)
        let peer = null;
        let peerConn = null;
        let wsLocal = null;

        function initConnections() {
            setStatus('disconnected', 'Connecting P2P Room...');

            // A. Try Local PC WebSocket Server
            try {
                wsLocal = new WebSocket('ws://localhost:8080/ws');
                wsLocal.onopen = () => setStatus('connected', 'Connected (Local PC)');
                wsLocal.onmessage = (evt) => handleIncomingPayload(evt.data);
            } catch(e){}

            // B. Connect to PeerJS Serverless Room using Lobby Code e.g. "AUTISMO_AF7SRW"
            try {
                const peerIdReceiver = `AUTISMO_RCV_${lobbyId}_${Math.floor(Math.random()*1000)}`;
                const peerIdHost = `AUTISMO_HOST_${lobbyId}`;

                peer = new Peer(peerIdReceiver, {
                    debug: 1
                });

                peer.on('open', (id) => {
                    // Connect to game host room
                    connectToHost(peerIdHost);
                });

                peer.on('error', (err) => {
                    setTimeout(() => connectToHost(peerIdHost), 4000);
                });
            } catch(e) {}
        }

        function connectToHost(hostPeerId) {
            if (!peer) return;
            peerConn = peer.connect(hostPeerId, { reliable: false });

            peerConn.on('open', () => {
                setStatus('connected', 'Connected (Cloud Room)');
            });

            peerConn.on('data', (data) => {
                handleIncomingPayload(data);
                setStatus('connected', 'Connected (Live)');
            });

            peerConn.on('close', () => {
                setStatus('disconnected', 'Reconnecting...');
                setTimeout(() => connectToHost(hostPeerId), 3000);
            });
        }

        function handleIncomingPayload(raw) {
            try {
                let msg = (typeof raw === 'string') ? JSON.parse(raw) : raw;
                if (msg.local) localPlayer = msg.local;
                if (msg.map) mapBlocks = msg.map;
                if (msg.players) {
                    remotePlayers = {};
                    const now = Date.now();
                    for (let p of msg.players) {
                        remotePlayers[p.name] = {
                            x: p.x, y: p.y, z: p.z, yaw: p.yaw,
                            isFriend: !!p.isFriend,
                            lastUpdate: now
                        };
                    }
                }
                setStatus('connected', 'Connected (Live)');
            } catch(e) {}
        }

        initConnections();

        // Render Frame Loop (Matching In-Game Radar Visuals)
        function drawFrame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // 1. Draw Radar Container Background & Border
            ctx.strokeStyle = 'rgba(204, 255, 0, 0.25)';
            ctx.lineWidth = 2.5;
            ctx.fillStyle = '#0b0d14';

            if (shapeBoxToggle.checked) {
                ctx.beginPath();
                ctx.roundRect(center.x - radius, center.y - radius, radius * 2, radius * 2, 32);
                ctx.fill();
                ctx.stroke();
            } else {
                ctx.beginPath();
                ctx.arc(center.x, center.y, radius, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();
            }

            // Player Angle Trigonometry
            const angleRad = (localPlayer.yaw + 180) * Math.PI / 180;
            const c = Math.cos(angleRad);
            const s = Math.sin(angleRad);

            // 2. Draw Terrain Map Overview (In-Game Radar Matching Colors & Slope Shading)
            if (showTerrainToggle.checked && mapBlocks && mapBlocks.length > 0) {
                ctx.save();
                ctx.globalAlpha = terrainOpacity;
                ctx.beginPath();
                if (shapeBoxToggle.checked) {
                    ctx.roundRect(center.x - radius, center.y - radius, radius * 2, radius * 2, 32);
                } else {
                    ctx.arc(center.x, center.y, radius, 0, Math.PI * 2);
                }
                ctx.clip();

                const blockSize = Math.max(1.8, radarScale * 2.2);
                for (let i = 0; i < mapBlocks.length; i++) {
                    const b = mapBlocks[i]; // [dx, dz, colorHex]
                    const dx = b[0];
                    const dz = b[1];
                    const color = b[2];

                    let px = dx * c + dz * s;
                    let py = -dx * s + dz * c;

                    px *= radarScale;
                    py *= radarScale;

                    if (Math.abs(px) < radius && Math.abs(py) < radius) {
                        ctx.fillStyle = color;
                        ctx.fillRect(center.x + px - blockSize * 0.5, center.y + py - blockSize * 0.5, blockSize + 0.4, blockSize + 0.4);
                    }
                }
                ctx.restore();
            }

            // 3. Draw FOV View Cone
            if (showFovToggle.checked) {
                ctx.save();
                ctx.fillStyle = 'rgba(204, 255, 0, 0.08)';
                ctx.strokeStyle = 'rgba(204, 255, 0, 0.25)';
                ctx.lineWidth = 1;

                ctx.beginPath();
                ctx.moveTo(center.x, center.y);
                const fovAngle = 70 * Math.PI / 180; // 70° FOV
                const fovRadius = radius * 0.95;
                ctx.arc(center.x, center.y, fovRadius, -Math.PI / 2 - fovAngle / 2, -Math.PI / 2 + fovAngle / 2);
                ctx.closePath();
                ctx.fill();
                ctx.stroke();
                ctx.restore();
            }

            // 4. Draw Distance Rings & Metrics
            if (showRingsToggle.checked) {
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.07)';
                ctx.lineWidth = 1.2;
                ctx.setLineDash([4, 4]);

                const ringDistances = [25, 50, 100, 150, 200, 300, 400, 500];
                for (let dist of ringDistances) {
                    const r = dist * radarScale;
                    if (r < radius) {
                        ctx.beginPath();
                        if (shapeBoxToggle.checked) {
                            ctx.roundRect(center.x - r, center.y - r, r * 2, r * 2, 10);
                        } else {
                            ctx.arc(center.x, center.y, r, 0, Math.PI * 2);
                        }
                        ctx.stroke();

                        // Label metric
                        ctx.fillStyle = 'rgba(255, 255, 255, 0.4)';
                        ctx.font = '600 11px Outfit';
                        ctx.fillText(dist + 'm', center.x + r - 28, center.y - 4);
                    }
                }
                ctx.setLineDash([]); // Reset line dash
            }

            // 5. Draw Compass Directions ('N', 'S', 'E', 'W') outside radar border
            if (showCompassToggle.checked) {
                const dirs = [
                    { label: 'N', dx: 0, dz: -1 },
                    { label: 'S', dx: 0, dz: 1 },
                    { label: 'E', dx: 1, dz: 0 },
                    { label: 'W', dx: -1, dz: 0 }
                ];

                ctx.font = '800 14px Outfit';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';

                for (let d of dirs) {
                    let px = d.dx * c + d.dz * s;
                    let py = -d.dx * s + d.dz * c;

                    let labelDist = radius + 20;
                    if (shapeBoxToggle.checked) {
                        const scaleFactor = 1.0 / Math.max(Math.abs(px), Math.abs(py));
                        px *= scaleFactor;
                        py *= scaleFactor;
                        labelDist = radius + 22;
                    }

                    const tx = center.x + px * labelDist;
                    const ty = center.y + py * labelDist;

                    ctx.fillStyle = (d.label === 'N') ? '#ccff00' : 'rgba(255, 255, 255, 0.85)';
                    ctx.fillText(d.label, tx, ty);
                }
            }

            // 6. Clean up old inactive remote players (timeout after 8s)
            const now = Date.now();
            for (let name in remotePlayers) {
                if (now - remotePlayers[name].lastUpdate > 8000) {
                    delete remotePlayers[name];
                }
            }

            // 7. Render Remote Players (Enemies & Friends)
            for (let name in remotePlayers) {
                const player = remotePlayers[name];
                const isFriend = player.isFriend;

                if (isFriend && !showFriendsToggle.checked) continue;
                if (!isFriend && !showEnemiesToggle.checked) continue;

                const dx = player.x - localPlayer.x;
                const dz = player.z - localPlayer.z;

                let px = dx * c + dz * s;
                let py = -dx * s + dz * c;

                px *= radarScale;
                py *= radarScale;

                let outOfBounds = false;
                if (shapeBoxToggle.checked) {
                    const limit = radius - 14;
                    if (Math.abs(px) > limit || Math.abs(py) > limit) {
                        outOfBounds = true;
                        px = Math.max(-limit, Math.min(limit, px));
                        py = Math.max(-limit, Math.min(limit, py));
                    }
                } else {
                    const dist = Math.sqrt(px * px + py * py);
                    if (dist > radius - 14) {
                        outOfBounds = true;
                        const angle = Math.atan2(py, px);
                        px = (radius - 14) * Math.cos(angle);
                        py = (radius - 14) * Math.sin(angle);
                    }
                }

                const drawX = center.x + px;
                const drawY = center.y + py;

                // Color palette matching in-game radar
                const mainColor = isFriend ? '#00bfff' : '#ff3344';
                const auraColor = isFriend ? 'rgba(0, 191, 255, 0.35)' : 'rgba(255, 51, 68, 0.35)';

                // Draw Outer Glow Ring
                ctx.fillStyle = auraColor;
                ctx.beginPath();
                ctx.arc(drawX, drawY, outOfBounds ? 6 : 9, 0, Math.PI * 2);
                ctx.fill();

                // Draw Core Player Dot
                ctx.fillStyle = mainColor;
                ctx.beginPath();
                ctx.arc(drawX, drawY, outOfBounds ? 4 : 5.5, 0, Math.PI * 2);
                ctx.fill();

                // Direction Pointer Line
                if (!outOfBounds) {
                    const relativeYawRad = ((player.yaw - localPlayer.yaw) + 180) * Math.PI / 180;
                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 1.8;
                    ctx.beginPath();
                    ctx.moveTo(drawX, drawY);
                    ctx.lineTo(
                        drawX - Math.sin(relativeYawRad) * 14,
                        drawY + Math.cos(relativeYawRad) * 14
                    );
                    ctx.stroke();
                }

                // Render Name Tag & Distance
                if (showNamesToggle.checked && !outOfBounds) {
                    const distMeters = Math.round(Math.sqrt(dx * dx + dz * dz));
                    const tagText = `${name} (${distMeters}m)`;

                    ctx.font = '600 11px Outfit';
                    ctx.textAlign = 'center';
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.7)';
                    const tw = ctx.measureText(tagText).width;
                    ctx.fillRect(drawX - tw / 2 - 4, drawY - 24, tw + 8, 14);

                    ctx.fillStyle = '#ffffff';
                    ctx.fillText(tagText, drawX, drawY - 14);
                }
            }

            // 8. Render Local Player Arrow Pointer (Center)
            if (showLocalToggle.checked) {
                ctx.save();
                ctx.translate(center.x, center.y);

                // Local Player Direction Triangle Pointer
                ctx.fillStyle = '#ccff00';
                ctx.shadowColor = '#ccff00';
                ctx.shadowBlur = 14;

                ctx.beginPath();
                ctx.moveTo(0, -12); // Tip
                ctx.lineTo(-8, 9);  // Left wing
                ctx.lineTo(0, 4);   // Inner notch
                ctx.lineTo(8, 9);   // Right wing
                ctx.closePath();
                ctx.fill();

                ctx.restore();
            }

            requestAnimationFrame(drawFrame);
        }

        // Start 60 FPS Render Loop
        drawFrame();
    </script>
</body>
</html>
