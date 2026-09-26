
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MARINHA DO BRASIL // COMMANDO SUBMARINO SN-BR ÁLVARO ALBERTO</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; user-select: none; }
        body, html { width: 100%; height: 100%; overflow: hidden; background-color: #010a15; font-family: 'Segoe UI', 'Courier New', monospace; color: #00f2fe; }
        
        #canvas-container { width: 100%; height: 100%; position: absolute; top: 0; left: 0; z-index: 1; }
        canvas { display: block; width: 100%; height: 100%; }

        /* HUD PAINEL DE CONTROLE SUBMARINO */
        .hud-panel {
            position: absolute; top: 15px; left: 15px; z-index: 10;
            background: rgba(1, 18, 38, 0.92); border: 1px solid #00f2fe;
            border-radius: 10px; padding: 15px; width: 360px; max-height: calc(100vh - 30px);
            overflow-y: auto; box-shadow: 0 0 30px rgba(0, 242, 254, 0.25);
            backdrop-filter: blur(10px);
        }

        .hud-header {
            font-size: 13px; font-weight: 900; letter-spacing: 1.5px;
            text-transform: uppercase; color: #00f2fe; margin-bottom: 12px;
            border-bottom: 2px solid #00f2fe; padding-bottom: 6px;
            display: flex; justify-content: space-between; align-items: center;
        }

        .hud-header .badge-navy {
            background: #00ffbc; color: #010a15; font-size: 10px; padding: 2px 7px; border-radius: 3px; font-weight: 900;
        }

        .section-title {
            font-size: 11px; font-weight: bold; color: #64748b; margin: 10px 0 6px 0; text-transform: uppercase; letter-spacing: 1px;
        }

        /* SISTEMAS DA TRÍADE MILITAR */
        .triad-box {
            background: rgba(0, 255, 188, 0.05); border: 1px solid rgba(0, 255, 188, 0.3);
            border-radius: 6px; padding: 8px; margin-bottom: 12px;
        }

        .triad-item {
            display: flex; justify-content: space-between; align-items: center;
            font-size: 10px; font-weight: bold; margin: 4px 0; color: #e2e8f0;
        }

        .triad-status {
            padding: 2px 6px; border-radius: 3px; font-size: 9px; font-weight: bold;
        }
        .status-mhd { background: rgba(0, 242, 254, 0.2); color: #00f2fe; border: 1px solid #00f2fe; }
        .status-sams { background: rgba(192, 132, 252, 0.2); color: #c084fc; border: 1px solid #c084fc; }
        .status-sros { background: rgba(255, 0, 128, 0.2); color: #ff0080; border: 1px solid #ff0080; }

        .btn-group { display: flex; gap: 8px; margin-bottom: 8px; }

        .btn-mode {
            width: 100%; background: rgba(0, 242, 254, 0.06); border: 1px solid rgba(0, 242, 254, 0.3);
            color: #00f2fe; padding: 9px 10px; margin-bottom: 6px; border-radius: 4px;
            font-family: inherit; font-size: 10.5px; font-weight: bold; cursor: pointer;
            display: flex; justify-content: space-between; align-items: center;
            transition: all 0.2s ease; text-transform: uppercase; letter-spacing: 0.5px;
        }

        .btn-mode:hover {
            background: rgba(0, 242, 254, 0.25); border-color: #00f2fe;
            box-shadow: 0 0 14px rgba(0, 242, 254, 0.4); transform: translateX(2px);
        }

        .btn-mode.active {
            background: #00f2fe; color: #010a15; border-color: #00f2fe;
            box-shadow: 0 0 18px rgba(0, 242, 254, 0.6);
        }

        .btn-view {
            flex: 1; background: rgba(0, 255, 188, 0.1); border: 1px solid #00ffbc;
            color: #00ffbc; padding: 8px; border-radius: 4px; font-family: inherit;
            font-size: 10px; font-weight: bold; cursor: pointer; text-transform: uppercase;
            text-align: center; transition: all 0.2s ease;
        }

        .btn-view.active {
            background: #00ffbc; color: #010a15; box-shadow: 0 0 12px #00ffbc;
        }

        /* TELEMETRIA SUBMARINA */
        .telemetry-panel {
            position: absolute; top: 15px; right: 15px; z-index: 10;
            background: rgba(1, 18, 38, 0.92); border: 1px solid #00ffbc;
            border-radius: 10px; padding: 15px; width: 320px;
            box-shadow: 0 0 30px rgba(0, 255, 188, 0.2);
            backdrop-filter: blur(10px);
        }

        .telemetry-header {
            font-size: 12px; font-weight: bold; letter-spacing: 1px;
            color: #00ffbc; border-bottom: 1px solid #00ffbc; padding-bottom: 6px; margin-bottom: 10px;
            text-transform: uppercase;
        }

        .metric-row { display: flex; justify-content: space-between; margin: 6px 0; font-size: 10.5px; color: #cbd5e1; }
        .metric-val { font-family: monospace; font-weight: bold; color: #00ffbc; }

        .progress-bg { width: 100%; height: 5px; background: rgba(255, 255, 255, 0.1); border-radius: 3px; overflow: hidden; margin-top: 2px; }
        .progress-fill { height: 100%; background: #00ffbc; width: 100%; transition: width 0.3s ease; box-shadow: 0 0 8px #00ffbc; }

        /* BANNER TÁTICO INFERIOR */
        .tactical-banner {
            position: absolute; bottom: 15px; left: 50%; transform: translateX(-50%); z-index: 10;
            background: rgba(1, 18, 38, 0.94); border: 1px solid #c084fc; color: #c084fc;
            padding: 10px 22px; border-radius: 20px; font-size: 11px; font-weight: bold;
            letter-spacing: 1px; box-shadow: 0 0 25px rgba(192, 132, 252, 0.25); text-align: center;
            max-width: 700px; width: 90%;
        }

        /* SCROLLBAR CUSTOMIZADA */
        .hud-panel::-webkit-scrollbar { width: 4px; }
        .hud-panel::-webkit-scrollbar-track { background: rgba(0,0,0,0.3); }
        .hud-panel::-webkit-scrollbar-thumb { background: #00f2fe; border-radius: 2px; }
    </style>
</head>
<body>

    <div id="canvas-container">
        <canvas id="subCanvas"></canvas>
    </div>

    <!-- PAINEL PRINCIPAL DE CONTROLE -->
    <div class="hud-panel">
        <div class="hud-header">
            <span>SUBMARINO SN-BR ÁLVARO ALBERTO</span>
            <span class="badge-navy">MARINHA DO BRASIL</span>
        </div>

        <!-- PAINEL DA TRÍADE DE SISTEMAS INTEGRADOS -->
        <div class="section-title">TRÍADE DE SISTEMAS AVANÇADOS</div>
        <div class="triad-box">
            <div class="triad-item">
                <span>1. PROPULSÃO SILENCIOSA (MHD):</span>
                <span class="triad-status status-mhd" id="st-mhd">ATIVO // 100%</span>
            </div>
            <div class="triad-item">
                <span>2. ABSORÇÃO ANTI-SONAR (SAMS):</span>
                <span class="triad-status status-sams" id="st-sams">OPERACIONAL</span>
            </div>
            <div class="triad-item">
                <span>3. REGENERAÇÃO ENERGÉTICA (SROS):</span>
                <span class="triad-status status-sros" id="st-sros">CARREGANDO</span>
            </div>
        </div>

        <div class="section-title">PERSPECTIVA DA CÂMERA SUBMARINA</div>
        <div class="btn-group">
            <button class="btn-view active" id="view-side" onclick="setView('side')">🌊 Visão Profunda (Abissal)</button>
            <button class="btn-view" id="view-top" onclick="setView('top')">📐 Visão Superior (Sonar)</button>
        </div>

        <div class="section-title">SIMULAÇÕES AMBIENTAIS & AMEAÇAS</div>
        
        <button class="btn-mode active" id="btn-storm" onclick="setSimulation('storm')">
            <span>1. TEMPESTADE MARÍTIMA & RAIOS (SROS)</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#00f2fe;"></div>
        </button>

        <button class="btn-mode" id="btn-volcano" onclick="setSimulation('volcano')">
            <span>2. VULCÃO SUBMARINO & MAGMA FLUIDO</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#ff4500;"></div>
        </button>

        <button class="btn-mode" id="btn-tsunami" onclick="setSimulation('tsunami')">
            <span>3. TSUNAMI & ABSORÇÃO ONDULATÓRIA</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#00ffbc;"></div>
        </button>

        <button class="btn-mode" id="btn-tornado" onclick="setSimulation('tornado')">
            <span>4. TROMBA D'ÁGUA & VÓRTICE OCEÂNICO</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#c084fc;"></div>
        </button>

        <button class="btn-mode" id="btn-meteor" onclick="setSimulation('meteor')">
            <span>5. IMPACTO DE METEORO EM ALTO MAR</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#ffaa00;"></div>
        </button>

        <button class="btn-mode" id="btn-nuke" onclick="setSimulation('nuke')">
            <span>6. DETONAÇÃO ATÔMICA SUBMARINA</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#ff0055;"></div>
        </button>

        <button class="btn-mode" id="btn-hypersonic" onclick="setSimulation('hypersonic')">
            <span>7. ANTI-TORPEDO HIPERSÔNICO & LASER</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#38bdf8;"></div>
        </button>

        <button class="btn-mode" id="btn-antivirus" onclick="setSimulation('antivirus')">
            <span>8. VARREDURA BIO-DIGITAL SUB-AQUÁTICA</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#39ff14;"></div>
        </button>

        <button class="btn-mode" style="border-color:#ff0080; color:#ff0080; margin-top:10px;" onclick="triggerPulse()">
            <span>🌀 DISPARAR PULSO REVOLUCIONÁRIO SROS</span>
            <div style="width:8px; height:8px; border-radius:50%; background:#ff0080;"></div>
        </button>
    </div>

    <!-- PAINEL DE TELEMETRIA TÁTICA SUBMARINA -->
    <div class="telemetry-panel">
        <div class="telemetry-header">TELEMETRIA INTEGRADA DE SUBMERSÃO</div>

        <div class="metric-row">
            <span>PROFUNDIDADE OPERACIONAL:</span>
            <span class="metric-val" id="val-depth">420 METROS</span>
        </div>
        <div class="progress-bg"><div class="progress-fill" id="bar-depth" style="width: 70%; background:#00f2fe;"></div></div>

        <div class="metric-row" style="margin-top:8px;">
            <span>PRESSÃO HIDROSTÁTICA:</span>
            <span class="metric-val" id="val-press">42.5 BAR</span>
        </div>

        <div class="metric-row" style="margin-top:8px;">
            <span>ÍNDICE DE FURTIVIDADE (MHD+SAMS):</span>
            <span class="metric-val" id="val-stealth">99.8% (INVISÍVEL)</span>
        </div>
        <div class="progress-bg"><div class="progress-fill" id="bar-stealth" style="width: 99.8%; background:#00ffbc;"></div></div>

        <div class="metric-row" style="margin-top:8px;">
            <span>RESERVA ENERGÉTICA (REATOR SN-BR):</span>
            <span class="metric-val" id="val-power">100.0%</span>
        </div>
        <div class="progress-bg"><div class="progress-fill" id="bar-power" style="width: 100%; background:#c084fc;"></div></div>

        <div class="metric-row" style="margin-top:8px;">
            <span>DISTÂNCIA DA COSTA URBANA:</span>
            <span class="metric-val" id="val-dist">18.4 MILHAS NÁUTICAS</span>
        </div>

        <div class="metric-row" style="margin-top:8px;">
            <span>TRIPULAÇÃO / ECO-SISTEMA:</span>
            <span class="metric-val" style="color:#00ffbc;">TOTALMENTE ISOLADO</span>
        </div>
    </div>

    <!-- BANNER INFORMATIVO TÁTICO -->
    <div class="tactical-banner" id="info-banner">
        SIMULAÇÃO 1: TEMPESTADE EM ALTO MAR - CAPTURA DE ENERGIA DE RAIOS PELO SISTEMA REGENERATIVO SROS.
    </div>

    <script>
        const canvas = document.getElementById('subCanvas');
        const ctx = canvas.getContext('2d');

        // SINTETIZADOR DE ÁUDIO SONAR E AMEAÇAS
        const AudioCtx = window.AudioContext || window.webkitAudioContext;
        let audioCtx = null;

        function playAudio(freq = 300, duration = 0.15, type = 'sine', vol = 0.04) {
            try {
                if (!audioCtx) audioCtx = new AudioCtx();
                if (audioCtx.state === 'suspended') audioCtx.resume();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = type;
                osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
                gain.gain.setValueAtTime(vol, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + duration);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + duration);
            } catch(e) {}
        }

        // SONAR PING AUTOMÁTICO
        setInterval(() => {
            playAudio(880, 0.25, 'sine', 0.02);
        }, 4000);

        function resizeCanvas() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // ESTADOS DA SIMULAÇÃO
        let currentSim = 'storm';
        let currentView = 'side'; // 'side' ou 'top'
        let frameCount = 0;

        // SUBMARINO BRASILEIRO (SN-BR ÁLVARO ALBERTO / RIACHUELO CLASS)
        const submarine = {
            x: 0, y: 0,
            length: 220, height: 42,
            depth: 420,
            stealth: 99.8,
            power: 100
        };

        // PARTICULAS DA FAUNA/FLORA MARINHA E EFETOS
        const oceanParticles = [];
        const kelpForest = [];
        const meteors = [];
        const torpedoes = [];
        const lightningBolts = [];

        // GERAÇÃO DE VEGETAÇÃO MARINHA SUB-AQUÁTICA (KELP/ALGAS BIOLUMINESCENTES)
        function generateKelp() {
            kelpForest.length = 0;
            for (let i = 0; i < 40; i++) {
                kelpForest.push({
                    x: Math.random() * canvas.width,
                    height: 100 + Math.random() * 200,
                    nodes: 6 + Math.floor(Math.random() * 6),
                    color: Math.random() > 0.5 ? '#00ffbc' : '#39ff14'
                });
            }
        }
        generateKelp();

        function setView(mode) {
            currentView = mode;
            document.getElementById('view-top').classList.toggle('active', mode === 'top');
            document.getElementById('view-side').classList.toggle('active', mode === 'side');
            playAudio(500, 0.1, 'sine');
        }

        function setSimulation(mode) {
            currentSim = mode;
            oceanParticles.length = 0;
            meteors.length = 0;
            torpedoes.length = 0;
            lightningBolts.length = 0;

            document.querySelectorAll('.btn-mode').forEach(b => b.classList.remove('active'));
            document.getElementById(`btn-${mode}`).classList.add('active');

            const info = {
                storm: "SIMULAÇÃO 1: TEMPESTADE MARÍTIMA & RAIOS ABSORVIDOS PELO SISTEMA DE REGENERAÇÃO SROS.",
                volcano: "SIMULAÇÃO 2: VULCÃO SUBMARINO & LAVA ABSORVIDA COM RESFRIAMENTO MAGNETOHIDRODINÂMICO (MHD).",
                tsunami: "SIMULAÇÃO 3: ONDA TSUNÂMICA DISTANTE & DISSIPAÇÃO HIDRODINÂMICA PELO CAMPO SAMS.",
                tornado: "SIMULAÇÃO 4: TROMBA D'ÁGUA EM ALTO MAR COM ESTABILIZAÇÃO ATMOSFÉRICA-OCEÂNICA.",
                meteor: "SIMULAÇÃO 5: IMPACTO DE ASTEROIDE NO OCEANO COM ESCUDO DE BOLHA HIDROSTÁTICA SAMS.",
                nuke: "SIMULAÇÃO 6: DETONAÇÃO ATÔMICA SUB-AQUÁTICA & REGENERAÇÃO ENERGÉTICA VIA REATOR SROS.",
                hypersonic: "SIMULAÇÃO 7: INTERCEPTAÇÃO DE TORPEDO HIPERSÔNICO COM SISTEMA LASER SUPERCAVITACIONAL.",
                antivirus: "SIMULAÇÃO 8: VARREDURA BIO-DIGITAL & NANOTECNOLOGIA DE PURIFICAÇÃO MARINHA."
            };

            document.getElementById('info-banner').innerText = info[mode];
            playAudio(750, 0.12, 'triangle');
        }

        function triggerPulse() {
            playAudio(150, 0.5, 'sawtooth', 0.25);
            for (let i = 0; i < 100; i++) {
                oceanParticles.push({
                    x: canvas.width / 2, y: canvas.height / 2,
                    vx: (Math.random() - 0.5) * 22, vy: (Math.random() - 0.5) * 22,
                    life: 1, color: '#ff0080', size: 3 + Math.random() * 6
                });
            }
        }

        // DESENHAR O SUBMARINO BRASILEIRO (SN-BR ÁLVARO ALBERTO)
        function drawBrazilianSubmarine(x, y, view) {
            ctx.save();
            ctx.translate(x, y);

            if (view === 'side') {
                // VISÃO PROFUNDA DE PERFIL (SIDE VIEW)
                
                // 1. PROPULSÃO SILENCIOSA MHD (FLUIDO JETO IONIZADO NA POPA)
                ctx.shadowColor = '#00f2fe';
                ctx.shadowBlur = 20;
                ctx.fillStyle = 'rgba(0, 242, 254, 0.6)';
                ctx.beginPath();
                ctx.moveTo(-110, 0);
                ctx.lineTo(-170 + Math.sin(frameCount * 0.3) * 15, -15);
                ctx.lineTo(-170 + Math.cos(frameCount * 0.3) * 15, 15);
                ctx.closePath();
                ctx.fill();

                // 2. CASCO HIDRODINÂMICO BLINDADO (NEGRO ABISSAL COM ACENTOS VERDE/AMARELO MILITAR)
                ctx.shadowBlur = 0;
                ctx.fillStyle = '#031326';
                ctx.strokeStyle = '#00ffbc';
                ctx.lineWidth = 2;

                ctx.beginPath();
                // Nariz / Domo do Sonar
                ctx.arc(80, 0, 20, -Math.PI / 2, Math.PI / 2);
                // Corpo Inferior
                ctx.lineTo(-100, 20);
                // Popa
                ctx.lineTo(-110, 0);
                // Corpo Superior
                ctx.lineTo(-100, -20);
                ctx.closePath();
                ctx.fill(); ctx.stroke();

                // Torre de Comando / Conduíte (Vela)
                ctx.fillStyle = '#07203b';
                ctx.beginPath();
                ctx.roundRect(-10, -42, 45, 24, [6, 6, 0, 0]);
                ctx.fill(); ctx.stroke();

                // Lista Tricolor Sutil (Marinha do Brasil)
                ctx.fillStyle = '#009c3b'; ctx.fillRect(15, -40, 4, 20);
                ctx.fillStyle = '#ffdf00'; ctx.fillRect(19, -40, 4, 20);
                ctx.fillStyle = '#002776'; ctx.fillRect(23, -40, 4, 20);

                // Leme em 'X' Traseiro (Popa)
                ctx.strokeStyle = '#00f2fe';
                ctx.lineWidth = 3;
                ctx.beginPath();
                ctx.moveTo(-100, -20); ctx.lineTo(-120, -32);
                ctx.moveTo(-100, 20); ctx.lineTo(-120, 32);
                ctx.stroke();

                // Janelas / Sensores Optrônicos SROS
                ctx.fillStyle = '#ff0080';
                ctx.beginPath(); ctx.arc(15, -35, 3, 0, Math.PI * 2); ctx.fill();

                // CAMPO DE ABSORÇÃO ANTI-SONAR SAMS (AURA PURPURA/AZUL ENERGÉTICA)
                ctx.strokeStyle = currentSim === 'antivirus' ? '#39ff14' : (currentSim === 'nuke' ? '#ff0055' : '#c084fc');
                ctx.lineWidth = 1.5;
                ctx.shadowColor = ctx.strokeStyle;
                ctx.shadowBlur = 18;
                ctx.beginPath();
                ctx.ellipse(0, 0, 130, 45, 0, 0, Math.PI * 2);
                ctx.stroke();
                ctx.shadowBlur = 0;

            } else {
                // VISÃO SUPERIOR (TOP-DOWN SONAR VIEW)
                
                // Casco Vista de Cima
                ctx.fillStyle = '#031326';
                ctx.strokeStyle = '#00f2fe';
                ctx.lineWidth = 2.5;

                ctx.beginPath();
                ctx.ellipse(0, 0, 110, 24, 0, 0, Math.PI * 2);
                ctx.fill(); ctx.stroke();

                // Estabilizadores Laterais
                ctx.fillStyle = '#00ffbc';
                ctx.fillRect(10, -32, 12, 64);

                // Propulsão MHD Dupla Traseira
                ctx.fillStyle = 'rgba(0, 242, 254, 0.8)';
                ctx.shadowColor = '#00f2fe'; ctx.shadowBlur = 15;
                ctx.fillRect(-130, -12, 20, 6);
                ctx.fillRect(-130, 6, 20, 6);
                ctx.shadowBlur = 0;
            }

            ctx.restore();
        }

        // RENDERIZAÇÃO COMPLETA DAS SIMULAÇÕES E AMBIENTE
        function renderScene() {
            frameCount++;
            const cx = canvas.width / 2;
            const cy = canvas.height / 2 + 50;

            // 1. DESENHO DO CÉU, LINHA DO HORIZONTE E CIDADE DISTANTE (FUNDO REAL)
            // Céu Superior
            const skyGrad = ctx.createLinearGradient(0, 0, 0, canvas.height * 0.35);
            skyGrad.addColorStop(0, '#01040a');
            skyGrad.addColorStop(1, '#061727');
            ctx.fillStyle = skyGrad;
            ctx.fillRect(0, 0, canvas.width, canvas.height * 0.35);

            // Cidade e Prédios Iluminados ao Fundo (Costa Distante)
            ctx.fillStyle = '#030d1a';
            for (let x = 50; x < canvas.width; x += 35) {
                const h = 40 + Math.sin(x) * 35;
                ctx.fillRect(x, canvas.height * 0.35 - h, 25, h);
                // Luzes de Janelas de Prédios
                ctx.fillStyle = 'rgba(0, 242, 254, 0.4)';
                if (x % 2 === 0) ctx.fillRect(x + 5, canvas.height * 0.35 - h + 10, 4, 4);
                ctx.fillStyle = '#030d1a';
            }

            // Mar Profundo e Gradiente Abissal
            const seaGrad = ctx.createLinearGradient(0, canvas.height * 0.35, 0, canvas.height);
            seaGrad.addColorStop(0, 'rgba(0, 119, 182, 0.8)');
            seaGrad.addColorStop(0.3, '#02182e');
            seaGrad.addColorStop(1, '#010811');
            ctx.fillStyle = seaGrad;
            ctx.fillRect(0, canvas.height * 0.35, canvas.width, canvas.height * 0.65);

            // Ondas na Superfície do Mar
            ctx.strokeStyle = 'rgba(0, 255, 188, 0.3)';
            ctx.lineWidth = 2;
            ctx.beginPath();
            for (let x = 0; x < canvas.width; x += 20) {
                const y = canvas.height * 0.35 + Math.sin(x * 0.02 + frameCount * 0.05) * 6;
                if (x === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
            }
            ctx.stroke();

            // 2. VEGETAÇÃO MARINHA NO FUNDO DO OCEANO (FLORA BIOLUMINESCENTE)
            kelpForest.forEach(k => {
                ctx.strokeStyle = k.color;
                ctx.lineWidth = 3;
                ctx.beginPath();
                let currX = k.x;
                let currY = canvas.height;
                ctx.moveTo(currX, currY);
                for (let i = 0; i < k.nodes; i++) {
                    currX += Math.sin(frameCount * 0.03 + i) * 8;
                    currY -= k.height / k.nodes;
                    ctx.lineTo(currX, currY);
                }
                ctx.stroke();
            });

            // 3. EXECUÇÃO ESPECÍFICA DAS SIMULAÇÕES
            if (currentSim === 'storm') {
                // Raios caindo da atmosfera, atingindo o mar e sendo absorvidos pelo SROS
                if (Math.random() > 0.8) {
                    playAudio(120 + Math.random() * 180, 0.18, 'sawtooth');
                    lightningBolts.push({
                        x1: Math.random() * canvas.width, y1: 0,
                        x2: cx + (Math.random() - 0.5) * 100, y2: cy,
                        life: 1
                    });
                }

                lightningBolts.forEach((bolt, idx) => {
                    ctx.strokeStyle = '#00f2fe';
                    ctx.lineWidth = 3;
                    ctx.shadowColor = '#00f2fe'; ctx.shadowBlur = 20;
                    ctx.beginPath();
                    ctx.moveTo(bolt.x1, bolt.y1);
                    ctx.lineTo((bolt.x1 + bolt.x2) / 2 + (Math.random() - 0.5) * 40, canvas.height * 0.35);
                    ctx.lineTo(bolt.x2, bolt.y2);
                    ctx.stroke();
                    ctx.shadowBlur = 0;
                    bolt.life -= 0.25;
                    if (bolt.life <= 0) lightningBolts.splice(idx, 1);
                });

            } else if (currentSim === 'volcano') {
                // Erupção Vulcânica Submarina (Magma e Calor Absorvido por MHD/SROS)
                if (Math.random() > 0.25) {
                    oceanParticles.push({
                        x: cx + (Math.random() - 0.5) * 400, y: canvas.height,
                        vx: (Math.random() - 0.5) * 3, vy: -Math.random() * 6 - 2,
                        size: Math.random() * 7 + 3, color: '#ff4500', life: 1
                    });
                }

            } else if (currentSim === 'tsunami') {
                // Mega Tsunami e Absorção de Onda Hidrodinâmica
                const radius = (frameCount * 5) % (canvas.width * 0.9);
                ctx.strokeStyle = 'rgba(0, 255, 188, 0.4)';
                ctx.lineWidth = 6;
                ctx.beginPath();
                ctx.arc(cx, cy, radius, 0, Math.PI * 2);
                ctx.stroke();

            } else if (currentSim === 'tornado') {
                // Tromba D'Água Conectando Nuvem ao Oceano
                ctx.fillStyle = 'rgba(192, 132, 252, 0.15)';
                ctx.beginPath();
                ctx.moveTo(cx - 80, 0); ctx.lineTo(cx + 80, 0);
                ctx.lineTo(cx + 20, canvas.height * 0.35); ctx.lineTo(cx - 20, canvas.height * 0.35);
                ctx.closePath(); ctx.fill();

            } else if (currentSim === 'meteor') {
                // Impacto de Asteroide
                if (Math.random() > 0.9) {
                    meteors.push({ x: Math.random() * canvas.width, y: 0, targetX: cx, targetY: cy, progress: 0 });
                }

                meteors.forEach((m, idx) => {
                    m.progress += 0.03;
                    const mx = m.x + (m.targetX - m.x) * m.progress;
                    const my = m.y + (m.targetY - m.y) * m.progress;

                    ctx.fillStyle = '#ffaa00';
                    ctx.shadowColor = '#ffaa00'; ctx.shadowBlur = 18;
                    ctx.beginPath(); ctx.arc(mx, my, 9, 0, Math.PI * 2); ctx.fill();
                    ctx.shadowBlur = 0;

                    if (m.progress >= 1) {
                        playAudio(90, 0.35, 'sawtooth');
                        meteors.splice(idx, 1);
                    }
                });

            } else if (currentSim === 'nuke') {
                // Detonação Atômica Sub-aquática e EMP
                const rad = (frameCount * 8) % 700;
                ctx.fillStyle = 'rgba(255, 0, 85, 0.12)';
                ctx.beginPath(); ctx.arc(cx - 200, cy, rad, 0, Math.PI * 2); ctx.fill();

            } else if (currentSim === 'hypersonic') {
                // Interceptação de Torpedo Hipersônico com Laser Supercavitacional
                if (Math.random() > 0.92) {
                    torpedoes.push({ x: canvas.width, y: cy + (Math.random() - 0.5) * 60, vx: -14 });
                }

                torpedoes.forEach((t, idx) => {
                    t.x += t.vx;
                    ctx.fillStyle = '#38bdf8';
                    ctx.fillRect(t.x, t.y, 20, 4);

                    // Disparo Laser
                    if (t.x < cx + 280) {
                        playAudio(1100, 0.06, 'square');
                        ctx.strokeStyle = '#00f2fe';
                        ctx.lineWidth = 3;
                        ctx.beginPath(); ctx.moveTo(cx + 80, cy); ctx.lineTo(t.x, t.y); ctx.stroke();
                        torpedoes.splice(idx, 1);
                    }
                });

            } else if (currentSim === 'antivirus') {
                // Varredura Bio-Digital Nanotécnica
                for (let i = 0; i < 3; i++) {
                    oceanParticles.push({
                        x: Math.random() * canvas.width, y: canvas.height * 0.35 + Math.random() * (canvas.height * 0.65),
                        vx: (Math.random() - 0.5) * 2, vy: (Math.random() - 0.5) * 2,
                        size: 3, color: '#39ff14', life: 0.8
                    });
                }
            }

            // 4. ATUALIZAR E DESENHAR PARTICULAS LIVRES
            for (let i = oceanParticles.length - 1; i >= 0; i--) {
                let p = oceanParticles[i];
                p.x += p.vx; p.y += p.vy; p.life -= 0.015;
                ctx.fillStyle = p.color;
                ctx.beginPath(); ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2); ctx.fill();
                if (p.life <= 0) oceanParticles.splice(i, 1);
            }

            // 5. DESENHAR O SUBMARINO BRASILEIRO NO CENTRO
            drawBrazilianSubmarine(cx, cy, currentView);

            // 6. ATUALIZAÇÃO DA TELEMETRIA NO HUD
            document.getElementById('val-depth').innerText = submarine.depth.toFixed(0) + " METROS";
            document.getElementById('val-press').innerText = (submarine.depth / 10).toFixed(1) + " BAR";
            document.getElementById('val-stealth').innerText = submarine.stealth.toFixed(1) + "% (INVISÍVEL)";

            requestAnimationFrame(renderScene);
        }

        // INICIAR SIMULAÇÃO SUBMARINA
        renderScene();
    </script>
</body>
</html>
