<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Autismoware Web Radar</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700;800&display=swap');
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
        }

        :root {
            --bg-color: #0c0d12;
            --panel-bg: rgba(18, 21, 31, 0.85);
            --accent-color: #ccff00;
            --accent-glow: rgba(204, 255, 0, 0.35);
            --border-color: rgba(204, 255, 0, 0.15);
            --text-main: #ffffff;
            --text-dim: #8b92a5;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            font-family: 'Outfit', sans-serif;
            overflow: hidden;
            display: flex;
            height: 100vh;
        }

        /* Glassmorphism Sidebar */
        #sidebar {
            width: 340px;
            background: var(--panel-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border-right: 1px solid var(--border-color);
            padding: 25px;
            display: flex;
            flex-direction: column;
            gap: 20px;
            z-index: 10;
            box-shadow: 15px 0 35px rgba(0, 0, 0, 0.6);
            overflow-y: auto;
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
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.06);
        }

        h1 {
            font-size: 22px;
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

        /* Lobby Info Box */
        .lobby-box {
            background: rgba(255, 255, 255, 0.025);
            border: 1px solid var(--border-color);
            border-radius: 14px;
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 10px;
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
            padding: 16px;
            display: flex;
            flex-direction: column;
            gap: 12px;
        }

        .group-title {
            font-size: 12px;
            font-weight: 700;
            color: var(--accent-color);
            text-transform: uppercase;
            letter-spacing: 1.5px;
            margin-bottom: 2px;
        }

        /* Custom Switch */
        .checkbox-container {
            display: flex;
            align-items: center;
            justify-content: space-between;
            cursor: pointer;
        }

        .checkbox-label {
            font-size: 13px;
            color: #d1d5db;
            font-weight: 500;
        }

        .switch {
            position: relative;
            display: inline-block;
            width: 42px;
            height: 22px;
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
            border-radius: 22px;
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 14px;
            width: 14px;
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
            transform: translateX(20px);
            background-color: #0c0d12;
        }

        /* Sliders */
        .range-container {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .range-header {
            display: flex;
            justify-content: space-between;
            font-size: 13px;
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

        /* Canvas Viewport */
        #viewport {
            flex-grow: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
            background: radial-gradient(circle at center, #151824 0%, #08090d 100%);
        }

        canvas {
            background: #0b0d14;
            border: 1px solid var(--border-color);
            border-radius: 28px;
            box-shadow: 0 25px 60px rgba(0, 0, 0, 0.8), 0 0 30px rgba(204, 255, 0, 0.05);
        }
    </style>
</head>
<body>
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
                <span class="lobby-label">Active Lobby</span>
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
                <span class="checkbox-label">Terrain Map</span>
                <span class="switch">
                    <input type="checkbox" id="show-terrain" checked>
                    <span class="slider"></span>
                </span>
            </label>

            <label class="checkbox-container">
                <span class="checkbox-label">Compass Overlay (N,S,E,W)</span>
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
                <span class="checkbox-label">Player Names</span>
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
                    <span id="scale-val" class="range-value">1.2x</span>
                </div>
                <input type="range" id="scale-slider" class="range-input" min="0.2" max="5.0" step="0.1" value="1.2">
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

    <div id="viewport">
        <canvas id="radarCanvas" width="900" height="900"></canvas>
    </div>

    <script>
        // Parse Lobby ID from URL parameter e.g. ?lobby=4GSCOD
        const urlParams = new URLSearchParams(window.location.search);
        let lobbyId = urlParams.get('lobby') || 'AUTISMO';
        lobbyId = lobbyId.toUpperCase();
        document.getElementById('lobby-id').innerText = lobbyId;

        // Copy Share Link Button
        document.getElementById('copy-btn').addEventListener('click', () => {
            navigator.clipboard.writeText(window.location.href).then(() => {
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

        // Setup Canvas
        const canvas = document.getElementById('radarCanvas');
        const ctx = canvas.getContext('2d');
        const center = { x: canvas.width / 2, y: canvas.height / 2 };
        const radius = (canvas.width / 2) - 40;

        // Shared Player state & Terrain Map
        let localPlayer = { x: 0, y: 0, z: 0, yaw: 0, name: 'Local' };
        let remotePlayers = {}; // name -> { x, y, z, yaw, isFriend, lastUpdate }
        let mapBlocks = []; // Array of [dx, dz, colorHex]

        // Dual Connection Setup:
        // 1. Direct Local Embedded Server: ws://localhost:8080/ws (or IP)
        // 2. Public Internet Broker: wss://broker.emqx.io:8084/mqtt (Fallback for GitHub Pages / Internet sharing)
        let wsHost = window.location.host;
        if (!wsHost || window.location.protocol === 'file:' || wsHost.includes('github.io')) {
            wsHost = 'localhost:8080';
        }
        let wsProto = (window.location.protocol === 'https:') ? 'wss:' : 'ws:';
        if (wsHost === 'localhost:8080' || wsHost.startsWith('127.0.0.1')) wsProto = 'ws:';
        
        const wsServer = `${wsProto}//${wsHost}/ws`;
        let ws = null;
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

        function connectWebSocket() {
            try {
                ws = new WebSocket(wsServer);

                ws.onopen = () => {
                    setStatus('connected', 'Connected (Live)');
                };

                ws.onclose = () => {
                    setStatus('disconnected', 'Reconnecting...');
                    setTimeout(connectWebSocket, 3000);
                };

                ws.onerror = () => {
                    setStatus('disconnected', 'Offline / Retrying...');
                };

                ws.onmessage = (event) => {
                    if (typeof event.data === 'string' && event.data.trim().startsWith('{')) {
                        try {
                            let msg = JSON.parse(event.data);
                            if (msg.local) {
                                localPlayer = msg.local;
                            }
                            if (msg.map) {
                                mapBlocks = msg.map;
                            }
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
                };
            } catch(e) {
                setTimeout(connectWebSocket, 4000);
            }
        }

        connectWebSocket();

        // Render Frame Loop (Matching In-Game Radar Visuals)
        function drawFrame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // 1. Draw Radar Container Background & Border
            ctx.strokeStyle = 'rgba(204, 255, 0, 0.25)';
            ctx.lineWidth = 2.5;
            ctx.fillStyle = '#0b0d14';

            if (shapeBoxToggle.checked) {
                ctx.beginPath();
                ctx.roundRect(center.x - radius, center.y - radius, radius * 2, radius * 2, 28);
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
                    ctx.roundRect(center.x - radius, center.y - radius, radius * 2, radius * 2, 28);
                } else {
                    ctx.arc(center.x, center.y, radius, 0, Math.PI * 2);
                }
                ctx.clip();

                const blockSize = Math.max(1.8, radarScale * 2.0);
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
                const fovRadius = radius * 0.9;
                ctx.arc(center.x, center.y, fovRadius, -Math.PI / 2 - fovAngle / 2, -Math.PI / 2 + fovAngle / 2);
                ctx.closePath();
                ctx.fill();
                ctx.stroke();
                ctx.restore();
            }

            // 4. Draw Distance Rings & Metrics
            if (showRingsToggle.checked) {
                ctx.strokeStyle = 'rgba(255, 255, 255, 0.06)';
                ctx.lineWidth = 1.2;
                ctx.setLineDash([4, 4]);

                const ringDistances = [25, 50, 100, 150, 200, 300];
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
                        ctx.fillStyle = 'rgba(255, 255, 255, 0.35)';
                        ctx.font = '600 10px Outfit';
                        ctx.fillText(dist + 'm', center.x + r - 26, center.y - 4);
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

                ctx.font = '800 13px Outfit';
                ctx.textAlign = 'center';
                ctx.textBaseline = 'middle';

                for (let d of dirs) {
                    let px = d.dx * c + d.dz * s;
                    let py = -d.dx * s + d.dz * c;

                    let labelDist = radius + 18;
                    if (shapeBoxToggle.checked) {
                        const scaleFactor = 1.0 / Math.max(Math.abs(px), Math.abs(py));
                        px *= scaleFactor;
                        py *= scaleFactor;
                        labelDist = radius + 20;
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
                    const limit = radius - 12;
                    if (Math.abs(px) > limit || Math.abs(py) > limit) {
                        outOfBounds = true;
                        px = Math.max(-limit, Math.min(limit, px));
                        py = Math.max(-limit, Math.min(limit, py));
                    }
                } else {
                    const dist = Math.sqrt(px * px + py * py);
                    if (dist > radius - 12) {
                        outOfBounds = true;
                        const angle = Math.atan2(py, px);
                        px = (radius - 12) * Math.cos(angle);
                        py = (radius - 12) * Math.sin(angle);
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
                    ctx.fillStyle = 'rgba(0, 0, 0, 0.65)';
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
                ctx.shadowBlur = 12;

                ctx.beginPath();
                ctx.moveTo(0, -10); // Tip
                ctx.lineTo(-7, 8);  // Left wing
                ctx.lineTo(0, 4);   // Inner notch
                ctx.lineTo(7, 8);   // Right wing
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
