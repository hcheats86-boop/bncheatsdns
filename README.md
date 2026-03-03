<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Auxílio DNS - Premium</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --background: #000000;
            --foreground: #ffffff;
            --card: rgba(20, 20, 20, 0.6);
            --border: rgba(255, 255, 255, 0.12);
        }

        body {
            background-color: var(--background);
            color: var(--foreground);
            font-family: 'Inter', sans-serif;
            margin: 0;
            overflow-x: hidden;
        }

        .font-display { font-family: 'JetBrains Mono', monospace; }

        .card-glow-active {
            box-shadow: inset 0 0 20px rgba(255, 255, 255, 0.05);
        }

        .btn-glow-active {
            box-shadow: 0 0 15px rgba(255, 255, 255, 0.1);
        }

        #toast-container {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 10000;
        }

        .toast {
            background: #1a1a1a;
            border: 1px solid var(--border);
            color: #eee;
            padding: 12px 20px;
            border-radius: 8px;
            font-size: 12px;
            margin-bottom: 10px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            animation: slideIn 0.3s ease-out forwards;
        }

        @keyframes slideIn {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }

        /* TELA DE LOGIN FIXA */
        #lock-screen {
            position: fixed;
            inset: 0;
            background: black;
            z-index: 9999;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
    </style>
</head>
<body class="min-h-screen relative">

    <div id="lock-screen">
        <div class="w-full max-w-sm bg-white/5 border border-white/10 p-6 rounded-2xl backdrop-blur-xl text-center">
            <h2 class="font-display text-white text-lg font-bold mb-2">SISTEMA DE ACESSO</h2>
            <p class="text-gray-500 text-[10px] uppercase tracking-widest mb-6">Insira sua Chave Premium</p>
            <input type="text" id="pass-input" placeholder="DNS-XXXX-XXXX" class="w-full bg-black border border-white/10 rounded-lg py-4 text-center font-display text-xs text-white mb-4 uppercase focus:outline-none focus:border-white/40 transition-all">
            <button onclick="checkAccess()" class="w-full py-4 rounded-lg bg-white text-black font-display font-bold text-[10px] tracking-widest uppercase hover:scale-[1.02] active:scale-[0.95] transition-all">VALIDAR ACESSO</button>
            <p class="text-[9px] text-white/20 mt-4 uppercase tracking-tighter">Acesso Restrito - v2.1.0</p>
        </div>
    </div>

    <div id="main-content" style="display:none;">
        <canvas id="particleCanvas" class="fixed inset-0 pointer-events-none" style="z-index: 0;"></canvas>

        <div id="toast-container"></div>

        <div class="relative z-10 px-4 py-6 max-w-md mx-auto">
            
            <div class="text-center mb-5">
                <div class="inline-block px-3 py-0.5 rounded-full bg-white/5 border border-white/10 mb-2">
                    <span class="text-[10px] text-gray-400 font-display tracking-widest uppercase">v2.1.0</span>
                </div>
                <h1 class="font-display text-xl font-bold text-white tracking-wide">
                    Auxílio DNS Android
                </h1>
                <p class="text-white/40 font-display text-xs tracking-[0.3em] uppercase mt-0.5">Premium</p>
            </div>

            <div class="bg-white/5 backdrop-blur-md border border-white/10 rounded-xl p-3 mb-5 flex items-center justify-between">
                <div class="flex items-center gap-2">
                    <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
                    <span class="text-[10px] font-display uppercase tracking-wider text-gray-300">Servidor Online</span>
                </div>
                <div class="text-[10px] font-display text-white/40 uppercase">Proteção Ativa</div>
            </div>

            <div id="function-cards-list" class="space-y-3 mb-5"></div>

            <button onclick="showToast('Bypass Anti-Ban ativado')" class="w-full mb-3 py-3 rounded-xl bg-white/5 border border-white/10 text-white/50 font-display text-[10px] tracking-widest uppercase hover:bg-white/10 transition-all">
                Ativar Bypass Anti-Ban
            </button>

            <a href="freefire://" class="mt-3 flex items-center justify-center gap-2 py-3.5 rounded-xl bg-white text-black font-display font-bold text-xs tracking-widest uppercase btn-glow-active hover:scale-[1.02] active:scale-[0.97] transition-all duration-200">
                INJETAR NO FREE FIRE
            </a>

            <p class="text-center text-[10px] text-white/20 mt-6 font-display uppercase tracking-widest">
                Servidor Premium · Conexão Segura
            </p>
        </div>
    </div>

    <script>
        // ==========================================
        // SISTEMA DE SEGURANÇA (NOVO)
        // ==========================================
        const SEGREDO_SISTEMA = "CHAVE_MESTRA_2026"; 

        function checkAccess() {
            const input = document.getElementById('pass-input').value.trim().toUpperCase();
            
            // Senha Permanente
            if (input === "ADMIN2026") {
                liberarAcesso();
                return;
            }

            // Validação da Key Gerada
            try {
                const partes = input.split('-');
                if (partes.length !== 3 || partes[0] !== 'DNS') throw "invalida";
                
                const codAleatorio = partes[1];
                const hashSeguranca = partes[2];
                
                // Reconstrói o cálculo para validar
                const checkHash = btoa(codAleatorio + SEGREDO_SISTEMA).substring(0, 4).toUpperCase();
                
                if (hashSeguranca === checkHash) {
                    liberarAcesso();
                } else {
                    alert("CHAVE INVÁLIDA!");
                }
            } catch(e) {
                alert("FORMATO INCORRETO!");
            }
        }

        function liberarAcesso() {
            localStorage.setItem('acesso_concedido_v2', 'true');
            document.getElementById('lock-screen').style.display = 'none';
            document.getElementById('main-content').style.display = 'block';
            startParticles();
        }

        // Bloqueio Automático ao abrir
        window.onload = function() {
            if (localStorage.getItem('acesso_concedido_v2') === 'true') {
                liberarAcesso();
            }
        };

        // ==========================================
        // TEU CÓDIGO ORIGINAL (PARTÍCULAS)
        // ==========================================
        function startParticles() {
            const canvas = document.getElementById('particleCanvas');
            const ctx = canvas.getContext('2d');
            let particles = [];
            const count = 40;
            const maxDist = 120;
            const resize = () => { canvas.width = window.innerWidth; canvas.height = window.innerHeight; };
            window.addEventListener('resize', resize);
            resize();
            for (let i = 0; i < count; i++) {
                particles.push({
                    x: Math.random() * canvas.width,
                    y: Math.random() * canvas.height,
                    vx: (Math.random() - 0.5) * 0.4,
                    vy: (Math.random() - 0.5) * 0.4,
                });
            }
            const drawParticles = () => {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                for (let i = 0; i < count; i++) {
                    for (let j = i + 1; j < count; j++) {
                        const dx = particles[i].x - particles[j].x;
                        const dy = particles[i].y - particles[j].y;
                        const dist = Math.sqrt(dx * dx + dy * dy);
                        if (dist < maxDist) {
                            ctx.beginPath(); ctx.moveTo(particles[i].x, particles[i].y);
                            ctx.lineTo(particles[j].x, particles[j].y);
                            ctx.strokeStyle = `rgba(255,255,255,${0.06 * (1 - dist / maxDist)})`;
                            ctx.lineWidth = 0.5; ctx.stroke();
                        }
                    }
                }
                for (const p of particles) {
                    ctx.beginPath(); ctx.arc(p.x, p.y, 1.5, 0, Math.PI * 2);
                    ctx.fillStyle = "rgba(255,255,255,0.12)"; ctx.fill();
                    p.x += p.vx; p.y += p.vy;
                    if (p.x < 0 || p.x > canvas.width) p.vx *= -1;
                    if (p.y < 0 || p.y > canvas.height) p.vy *= -1;
                }
                requestAnimationFrame(drawParticles);
            };
            drawParticles();
        }

        // ==========================================
        // TEU CÓDIGO ORIGINAL (CARDS)
        // ==========================================
        const functions = [
            { title: "DNS Premium Android", description: "DNS Android Premium Full HS", buttonLabel: "ATIVAR DNS PREMIUM" },
            { title: "Remover Input Lag", description: "Acelera a sensibilidade removendo delay.", buttonLabel: "ATIVAR INPUT BOOST" },
            { title: "Injetar Auxílio Premium", description: "Injetar arquivos Headtrick Premium.", buttonLabel: "ATIVAR AUXÍLIO PREMIUM" },
            { title: "Injetar Auxílio Premium", description: "Injetar arquivos Headtrick Premium.", buttonLabel: "ATIVAR AUXÍLIO PREMIUM" },
        ];

        function showToast(msg) {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = 'toast'; toast.innerText = msg;
            container.appendChild(toast);
            setTimeout(() => toast.remove(), 3000);
        }

        const list = document.getElementById('function-cards-list');
        functions.forEach((fn, index) => {
            const cardId = `card-${index}`;
            list.innerHTML += `
                <div class="bg-white/5 backdrop-blur-sm rounded-xl border border-white/10 overflow-hidden" id="${cardId}">
                    <div class="flex gap-1.5 px-3 pt-3">
                        <div class="indicator-1 h-1 flex-1 rounded-full bg-white/5"></div>
                        <div class="indicator-2 h-1 flex-1 rounded-full bg-white/5"></div>
                        <div class="indicator-3 h-1 flex-1 rounded-full bg-white/5"></div>
                    </div>
                    <div class="content-div px-4 py-3 transition-all duration-500">
                        <h3 class="font-display text-sm font-bold text-white leading-tight">${fn.title}</h3>
                        <p class="text-[11px] text-gray-500 mt-0.5 mb-3 font-light leading-snug">${fn.description}</p>
                        <div class="flex gap-2 mb-3">
                            <div class="status-box flex-1 rounded-lg border border-white/5 bg-white/5 px-2 py-1.5 text-center">
                                <div class="text-[9px] text-gray-500 uppercase tracking-wider">Status</div>
                                <div class="status-text text-[11px] font-display font-semibold mt-0.5 text-gray-600">Offline</div>
                            </div>
                            <div class="status-box flex-1 rounded-lg border border-white/5 bg-white/5 px-2 py-1.5 text-center">
                                <div class="text-[9px] text-gray-500 uppercase tracking-wider">Ping</div>
                                <div class="ping-text text-[11px] font-display font-semibold mt-0.5 text-gray-600">—</div>
                            </div>
                            <div class="status-box flex-1 rounded-lg border border-white/5 bg-white/5 px-2 py-1.5 text-center">
                                <div class="text-[9px] text-gray-500 uppercase tracking-wider">Nível</div>
                                <div class="level-text text-[11px] font-display font-semibold mt-0.5 text-gray-600">—</div>
                            </div>
                        </div>
                        <button onclick="activateCard('${cardId}', '${fn.title}')" class="action-btn w-full py-2 rounded-lg bg-white text-black font-display font-semibold text-[11px] tracking-widest uppercase transition-all duration-300 hover:bg-white/90 active:scale-[0.97]">
                            ${fn.buttonLabel}
                        </button>
                    </div>
                </div>`;
        });

        function activateCard(id, title) {
            const card = document.getElementById(id);
            const btn = card.querySelector('.action-btn');
            const indicators = card.querySelectorAll('[class^="indicator-"]');
            card.querySelector('.content-div').classList.add('card-glow-active');
            card.querySelector('.status-text').innerText = 'Online';
            card.querySelector('.status-text').classList.replace('text-gray-600', 'text-white');
            card.querySelector('.ping-text').innerText = '12ms';
            card.querySelector('.ping-text').classList.replace('text-gray-600', 'text-white');
            card.querySelector('.level-text').innerText = 'MAX';
            card.querySelector('.level-text').classList.replace('text-gray-600', 'text-white');
            indicators[0].style.backgroundColor = 'rgba(255,255,255,0.25)';
            indicators[1].style.backgroundColor = 'rgba(255,255,255,0.20)';
            indicators[2].style.backgroundColor = 'rgba(255,255,255,0.15)';
            btn.disabled = true;
            btn.innerText = 'ATIVADO';
            btn.className = "w-full py-2 rounded-lg bg-white/10 text-white/70 border border-white/10 font-display font-semibold text-[11px] tracking-widest uppercase cursor-default btn-glow-active";
            showToast(`${title} ativado com sucesso`);
        }
    </script>
</body>
</html>
