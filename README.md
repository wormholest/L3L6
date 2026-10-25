<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
    <title>字彙特攻：四大元素之域 (Vocab Quest HD)</title>
    <style>
        :root {
            --primary: #f59e0b;
            --bg-dark: #090d16;
            --panel-bg: rgba(15, 23, 42, 0.96);
            --border: #475569;
            --text-light: #f8fafc;
            --accent-red: #ef4444;
            --accent-green: #10b981;
            --accent-blue: #3b82f6;
            --accent-purple: #8b5cf6;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            user-select: none;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background-color: #020617;
            color: var(--text-light);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            overflow: hidden;
        }

        #game-container {
            position: relative;
            width: 800px;
            height: 600px;
            border: 4px solid var(--border);
            border-radius: 12px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.9);
            background: #000;
            overflow: hidden;
        }

        canvas {
            display: block;
            image-rendering: pixelated;
        }

        /* Large HUD Styling */
        #hud {
            position: absolute;
            top: 10px;
            left: 10px;
            right: 10px;
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            pointer-events: none;
            z-index: 10;
        }

        .hud-panel {
            background: var(--panel-bg);
            border: 2px solid var(--border);
            padding: 8px 14px;
            border-radius: 8px;
            font-size: 15px;
            font-weight: 600;
            box-shadow: 0 4px 12px rgba(0,0,0,0.6);
            pointer-events: auto;
        }

        .bar-container {
            width: 130px;
            height: 10px;
            background: #1e293b;
            border-radius: 5px;
            overflow: hidden;
            margin-top: 4px;
            border: 1px solid #64748b;
        }

        .bar-fill {
            height: 100%;
            width: 100%;
            transition: width 0.25s ease;
        }

        #hp-fill { background: linear-gradient(90deg, #ef4444, #f87171); }
        #exp-fill { background: linear-gradient(90deg, #3b82f6, #60a5fa); }

        /* Quest Tracker - Enlarged */
        #quest-tracker {
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            width: 340px;
            background: var(--panel-bg);
            border: 2px solid var(--primary);
            border-radius: 8px;
            padding: 8px 14px;
            font-size: 14px;
            z-index: 10;
            text-align: center;
            box-shadow: 0 4px 12px rgba(0,0,0,0.6);
        }

        #quest-tracker h4 {
            color: var(--primary);
            font-size: 15px;
            margin-bottom: 3px;
            display: flex;
            justify-content: space-between;
        }

        /* Modals & Dialogs */
        .modal {
            position: absolute;
            inset: 0;
            background: rgba(10, 15, 30, 0.9);
            backdrop-filter: blur(5px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 50;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.2s ease;
        }

        .modal.active {
            opacity: 1;
            pointer-events: auto;
        }

        .dialog-box {
            background: var(--panel-bg);
            border: 3px solid var(--primary);
            border-radius: 12px;
            width: 90%;
            max-width: 720px;
            padding: 24px;
            box-shadow: 0 25px 50px rgba(0,0,0,0.9);
            color: var(--text-light);
            position: relative;
        }

        .dialog-header {
            font-size: 22px;
            font-weight: bold;
            color: var(--primary);
            margin-bottom: 12px;
            border-bottom: 2px solid var(--border);
            padding-bottom: 8px;
        }

        .dialog-body {
            font-size: 18px;
            line-height: 1.7;
            margin-bottom: 20px;
            min-height: 70px;
        }

        .btn-group {
            display: flex;
            gap: 12px;
            justify-content: flex-end;
        }

        button {
            background: #1e293b;
            color: white;
            border: 2px solid #64748b;
            padding: 10px 22px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            transition: all 0.2s;
        }

        button:hover {
            background: var(--primary);
            color: #000;
            border-color: #f59e0b;
        }

        /* Battle UI */
        #battle-modal .dialog-box {
            border-color: var(--accent-red);
        }

        .battle-arena {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 16px;
            background: rgba(0,0,0,0.6);
            padding: 16px 24px;
            border-radius: 10px;
            border: 1px solid var(--border);
        }

        .fighter {
            text-align: center;
            width: 45%;
        }

        .portrait-canvas {
            background: #0f172a;
            border: 2px solid var(--border);
            border-radius: 8px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.5);
            margin-bottom: 6px;
        }

        .sentence-box {
            background: #020617;
            border: 2px dashed #64748b;
            padding: 20px;
            border-radius: 10px;
            font-size: 22px;
            line-height: 1.6;
            margin-bottom: 16px;
            text-align: center;
        }

        .sentence-box span.blank {
            color: var(--primary);
            font-weight: bold;
            text-decoration: underline;
            padding: 0 8px;
        }

        .options-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 14px;
        }

        .option-btn {
            text-align: left;
            background: #1e293b;
            border: 2px solid #475569;
            padding: 14px 18px;
            border-radius: 8px;
            font-size: 18px;
            font-weight: 600;
        }

        .option-btn:hover {
            background: #334155;
            border-color: var(--accent-blue);
        }

        /* Timer Bar */
        #timer-container {
            width: 100%;
            height: 12px;
            background: #1e293b;
            border-radius: 6px;
            overflow: hidden;
            margin-bottom: 14px;
            border: 1px solid #64748b;
        }

        #timer-fill {
            height: 100%;
            width: 100%;
            background: linear-gradient(90deg, #22c55e, #eab308, #ef4444);
            transition: width 0.1s linear;
        }

        /* Shop Modal */
        .shop-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 16px;
            margin-bottom: 18px;
        }

        .shop-item {
            background: #020617;
            border: 1px solid var(--border);
            border-radius: 8px;
            padding: 14px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        #audio-ctrl {
            position: absolute;
            bottom: 10px;
            right: 10px;
            z-index: 10;
            padding: 6px 14px;
            font-size: 13px;
        }
    
body{padding:env(safe-area-inset-top,0) 0 env(safe-area-inset-bottom,0);background:radial-gradient(circle at 50% 30%,#14182e,#03040b)}
*{font-family:'DotGothic16','PingFang TC','Microsoft JhengHei','Noto Sans TC',monospace}
#game-container{border:5px solid #231b12;border-radius:4px;box-shadow:0 0 0 2px #d8c18a,0 0 0 5px #231b12,0 30px 70px rgba(0,0,0,.95),0 0 90px rgba(216,193,138,.12)}
canvas#gameCanvas{filter:saturate(1.18) contrast(1.07) brightness(1.04)}
#tilt-top,#tilt-bot{position:absolute;left:0;right:0;height:130px;z-index:5;pointer-events:none;backdrop-filter:blur(2.6px);-webkit-backdrop-filter:blur(2.6px)}
#tilt-top{top:0;-webkit-mask-image:linear-gradient(#000,transparent);mask-image:linear-gradient(#000,transparent)}
#tilt-bot{bottom:0;-webkit-mask-image:linear-gradient(transparent,#000);mask-image:linear-gradient(transparent,#000)}
#vig{position:absolute;inset:0;z-index:6;pointer-events:none;background:radial-gradient(ellipse at center,transparent 52%,rgba(6,6,24,.6) 100%)}
.hud-panel,#quest-tracker,.dialog-box{background:linear-gradient(180deg,rgba(26,38,76,.94),rgba(9,14,36,.95));border:2px solid #d8c18a;border-radius:5px;box-shadow:inset 0 0 0 2px #0a1026,inset 0 0 0 3px rgba(216,193,138,.45),0 6px 16px rgba(0,0,0,.65)}
#quest-tracker h4,.dialog-header{color:#f4d98e}
.dialog-header{border-bottom:1px solid rgba(216,193,138,.5)}
#battle-modal .dialog-box{border-color:#e8907e}
.bar-container{border-radius:0;height:8px;border:1px solid #d8c18a;background:#0a1026}
.bar-fill{background-image:repeating-linear-gradient(90deg,transparent 0 5px,rgba(0,0,0,.25) 5px 6px)}
button{border-radius:3px;border:2px solid #d8c18a;background:#142046}
button:hover{background:#f4d98e;color:#1a1208}
.option-btn{background:#142046;border-color:#7b8fc4}
.battle-arena{background:linear-gradient(180deg,#1b2a55,#0a1026 70%);border:1px solid #d8c18a}
.portrait-canvas{width:128px;height:128px;image-rendering:pixelated;border-color:#d8c18a}
.toast{position:absolute;left:50%;top:38%;transform:translateX(-50%);z-index:80;white-space:pre-line;text-align:center;padding:12px 22px;font-size:17px;line-height:1.6;background:rgba(10,16,38,.96);border:2px solid #f4d98e;border-radius:5px;animation:tf 2.6s forwards;pointer-events:none}
@keyframes tf{0%{opacity:0;margin-top:14px}10%{opacity:1;margin-top:0}85%{opacity:1}100%{opacity:0}}
.portrait-canvas{width:80px;height:120px}
#dlg-portrait{position:absolute;left:18px;top:-98px;width:96px;height:96px;image-rendering:pixelated;border:2px solid #d8c18a;border-bottom:none;border-radius:5px 5px 0 0;background:#0a1026}
@keyframes shk{0%,100%{transform:translateX(0)}25%{transform:translateX(-7px)}75%{transform:translateX(7px)}}
.shk{animation:shk .3s}
#game-container{height:auto;width:800px;display:flex;flex-direction:column}
#stage{position:relative;width:800px;height:600px;flex:none;overflow:hidden}
#hud{position:static;flex:none;padding:6px 8px}
#hud .hud-panel{font-size:13px;padding:5px 12px;line-height:1.35}
#quest-tracker{position:static;transform:none;width:auto;margin:6px 8px 0;flex:none;padding:5px 12px;font-size:13px;text-align:left}
#quest-tracker h4{display:inline-flex;gap:8px;margin:0 8px 0 0}#quest-desc{display:inline}
#copy{text-align:center;font-size:13px;color:#b9a679;padding:6px 0 8px;flex:none}
#audio-ctrl{bottom:8px;right:8px}
#shop-modal .shop-grid{max-height:430px;overflow:auto;grid-template-columns:1fr 1fr;gap:10px}
#flee{margin-top:10px;float:right}
.portrait-canvas{width:96px;height:144px}
#dlg-portrait{width:128px;height:128px;top:-130px}
.fx{position:absolute;inset:0;z-index:60;pointer-events:none;overflow:hidden;background:radial-gradient(circle,var(--c) 0%,transparent 70%);animation:fxa 1.4s forwards}
.fx span{position:absolute;top:-10%;font-size:34px;animation:fxf 1s ease-in forwards}
.fxn{position:absolute;top:36%;width:100%;text-align:center;font-size:34px;color:#fff;text-shadow:0 0 14px var(--c),0 0 3px #000}
.fxc{position:absolute;top:52%;width:100%;text-align:center;font-size:28px;color:#ff4d4d;text-shadow:0 0 8px #000;animation:shk .4s 2}
@keyframes fxa{0%{opacity:0}15%{opacity:.9}100%{opacity:0}}
@keyframes fxf{to{top:105%;transform:rotate(360deg)}}
</style>
</head>
<body>

    <div id="game-container">
        <canvas id="gameCanvas" width="800" height="600"></canvas><div id="tilt-top"></div><div id="tilt-bot"></div><div id="vig"></div>

        <!-- HUD -->
        <div id="hud">
            <div class="hud-panel">
                <div><strong>英雄</strong> Lv.<span id="hud-level">1</span> | HP: <span id="hud-hp">100/100</span></div>
                <div class="bar-container"><div id="hp-fill" class="bar-fill"></div></div>
                <div style="margin-top:4px;">EXP: <span id="hud-exp">0/100</span></div>
                <div class="bar-container"><div id="exp-fill" class="bar-fill"></div></div>
            </div>

            <div class="hud-panel" style="text-align: right;">
                <div>💰 <span id="hud-gold" style="color:var(--primary); font-weight:bold;">50</span> | ⚔️ <span id="hud-atk">12</span> | 🛡️ <span id="hud-def">2</span></div>
                <div>📍 <span id="hud-zone" style="color:var(--accent-blue);">橡樹村莊</span></div>
                <div>🔮 水晶: <span id="hud-crystals">0/4</span></div>
            </div>
        </div>

        <!-- Quest Tracker -->
        <div id="quest-tracker">
            <h4><span>主線任務</span> <span id="quest-status" style="color:var(--primary)">進行中</span></h4>
            <div id="quest-desc">與村莊中央的村長艾爾登對話。</div>
        </div>

        <button id="audio-ctrl" onclick="toggleAudio()">🔊 音效: 開</button>

        <!-- General Dialog Modal -->
        <div id="dialog-modal" class="modal">
            <div class="dialog-box">
                <div id="dialog-name" class="dialog-header">NPC</div>
                <div id="dialog-text" class="dialog-body">對話內容...</div>
                <div class="btn-group">
                    <button id="dialog-next-btn" onclick="advanceDialog()">繼續</button>
                </div>
            </div>
        </div>

        <!-- Battle Modal -->
        <div id="battle-modal" class="modal">
            <div class="dialog-box">
                <div class="dialog-header" style="color:var(--accent-red); display:flex; justify-content:space-between; align-items:center;">
                    <span>⚔️ 戰鬥爆發！</span>
                    <span id="battle-timer-text" style="color:var(--primary); font-size:20px;">⏱️ 10 秒</span>
                    <span id="battle-turn">回合 1</span>
                </div>

                <div id="timer-container"><div id="timer-fill"></div></div>

                <div class="battle-arena">
                    <div class="fighter">
                        <canvas id="player-portrait" class="portrait-canvas" width="64" height="64"></canvas>
                        <div style="font-weight:bold; font-size:17px;">英雄 (Player)</div>
                        <div class="bar-container" style="margin:4px auto; width:120px;"><div id="battle-player-hp" class="bar-fill" style="background:#ef4444;"></div></div>
                    </div>
                    <div style="font-size:28px; font-weight:bold; color:var(--primary);">VS</div>
                    <div class="fighter">
                        <canvas id="enemy-portrait" class="portrait-canvas" width="64" height="64"></canvas>
                        <div id="enemy-name" style="font-weight:bold; font-size:17px;">野性怪物</div>
                        <div class="bar-container" style="margin:4px auto; width:120px;"><div id="battle-enemy-hp" class="bar-fill" style="background:#a855f7;"></div></div>
                    </div>
                </div>

                <div id="battle-question" class="sentence-box">
                    載入單字題庫中...
                </div>

                <div id="battle-options" class="options-grid"></div>
            </div>
        </div>

        <!-- Shop Modal -->
        <div id="shop-modal" class="modal">
            <div class="dialog-box">
                <div class="dialog-header">🏪 鐵匠武器舖 (高難度冒險備戰)</div>
                <div class="shop-grid">
                    <div class="shop-item">
                        <div>
                            <div style="font-size:17px;"><strong>鋼鐵長劍</strong> (+25 攻擊)</div>
                            <div style="color:var(--primary); font-size:14px; font-weight:bold;">價格: 80 金幣</div>
                        </div>
                        <button onclick="buyItem('weapon', 25, 80)">購買</button>
                    </div>
                    <div class="shop-item">
                        <div>
                            <div style="font-size:17px;"><strong>騎士鎧甲</strong> (+20 防禦)</div>
                            <div style="color:var(--primary); font-size:14px; font-weight:bold;">價格: 80 金幣</div>
                        </div>
                        <button onclick="buyItem('armor', 20, 80)">購買</button>
                    </div>
                    <div class="shop-item">
                        <div>
                            <div style="font-size:17px;"><strong>大生命藥水</strong> (恢復80 HP)</div>
                            <div style="color:var(--primary); font-size:14px; font-weight:bold;">價格: 35 金幣</div>
                        </div>
                        <button onclick="buyItem('potion', 80, 35)">購買</button>
                    </div>
                    <div class="shop-item">
                        <div>
                            <div style="font-size:17px;"><strong>元素神聖劍</strong> (+55 攻擊)</div>
                            <div style="color:var(--primary); font-size:14px; font-weight:bold;">價格: 260 金幣</div>
                        </div>
                        <button onclick="buyItem('weapon_master', 55, 260)">購買</button>
                    </div>
                </div>
                <div class="btn-group">
                    <button onclick="closeShop()">離開商店</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        /* --- AUDIO SYNTHESIZER --- */
        let audioCtx = null;
        let soundEnabled = true;

        function initAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }

        function playSound(type) {
            if (!soundEnabled) return;
            initAudio();
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            const now = audioCtx.currentTime;

            if (type === 'hit') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(160, now);
                osc.frequency.exponentialRampToValueAtTime(30, now + 0.15);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.15);
                osc.start(now); osc.stop(now + 0.15);
            } else if (type === 'correct') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(523.25, now);
                osc.frequency.setValueAtTime(659.25, now + 0.1);
                osc.frequency.setValueAtTime(783.99, now + 0.2);
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.35);
                osc.start(now); osc.stop(now + 0.35);
            } else if (type === 'wrong') {
                osc.type = 'square';
                osc.frequency.setValueAtTime(180, now);
                osc.frequency.setValueAtTime(110, now + 0.15);
                gain.gain.setValueAtTime(0.25, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.3);
                osc.start(now); osc.stop(now + 0.3);
            } else if (type === 'fanfare') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(440, now);
                osc.frequency.setValueAtTime(554.37, now + 0.15);
                osc.frequency.setValueAtTime(659.25, now + 0.3);
                osc.frequency.setValueAtTime(880, now + 0.45);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.7);
                osc.start(now); osc.stop(now + 0.7);
            }
        }

        function toggleAudio() {
            soundEnabled = !soundEnabled;
            document.getElementById('audio-ctrl').innerText = soundEnabled ? '🔊 音效: 開' : '🔇 音效: 關';
        }

        /* --- MASSIVE NON-REPEATING VOCABULARY DATABASE --- */
        const vocabDB = [
            { word: "differ", pos: "v.", sentence: "Opinions <span class='blank'>____</span> greatly on this controversial topic.", options: ["differ", "march", "sail", "settle"] },
            { word: "primitive", pos: "adj.", sentence: "Ancient tribes used <span class='blank'>____</span> stone tools for survival.", options: ["primitive", "prominent", "spicy", "sticky"] },
            { word: "prominent", pos: "adj.", sentence: "She is a <span class='blank'>____</span> scholar in modern political science.", options: ["prominent", "hasty", "slender", "clumsy"] },
            { word: "march", pos: "v.", sentence: "The soldiers began to <span class='blank'>____</span> across the border at dawn.", options: ["march", "differ", "settle", "provoke"] },
            { word: "sticky", pos: "adj.", sentence: "Her hands were <span class='blank'>____</span> after eating honey pancakes.", options: ["sticky", "spicy", "tight", "diligent"] },
            { word: "spicy", pos: "adj.", sentence: "Thai cuisine is famous for its fresh and <span class='blank'>____</span> dishes.", options: ["spicy", "greasy", "slight", "routine"] },
            { word: "differentiate", pos: "v.", sentence: "It is hard to <span class='blank'>____</span> between real news and rumors online.", options: ["differentiate", "provoke", "sustain", "accelerate"] },
            { word: "fiber", pos: "n.", sentence: "Dietary <span class='blank'>____</span> found in vegetables improves digestion.", options: ["fiber", "tissue", "outline", "commerce"] },
            { word: "commerce", pos: "n.", sentence: "E-commerce has transformed global <span class='blank'>____</span> and trade.", options: ["commerce", "racism", "memorial", "tide"] },
            { word: "memorial", pos: "n.", sentence: "A monumental <span class='blank'>____</span> was built to honor fallen heroes.", options: ["memorial", "outline", "racism", "surge"] },
            { word: "greasy", pos: "adj.", sentence: "Avoid eating overly <span class='blank'>____</span> fast food late at night.", options: ["greasy", "slender", "clumsy", "slippery"] },
            { word: "slender", pos: "adj.", sentence: "The graceful dancer has a remarkably <span class='blank'>____</span> figure.", options: ["slender", "greasy", "hasty", "tight"] },
            { word: "timer", pos: "n.", sentence: "Set a kitchen <span class='blank'>____</span> for fifteen minutes to bake pizza.", options: ["timer", "container", "marker", "brand"] },
            { word: "diligent", pos: "adj.", sentence: "The <span class='blank'>____</span> student revised all vocabulary notes daily.", options: ["diligent", "clumsy", "slight", "amateur"] },
            { word: "crew", pos: "n.", sentence: "The flight <span class='blank'>____</span> greeted passengers warmly upon boarding.", options: ["crew", "sailor", "settler", "commuters"] },
            { word: "provoke", pos: "v.", sentence: "Do not poke the wild animal; it will <span class='blank'>____</span> an attack.", options: ["provoke", "settle", "possess", "torment"] },
            { word: "vast", pos: "adj.", sentence: "Astronauts gaze into the <span class='blank'>____</span> expanse of outer space.", options: ["vast", "tranquil", "literal", "decisive"] },
            { word: "racism", pos: "n.", sentence: "Global laws strictly condemn human hatred and <span class='blank'>____</span>.", options: ["racism", "racist", "racial", "ethics"] },
            { word: "literally", pos: "adv.", sentence: "He was <span class='blank'>____</span> trembling with cold in the blizzard.", options: ["literally", "decisive", "civil", "hasty"] },
            { word: "surge", pos: "n.", sentence: "A massive <span class='blank'>____</span> in power damaged electric generators.", options: ["surge", "tide", "likelihood", "elevation"] },
            { word: "likelihood", pos: "n.", sentence: "There is a high <span class='blank'>____</span> of rainfall this afternoon.", options: ["likelihood", "elevation", "solidity", "ethics"] },
            { word: "solid", pos: "adj.", sentence: "Water turns into <span class='blank'>____</span> ice below zero degrees.", options: ["solid", "bare", "hollow", "queer"] },
            { word: "ethical", pos: "adj.", sentence: "Doctors must follow strict <span class='blank'>____</span> guidelines in research.", options: ["ethical", "unethical", "decisive", "civil"] },
            { word: "torment", pos: "v.", sentence: "Incurable illness continues to <span class='blank'>____</span> the patient.", options: ["torment", "elevate", "cripple", "segregate"] },
            { word: "elevation", pos: "n.", sentence: "Mountain climbers struggled at an <span class='blank'>____</span> of 4,000 meters.", options: ["elevation", "density", "humidity", "circulation"] },
            { word: "sustain", pos: "v.", sentence: "Ecosystems need water and sunlight to <span class='blank'>____</span> life.", options: ["sustain", "accelerate", "contend", "fuel"] },
            { word: "accelerate", pos: "v.", sentence: "Press down on the gas pedal to <span class='blank'>____</span> the sports car.", options: ["accelerate", "sustain", "melt", "confirm"] },
            { word: "fatigue", pos: "n.", sentence: "Driver <span class='blank'>____</span> is a leading cause of highway accidents.", options: ["fatigue", "mobility", "shame", "fraction"] },
            { word: "privilege", pos: "n.", sentence: "It was a rare <span class='blank'>____</span> to meet the renowned president.", options: ["privilege", "generosity", "integrity", "remedy"] },
            { word: "eloquent", pos: "adj.", sentence: "The lawyer gave an <span class='blank'>____</span> speech to convince the jury.", options: ["eloquent", "rhetorical", "vicious", "compulsory"] },
            { word: "hasty", pos: "adj.", sentence: "Don't make a <span class='blank'>____</span> decision without reading details.", options: ["hasty", "diligent", "slender", "tight"] },
            { word: "emerging", pos: "adj.", sentence: "Artificial Intelligence is a key <span class='blank'>____</span> industry worldwide.", options: ["emerging", "flashing", "floating", "rushing"] },
            { word: "brand", pos: "n.", sentence: "This international smartphone <span class='blank'>____</span> is extremely popular.", options: ["brand", "trend", "fashion", "career"] },
            { word: "vacancy", pos: "n.", sentence: "The firm posted an urgent job <span class='blank'>____</span> for software engineers.", options: ["vacancy", "career", "expectation", "inspiration"] },
            { word: "dimension", pos: "n.", sentence: "VR technology adds a brand-new <span class='blank'>____</span> to video games.", options: ["dimension", "integration", "provision", "consumption"] },
            { word: "fundamental", pos: "adj.", sentence: "Freedom of speech is a <span class='blank'>____</span> human right.", options: ["fundamental", "offensive", "stubborn", "fragile"] },
            { word: "fragile", pos: "adj.", sentence: "Handle these <span class='blank'>____</span> porcelain cups with extreme care.", options: ["fragile", "gigantic", "remote", "casual"] },
            { word: "gigantic", pos: "adj.", sentence: "A <span class='blank'>____</span> statue was erected in the castle plaza.", options: ["gigantic", "slight", "fragile", "vacant"] },
            { word: "passion", pos: "n.", sentence: "She plays piano with immense <span class='blank'>____</span> and emotion.", options: ["passion", "emphasis", "resource", "impact"] },
            { word: "emphasis", pos: "n.", sentence: "The school places strong <span class='blank'>____</span> on critical thinking.", options: ["emphasis", "passion", "impact", "resource"] },
            { word: "unethical", pos: "adj.", sentence: "It is considered <span class='blank'>____</span> to copy someone else's exam work.", options: ["unethical", "ethical", "diligent", "prominent"] },
            { word: "civil", pos: "adj.", sentence: "Citizens have basic <span class='blank'>____</span> rights guaranteed by law.", options: ["civil", "spicy", "fragile", "hasty"] },
            { word: "settlement", pos: "n.", sentence: "Archaeologists found an ancient <span class='blank'>____</span> near the riverbank.", options: ["settlement", "vacancy", "fatigue", "elevation"] },
            { word: "possession", pos: "n.", sentence: "This golden watch was his most treasured <span class='blank'>____</span>.", options: ["possession", "dimension", "privilege", "likelihood"] },
            { word: "excessive", pos: "adj.", sentence: "Consuming <span class='blank'>____</span> amounts of sugar can cause health problems.", options: ["excessive", "slender", "fragile", "primitive"] },
            { word: "humidity", pos: "n.", sentence: "High <span class='blank'>____</span> makes the summer air feel extremely sticky.", options: ["humidity", "elevation", "fatigue", "commerce"] },
            { word: "circulation", pos: "n.", sentence: "Regular exercise improves blood <span class='blank'>____</span> throughout the body.", options: ["circulation", "dimension", "settlement", "vacancy"] },
            { word: "infrastructure", pos: "n.", sentence: "The government invested in modernizing transport <span class='blank'>____</span>.", options: ["infrastructure", "privilege", "possession", "memorial"] }
        ];

        let vocabQueue = [...vocabDB];
        function getNextQuestion() {
            if (vocabQueue.length === 0) vocabQueue = [...vocabDB];
            const index = Math.floor(Math.random() * vocabQueue.length);
            return vocabQueue.splice(index, 1)[0];
        }

        /* --- GAME ENGINE & STATE --- */
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const TILE_SIZE = 40;
        const MAP_COLS = 20;
        const MAP_ROWS = 15;

        const player = {
            x: 10,
            y: 7,
            level: 1,
            hp: 100,
            maxHp: 100,
            exp: 0,
            maxExp: 100,
            gold: 50,
            atk: 12,
            def: 2,
            zone: 0, 
            crystals: 0,
            weaponAtk: 0,
            armorDef: 0
        };

        const questState = {
            step: 0, 
            bossesDefeated: [false, false, false, false],
            sideQuests: { farmerQuizDone: false, grannyLetterDelivered: false }
        };

        const zoneNames = [
            "橡樹村莊 (Central Village)", 
            "風之森林 (North: Wind Forest)", 
            "烈焰荒漠 (East: Fire Desert)", 
            "冰霜雪原 (West: Ice Glacier)", 
            "暗黑地牢 (South: Shadow Dungeon)"
        ];

        /* --- MAP TILE MAPS (1: Wall/Obstacle, 2: Portal, 3: NPC, 4: Boss, 0: Path) --- */
        const maps = [
            // Zone 0: Central Village
            [
                [1,1,1,1,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,1],
                [1,0,0,0,1,0,0,0,0,0,0,0,0,0,1,0,0,0,0,1],
                [1,0,3,0,1,0,3,0,0,0,0,3,0,0,1,0,3,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,0,0,1,1,1,1,0,0,1,1,1,1,0,0,1,1,1],
                [1,0,0,0,0,0,0,0,1,0,0,1,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [2,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,2],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,1,0,0,1,0,0,0,0,0,0,0,1],
                [1,1,1,0,0,1,1,1,1,0,0,1,1,1,1,0,0,1,1,1],
                [1,0,3,0,0,0,0,0,0,0,0,0,0,0,0,0,3,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,1]
            ],
            // Zone 1: Wind Forest
            [
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
                [1,0,0,0,1,0,0,0,0,4,0,0,0,0,1,0,0,0,0,1],
                [1,0,0,0,1,0,1,1,1,0,0,1,1,0,1,0,0,0,0,1],
                [1,0,3,0,0,0,1,0,0,0,0,0,1,0,0,0,0,0,0,1],
                [1,1,1,0,0,0,1,0,0,0,0,0,1,0,0,0,1,1,1,1],
                [1,0,0,0,0,0,1,1,1,1,1,1,1,0,0,0,0,0,0,1],
                [1,0,0,0,1,0,0,0,0,0,0,0,0,0,1,0,0,0,0,1],
                [1,0,1,1,1,1,1,0,0,0,0,0,1,1,1,1,1,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,1,1,1,1,0,0,0,1,1,1,1,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,1]
            ],
            // Zone 2: Fire Desert
            [
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,1,1,1,1,0,0,0,0,0,0,0,0,1,1,1,1,0,1],
                [1,0,1,0,0,1,0,0,0,3,0,0,0,0,1,0,0,1,0,1],
                [1,0,1,0,0,1,0,0,0,0,0,0,0,0,1,0,0,1,0,1],
                [1,0,1,1,0,1,1,1,1,0,0,1,1,1,1,0,1,1,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [2,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,4,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
            ],
            // Zone 3: Ice Glacier
            [
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,1,0,1],
                [1,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,1],
                [1,0,1,0,1,1,1,1,1,0,0,1,1,1,1,1,0,1,0,1],
                [1,0,1,0,1,0,0,0,0,3,0,0,0,0,0,1,0,1,0,1],
                [1,0,0,0,1,0,0,0,0,0,0,0,0,0,0,1,0,0,0,1],
                [1,0,4,0,1,1,1,1,0,0,0,1,1,1,1,1,0,0,0,2],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,0,0,1,1,1,1,1,1,0,0,1,1,1,1,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
            ],
            // Zone 4: Shadow Dungeon
            [
                [1,1,1,1,1,1,1,1,1,2,2,1,1,1,1,1,1,1,1,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,1,1,1,1,1,1,1,0,0,1,1,1,1,1,1,1,0,1],
                [1,0,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,0,1],
                [1,0,1,0,1,1,1,1,1,0,0,1,1,1,1,1,0,1,0,1],
                [1,0,1,0,1,0,0,0,0,0,0,0,0,0,0,1,0,1,0,1],
                [1,0,0,0,1,0,0,0,0,3,0,0,0,0,0,1,0,0,0,1],
                [1,0,0,0,1,1,1,1,0,0,0,1,1,1,1,1,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,4,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,0,0,1,1,1,1,1,1,0,0,1,1,1,1,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
                [1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1]
            ]
        ];

        /* --- HD-2D RENDERER: pixel sprites + dynamic light, bloom, tilt-shift --- */
const T=20,lo=document.createElement('canvas');lo.width=400;lo.height=300;const lg=lo.getContext('2d');
function rnd(a,b,c){const s=Math.sin(a*127.1+b*311.7+c*74.7)*43758.5453;return s-Math.floor(s);}
const ZP=[
{g:['#7d6c4f','#74644a'],d:'#9b8a66',s:'#5c4e38',tint:'#fff0d2',p:{c:'#ffe9a0',vy:-.15,vx:.1,n:36}},
{g:['#2d6a47','#35744f'],d:'#5fb072',s:'#1f4a32',tint:'#d3ead6',p:{c:'#c8ff9a',vy:.2,vx:-.2,n:44}},
{g:['#c68c4e','#d09858'],d:'#e9c184',s:'#9b6a36',tint:'#ffdcae',p:{c:'#ffd9a0',vy:-.05,vx:-.6,n:50}},
{g:['#98c0e0','#a4cbe8'],d:'#e8f5ff',s:'#6f9bc4',tint:'#d8eaff',p:{c:'#ffffff',vy:.45,vx:-.15,n:70}},
{g:['#2c2242','#342948'],d:'#4b3a70',s:'#1a1428',tint:'#9c8ac8',p:{c:'#d09cff',vy:-.3,vx:.05,n:40}}];
const HERO=["...ooo","..oHHH",".oHHHH",".oHHHH",".oHssH",".oHses","..osss",".ooccc",".ocaaa",".ocaag",".ocaaa",".ocbbb","..ocbb","..obbb","..obbo","..oooo"];
const HPAL={o:'#1b1220',H:'#7a3b1d',s:'#f4cfa0',e:'#25324f',c:'#c8362f',a:'#aab4c8',g:'#f2c94c',b:'#4a3326'};
const BS=[
["o.......","oo...ooo","oxo.ollb","oxxooblb",".oxbbbbb","..obeebb","..obeeeb","..obbxxx","..obbxxl",".oobbbbb","oxobllbb","oxxobbbb",".oxoblbb","..oobbbb","...oxbxb","....oooo"],
["xo......","xxo...oo","oxo.ooxb",".ooobbbb","..obblbb","..obeebb","..obeebb","..obbbbb","..oxxxxx",".oobblbb","oobbbbbl","oxobbxbb","oxoobbbb",".oobbbbb","..obbobb","..oooooo"],
["..oooooo",".ollllll","oblllbbb","oblbbbbb","obbbbbbb","obbeebbb","obbeebbb","obbbbbbb","oxxbbxxb","oblbbllb","oblbbllb","obbbbbbb","oobbbbbb",".oobbbbb","..oxxbbb","..oooooo"],
["..x.x.x.","..xxxxxx","..oxxxxx",".oobbbbb",".obbbbbb",".obeebbb",".obbbbbb","..obbbbb","..oxbbbb",".ooobbbl","oobbbbll","obbbxbbb","obbbxbbb","obbbbbbb","oobboobb","..oooooo"]];
const BP=[{o:'#0b2a35',b:'#2fb6c9',l:'#bff6ff',e:'#ffffff',x:'#f5a623'},{o:'#2a0c05',b:'#d9561c',l:'#ff9a3c',e:'#ffee66',x:'#ffc233'},{o:'#0b1e45',b:'#4a86d8',l:'#cfe6ff',e:'#ffffff',x:'#8fc7ff'},{o:'#12051c',b:'#5b2a87',l:'#8a4fc2',e:'#ff3355',x:'#e0b030'}];
const BL=['80,230,255','255,150,60','140,200,255','210,80,255'];
const MOB=["........","....oooo","..ooblbb",".obblbbb",".obbbbbb","obeebbbb","obeebbbb","obbbbbbb","obbxxxxb","obbxoxox","obbbbbbb",".obbbbbb",".oobbbbb","..oobbbb","...ooooo","........"];
const MP=[{},{b:'#4a9bd0',l:'#bfe6ff'},{b:'#d9703a',l:'#ffc27a'},{b:'#7fb0e8',l:'#e6f5ff'},{b:'#7a4bb0',l:'#b58cf0'}];
function drawPix(g,rows,pal,x,y,s){const W=rows[0].length*2;rows.forEach((row,j)=>{for(let i=0;i<row.length;i++){const k=row[i];if(k==='.')continue;g.fillStyle=pal[k];g.fillRect(x+i*s,y+j*s,s,s);g.fillRect(x+(W-1-i)*s,y+j*s,s,s);}});}
function drawHero(g,x,y,s,flip,pal,nosw){drawPix(g,HERO,pal,x,y,s);if(nosw)return;const sx=x+(flip?-2:12)*s;g.fillStyle='#e4edf7';g.fillRect(sx,y+3*s,s,7*s);g.fillStyle='#8fa0b8';g.fillRect(sx+s,y+3*s,s,7*s);g.fillStyle='#f2c94c';g.fillRect(sx,y+10*s,2*s,s);}
function shadow(g,x,y,w){g.fillStyle='rgba(0,0,0,.34)';g.fillRect(x+2,y,w-4,3);g.fillRect(x,y+1,w,1);}
function portraitBG(g,c){const r=g.createRadialGradient(32,30,3,32,32,46);r.addColorStop(0,c);r.addColorStop(1,'#0a0f22');g.fillStyle=r;g.fillRect(0,0,64,64);}
function drawPlayerSprite(g,cx,cy,size=32){const s=size/16;portraitBG(g,'#3a4f86');g.fillStyle='rgba(0,0,0,.4)';g.fillRect(cx+2*s,cy+15*s,12*s,s);drawHero(g,cx+s,cy,s,false,HPAL);}
function drawBossPortrait(g,i){g.clearRect(0,0,64,64);portraitBG(g,'#2a3868');drawPix(g,BS[i],BP[i],0,0,4);}
function drawMob(z){const g=document.getElementById('enemy-portrait').getContext('2d');g.clearRect(0,0,64,64);portraitBG(g,'#2a3868');drawPix(g,MOB,Object.assign({o:'#140d1d',e:'#fff',x:'#ffe08a'},MP[z]),0,0,4);}
const gcache={};let LT=[],PT=[],lz=-1;const disp={x:10,y:7,f:1};
function ground(z){if(gcache[z])return gcache[z];const c=document.createElement('canvas');c.width=400;c.height=300;const g=c.getContext('2d'),P=ZP[z],M=maps[z];
for(let r=0;r<15;r++)for(let q=0;q<20;q++){const x=q*T,y=r*T,v=rnd(q,r,z),path=z===0&&M[r][q]!==1&&(q===9||q===10||r===7);
g.fillStyle=path?(v>.5?'#b89a6a':'#ad8f60'):P.g[v>.5?1:0];g.fillRect(x,y,T,T);
for(let k=0;k<6;k++){const px=rnd(q,r,k+9)*18|0,py=rnd(r,q,k+3)*18|0;g.fillStyle=k%2?P.d:P.s;g.fillRect(x+px,y+py,k%2?2:1,k%3?1:2);}
if(z===1&&v>.55){g.fillStyle=P.d;g.fillRect(x+6,y+8,1,3);g.fillRect(x+8,y+7,1,4);g.fillRect(x+10,y+9,1,2);}
if(r>0&&M[r-1][q]===1){g.fillStyle='rgba(0,0,0,.2)';g.fillRect(x,y,T,5);}}
return gcache[z]=c;}
function obj(z,q,r,t){const x=q*T,y=r*T,v=rnd(q,r,z+5),g=lg,edge=q===0||q===19||r===0||r===14;
if(z===0){if(edge){g.fillStyle='#6b6354';g.fillRect(x,y+2,T,18);g.fillStyle='#8d8472';g.fillRect(x,y+2,T,4);g.fillStyle='#4f4939';g.fillRect(x,y+10,T,1);g.fillRect(x+10,y+11,1,4);g.fillRect(x+4,y+6,1,4);}
else{g.fillStyle='rgba(0,0,0,.28)';g.fillRect(x+3,y+18,T,3);g.fillStyle='#e6d6b0';g.fillRect(x,y+4,T,16);g.fillStyle='#c8b78e';g.fillRect(x,y+16,T,4);
g.fillStyle='#a8322a';g.fillRect(x-1,y-8,T+2,13);g.fillStyle='#d9574a';g.fillRect(x-1,y-8,T+2,3);g.fillStyle='#7d2420';g.fillRect(x-1,y+3,T+2,2);
g.fillStyle='#ffd27a';g.fillRect(x+3,y+9,5,5);g.fillStyle='#3a2a1a';g.fillRect(x+5,y+9,1,5);g.fillRect(x+3,y+11,5,1);g.fillStyle='#5a3a22';g.fillRect(x+12,y+10,5,10);LT.push([x+5,y+12,'255,200,110',34]);}}
else if(z===1){const sw=Math.round(Math.sin(t/700+q));g.fillStyle='rgba(0,0,0,.3)';g.fillRect(x+3,y+16,15,4);g.fillStyle='#4b2f1b';g.fillRect(x+8,y+11,4,9);
g.fillStyle='#1b4f33';g.fillRect(x+2+sw,y-8,16,7);g.fillRect(x+sw,y-3,20,9);g.fillRect(x+3,y+5,14,5);
g.fillStyle='#2f8a52';g.fillRect(x+4+sw,y-8,10,4);g.fillRect(x+2+sw,y-3,12,5);g.fillStyle='#7fd28d';g.fillRect(x+6+sw,y-7,4,2);g.fillRect(x+3+sw,y-2,3,2);}
else if(z===2){g.fillStyle='rgba(0,0,0,.28)';g.fillRect(x+2,y+17,17,3);
if(v>.5){g.fillStyle='#3b8f4a';g.fillRect(x+8,y-6,4,24);g.fillRect(x+3,y+2,5,3);g.fillRect(x+3,y-2,3,7);g.fillRect(x+12,y+5,5,3);g.fillRect(x+14,y+1,3,7);g.fillStyle='#7bd07f';g.fillRect(x+9,y-6,1,24);g.fillRect(x+4,y-2,1,6);}
else{g.fillStyle='#8a4a28';g.fillRect(x+1,y+3,18,17);g.fillStyle='#c47a42';g.fillRect(x+1,y+1,18,5);g.fillStyle='#e0a066';g.fillRect(x+3,y,10,2);g.fillStyle='#6a3519';g.fillRect(x+11,y+8,8,12);}}
else if(z===3){g.fillStyle='rgba(20,40,90,.3)';g.fillRect(x+2,y+17,17,3);g.fillStyle='#4f8fd8';g.fillRect(x+5,y-4,10,22);g.fillRect(x+2,y+4,5,14);g.fillRect(x+13,y+7,5,11);
g.fillStyle='#a9d6ff';g.fillRect(x+7,y-8,6,6);g.fillRect(x+6,y-3,3,18);g.fillStyle='#fff';g.fillRect(x+8,y-7,2,5);LT.push([x+10,y+6,'120,200,255',30]);}
else{const p=.6+Math.sin(t/400+q*2+r)*.4;g.fillStyle='rgba(0,0,0,.4)';g.fillRect(x+1,y+17,19,3);g.fillStyle='#120d1c';g.fillRect(x+2,y-6,16,26);g.fillStyle='#2a2044';g.fillRect(x+2,y-6,4,26);g.fillRect(x+2,y-6,16,3);
g.fillStyle='rgba(190,110,255,'+p+')';g.fillRect(x+9,y+3,3,6);g.fillRect(x+7,y+5,7,2);LT.push([x+10,y+6,'170,90,255',34*p+8]);}}
function portal(q,r,t){const x=q*T,y=r*T,a=3+Math.round(Math.sin(t/300)),g=lg;g.fillStyle='#14296e';g.fillRect(x+1,y+1,18,18);g.fillStyle='#2f66e0';g.fillRect(x+a,y+a,20-2*a,20-2*a);g.fillStyle='#8fc1ff';g.fillRect(x+7,y+7,6,6);g.fillStyle='#fff';g.fillRect(x+9,y+9,2,2);LT.push([x+10,y+10,'90,150,255',40]);}
function npc(q,r,t){const x=q*T,y=r*T,k=q*7+r*13,b=Math.round((Math.sin(t/450+k)+1)/2);
const pal=Object.assign({},HPAL,{H:['#e8d6a0','#444','#b5651d','#c9c9c9','#6a3b8a'][k%5],c:['#2f6fb5','#3f8f4a','#8a5a2b','#7a4a9a','#b5832f'][k%5]});shadow(lg,x+4,y+18,12);drawHero(lg,x+4,y+4-b,1,false,pal,true);}
function boss(q,r,t,z){const x=q*T,y=r*T,i=z-1,g=lg;
if(questState.bossesDefeated[i]){g.fillStyle=BP[i].b;g.fillRect(x+8,y+5,4,10);g.fillRect(x+6,y+7,8,6);g.fillStyle='#fff';g.fillRect(x+9,y+8,2,3);LT.push([x+10,y+10,'255,255,255',26]);return;}
const b=Math.round(Math.sin(t/350));shadow(g,x+2,y+18,16);drawPix(g,BS[i],BP[i],x+2,y+4-b,1);LT.push([x+10,y+10,BL[i],38]);}
function hero(t){const mv=Math.abs(player.x-disp.x)+Math.abs(player.y-disp.y)>.12,b=mv?((t/90|0)%2):0,x=Math.round(disp.x*T)+4,y=Math.round(disp.y*T)+4;shadow(lg,x,y+14,12);drawHero(lg,x,y-b,1,disp.f<0,HPAL);}
function initP(){const n=ZP[player.zone].p.n;PT=Array.from({length:n},()=>({x:Math.random()*800,y:Math.random()*600,s:Math.random()*6.28,z:1+Math.random()*2}));}
function draw(t){const z=player.zone,P=ZP[z],M=maps[z],c=ctx;
if(z!==lz){lz=z;initP();disp.x=player.x;disp.y=player.y;}
if(player.x<disp.x-.02)disp.f=-1;else if(player.x>disp.x+.02)disp.f=1;
disp.x+=(player.x-disp.x)*.3;disp.y+=(player.y-disp.y)*.3;
LT=[];lg.drawImage(ground(z),0,0);const pr=Math.round(disp.y);
for(let r=0;r<15;r++){for(let q=0;q<20;q++){const k=M[r][q];if(k===1)obj(z,q,r,t);else if(k===2)portal(q,r,t);else if(k===3)npc(q,r,t);else if(k===4)boss(q,r,t,z);else if(k===5)chest(q,r,t);else if(k===6)pool(q,r,t,z);else if(k===7)gate(q,r,t);}mobsDraw(r,t);if(r===pr)hero(t);}
c.imageSmoothingEnabled=false;c.globalCompositeOperation='source-over';c.drawImage(lo,0,0,800,600);c.save();c.filter="blur(5px) brightness(1.25)";c.globalAlpha=.12;c.globalCompositeOperation="screen";c.drawImage(lo,0,0,800,600);c.restore();
c.globalCompositeOperation='multiply';c.fillStyle=P.tint;c.fillRect(0,0,800,600);
c.globalCompositeOperation='lighter';
LT.forEach(([lx,ly,col,rad])=>{const fl=.85+Math.sin(t/180+lx)*.1,X=lx*2,Y=ly*2,R=rad*2.2,gr=c.createRadialGradient(X,Y,0,X,Y,R);gr.addColorStop(0,'rgba('+col+','+.3*fl+')');gr.addColorStop(1,'rgba('+col+',0)');c.fillStyle=gr;c.fillRect(X-R,Y-R,2*R,2*R);});
if(z<3){c.fillStyle='rgba(255,236,170,.07)';for(let i=0;i<3;i++){const o=120+i*230+Math.sin(t/2500+i)*30;c.beginPath();c.moveTo(o,0);c.lineTo(o+70,0);c.lineTo(o-160,600);c.lineTo(o-250,600);c.fill();}}
const pp=P.p;PT.forEach(p=>{p.x+=pp.vx*1.5+Math.sin(t/900+p.s)*.3;p.y+=pp.vy*1.5*p.z/2;if(p.x<-5)p.x=805;if(p.x>805)p.x=-5;if(p.y<-5)p.y=605;if(p.y>605)p.y=-5;c.fillStyle=pp.c;c.globalAlpha=.35+.35*Math.sin(t/300+p.s);const sz=p.z>2?4:2;c.fillRect(p.x|0,p.y|0,sz,sz);});
c.globalAlpha=1;c.globalCompositeOperation='source-over';requestAnimationFrame(draw);}

        /* --- NPC DIALOG & QUEST SYSTEM --- */
        const npcs = {
            "6_2": { name: "村長 艾爾登", role: "mayor" },
            "11_2": { name: "鐵匠 咕嚕", role: "blacksmith" },
            "2_2": { name: "農夫 Tom", role: "farmer" },
            "16_2": { name: "老奶奶 Rose", role: "granny" },
            "2_11": { name: "衛隊長 Marcus", role: "marcus" },
            "16_11": { name: "學者 Lilith", role: "scholar" },
            "2_3_1": { name: "森林隱士 (Wind Hermit)", role: "hermit" },
            "9_3_2": { name: "流浪商人 (Desert Nomad)", role: "nomad" },
            "9_5_3": { name: "冰原探險家 (Ice Explorer)", role: "explorer" },
            "9_6_4": { name: "聖騎士英靈 (Spirit Knight)", role: "spirit" }
        };

        let dialogQueue = [];
        let dialogCallback = null;

        function showDialog(name, textArray, callback = null) {
            dialogQueue = [...textArray];
            dialogCallback = callback;
            document.getElementById('dialog-name').innerText = name;
            document.getElementById('dialog-modal').classList.add('active');
            advanceDialog();
        }

        function advanceDialog() {
            if (dialogQueue.length > 0) {
                document.getElementById('dialog-text').innerText = dialogQueue.shift();
            } else {
                document.getElementById('dialog-modal').classList.remove('active');
                if (dialogCallback) {
                    dialogCallback();
                    dialogCallback = null;
                }
            }
        }

        function interactNPC(role) {
            if (role === 'mayor') {
                const count = questState.bossesDefeated.filter(Boolean).length;
                if (count === 0) {
                    showDialog("村長 艾爾登", [
                        "勇敢的冒險者啊！四大方向的領地已被強大魔王佔領！",
                        "北方『風之森林』、東方『烈焰荒漠』、西方『冰霜雪原』、南方『暗黑地牢』。",
                        "怪物極為兇殘！戰鬥時只有 10 秒作答時間！請先去鐵匠舖購買最強裝備！"
                    ]);
                } else if (count < 4) {
                    showDialog("村長 艾爾登", [`做得好！你已經成功奪回了 ${count}/4 顆水晶！繼續加油！`]);
                } else {
                    showDialog("村長 艾爾登", ["太感人了！四大水晶皆已歸位，世界的單字文法秩序徹底恢復了！"]);
                }
            } else if (role === 'blacksmith') {
                document.getElementById('shop-modal').classList.add('active');
            } else if (role === 'farmer') {
                if (!questState.sideQuests.farmerQuizDone) {
                    showDialog("農夫 Tom", ["答對我的單字題，我就送你 60 金幣作為冒險贊助！"], () => {
                        triggerMiniQuiz((correct) => {
                            if (correct) {
                                player.gold += 60;
                                questState.sideQuests.farmerQuizDone = true;
                                updateHUD();
                                showDialog("農夫 Tom", ["太厲害了！答對了！這 60 金幣是你的了！"]);
                            } else {
                                showDialog("農夫 Tom", ["答錯了，準備好再來吧！"]);
                            }
                        });
                    });
                } else {
                    showDialog("農夫 Tom", ["作物生長得很健康，感謝你的幫助！"]);
                }
            } else if (role === 'granny') {
                showDialog("老奶奶 Rose", ["祝福你的冒險一切順利，願文法之光護佑著你！"]);
            } else if (role === 'marcus') {
                showDialog("衛隊長 Marcus", ["野外小怪非常兇猛，沒有裝備鎧甲會被瞬間秒殺！"]);
            } else if (role === 'scholar') {
                showDialog("學者 Lilith", ["高頻單字提示：『prominent』著名的；『differentiate』區別！"]);
            } else if (role === 'hermit' || role === 'nomad' || role === 'explorer' || role === 'spirit') {
                showDialog("領地守望者", ["魔王就在前方！準備好神兵利器與單字知識再發起挑戰！"]);
            }
        }

        function triggerMiniQuiz(callback) {
            const q = getNextQuestion();
            const choices = [...q.options].sort(() => Math.random() - 0.5);
            let optionsText = choices.map((c, i) => `${i + 1}. ${c}`).join('\n');
            let ans = prompt(`[單字挑戰]\n${q.sentence}\n(詞性: ${q.pos})\n\n請輸入正確數字 (1-4):\n${optionsText}`);
            if (ans && choices[parseInt(ans) - 1] === q.word) callback(true);
            else callback(false);
        }

        function closeShop() {
            document.getElementById('shop-modal').classList.remove('active');
        }

        function buyItem(type, val, cost) {
            if (player.gold >= cost) {
                player.gold -= cost;
                if (type === 'weapon') {
                    player.weaponAtk = val;
                    showDialog("鐵匠 咕嚕", ["鋼鐵長劍已裝備，攻擊力 +25！"]);
                } else if (type === 'armor') {
                    player.armorDef = val;
                    showDialog("鐵匠 咕嚕", ["騎士鎧甲已裝備，防禦力 +20！"]);
                } else if (type === 'potion') {
                    player.hp = Math.min(player.maxHp, player.hp + val);
                    showDialog("鐵匠 咕嚕", ["喝下大生命藥水，恢復了 80 HP！"]);
                } else if (type === 'weapon_master') {
                    player.weaponAtk = val;
                    showDialog("鐵匠 咕嚕", ["元素神聖劍發出耀眼光芒，攻擊力大幅 +55！"]);
                }
                updateHUD();
            } else {
                showDialog("鐵匠 咕嚕", ["金幣不足！多去打怪或完成任務賺錢吧！"]);
            }
        }

        /* --- HARDER BATTLE SYSTEM (7-SECOND TIMER & HIGH MONSTER STATS) --- */
        let activeEnemy = null;
        let currentQuestion = null;
        let battleTurn = 1;
        let battleTimer = null;
        let timeLeft = 10;

        function startBattle(isBoss = false) {
            activeEnemy = createEnemy(isBoss);
            battleTurn = 1;
            document.getElementById('battle-turn').innerText = `回合 ${battleTurn}`;
            document.getElementById('enemy-name').innerText = activeEnemy.name;
            document.getElementById('battle-modal').classList.add('active');

            // Draw Portraits
            const pCtx = document.getElementById('player-portrait').getContext('2d');
            pCtx.clearRect(0,0,64,64);
            drawPlayerSprite(pCtx, 0, 0, 64);

            const eCtx = document.getElementById('enemy-portrait').getContext('2d');
            if (isBoss) {
                drawBossPortrait(eCtx, activeEnemy.bossIdx);
            } else {
                eCtx.clearRect(0,0,64,64);
                eCtx.fillStyle = "#1e293b"; eCtx.fillRect(0,0,64,64);
                eCtx.fillStyle = "#ef4444"; eCtx.beginPath(); eCtx.arc(32,32,20,0,Math.PI*2); eCtx.fill();
            }

            updateBattleBars();
            loadBattleQuestion();
        }

        function createEnemy(isBoss) {
            if (isBoss) {
                // Massive HP & ATK
                if (player.zone === 1) return { name: "🌲 風之森林守護者 (Boss)", hp: 160, maxHp: 160, atk: 35, def: 8, gold: 150, exp: 180, bossIdx: 0 };
                if (player.zone === 2) return { name: "🔥 烈焰荒漠領主 (Boss)", hp: 240, maxHp: 240, atk: 48, def: 12, gold: 220, exp: 260, bossIdx: 1 };
                if (player.zone === 3) return { name: "❄️ 冰霜雪原巨人 (Boss)", hp: 320, maxHp: 320, atk: 60, def: 18, gold: 300, exp: 350, bossIdx: 2 };
                if (player.zone === 4) return { name: "🔥 暗黑魔王瑪拉科爾 (Final Boss)", hp: 500, maxHp: 500, atk: 80, def: 25, gold: 600, exp: 800, bossIdx: 3 };
            }
            
            const enemyNames = ["", "風疾飛狼", "火炎毒蠍", "霜凍幽靈", "暗黑衛士"];
            const scale = player.zone;
            return {
                name: enemyNames[player.zone] || "野性怪物",
                hp: 35 * scale,
                maxHp: 35 * scale,
                atk: 18 * scale, // Severe damage without armor
                def: 3 * scale,
                gold: 35 * scale,
                exp: 45 * scale,
                isBoss: false
            };
        }

        function updateBattleBars() {
            const playerHpPct = Math.max(0, (player.hp / player.maxHp) * 100);
            const enemyHpPct = Math.max(0, (activeEnemy.hp / activeEnemy.maxHp) * 100);
            document.getElementById('battle-player-hp').style.width = playerHpPct + '%';
            document.getElementById('battle-enemy-hp').style.width = enemyHpPct + '%';
        }

        function startTimer() {
            clearInterval(battleTimer);
            timeLeft = 10;
            updateTimerDisplay();

            battleTimer = setInterval(() => {
                timeLeft -= 0.1;
                if (timeLeft <= 0) {
                    timeLeft = 0;
                    updateTimerDisplay();
                    clearInterval(battleTimer);
                    handleTimeout();
                } else {
                    updateTimerDisplay();
                }
            }, 100);
        }

        function updateTimerDisplay() {
            document.getElementById('battle-timer-text').innerText = `⏱️ ${Math.ceil(timeLeft)} 秒`;
            const pct = (timeLeft / 10) * 100;
            document.getElementById('timer-fill').style.width = pct + '%';
        }

        function handleTimeout() {
            playSound('wrong');
            const totalDef = player.def + player.armorDef;
            const enemyDmg = bossHit(Math.max(12, activeEnemy.atk - totalDef));
            player.hp -= enemyDmg;
            playSound('hit');
            alert(`⏰ 超時！你未在 10 秒內作答！\n${activeEnemy.name} 對你造成了 ${enemyDmg} 點致命打擊！`);

            updateBattleBars();
            updateHUD();

            if (player.hp <= 0) setTimeout(loseBattle, 300);
            else nextTurn();
        }

        function loadBattleQuestion() {
            currentQuestion = getNextQuestion();
            document.getElementById('battle-question').innerHTML = `
                <div><strong>[文意選填]</strong> ${currentQuestion.sentence}</div>
                <div style="font-size:15px; color:#94a3b8; margin-top:8px;">詞性提示: (${currentQuestion.pos})</div>
            `;

            const choices = [...currentQuestion.options].sort(() => Math.random() - 0.5);
            const optionsContainer = document.getElementById('battle-options');
            optionsContainer.innerHTML = '';

            choices.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'option-btn';
                btn.innerText = opt;
                btn.onclick = () => handleAnswer(opt);
                optionsContainer.appendChild(btn);
            });

            startTimer();
        }

        function handleAnswer(selectedOpt) {
            clearInterval(battleTimer);
            const isCorrect = selectedOpt === currentQuestion.word;

            if (isCorrect) {
                playSound('correct');
                const totalAtk = player.atk + player.weaponAtk;
                const damage = Math.round(Math.max(10, totalAtk - activeEnemy.def)*comboMult());
                activeEnemy.hp -= damage;
                playSound('hit');
                alert(`✨ 回答正確！你對 ${activeEnemy.name} 造成了 ${damage} 點傷害！`);
            } else {
                playSound('wrong');
                const totalDef = player.def + player.armorDef;
                const enemyDmg = bossHit(Math.max(12, activeEnemy.atk - totalDef));
                player.hp -= enemyDmg;
                playSound('hit');
                alert(`❌ 回答錯誤！正確單字是【${currentQuestion.word}】。\n${activeEnemy.name} 反擊造成了 ${enemyDmg} 點傷害！`);
            }

            updateBattleBars();
            updateHUD();

            if (activeEnemy.hp <= 0) setTimeout(winBattle, 300);
            else if (player.hp <= 0) setTimeout(loseBattle, 300);
            else nextTurn();
        }

        function nextTurn() {
            battleTurn++;
            document.getElementById('battle-turn').innerText = `回合 ${battleTurn}`;
            loadBattleQuestion();
        }

        function winBattle() {
            clearInterval(battleTimer);
            playSound('fanfare');
            document.getElementById('battle-modal').classList.remove('active');
            player.gold += activeEnemy.gold;
            player.exp += activeEnemy.exp;

            let msg = `🎉 戰鬥勝利！獲得 ${activeEnemy.gold} 金幣與 ${activeEnemy.exp} 經驗值！`;

            if (activeEnemy.bossIdx !== undefined) {
                questState.bossesDefeated[activeEnemy.bossIdx] = true;
                player.crystals++;
                msg += `\n🔮 成功擊敗魔王！奪回了【元素水晶】！\n您可以隨時經由傳送門返回村莊整整補。`;
                updateQuestHUD();
            }

            checkLevelUp();
            updateHUD();

            if (questState.bossesDefeated.every(Boolean)) {
                setTimeout(() => {
                    showDialog("🏆 拯救世界的偉大英雄", [
                        "恭喜你！四大元素領地的魔王已被全數擊敗！四大水晶重獲光芒！",
                        "暗黑魔王瑪拉科爾化為灰燼，語言世界的文法秩序與和平重新歸來！",
                        "【遊戲通關】感謝你的遊玩！你可以繼續留在地圖中自由探索！"
                    ]);
                }, 500);
            } else {
                alert(msg);
            }
        }

        function loseBattle() {
            clearInterval(battleTimer);
            document.getElementById('battle-modal').classList.remove('active');
            alert("💀 你在戰鬥中倒下了... 村民將你救回了橡樹村莊。請先提升裝備再來挑戰！");
            player.hp = player.maxHp;
            player.zone = 0;
            player.x = 10;
            player.y = 7;
            updateHUD();
        }

        function checkLevelUp() {
            if (player.exp >= player.maxExp) {
                player.level++;
                player.exp -= player.maxExp;
                player.maxExp = Math.floor(player.maxExp * 1.4);
                player.maxHp += 15;
                
                player.atk += 5;
                player.def += 3;
                alert(`🎊 升級了！達到 Level ${player.level}！\nHP上限 +15（不會自動回血）, 攻擊力 +5, 防禦力 +3！`);
            }
        }

        /* --- MAP & CONTROLS --- */
        function updateHUD() {
            document.getElementById('hud-level').innerText = player.level;
            document.getElementById('hud-hp').innerText = `${player.hp}/${player.maxHp}`;
            document.getElementById('hp-fill').style.width = `${(player.hp / player.maxHp) * 100}%`;
            document.getElementById('hud-exp').innerText = `${player.exp}/${player.maxExp}`;
            document.getElementById('exp-fill').style.width = `${(player.exp / player.maxExp) * 100}%`;
            document.getElementById('hud-gold').innerText = player.gold;
            document.getElementById('hud-atk').innerText = player.atk + player.weaponAtk;
            document.getElementById('hud-def').innerText = player.def + player.armorDef;
            document.getElementById('hud-zone').innerText = zoneNames[player.zone];
            document.getElementById('hud-crystals').innerText = `${player.crystals}/4`;
        }

        function updateQuestHUD() {
            const desc = document.getElementById('quest-desc');
            const status = document.getElementById('quest-status');
            const count = questState.bossesDefeated.filter(Boolean).length;
            
            if (count === 4) {
                desc.innerText = "四大水晶全數奪回，世界恢復和平！通關完成！";
                status.innerText = "已完成";
                status.style.color = "var(--accent-green)";
            } else {
                desc.innerText = `前往東西南北四方領地擊敗魔王 (已完成 ${count}/4)。`;
            }
        }

        function handleTileInteraction(targetX, targetY) {
            const currentMap = maps[player.zone];
            const tile = currentMap[targetY][targetX];

            if (tile === 3) {
                const key = `${targetX}_${targetY}` + (player.zone > 0 ? `_${player.zone}` : '');
                if (npcs[key]) interactNPC(npcs[key].role);
                return false;
            }

            if (tile === 4) {
                const bIdx = player.zone - 1;
                if (!questState.bossesDefeated[bIdx]) startBattle(true);
                else alert("該區域的魔王已被你消滅！");
                return false;
            }

            if (tile === 2) { // Portal
                if (player.zone === 0) {
                    if (targetY === 0) { player.zone = 1; player.x = 9; player.y = 13; }
                    else if (targetX === 19) { player.zone = 2; player.x = 1; player.y = 7; }
                    else if (targetX === 0) { player.zone = 3; player.x = 18; player.y = 7; }
                    else if (targetY === 14) { player.zone = 4; player.x = 9; player.y = 1; }
                } else {
                    if (player.zone === 1) { player.zone = 0; player.x = 9; player.y = 1; }
                    else if (player.zone === 2) { player.zone = 0; player.x = 18; player.y = 7; }
                    else if (player.zone === 3) { player.zone = 0; player.x = 1; player.y = 7; }
                    else if (player.zone === 4) { player.zone = 0; player.x = 9; player.y = 13; }
                }
                updateHUD();
                return false;
            }

            if (tile === 1) return false;
            return true;
        }

        function movePlayer(dx, dy) {
            if (document.querySelector('.modal.active')) return;

            const newX = player.x + dx;
            const newY = player.y + dy;

            if (newX >= 0 && newX < MAP_COLS && newY >= 0 && newY < MAP_ROWS) {
                if (handleTileInteraction(newX, newY)) {
                    player.x = newX;
                    player.y = newY;

                    if (player.zone > 0 && maps[player.zone][player.y][player.x] === 0) {
                        
                    }
                }
            }
        }

        window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp' || e.key === 'w' || e.key === 'W') movePlayer(0, -1);
            if (e.key === 'ArrowDown' || e.key === 's' || e.key === 'S') movePlayer(0, 1);
            if (e.key === 'ArrowLeft' || e.key === 'a' || e.key === 'A') movePlayer(-1, 0);
            if (e.key === 'ArrowRight' || e.key === 'd' || e.key === 'D') movePlayer(1, 0);
        });

        updateHUD();
        updateQuestHUD();
    </script>
<script>
window.alert=function(m){const d=document.createElement('div');d.className='toast';d.innerText=m;document.getElementById('game-container').appendChild(d);setTimeout(()=>d.remove(),2600);};
function triggerMiniQuiz(cb){const q=getNextQuestion(),ch=[...q.options].sort(()=>Math.random()-.5),m=document.getElementById('dialog-modal'),t=document.getElementById('dialog-text'),nb=document.getElementById('dialog-next-btn');
document.getElementById('dialog-name').innerText='單字挑戰';t.innerHTML=q.sentence+'<div class="qz" style="margin-top:12px"></div>';nb.style.display='none';
ch.forEach(w=>{const b=document.createElement('button');b.innerText=w;b.style.margin='6px 8px 0 0';b.onclick=()=>{nb.style.display='';m.classList.remove('active');cb(w===q.word);};t.querySelector('.qz').appendChild(b);});m.classList.add('active');}
const _sb=startBattle;startBattle=function(b){_sb(b);if(!b)drawMob(player.zone);};
const gcn=document.getElementById('game-container');function fit(){gcn.style.transform='scale('+Math.min(1,innerWidth/830,innerHeight/630)+')';}addEventListener('resize',fit);fit();
</script>
<script>
var streak=0,kills=0,chests=0;
const HERO2=[".....ooo","...ooHHH","..oHHhhh",".oHHhhHH",".oHhHHHH",".oHHHHHH",".oHHsssH",".oHsssss",".oHseess",".oHssssS","..osssmm","...ooSss","..oggccc",".ocaaAAA","ocCaaaAg","ocCaaAAa","ocCcggAA",".ocCccCC",".ocCtttt","..octtt.","..octtt.","..obbbb.","..obBBb.","..ooooo."];
const HPAL2={o:'#1b1220',H:'#6b3318',h:'#9a5a2b',s:'#f4cfa0',S:'#d9a878',e:'#25324f',m:'#b5604f',c:'#c8362f',C:'#8a2420',a:'#b9c4d8',A:'#7e8aa3',g:'#f2c94c',t:'#3b3f63',b:'#4a3326',B:'#6a4a36'};
const BAT=["o.......","oo....o.","obo..oob","obboobbb",".obbbbbl","..obeebb","..obeebb","...obbxx","..obbbbb","..oblbbb",".obbbbbl","obbboobb","obbo.obb","obo...ob","oo.....o","o......."];
const WISP="......oo,....ooxl,...oxxll,..oxllll,..oxlleo,..oxllle,..oxxlll,...oxxxl,...oxxxx,....oxxx,....oxlx,...oxxxo,.....oxo,......oo,........,........".replace(/x/g,'b').split(',');
const SKULL=["..oooooo",".ollllll","ollllllb","olleellb","olleellb","ollllbbb","olllollx",".ollllxx","..ollxxx","..oxxxxx","..oobbbb","..obllbb","..oblbbb","...obbbb","...ooooo","........"];
const MB=[0,MOB,BAT,WISP,SKULL];
function drawPix(g,rows,pal,x,y,s){const W=rows[0].length*2,at=(i,j)=>{if(j<0||j>=rows.length)return'.';const w=rows[j].length,ii=i<w?i:W-1-i;return rows[j][ii]||'.'};
for(let j=0;j<rows.length;j++)for(let i=0;i<W;i++){const k=at(i,j);if(k==='.')continue;const px=x+i*s,py=y+j*s;g.fillStyle=pal[k];g.fillRect(px,py,s,s);
if(k!=='o'&&k!=='e'){const u=at(i,j-1),d=at(i,j+1);if(u==='.'||u==='o'){g.fillStyle='rgba(255,255,255,.2)';g.fillRect(px,py,s,s);}else if(d==='.'||d==='o'){g.fillStyle='rgba(0,0,0,.25)';g.fillRect(px,py,s,s);}}}}
function drawHero(g,x,y,s,flip,pal,nosw){drawPix(g,HERO2,pal,x,y,s);if(nosw)return;const sx=x+(flip?0:15)*s;g.fillStyle='#e4edf7';g.fillRect(sx,y+5*s,s,10*s);g.fillStyle='#f2c94c';g.fillRect(sx-s,y+15*s,3*s,s);g.fillStyle='#6b3a1d';g.fillRect(sx,y+16*s,s,2*s);}
function hero(t){const mv=Math.abs(player.x-disp.x)+Math.abs(player.y-disp.y)>.12,b=mv?((t/90|0)%2):0,x=Math.round(disp.x*T)+2,y=Math.round(disp.y*T)-4;shadow(lg,x+1,y+22,14);drawHero(lg,x,y-b,1,disp.f<0,HPAL2);}
function npc(q,r,t){const x=q*T+2,y=r*T-4,k=q*7+r*13,b=Math.round((Math.sin(t/450+k)+1)/2);
const pal=Object.assign({},HPAL2,{H:['#d8c690','#444','#b5651d','#c9c9c9','#6a3b8a'][k%5],h:'#ffffff55',c:['#2f6fb5','#3f8f4a','#8a5a2b','#7a4a9a','#b5832f'][k%5],C:'#00000055'});shadow(lg,x+1,y+22,14);drawHero(lg,x,y-b,1,false,pal,true);}
function portraitBG(g,c){const H=g.canvas.height,r=g.createRadialGradient(32,H*.5,3,32,H*.55,H*.7);r.addColorStop(0,c);r.addColorStop(1,'#0a0f22');g.fillStyle=r;g.fillRect(0,0,64,H);}
function drawPlayerSprite(g){g.clearRect(0,0,64,96);portraitBG(g,'#3a4f86');drawHero(g,0,0,4,false,HPAL2);}
function drawBossPortrait(g,i){g.clearRect(0,0,64,96);portraitBG(g,'#2a3868');drawPix(g,BS[i],BP[i],0,32,4);g.canvas.style.filter='drop-shadow(0 0 8px rgb('+BL[i]+'))';}
function drawMob(z){const g=document.getElementById('enemy-portrait').getContext('2d');g.clearRect(0,0,64,96);portraitBG(g,'#2a3868');g.canvas.style.filter='none';drawPix(g,MB[z],Object.assign({o:'#140d1d',e:'#fff',x:'#ffe08a'},MP[z]),0,32,4);}
['player-portrait','enemy-portrait'].forEach(i=>document.getElementById(i).height=96);
function chest(q,r,t){const x=q*T,y=r*T,g=lg;shadow(g,x+2,y+16,16);g.fillStyle='#6b3a1d';g.fillRect(x+3,y+8,14,10);g.fillStyle='#9a5a2b';g.fillRect(x+3,y+5,14,5);g.fillStyle='#f2c94c';g.fillRect(x+3,y+9,14,1);g.fillRect(x+9,y+9,3,4);g.fillStyle='#4a2810';g.fillRect(x+3,y+17,14,1);
if(((t/400|0)+q)%3===0){g.fillStyle='#fff';g.fillRect(x+14,y+3,1,3);g.fillRect(x+13,y+4,3,1);}LT.push([x+10,y+10,'255,210,90',20]);}
function pool(q,r,t,z){const x=q*T,y=r*T,g=lg,c=['#3a78c4','#2d8a8a','#e0572b','#6fb4e8','#6b2fb0'][z],l=['#8fc4f0','#7fd4c8','#ffb347','#d6efff','#c28cff'][z],w=(t/500+q*3|0)%4;
g.fillStyle='#2b2118';g.fillRect(x,y+2,T,18);g.fillStyle=c;g.fillRect(x+1,y+3,18,15);g.fillStyle=l;g.fillRect(x+3+w*3,y+7,4,1);g.fillRect(x+12-w*2,y+12,5,1);LT.push([x+10,y+10,['90,150,255','80,220,200','255,110,40','140,210,255','170,90,255'][z],26]);}
function decorate(c,z){const g=c.getContext('2d'),M=maps[z];for(let r=0;r<15;r++)for(let q=0;q<20;q++){if(M[r][q]!==0)continue;const v=rnd(q,r,77);if(v>.28)continue;const x=q*T+(rnd(q,r,5)*12|0)+3,y=r*T+(rnd(r,q,6)*12|0)+4,k=(v*100|0)%3;
if(z<2){g.fillStyle=['#ff7aa8','#fff2a0','#9ad0ff'][k];g.fillRect(x,y,2,2);g.fillStyle='#2f6b3a';g.fillRect(x,y+2,1,2);}
else if(z===2){g.fillStyle='#8a5a30';g.fillRect(x,y,4,2);g.fillStyle='#e9c184';g.fillRect(x,y,4,1);}
else if(z===3){g.fillStyle='#fff';g.fillRect(x,y,3,1);g.fillRect(x+1,y-1,1,3);}
else{g.fillStyle='#6a4aa0';g.fillRect(x,y,3,2);g.fillStyle='#b58cf0';g.fillRect(x,y,1,1);}}}
const _g=ground;ground=function(z){const had=gcache[z],c=_g(z);if(!had)decorate(c,z);return c;};
[[0,3,5,5],[0,16,12,5],[1,15,8,5],[1,2,12,5],[2,3,13,5],[2,17,10,5],[3,2,12,5],[3,17,8,5],[4,2,13,5],[4,17,12,5],[0,4,8,6],[0,5,8,6],[0,14,8,6],[0,15,8,6],[1,5,12,6],[1,6,12,6],[2,8,12,6],[2,9,12,6],[3,8,12,6],[3,9,12,6],[4,8,12,6],[4,9,12,6]].forEach(([z,q,r,k])=>maps[z][r][q]=k);
const _ht=handleTileInteraction;handleTileInteraction=function(x,y){const k=maps[player.zone][y][x];if(k===6)return false;
if(k===5){maps[player.zone][y][x]=0;const g=40+(rnd(x,y,3)*80|0);player.gold+=g;chests++;let m='📦 開啟寶箱！獲得 '+g+' 金幣';if(Math.random()<.25){player.hp=Math.min(player.maxHp,player.hp+40);m+='\n與一瓶藥水（HP +40）';}if(chests===10){player.gold+=200;m+='\n全寶箱收集！額外 +200 金幣';}updateHUD();alert(m);return false;}return _ht(x,y);};
function bust(g,W,k,hc){const s=W/48,hair=hc||['#e8d6a0','#3a2a22','#b5651d','#c9c9c9','#6a3b8a','#222'][k%6],skin=['#f4cfa0','#e8b98a','#d09a6a'][k%3],cl=['#2f6fb5','#3f8f4a','#8a5a2b','#7a4a9a','#b5832f'][(k>>3)%5],p=(x,y,w,h,c)=>{g.fillStyle=c;g.fillRect(x*s,y*s,w*s,h*s);};
const bg=g.createLinearGradient(0,0,0,W);bg.addColorStop(0,'#3a4f86');bg.addColorStop(1,'#0c1330');g.fillStyle=bg;g.fillRect(0,0,W,W);
if(k%4===1)p(10,12,28,32,hair);p(6,36,36,12,cl);p(6,36,36,2,'rgba(255,255,255,.25)');p(20,32,8,6,skin);p(14,10,20,24,skin);p(15,8,18,2,skin);p(14,28,20,6,'rgba(0,0,0,.08)');
p(18,22,4,3,'#fff');p(26,22,4,3,'#fff');p(19,22,2,3,'#25324f');p(27,22,2,3,'#25324f');p(19,22,1,1,'#fff');p(27,22,1,1,'#fff');p(18,20,4,1,hair);p(26,20,4,1,hair);p(22,29,4,1,'#b5604f');
p(13,6,22,6,hair);p(12,10,4,10,hair);p(32,10,4,10,hair);p(15,10,12,3,hair);
if(k%4===2)p(15,29,18,7,hair);if(k%4===3){p(12,4,24,4,'#8a5a2b');p(8,8,32,2,'#8a5a2b');}if(k%5===0){p(17,21,6,5,'rgba(180,220,255,.25)');p(25,21,6,5,'rgba(180,220,255,.25)');p(23,22,2,1,'#222');}
p(18,36,12,2,'#f2c94c');}
const dp=document.createElement('canvas');dp.id='dlg-portrait';dp.width=dp.height=96;document.querySelector('#dialog-modal .dialog-box').prepend(dp);
const _sd=showDialog;showDialog=function(n,t,c){_sd(n,t,c);const hs=/英雄|拯救/.test(n);bust(dp.getContext('2d'),96,hs?1443:[...n].reduce((a,ch)=>(a*31+ch.charCodeAt(0))|0,7)>>>0,hs?'#6b3318':null);};
const LORE={hermit:['森林隱士','守護者曾是我的學徒⋯⋯牠被水晶的黑暗力量扭曲了。請擊敗牠！'],nomad:['沙漠商人','烈焰領主以岩漿為食，牠的攻擊比你想的更痛，多帶裝備。'],explorer:['冰原探險家','巨人只在暴風雪中現身，帶足藥水再去。'],spirit:['聖騎士英靈','瑪拉科爾就在前方⋯⋯願文法與你同在。']};
function chain(n){triggerMiniQuiz(ok=>{const S=questState.sideQuests;if(!ok){S.l=0;showDialog('學者 Lilith',['可惜，再來一次吧！']);return;}if(n>1)chain(n-1);else{S.l=2;player.exp+=150;checkLevelUp();updateHUD();showDialog('學者 Lilith',['記憶術大成！經驗值 +150！']);}});}
const _in=interactNPC;interactNPC=function(role){const S=questState.sideQuests,G='老奶奶 Rose',M='衛隊長 Marcus',L='學者 Lilith';
if(LORE[role]){showDialog(LORE[role][0],[LORE[role][1]]);return;}
if(role==='granny'){if(!S.g){S.g=1;showDialog(G,['孩子，能幫我把這封信送給衛隊長嗎？','他總是忘了吃飯⋯⋯信裡夾著我烤的餅乾。'],()=>alert('📜 接受任務：送信給衛隊長 Marcus'));}
else if(S.g===1)showDialog(G,['還沒送到嗎？Marcus 在村子左下角。']);
else if(S.g===2){S.g=3;player.gold+=100;player.exp+=60;checkLevelUp();updateHUD();showDialog(G,['收到了嗎？太好了！這是謝禮。'],()=>alert('✅ 任務完成：100 金幣、60 經驗'));}
else return _in(role);}
else if(role==='marcus'){if(S.g===1){S.g=2;showDialog(M,['Rose 婆婆的信？她真貼心！請替我道謝。']);}
else if(!S.m){S.m=1;kills=0;showDialog(M,['野外怪物太多了，幫我擊敗 5 隻，我有重賞！'],()=>alert('📜 接受任務：擊敗 5 隻小怪'));}
else if(S.m===1){if(kills>=5){S.m=2;player.atk+=4;player.gold+=60;updateHUD();showDialog(M,['了不起！這塊磨刀石送你，攻擊力 +4！']);}else showDialog(M,['進度 '+kills+'/5，繼續加油！']);}
else return _in(role);}
else if(role==='scholar'){if(S.l===2)return _in(role);S.l=1;showDialog(L,['連續答對 3 題字彙，我就傳授你「記憶術」。'],()=>chain(3));}
else return _in(role);updateQuestHUD();};
const _uq=updateQuestHUD;updateQuestHUD=function(){_uq();const S=questState.sideQuests,d=[];if(S.g===1)d.push('送信給 Marcus');if(S.g===2)d.push('回報 Rose');if(S.m===1)d.push('小怪 '+Math.min(kills,5)+'/5');if(d.length)document.getElementById('quest-desc').innerText+='｜支線：'+d.join('、');};
const _wb=winBattle;winBattle=function(){const b=activeEnemy&&activeEnemy.bossIdx!==undefined;_wb();if(!b){kills++;updateQuestHUD();}};
const _ha=handleAnswer;handleAnswer=function(s){const ok=s===currentQuestion.word;if(!ok)streak=0;_ha(s);if(ok)streak++;const e=document.getElementById(ok?'enemy-portrait':'player-portrait');e.classList.remove('shk');void e.offsetWidth;e.classList.add('shk');const bt=document.getElementById('battle-turn');bt.innerText=bt.innerText.split(' · ')[0]+(streak>1?' · 連擊×'+streak:'');};

</script>
<script>
const BK=[
["Overtourism occurs when a ____ number of visitors flock to a destination and cause negative effects.|substantial|tranquil|slender|hasty",
"The mother cried out in ____ when she learned her only son had been killed in a car accident.|torment|commerce|outline|surge",
"In order to ____ interest in readers, some newspapers put sensational headlines on their front pages.|induce|sustain|segregate|possess",
"Since the president had received a ____ report, she was able to make a highly informed decision.|comprehensive|primitive|gigantic|fragile",
"When COVID-19 broke out, our government donated large ____ of face masks to foreign countries.|quantities|memorials|privileges|dimensions",
"Jill tried to stay calm, but her brother's mocking tone ____ her into responding angrily.|provoked|accelerated|settled|sustained",
"Traditional meat production ____ the environment, since cattle farming creates greenhouse gases.|takes a toll on|takes a look at|puts an end to|keeps an eye on",
"Producing lab-grown meat means there is no need for ____, which could eliminate slaughterhouses.|livestock|fiber|allergy|composition",
"Never ____ what you can do. If you set a goal and stick to it, you will achieve it.|underestimate|overestimate|estimates|estimated",
"Many people criticize the ____ treatment of animals in slaughterhouses.|unethical|ethical|civil|diligent"],
["Fish, meat, and eggs are rich in B12, an essential ____ for a healthy nervous system.|vitamin|fiber|mineral|gene",
"After entering high school, Freddy has ____ his behavior and gotten rid of bad habits.|altered|soared|provoked|bustled",
"There is a ____ difference between dogs and cats: dogs are social, while cats are solitary.|stark|dense|lush|mighty",
"The two restaurants are at ____ ends of the street, but both are famous for their desserts.|opposite|natural|negative|unethical",
"It is against ____ for a YouTuber to sell fake products in his or her videos.|ethics|torment|estimate|allergy",
"You should avoid driving after taking cold medicine that ____ sleep.|induces|differs|suits|exceeds",
"The company's new ____ is to focus on online sales rather than traditional retail outlets.|strategy|patty|reaction|location",
"In summer we like to go fishing in the ____ near our farm.|stream|valley|plain|surface",
"The president claimed he would solve a problem that ____ many people: racial inequality.|concerns|underestimates|possesses|penetrates",
"A(n) ____ number of discarded plastic bottles have caused serious pollution.|substantial|mountainous|agricultural|allergic"],
["The hikers set up their camp ____ a cliff because they wanted shelter from the strong winds.|beneath|beyond|besides|between",
"New technology has ____ through the industry, changing how people work and communicate.|swept|settled|sustained|surged",
"Port cities were prosperous because they were centers of ____. Businessmen gathered there to trade.|commerce|racism|memorial|tide",
"The relaxing music created a ____ atmosphere, making people feel peaceful and calm.|tranquil|hasty|greasy|sticky",
"The actress announced her plan to marry a ____ banker, and billionaires' names were tossed around.|prominent|slender|clumsy|fragile",
"Despite some minor flaws, the ____ reviews of the new smartphone have been positive.|overall|overseas|overtime|overdue",
"The ____ of violent crime is lower in suburban areas than in urban centers.|incidence|incident|instance|insistence",
"The ____ of the disease's spread proved it was more dangerous than first estimated.|acceleration|accelerate|accelerated|accelerating",
"The company's ____ of the mistake was a necessary step toward regaining customers' trust.|acknowledgement|acknowledge|acknowledged|acknowledging",
"This station has no ____, so passengers must carry luggage up and down the stairs.|elevator|elevation|elevate|elevated",
"Venice's flooding problem ____ two natural factors: acqua alta and natural subsidence.|results from|deals with|makes up|leads to"],
["Nina's ____ has suffered since she faked a college diploma. She feels guilty around her boss.|conscience|conscious|consequence|consensus",
"The captain's ____ three-pointer in the final minutes led his team to victory.|decisive|decision|decide|decisively",
"The sprinter was ____ in a car accident. He can no longer walk, let alone run.|crippled|tripled|crumpled|cramped",
"The researcher gave a broad ____ of the research the team has been doing.|outline|outcome|outlook|outlet",
"After working sixteen hours a day for a week, the engineer was suffering from ____.|fatigue|fraction|shame|density",
"In the past, the ____ of women was seen in the deprivation of their right to vote.|oppression|oppress|oppressive|oppressed",
"After twenty years of ____, the criminal was released from prison.|captivity|captive|captivate|captivating",
"____ Jack's mother not persuaded him to do the audition, he couldn't have lived out his dream.|Had|Has|Have|If",
"____ Jenny not a mother of three, she would quit her job and travel around the world.|Were|Was|Be|Is",
"Pizza is to Italians ____ sushi is to Japanese people.|what|which|that|who",
"Many people rely on ____ devices to check email and stay connected all day.|mobile|mobility|mobilize|mobilization"]].map(a=>a.map(s=>s.split('|')));
const LB=['第4課 Lab-Grown Meat','Test 2：第3~4課','第5課 Venice','第6課 I Have a Dream'];let bq=[];
function getNextQuestion(){const z=player.zone;if(!bq.length||bq.z!==z){bq=[...(z?BK[z-1]:BK.flat())].sort(()=>Math.random()-.5);bq.z=z;}const [s,a,x,y,w]=bq.pop();return{word:a,pos:z?LB[z-1]:'綜合複習',sentence:s.replace('____',"<span class='blank'>____</span>"),options:[a,x,y,w]};}
/* layout: HUD above map, quest + copyright below */
const stage=document.createElement('div');stage.id='stage';document.getElementById('gameCanvas').before(stage);['gameCanvas','tilt-top','tilt-bot','vig','audio-ctrl'].forEach(i=>stage.appendChild(document.getElementById(i)));
const hudE=document.getElementById('hud');stage.before(hudE);stage.after(document.getElementById('quest-tracker'));
const cp=document.createElement('div');cp.id='copy';cp.innerText='copyright@Maxine';gcn.appendChild(cp);
function fit(){const w=innerWidth||830,hh=innerHeight||800;gcn.style.transform='scale('+Math.max(.4,Math.min(1,w/830,hh/(gcn.offsetHeight+24)))+')';}addEventListener('resize',fit);addEventListener('load',fit);setInterval(fit,800);fit();
/* mazes */
const ENT={1:[9,13],2:[1,7],3:[17,7],4:[9,1]};
function genMaze(z){let s=z*9973+7;const R=()=>(s=(s*16807)%2147483647)/2147483647,m=Array.from({length:15},()=>Array(20).fill(1)),e=ENT[z],st=[e.slice()],D=[[2,0],[-2,0],[0,2],[0,-2]],N4=[[1,0],[-1,0],[0,1],[0,-1]];m[e[1]][e[0]]=0;
while(st.length){const [x,y]=st[st.length-1],o=D.filter(([a,b])=>{const nx=x+a,ny=y+b;return nx>0&&nx<19&&ny>0&&ny<14&&m[ny][nx]===1;}).sort(()=>R()-.5);if(!o.length){st.pop();continue;}const [a,b]=o[0];m[y+b/2][x+a/2]=0;m[y+b][x+a]=0;st.push([x+a,y+b]);}
const dist={},q=[e];dist[e]=0;for(let i=0;i<q.length;i++){const [x,y]=q[i];for(const [a,b] of N4){const nx=x+a,ny=y+b;if(m[ny]&&m[ny][nx]===0&&dist[[nx,ny]]===undefined){dist[[nx,ny]]=dist[[x,y]]+1;q.push([nx,ny]);}}}
const nb=(x,y)=>N4.filter(([a,b])=>m[y+b]&&m[y+b][x+a]===0),DE=()=>q.filter(([x,y])=>x%2&&y%2&&nb(x,y).length===1&&(x!==e[0]||y!==e[1])).sort((p,r)=>dist[r]-dist[p]);
const [bx,by]=DE()[0],[pa,pb]=nb(bx,by)[0];
for(let i=0;i<16;i++){let x,y;if(R()<.5){x=2*(1+(R()*8|0));y=1+2*(R()*7|0);}else{x=1+2*(R()*9|0);y=2*(1+(R()*6|0));}if(Math.abs(x-bx)+Math.abs(y-by)>1)m[y][x]=0;}
m[by][bx]=4;m[by+pb][bx+pa]=7;const dd=DE().filter(c=>(c[0]!==bx||c[1]!==by)&&(c[0]!==bx+2*pa||c[1]!==by+2*pb)),put=(c,v,r)=>{if(c){m[c[1]][c[0]]=v;if(r)npcs[c[0]+'_'+c[1]+'_'+z]={name:r,role:r};}};
put(dd[0],3,['hermit','nomad','explorer','spirit'][z-1]);put(dd[dd.length-1],3,'healer');put(dd[1],5);put(dd[2],5);
if(z===1)m[14][9]=m[14][10]=2;if(z===2)m[7][0]=2;if(z===3){m[7][18]=0;m[7][19]=2;}if(z===4)m[0][9]=m[0][10]=2;maps[z]=m;}
Object.keys(npcs).forEach(k=>{if(k.split('_').length===3)delete npcs[k];});[1,2,3,4].forEach(genMaze);
function gate(q,r,t){const x=q*T,y=r*T,g=lg,p=.6+Math.sin(t/300)*.3;g.fillStyle='#2a2044';g.fillRect(x,y-6,T,26);g.fillStyle='#4b3a70';g.fillRect(x+1,y-6,3,26);g.fillRect(x+16,y-6,3,26);g.fillStyle='rgba(255,120,120,'+p+')';g.fillRect(x+4,y-2,12,18);g.fillStyle='#f2c94c';g.fillRect(x+4,y-4,12,2);LT.push([x+10,y+8,'255,90,90',28]);}
const _h2=handleTileInteraction;handleTileInteraction=function(x,y){const z=player.zone,k=maps[z][y][x];if(k===7){const need=[0,3,5,7,9][z];if(player.level>=need){maps[z][y][x]=0;alert('🔓 封印之門開啟了！魔王就在前方');}else alert('🔒 封印之門：需要 Lv.'+need+'（目前 Lv.'+player.level+'）\n先擊敗地圖上的小怪鍛鍊吧！');return false;}return _h2(x,y);};
/* visible monsters + flee */
const MOBS={};let curMob=null,grace=0;
function spawnMobs(z){const m=maps[z],a=[];for(let n=0;a.length<5&&n<500;n++){const x=1+(Math.random()*18|0),y=1+(Math.random()*13|0);if(m[y][x]===0&&Math.abs(x-ENT[z][0])+Math.abs(y-ENT[z][1])>6&&!a.some(o=>o.x===x&&o.y===y))a.push({x,y,t:Math.random()*9});}MOBS[z]=a;}
function mobsDraw(r,t){const z=player.zone;(MOBS[z]||[]).forEach(o=>{if(o.y!==r)return;const x=o.x*T+2,y=o.y*T+4-Math.round((Math.sin(t/200+o.t)+1)/2);shadow(lg,x,y+14,16);drawPix(lg,MB[z],Object.assign({o:'#140d1d',e:'#fff',x:'#ffe08a'},MP[z]),x,y,1);});}
function checkEncounter(){if(document.querySelector('.modal.active')||performance.now()<grace)return;for(const o of MOBS[player.zone]||[])if(Math.abs(o.x-player.x)+Math.abs(o.y-player.y)<=1){curMob=o;alert('⚠️ 遭遇怪物！');startBattle(false);return;}}
setInterval(()=>{const z=player.zone;if(!z)return;if(!MOBS[z]||MOBS[z].length<3)spawnMobs(z);if(!document.querySelector('.modal.active')){MOBS[z].forEach(o=>{const d=[[1,0],[-1,0],[0,1],[0,-1]][Math.random()*4|0],nx=o.x+d[0],ny=o.y+d[1];if(maps[z][ny]&&maps[z][ny][nx]===0&&!(nx===player.x&&ny===player.y)&&!MOBS[z].some(p=>p.x===nx&&p.y===ny)){o.x=nx;o.y=ny;}});checkEncounter();}},650);
const _mp=movePlayer;movePlayer=function(dx,dy){_mp(dx,dy);checkEncounter();};
const fb=document.createElement('button');fb.id='flee';fb.innerText='🏃 逃跑';document.getElementById('battle-options').after(fb);
fb.onclick=function(){if(!activeEnemy)return;clearInterval(battleTimer);const boss=activeEnemy.bossIdx!==undefined;
if(boss||Math.random()<.7){document.getElementById('battle-modal').classList.remove('active');grace=performance.now()+4000;alert(boss?'🏃 你撤退了，等變強再來挑戰！':'🏃 成功逃跑！');}
else{const d=Math.max(8,activeEnemy.atk-player.def-player.armorDef);player.hp-=d;updateBattleBars();updateHUD();alert('逃跑失敗！受到 '+d+' 點傷害');if(player.hp<=0)setTimeout(loseBattle,300);else loadBattleQuestion();}};
const _s2=startBattle;startBattle=function(b){if(b)curMob=null;_s2(b);};
const _w4=winBattle;winBattle=function(){const o=curMob;curMob=null;_w4();if(o&&MOBS[player.zone])MOBS[player.zone]=MOBS[player.zone].filter(m=>m!==o);};
/* shop */
const SH=[['weapon','青銅短劍',12,40],['weapon','鋼鐵長劍',25,80],['weapon','戰斧',38,150],['weapon','精靈長弓',46,200],['weapon','元素神聖劍',55,260],['weapon','龍牙大劍',80,480],['armor','皮甲',8,30],['armor','騎士鎧甲',20,80],['armor','秘銀鎧甲',35,200],['armor','聖騎士鎧甲',55,400],['potion','大生命藥水',80,35],['potion','神癒藥水',9999,70]];
function renderShop(){const g=document.querySelector('#shop-modal .shop-grid');g.innerHTML='';SH.forEach(([k,n,v,c],i)=>{const d=document.createElement('div');d.className='shop-item';d.innerHTML='<div><div style="font-size:15px"><strong>'+n+'</strong>（'+(k==='weapon'?'+'+v+' 攻擊':k==='armor'?'+'+v+' 防禦':v>999?'完全恢復 HP':'恢復 '+v+' HP')+'）</div><div style="color:#f4d98e;font-size:14px">價格：'+c+' 金幣</div></div>';const b=document.createElement('button');b.innerText='購買';b.onclick=()=>buy(i);d.appendChild(b);g.appendChild(d);});document.querySelector('#shop-modal .dialog-header').innerText='⚒ 鐵匠武器舖　持有：'+player.gold+' 金幣';}
function buy(i){const [k,n,v,c]=SH[i];if(player.gold<c){alert('金幣不足！\n'+n+' 需要 '+c+' 金幣，目前只有 '+player.gold);return;}
if((k==='weapon'&&v<=player.weaponAtk)||(k==='armor'&&v<=player.armorDef)){alert('你已裝備更強的'+(k==='weapon'?'武器':'防具')+'，不需要購買。');return;}
player.gold-=c;if(k==='weapon')player.weaponAtk=v;else if(k==='armor')player.armorDef=v;else player.hp=Math.min(player.maxHp,player.hp+v);updateHUD();renderShop();alert('🛒 已購買【'+n+'】\n花費 '+c+' 金幣，剩餘 '+player.gold+' 金幣');}
/* cross-map quests */
function chain(n){triggerMiniQuiz(ok=>{const S=questState.sideQuests;if(!ok){showDialog('學者 Lilith',['可惜，再來一次吧！']);return;}if(n>1)chain(n-1);else{S.l=3;player.exp+=150;checkLevelUp();updateHUD();showDialog('學者 Lilith',['記憶術大成！經驗值 +150！']);updateQuestHUD();}});}
const _i2=interactNPC;interactNPC=function(role){const S=questState.sideQuests,G='老奶奶 Rose',M='衛隊長 Marcus',L='學者 Lilith',u=()=>updateQuestHUD();
if(role==='blacksmith')renderShop();
if(role==='healer'){player.hp=player.maxHp;updateHUD();showDialog('旅行的治療師',['辛苦了，讓我替你療傷。','（HP 已完全恢復）']);return;}
if(role==='explorer'&&S.g===1){S.g=2;showDialog('冰原探險家',['這是 Rose 婆婆的信？她還記得我愛吃的餅乾！','請回去告訴她，我一切安好。'],u);return;}
if(role==='nomad'&&S.l===1){S.l=2;showDialog('沙漠商人',['Lilith 的字典在我這裡，拿去吧！'],()=>{alert('📖 取得「古老字典」，回去找 Lilith');u();});return;}
if(role==='granny'){if(!S.g){S.g=1;showDialog(G,['孩子，能幫我把這封信送給遠在冰霜雪原（西方）的老朋友「冰原探險家」嗎？','路途遙遠，記得先鍛鍊再出發。'],()=>{alert('📜 任務：把信送到冰霜雪原的探險家');u();});}
else if(S.g===1)showDialog(G,['信還沒送到嗎？探險家在冰霜雪原的迷宮深處。']);
else if(S.g===2){S.g=3;player.gold+=100;player.exp+=60;checkLevelUp();updateHUD();showDialog(G,['他收到了？太好了！這是謝禮。'],()=>{alert('✅ 任務完成：100 金幣、60 經驗');u();});}
else showDialog(G,['祝福你的冒險一切順利！']);return;}
if(role==='marcus'){if(!S.m){S.m=1;kills=0;showDialog(M,['野外怪物太多了，幫我擊敗 5 隻，我有重賞！'],()=>{alert('📜 接受任務：擊敗 5 隻小怪');u();});}
else if(S.m===1){if(kills>=5){S.m=2;player.atk+=4;player.gold+=60;updateHUD();showDialog(M,['了不起！這塊磨刀石送你，攻擊力 +4！'],u);}else showDialog(M,['進度 '+kills+'/5，繼續加油！']);}
else showDialog(M,['有你在，村子安心多了。']);return;}
if(role==='scholar'){if(!S.l){S.l=1;showDialog(L,['我的古老字典被烈焰荒漠（東方）的流浪商人借走了⋯⋯','幫我取回，我就傳授你「記憶術」！'],()=>{alert('📜 任務：到烈焰荒漠找流浪商人');u();});}
else if(S.l===1)showDialog(L,['商人在烈焰荒漠的迷宮裡，請小心怪物。']);
else if(S.l===2)showDialog(L,['字典！太感謝了。來，連續答對 3 題證明你的實力！'],()=>chain(3));
else showDialog(L,['高頻單字：prominent 著名的；differentiate 區別！']);return;}
return _i2(role);};
updateQuestHUD=function(){_uq();const S=questState.sideQuests,d=[];if(S.g===1)d.push('送信到冰原');if(S.g===2)d.push('回報 Rose');if(S.l===1)d.push('找沙漠商人');if(S.l===2)d.push('回報 Lilith');if(S.m===1)d.push('小怪 '+Math.min(kills,5)+'/5');if(d.length)document.getElementById('quest-desc').innerText+='｜'+d.join('、');};
updateQuestHUD();
const _dr=draw;draw=function(t){try{_dr(t);}catch(e){console.error(e);requestAnimationFrame(draw);}};requestAnimationFrame(draw);
</script>
<script>
const NQ=[
["Lab-grown meat could cut greenhouse gas ____ from cattle farming.|emissions|n","Veggie burgers ____ the taste and texture of real meat.|imitate|v","Beans are a good source of ____, which helps build muscles.|protein|n","Too much ____ in the blood can lead to heart disease.|cholesterol|n","Cutting down all the forests is not a ____ way to use resources.|sustainable|a","Scientists turned stem cells into muscle ____ in the lab.|tissue|n","The soup is full of ____ that keep your body healthy.|nutrients|n","Vegetarian food can be a real ____ for a family with different diets.|blessing|n","If the meatballs thaw after purchase, do not ____ them.|refreeze|v","Many people feel lab-grown meat is ____ because it is not natural.|unnatural|a","The company plans to ____ plastic packaging completely.|eliminate|v","Packed with vitamins, the salad is both tasty and ____.|nutritious|a","Good chefs never ____ taste for convenience.|sacrifice|v","This sauce contains no artificial ____ or preservatives.|additives|n","____ materials are made in factories, not found in nature.|Synthetic|a","The police launched a thorough ____ into the case.|investigation|n","The ____ for meat rises as the world population grows.|demand|n","Changing people's old habits is an ____ challenge.|ongoing|a","The ____ of tofu is soft and smooth, unlike crunchy nuts.|texture|n","We must ____ the environment for future generations.|preserve|v","Many people are ____ about eating meat grown in a laboratory.|skeptical|a"],
["Memphis, Egypt, was the first ____ of ancient Egypt.|capital|n","The old factory was ____ into a modern art museum.|transformed|v","Rome has a ____ nightlife, with music on every street.|lively|a","The Eiffel Tower is a famous iron ____ in Paris.|structure|n","Of the two plans, I prefer the ____ one, which was presented last.|latter|a","The chef's cooking is truly ____; everyone asks for more.|exceptional|a","Corn is ____ modified to resist insects.|genetically|r","The drug is produced on a small ____ in the lab.|scale|n","Her social life has broadened ____ since she joined the club.|considerably|r","The two cities are similar, but there are some ____ between them.|differences|n","The river ____ through the old town and into the sea.|flows|v","Both cities have ____ buildings shaped like pyramids.|unique|a","The pyramids are ____ monuments in Egyptian history.|respected|a","The Paris Tower is only twenty meters ____, much shorter than the Eiffel Tower.|tall|a","The museum ____ ancient treasures from many dynasties.|displays|v","Many famous ____ are on display in the National Palace Museum.|masterpieces|n","The cyclist won the race and drew a large ____ of fans.|crowd|n","A ____ number of tourists visit the museum every year.|considerable|a","Antibiotics used on animals may create ____ dangerous to humans.|bacteria|n","Researchers are ____ the idea of growing meat from animal cells.|exploring|v","Cultured meat is a safer ____ to traditional meat.|alternative|n"],
["Flooding has been occurring at an increasing ____ in recent years.|rate|n","The city is slowly ____ because it was built on weak marshland.|sinking|v","Heavy rain ____ the village and destroyed many homes.|flooded|v","In winter, strong winds force a large ____ of water into the canals.|amount|n","Millions of tourists ____ to Venice every year.|flock|v","Scientists warn that rising sea ____ threaten coastal cities.|levels|n","The pumping program ____ caused the city to sink faster.|accidentally|r","Climate change is a ____ threat to low-lying islands.|serious|a","The ____ of the old buildings is damaged by salt water.|foundation|n","The government must ____ the problem before it gets worse.|confront|v","Bangladesh has a high population ____, with many people in a small area.|density|n","Drinking water containing heavy ____ like lead can harm health.|metals|n","The factory's waste led to water ____ in the river.|contamination|n","Ministries have been ____ to deal with the environmental crisis.|established|v","Taiwan passed the Greenhouse Gas ____ Act to cut emissions.|Reduction|n","The country relies heavily on ____ energy such as solar and wind.|renewable|a","Taiwan's lack of recognition leads to its ____ from international talks.|exclusion|n","Moving to cleaner energy is called an energy ____.|transition|n","The canals are the ____ of the city's transport system.|heart|n","The city is built on weak ____ and keeps sinking.|marshland|n","Local officials were slow to ____ to the crisis.|respond|v"],
["Martin Luther King, Jr., is a great ____ whose words changed history.|speaker|n","In the past, schools were ____ into white and black students.|segregated|a","They were ____ of their basic rights by unfair laws.|stripped|a","Beethoven composed his Fifth ____ even though he was almost deaf.|Symphony|n","Dry leaves caught fire, and bright ____ spread across the field.|flames|n","The ____ of the people fueled the civil rights movement.|determination|n","The Emancipation ____ freed enslaved people in 1863.|Proclamation|n","The crowd marched ____ to demand equal rights.|peacefully|r","King painted a ____ picture of a future without racism.|vivid|a","Black citizens suffered from police ____ for many years.|brutality|n","Many people were ____ for protesting against unfair laws.|arrested|v","The movement ____ millions of Americans to demand justice.|inspired|v","The army was forced to ____ from the French coast.|retreat|v","We shall never ____, no matter how hard the fight is.|surrender|v","Churchill promised to ____ the island whatever the cost.|defend|v","The Allies finally ____ after years of fighting.|prevailed|v","The cruel ruler's ____ made the people live in fear.|tyranny|n","The protesters held a ____ outside the courthouse every day.|rally|n","Eric Garner's death sparked ____ across the nation.|protests|n","People ____ for justice outside the police station.|chanted|v","The officer ____ to arrest him after an argument.|attempted|v"]];
const ALLA=[];NQ.forEach(a=>a.forEach(s=>{const p=s.split('|');ALLA.push([p[1],p[2]]);}));const bags={};
function zoneQs(z){const seen=new Set(),out=[],add=o=>{const k=o.w.toLowerCase();if(!seen.has(k)){seen.add(k);out.push(o);}};
BK[z-1].forEach(([s,a,x,y,w])=>add({s,w:a,o:[x,y,w]}));NQ[z-1].forEach(r=>{const [s,a,t]=r.split('|');add({s,w:a,t});});
vocabDB.forEach((v,i)=>{if(i%4===z-1)add({s:v.sentence.replace(/<span[^>]*>____<\/span>/,'____'),w:v.word,o:v.options.filter(x=>x!==v.word).slice(0,3)});});return out;}
function mkQ(q,z){let o=q.o;if(!o){const cap=q.w[0]!==q.w[0].toLowerCase();o=[...new Set(ALLA.filter(([w,t])=>t===q.t&&w.toLowerCase()!==q.w.toLowerCase()).map(r=>r[0]))].sort(()=>Math.random()-.5).slice(0,3).map(w=>cap?w[0].toUpperCase()+w.slice(1):w[0].toLowerCase()+w.slice(1));}
return{word:q.w,pos:z?LB[z-1]:'綜合複習',sentence:q.s.replace('____',"<span class='blank'>____</span>"),options:[q.w,...o]};}
function getNextQuestion(){const z=player.zone;let b=bags[z];if(!b||!b.length)b=bags[z]=(z?zoneQs(z):[1,2,3,4].flatMap(zoneQs)).sort(()=>Math.random()-.5);return mkQ(b.pop(),z);}
/* zone level hint */
const ZI=[null,'北方・風之森林\n建議等級 Lv.3–5（封印門需 Lv.3）','東方・烈焰荒漠\n建議等級 Lv.5–7（封印門需 Lv.5）','西方・冰霜雪原\n建議等級 Lv.7–9（封印門需 Lv.7）','南方・暗黑地牢\n建議等級 Lv.9–11（封印門需 Lv.9）'];
let zz=0;setInterval(()=>{if(player.zone!==zz){zz=player.zone;if(ZI[zz])alert('📍 '+ZI[zz]);}},200);
/* stronger bosses with elemental skills */
const _ce=createEnemy;createEnemy=function(b){const e=_ce(b);if(b){e.hp=e.maxHp=Math.round(e.hp*1.6);e.atk=Math.round(e.atk*1.3);}return e;};
const SK=[['疾風連斬','#7fe0a0','🌪️'],['龍焰息','#ff8a3c','🔥'],['冰之魔法・絕對零度','#9fd8ff','❄️'],['暗影審判','#b070ff','🌑']];
function fx(k,crit){const box=document.querySelector('#battle-modal .dialog-box'),f=document.createElement('div');f.className='fx';f.style.setProperty('--c',k[1]);let s='<div class="fxn">'+k[0]+'</div>'+(crit?'<div class="fxc">💥 CRITICAL HIT!</div>':'');for(let i=0;i<14;i++)s+='<span style="left:'+(Math.random()*95|0)+'%;animation-delay:'+(Math.random()*.5).toFixed(2)+'s">'+k[2]+'</span>';f.innerHTML=s;box.appendChild(f);setTimeout(()=>f.remove(),1500);const e=document.getElementById('player-portrait');e.classList.remove('shk');void e.offsetWidth;e.classList.add('shk');}
function bossHit(d){const e=activeEnemy;if(!e||e.bossIdx===undefined)return d;const crit=Math.random()<.25;fx(SK[e.bossIdx],crit);return Math.round(d*(crit?1.6:1));}
const _h3=handleAnswer;handleAnswer=function(s){const e=activeEnemy,ok=s===currentQuestion.word;_h3(s);if(ok&&e&&e.bossIdx!==undefined&&e.hp>0&&player.hp>0&&battleTurn%4===0)setTimeout(()=>{if(!document.getElementById('battle-modal').classList.contains('active'))return;const d=bossHit(Math.max(12,e.atk-player.def-player.armorDef));player.hp-=d;updateBattleBars();updateHUD();alert('⚠️ '+e.name+' 發動奇襲，造成 '+d+' 點傷害！');if(player.hp<=0)setTimeout(loseBattle,300);},700);};
const _s3=startBattle;startBattle=function(b){_s3(b);document.querySelector('.battle-arena').style.background=['','linear-gradient(#1b4a30,#0a1f14)','linear-gradient(#5a2a14,#1f0c06)','linear-gradient(#244a7a,#0a1630)','linear-gradient(#2a1450,#0a0518)'][player.zone]||'';};
/* portraits */
const PR=[[/英靈|騎士/,{sk:'#cfe4ff',hr:'#9ab',hl:'#fff',cl:'#5a7aa0',ac:'#9fd8ff',st:0,hat:2}],[/英雄|拯救/,{sk:'#f4cfa0',hr:'#6b3318',hl:'#9a5a2b',cl:'#c8362f',ac:'#f2c94c',st:0}],[/村長/,{sk:'#f0c8a0',hr:'#eee',hl:'#fff',cl:'#5a3a8a',ac:'#f2c94c',st:0,bd:1}],[/鐵匠/,{sk:'#d9a070',hr:'#3a2218',hl:'#5a3a28',cl:'#8a4a2a',ac:'#555',st:2,hat:5,bd:1}],[/農夫/,{sk:'#e8b98a',hr:'#b5651d',hl:'#d98a3a',cl:'#3f8f4a',ac:'#e8d28a',st:0,hat:1}],[/奶奶/,{sk:'#f4d8b8',hr:'#ccc',hl:'#fff',cl:'#a05a7a',ac:'#fff',st:3}],[/Marcus|衛隊/,{sk:'#e8b98a',hr:'#3a2a22',hl:'#5a4a42',cl:'#8a98b0',ac:'#d03030',st:0,hat:2}],[/Lilith|學者/,{sk:'#f4cfa0',hr:'#6a3b8a',hl:'#9a6aba',cl:'#4a3a8a',ac:'#f2c94c',st:1,gl:1}],[/隱士/,{sk:'#e8b98a',hr:'#4a6a3a',hl:'#6a8a5a',cl:'#2f6a3f',ac:'#9be08a',st:0,hat:3,bd:1}],[/商人/,{sk:'#c98a5a',hr:'#222',hl:'#444',cl:'#c9742a',ac:'#f2c94c',st:0,hat:4}],[/探險家/,{sk:'#f4cfa0',hr:'#cbb',hl:'#fff',cl:'#4a7ab0',ac:'#fff',st:0,hat:3}],[/治療/,{sk:'#f4cfa0',hr:'#d8c690',hl:'#fff',cl:'#e8e8f0',ac:'#d03030',st:1,hat:3}]];
function preset(n){const f=PR.find(r=>r[0].test(n));if(f)return f[1];const h=[...n].reduce((a,c)=>(a*31+c.charCodeAt(0))|0,7)>>>0;return{sk:'#f4cfa0',hr:['#e8d6a0','#3a2a22','#b5651d'][h%3],hl:'#ffffff88',cl:['#2f6fb5','#3f8f4a','#8a5a2b','#7a4a9a'][h%4],ac:'#f2c94c',st:h%2};}
function bust(g,W,p){const s=W/64,P=(x,y,w,h,c)=>{g.fillStyle=c;g.fillRect(x*s|0,y*s|0,Math.ceil(w*s),Math.ceil(h*s));};
const bg=g.createRadialGradient(W/2,W*.4,4,W/2,W/2,W*.75);bg.addColorStop(0,p.cl);bg.addColorStop(1,'#080c1e');g.fillStyle=bg;g.fillRect(0,0,W,W);P(0,0,64,64,'rgba(0,0,0,.4)');
if(p.st===1)P(12,14,40,46,p.hr);if(p.st===3)P(24,4,16,10,p.hr);
P(8,48,48,16,p.cl);P(8,48,48,3,'rgba(255,255,255,.3)');P(26,40,12,10,p.sk);P(26,44,12,4,'rgba(0,0,0,.18)');P(25,50,14,3,p.ac);P(31,52,2,12,'rgba(0,0,0,.25)');
P(18,14,28,32,p.sk);P(20,12,24,3,p.sk);P(18,40,28,6,'rgba(0,0,0,.1)');P(16,24,3,8,p.sk);P(45,24,3,8,p.sk);
P(22,28,7,5,'#fff');P(35,28,7,5,'#fff');P(25,28,4,5,'#3a5a9a');P(35,28,4,5,'#3a5a9a');P(26,29,2,3,'#111');P(36,29,2,3,'#111');P(26,29,1,1,'#fff');P(36,29,1,1,'#fff');P(22,27,7,1,'#1b1220');P(35,27,7,1,'#1b1220');
P(22,24,7,2,p.hr);P(35,24,7,2,p.hr);P(31,32,2,5,'rgba(0,0,0,.15)');P(28,39,8,1,'#b5604f');P(22,35,4,2,'rgba(255,120,120,.25)');P(38,35,4,2,'rgba(255,120,120,.25)');
if(p.st!==2){P(16,10,32,9,p.hr);P(15,14,5,14,p.hr);P(44,14,5,14,p.hr);P(20,17,22,3,p.hr);P(20,11,10,2,p.hl);P(34,13,8,2,p.hl);if(p.st===1){P(14,18,5,32,p.hr);P(45,18,5,32,p.hr);P(15,30,2,18,p.hl);}}
if(p.bd){P(20,38,24,10,p.hr);P(24,36,16,3,p.hr);P(24,40,16,2,p.hl);}
if(p.gl){g.strokeStyle='#f2c94c';g.lineWidth=s;g.strokeRect(21*s,27*s,9*s,7*s);g.strokeRect(34*s,27*s,9*s,7*s);P(30,30,4,1,'#f2c94c');}
const H=p.hat;if(H===1){P(8,10,48,4,'#e8d28a');P(18,2,28,10,'#d9b860');P(18,8,28,2,'#a0823a');}
if(H===2){P(15,4,34,14,'#a8b4c8');P(15,4,34,3,'#fff');P(30,0,4,6,p.ac);P(14,18,6,16,'#8896ac');P(44,18,6,16,'#8896ac');P(30,16,4,14,'#8896ac');}
if(H===3){P(12,6,40,16,p.cl);P(12,22,6,24,p.cl);P(46,22,6,24,p.cl);P(18,12,28,3,'rgba(0,0,0,.4)');}
if(H===4){P(14,4,36,14,p.cl);P(14,14,36,4,p.ac);P(40,6,8,12,p.ac);}
if(H===5){P(16,12,32,4,'#c8362f');P(46,14,8,3,'#c8362f');}}
dp.width=dp.height=128;
showDialog=function(n,t,c){_sd(n,t,c);bust(dp.getContext('2d'),128,preset(n));};
</script>
<script>
const DOCQ=[[{"s": "A successful marketing ____ requires understanding the target audience and delivering messages that appeal to their interests and needs.", "w": "strategy", "o": ["stability", "symmetry", "strain"]}, {"s": "It seems that poor families tend to have more children, which adds ____ to their financial burden, making them even poorer.", "w": "complexity", "o": ["simplicity", "commodity", "community"]}, {"s": "It is a basic economic principle that when supply ____s demand for a product or service, its price falls.", "w": "exceeds", "o": ["extends", "expects", "exhausts"]}, {"s": "When breeding season comes, salmon swim up the ____ and lay eggs at the head of the river.", "w": "stream", "o": ["steam", "stripe", "strain"]}, {"s": "First-time visitors to Victoria Falls National Park in Zimbabwe are often blown away by the amazing beauty and ____ of its famous giant waterfall.", "w": "majesty", "o": ["mystery", "majority", "misery"]}, {"s": "The beautiful actress’s ____ gown made her the center of attention at the awards ceremony.", "w": "gorgeous", "o": ["generous", "gigantic", "gloomy"]}, {"s": "The ____ spent months at sea, traveling through strong winds and high waves to reach his destination safely.", "w": "sailor", "o": ["sailing", "savior", "soldier"]}, {"s": "Tina realized that true happiness comes not from material ____ (possess) but from meaningful relationships and experiences.", "w": "possessions", "o": ["possessive", "possessing", "possessed"]}, {"s": "The athlete won a gold medal after displaying her ____ (exception) ice skating skills at the Olympic Games.", "w": "exceptional", "o": ["exception", "exceptionally", "excepted"]}, {"s": "To Gregory, East Asian people look so much alike that it is difficult for him to ____ (differ) between a Chinese person and a Japanese person.", "w": "differentiate", "o": ["different", "difference", "differently"]}, {"s": "Today, I want to talk to you about the similarities and differences between my home—Formosa, Argentina—and your home here, Taiwan, which is also ____ Formosa.", "w": "known as", "o": ["bound by", "filled with", "subjected to"]}, {"s": "They named it Formosa and decided to ____ there because of its extraordinary beauty.", "w": "settle", "o": ["differ", "impress", "soar"]}, {"s": "____ a globe, and you’ll notice another interesting fact: both Formosas lie on opposite sides of the world at about the same distance from the equator.", "w": "Take a look at", "o": ["Make up", "Make for", "Come across"]}, {"s": "Take a look at a globe, and you’ll notice another interesting fact: both Formosas lie on ____ sides of the world at about the same distance from the equator.", "w": "opposite", "o": ["mighty", "lush", "core"]}, {"s": "Therefore, if we dug a hole here in Taiwan straight down to the other side of the earth, we would end up ____ my home in Argentina!", "w": "in", "o": ["as", "by", "for"]}, {"s": "However, although they have many things ____, the two Formosas are quite different in many ways.", "w": "in common", "o": ["on purpose", "at risk", "out of control"]}, {"s": "For one, the area in Argentina is about ____ Taiwan.", "w": "twice the size of", "o": ["as twice as large", "the twice size of", "as twice large as"]}, {"s": "Also, Taiwan is a mountainous island covered in ____ forests, while my Formosa is fairly flat, covered with trees, grass, and marshes.", "w": "dense", "o": ["stark", "bustling", "plain"]}, {"s": "My Formosa’s economy, ____, relies primarily on agriculture, such as raising cattle and growing cotton and fruit.", "w": "by contrast", "o": ["after all", "for example", "in particular"]}, {"s": "____ their differences, both Formosas are beautiful places.", "w": "Despite", "o": ["Between", "Unlike", "Without"]}, {"s": "In places like Taipei and Taichung, many skyscrapers* ____ into the sky.", "w": "soar", "o": ["industrial", "stretches", "mountainous"]}, {"s": "In addition, the population ____ in the western part of the island is quite high, with around 90% of Taiwan’s people living there.", "w": "density", "o": ["differs", "soar", "mountainous"]}, {"s": "Geographically, the west coast is mostly a broad, flat plain that ____ almost the entire length of the island.", "w": "stretches", "o": ["lively", "be worlds apart", "industrial"]}, {"s": "The region is highly ____, with many factories and high-tech corporations based there.", "w": "industrial", "o": ["soar", "density", "differs"]}, {"s": "For example, Hsinchu Science Park, often referred to as Taiwan’s Silicon Valley, is a huge and ____ center of employment.", "w": "lively", "o": ["industrial", "mountainous", "stretches"]}, {"s": "The east coast, on the other hand, ____ from the west in that it is sparsely* populated.", "w": "differs", "o": ["be worlds apart", "stretches", "agricultural"]}, {"s": "Apart from that, the economy of the east relies more on ____ actitivies, with fruit and vegetable farms covering the countryside.", "w": "agricultural", "o": ["transportation", "be worlds apart", "soar"]}, {"s": "Finally, the east is very ____, with only a thin strip of flatland running between the central and coastal mountain ranges.", "w": "mountainous", "o": ["be worlds apart", "stretches", "density"]}, {"s": "There is thus not much room for a well-developed ____ system like the west coast’s High Speed Rail network.", "w": "transportation", "o": ["be worlds apart", "mountainous", "density"]}, {"s": "Despite the fact that they appear to ____, the east and west coasts of Taiwan are equally significant halves of the island.", "w": "be worlds apart", "o": ["transportation", "density", "industrial"]}], [{"s": "Overtourism occurs when a ____ number of visitors flock to a destination and cause negative effects on that place.", "w": "substantial", "o": ["sustainable", "subtle", "slender"]}, {"s": "The mother cried out in ____ when she learned that her only son had been killed in the car accident.", "w": "torment", "o": ["temper", "tournament", "torrent"]}, {"s": "In order to ____ interest in readers, some newspapers put sensational headlines on their front pages.", "w": "induce", "o": ["include", "inquire", "insure"]}, {"s": "Since the president had received a ____ report about the current situation, she was able to make a highly informed decision.", "w": "comprehensive", "o": ["competitive", "compulsory", "comparable"]}, {"s": "Being in its testing stage, the drug is only produced on a small ____ and used in treating only a limited number of patients.", "w": "scale", "o": ["scene", "skill", "shape"]}, {"s": "When COVID-19 broke out, our government donated large ____es of face masks to foreign countries to help them combat the pandemic.", "w": "quantities", "o": ["qualities", "quarters", "questions"]}, {"s": "Jill tried to stay calm, but her brother’s mocking tone ____d her into responding angrily.", "w": "provoked", "o": ["prohibited", "provided", "promoted"]}, {"s": "Never ____ (estimate) what you can do. If you set a goal and stick to it, you will be able to achieve it.", "w": "underestimate", "o": ["overestimate", "estimated", "estimation"]}, {"s": "Poor air quality is one reason why some people suffer from a nasal ____ (allergic).", "w": "allergy", "o": ["allergic", "allergen", "allergist"]}, {"s": "As a stranger in the city, I need information ____ (concern) the transportation system of this city.", "w": "concerning", "o": ["concerned", "concern", "concerns"]}, {"s": "You might soon be able to ____ out of a juicy burger that was grown in a laboratory instead of on a farm!", "w": "take a bite", "o": ["make a plan", "gain insight", "analyze data"]}, {"s": "Several start-ups have been working on a new process of meat production where they take animal stem cells and transform them into ____ fibers.", "w": "primitive", "o": ["allergic", "mental", "humble"]}, {"s": "Well, first of all, traditional meat production ____ the environment.", "w": "takes a heavy toll on", "o": ["takes a look at", "keeps an eye on", "puts an end to"]}, {"s": "Producing lab-grown meat means there is no need for ____.", "w": "livestock", "o": ["composition", "research", "infrastructure"]}, {"s": "This could eliminate slaughterhouses and the ____ treatment of animals.", "w": "unethical", "o": ["generous", "artificial", "lenient"]}, {"s": "____, we can enjoy our meat guilt-free, knowing that no animals were harmed in the process.", "w": "In other words", "o": ["By contrast", "To our surprise", "For a start"]}, {"s": "For example, some research suggests that the process of growing cell cultures may ____ because it requires significant amounts of energy and thus could lead to considerable gas emissions.", "w": "come at a price", "o": ["rest assured", "have them at heart", "jump to conclusions"]}, {"s": "In addition, some people still question ____ this type of meat is safe for humans to consume since it is “unnaturally” created, like genetically modified food.", "w": "whether", "o": ["what", "how", "where"]}, {"s": "Still, ____ lab-grown meat might not yet be on the table, it is exciting to think about it.", "w": "while", "o": ["since", "as", "if"]}, {"s": "This technology could mean a lot for ____ people in the future.", "w": "meat-loving", "o": ["loved-meat", "loving-meat", "meat-loved"]}, {"s": "The technological revolution continues to bring almost daily changes to the world. 3D printing is one of the more ____ innovations and is used in many applications.", "w": "recent", "o": ["concerns", "resulting in", "effects"]}, {"s": "One modern application, though, is worth ____: 3D-printed meat.", "w": "keeping an eye on", "o": ["jumping to conclusions", "tissue", "effects"]}, {"s": "This brand-new product has yet to have a(n) ____ impact on the meat industry, but it is certainly a trend worth following.", "w": "sustainable", "o": ["materials", "resulting in", "recent"]}, {"s": "The muscle, blood, and fat content of different cuts of meat ____ are analyzed.", "w": "tissue", "o": ["resulting in", "materials", "concerns"]}, {"s": "They are then reproduced* using plant-based ____ and 3D printers.", "w": "materials", "o": ["sustainable", "tissue", "effects"]}, {"s": "Unhealthy components like cholesterol* are completely eliminated, ____ a healthier piece of meat.", "w": "resulting in", "o": ["materials", "recent", "sustainable"]}, {"s": "Better still, meat can be produced without all the harmful environmental ____ of meat production.", "w": "effects", "o": ["jumping to conclusions", "support", "materials"]}, {"s": "However, there are still some ____ about this new product.", "w": "concerns", "o": ["resulting in", "tissue", "recent"]}, {"s": "While it is clear that 3D-printed meat leads to a healthier product, what is less clear is how much ____ for this kind of meat exists among consumers.", "w": "support", "o": ["effects", "keeping an eye on", "resulting in"]}, {"s": "That is, before ____ about the future of 3D-printed meat, we must remain cautious* about this trend.", "w": "jumping to conclusions", "o": ["effects", "support", "tissue"]}], [{"s": "The hikers set up their camp ____ a cliff because they wanted to stay sheltered from the strong winds.", "w": "beneath", "o": ["beyond", "besides", "between"]}, {"s": "New technology has ____ through the industry in recent years, changing not only how people work but also how they communicate with one another.", "w": "swept", "o": ["settled", "sustained", "surged"]}, {"s": "In the old days, port cities were the most prosperous ones because they were centers of ____. Businessmen gathered there to trade goods.", "w": "commerce", "o": ["racism", "memorial", "tide"]}, {"s": "The relaxing music created a ____ atmosphere, making people feel peaceful and calm.", "w": "tranquil", "o": ["hasty", "greasy", "sticky"]}, {"s": "When the actress announced her plan to marry a ____ banker, the names of several famous billionaires were tossed around.", "w": "prominent", "o": ["slender", "clumsy", "fragile"]}, {"s": "Despite some minor flaws in the new smartphone model, the ____ reviews have been positive.", "w": "overall", "o": ["overseas", "overtime", "overdue"]}, {"s": "The ____ of violent crime is lower in suburban areas than in urban centers.", "w": "incidence", "o": ["incident", "instance", "insistence"]}, {"s": "The ____ (accelerate) of the disease’s spread worldwide proved that it was more dangerous than scientists had first estimated.", "w": "acceleration", "o": ["accelerate", "accelerated", "accelerating"]}, {"s": "The company’s ____ (acknowledge) of the mistake was a necessary step toward regaining the trust of their customers.", "w": "acknowledgement", "o": ["acknowledge", "acknowledged", "acknowledging"]}, {"s": "This train station is not equipped with an ____ (elevation), so passengers need to carry their luggage upstairs and downstairs.", "w": "elevator", "o": ["elevation", "elevate", "elevated"]}, {"s": "It is a charming, historical city with many special features that ____ other tourist centers.", "w": "set it apart from", "o": ["make up", "result in", "keep an eye on"]}, {"s": "____, this topic is worthy of further discussion.", "w": "Therefore", "o": ["However", "Otherwise", "Additionally"]}, {"s": "To begin with, let’s ____ that these threats are real.", "w": "acknowledge", "o": ["shift", "curb", "elevate"]}, {"s": "The city’s flooding problem ____ two natural factors.", "w": "results from", "o": ["deals with", "consists of", "looks after"]}, {"s": "One is acqua alta, ____ literally means “high water.” In winter, high tides, strong winds, and waves usually force a(n) excessive amount of water into the city’s canals.", "w": "which", "o": ["that", "what", "where"]}, {"s": "One is acqua alta, which literally means “high water.” In winter, high tides, strong winds, and waves usually force a(n) ____ amount of water into the city’s canals.", "w": "excessive", "o": ["individual", "joint", "polar"]}, {"s": "The city has been sinking ____ because it was constructed on weak marshland.", "w": "over time", "o": ["at once", "under way", "for sake"]}, {"s": "A pumping program begun in the 1930s and intended to help sustain the city accidentally ____ the rate at which Venice was sinking.", "w": "accelerated", "o": ["melted", "swept", "confirmed"]}, {"s": "In addition, like the rest of the world, Venice must also ____ the realities of climate change.", "w": "contend with", "o": ["be far from", "take a toll on", "pose a threat to"]}, {"s": "While some environmentalists are making predictions as if Venice ____ hopeless, it is still not too late to save the city.", "w": "were", "o": ["is", "was", "has been"]}, {"s": "First came the hunting society, where people relied on their skills to ____ food from nature.", "w": "gather", "o": ["surges", "generations", "industrial"]}, {"s": "Next, there was a major ____ to an agricultural society.", "w": "shift", "o": ["generations", "participate", "industrial"]}, {"s": "People settled on farms and produced food to ____ themselves throughout the year.", "w": "sustain", "o": ["industrial", "rise", "surges"]}, {"s": "By the late 1700s, the ____ society began.", "w": "industrial", "o": ["participate", "rise", "shift"]}, {"s": "Cities saw great ____ in population as people left farms to take up factory work.", "w": "surges", "o": ["generations", "shift", "industrial"]}, {"s": "The ____ of industry also brought new social challenges and changes in daily life.", "w": "rise", "o": ["industrial", "vast", "shift"]}, {"s": "This has proven to be quite different from ____ societies, mainly because the processing and sharing of information has become a significant activity.", "w": "previous", "o": ["gather", "vast", "sustain"]}, {"s": "Access to information has enabled many people to ____ in economic activities and learn new skills.", "w": "participate", "o": ["industrial", "shift", "sustain"]}, {"s": "This new society will take advantage of the ____ amount of data made available by the Internet and AI.", "w": "vast", "o": ["shift", "rise", "surges"]}, {"s": "Its goal is to create an even better “human-centered society.” Future ____ will thus live in a smart world where social and environmental challenges are solved through analysis and technology.", "w": "generations", "o": ["sustain", "previous", "vast"]}], [{"s": "Nina’s ____ has suffered since she faked a college diploma to get a job. She now feels guilty every time she sees her boss.", "w": "conscience", "o": ["conscious", "consequence", "consensus"]}, {"s": "The captain’s ____ three-pointer in the final minutes of the basketball game led his team to victory.", "w": "decisive", "o": ["decision", "decide", "decisively"]}, {"s": "The sprinter was ____d in a car accident. He can no longer walk, let alone run.", "w": "crippled", "o": ["tripled", "crumpled", "cramped"]}, {"s": "In this paper, the researcher gave a broad ____ of the research the team has been doing.", "w": "outline", "o": ["outcome", "outlook", "outlet"]}, {"s": "When Ludwig van Beethoven composed one of his most famous musical works, ____ No. 5, he was almost completely deaf.", "w": "Symphony", "o": ["Sympathy", "Synthesis", "Syndrome"]}, {"s": "After working more than sixteen hours a day for a whole week, the engineer was suffering from ____ and desperately needed a good rest.", "w": "fatigue", "o": ["fraction", "shame", "density"]}, {"s": "Sparks flew and the dry leaves caught fire, and soon bright ____s spread across the field.", "w": "flames", "o": ["flaws", "flakes", "floods"]}, {"s": "Many people rely on ____ (mobility) devices to check email, browse the Internet, and stay connected throughout the day.", "w": "mobile", "o": ["mobility", "mobilize", "mobilization"]}, {"s": "In the past, the ____ (oppress) of women was manifested in the deprivation of their rights to receive education and to vote.", "w": "oppression", "o": ["oppress", "oppressive", "oppressed"]}, {"s": "After twenty years of ____ (captive), the criminal was released from prison.", "w": "captivity", "o": ["captive", "captivate", "captivating"]}, {"s": "His “I Have a Dream” speech has ____ as one of the most significant speeches of all time.", "w": "gone down in history", "o": ["ended up in jail", "made a name for himself", "set the stage for change"]}, {"s": "____ this speech at the Lincoln Memorial on August 28, 1963, the civil rights movement would not have gotten so much support in American society.", "w": "Had he not delivered", "o": ["He has not delivered", "He had not delivered", "Has he not delivered"]}, {"s": "Not only was the speech delivered with passion and ____, but it also moved a country to embrace racial equality.", "w": "eloquence", "o": ["robbery", "prosperity", "discrimination"]}, {"s": "King’s speech ____ to be one of the most important events in the history of the civil rights movement.", "w": "turned out", "o": ["called off", "shut down", "brought up"]}, {"s": "Giving the speech one hundred years after the Emancipation Proclamation, King requested his audience ____ how much real progress had been made.", "w": "to consider", "o": ["considering", "considers", "considered"]}, {"s": "He reminded them that much of America remained ____ into white communities and black communities.", "w": "segregated", "o": ["granted", "withered", "exiled"]}, {"s": "Black citizens, he argued, could never be satisfied when they were stripped of their ____ daily by laws and behaviors that treated them as unequal members of society.", "w": "dignity", "o": ["shame", "midst", "lodging"]}, {"s": "They could only be satisfied when they lived in a country ____ people would no longer be judged by their skin color.", "w": "where", "o": ["which", "when", "what"]}, {"s": "With the powerful phrase “I have a dream” punctuating* the beginning of each sentence, King painted a masterful picture of a racially united country which America could ____.", "w": "be transformed into", "o": ["ignore completely", "be torn apart", "become increasingly divided"]}, {"s": "At the end of the speech, he concluded by asking everyone to ____ to make this picture a reality.", "w": "join hands", "o": ["have yet", "come at a price", "jump to conclusions"]}, {"s": "On that day, Garner was approached by police officers on suspicion of selling ____ illegally.", "w": "cigarettes", "o": ["marches", "eloquent", "resisted"]}, {"s": "He complained about being bothered once again and accused the police of ____.", "w": "racism", "o": ["mobility", "memorial", "attempted"]}, {"s": "Angered, the police then ____ to arrest him.", "w": "attempted", "o": ["marches", "eloquent", "cigarettes"]}, {"s": "When Garner ____ arrest, the police pulled him to the ground.", "w": "resisted", "o": ["memorial", "attempted", "cigarettes"]}, {"s": "Trying to restrict Garner’s ____, one officer placed his arm around Garner’s neck.", "w": "mobility", "o": ["memorial", "racism", "vicious"]}, {"s": "The more Garner fought, the more ____ the attack became.", "w": "vicious", "o": ["attempted", "mobility", "racism"]}, {"s": "The ____ attack by the police was over, and Garner lay dead on the sidewalk.", "w": "shameful", "o": ["memorial", "attempted", "mobility"]}, {"s": "Protest ____ sprang up across the country.", "w": "marches", "o": ["mobility", "racism", "shameful"]}, {"s": "The Black Lives Matter movement exploded nationwide, and Garner’s three ____ words were printed on T-shirts.", "w": "eloquent", "o": ["attempted", "marches", "racism"]}, {"s": "“I Can’t Breathe” became a living ____ to Eric Garner.", "w": "memorial", "o": ["racism", "shameful", "attempted"]}]];const NZ=[1,0,2,3];
LB.splice(0,4,'第3課 Formosa: One Name, Two Stories','第4課 Lab-Grown Meat','第5課 Venice: A Sinking City','第6課 I Have a Dream');
function zoneQs(z){const seen=new Set(),ws=new Set(),out=[];DOCQ[z-1].forEach(o=>{if(!seen.has(o.s)){seen.add(o.s);ws.add(o.w.toLowerCase());out.push(o);}});
NQ[NZ[z-1]].forEach(r=>{const [s,a,t]=r.split('|');if(!ws.has(a.toLowerCase())&&!seen.has(s)){seen.add(s);out.push({s,w:a,t});}});return out.slice(0,40);}
for(const k in bags)delete bags[k];
</script>
<script>
NQ[1].splice(0,NQ[1].length,...["Taiwan and Argentina's Formosa lie at about the same distance from the ____.|equator|n", "Taiwan's mountains, valleys, rivers, and reefs are home to hundreds of unique ____.|species|n", "Argentina's Formosa raises ____ and grows cotton and fruit.|cattle|n", "The east coast is ____ populated, home to only about 10% of the island's people.|sparsely|r", "Hsinchu Science Park is an important ____ generator for the island.|revenue|n", "In Taipei and Taichung, many ____ rise into the sky.|skyscrapers|n", "The ____ tribes have settled mainly along the east coast.|aboriginal|a", "There is only a thin ____ of flatland between the central and coastal mountain ranges.|strip|n", "In-store prices and online prices are often ____, so you pay about the same.|interchangeable|a", "Prices are quite ____ across different stores, with only slight differences.|uniform|a", "One major ____ of Internet shopping is that you cannot examine products first.|drawback|n", "In a real store, you slowly ____ up and down the aisles.|browse|v", "Taiwan is covered in ____ green forests, while Formosa in Argentina is fairly flat.|lush|a"]);
ALLA.length=0;NQ.forEach(a=>a.forEach(s=>{const p=s.split('|');ALLA.push([p[1],p[2]]);}));for(const k in bags)delete bags[k];
/* combo, anti-spam, balance */
function comboMult(){const c=streak+1;return c<2?1:Math.min(1+(c-1)*.5,3);}
const CN=['','','二連擊！','三連擊！','四連擊！','五連擊・奧義！'];
function combo(c){fx([CN[Math.min(c,5)],'#ffd24a','⚔️'],false);const a=document.querySelectorAll('#battle-modal .fx'),d=document.createElement('div');d.className='fxc';d.style.color='#ffd24a';d.innerText='COMBO ×'+c+'　傷害 ×'+Math.min(1+(c-1)*.5,3);a[a.length-1].appendChild(d);}
let endLock=false,wlock=false;
const _h4=handleAnswer;handleAnswer=function(s){if(endLock||!document.getElementById('battle-modal').classList.contains('active'))return;const ok=s===currentQuestion.word,c=streak+1;_h4(s);if(ok&&c>=2)combo(c);if((activeEnemy&&activeEnemy.hp<=0)||player.hp<=0)endLock=true;};
const _w5=winBattle;winBattle=function(){if(wlock)return;wlock=true;_w5();};
const _l5=loseBattle;loseBattle=function(){if(wlock)return;wlock=true;_l5();};
const _s5=startBattle;startBattle=function(b){endLock=false;wlock=false;_s5(b);};
showDialog=function(n,t,c){let u=0;_sd(n,t,c&&(()=>{if(!u){u=1;c();}}));bust(dp.getContext('2d'),128,preset(n));};
const BSTAT=[[420,55,8,300,300],[650,68,14,450,450],[900,82,20,600,600],[1300,100,28,1000,1000]],MSTAT=[[70,30,4,40,55],[110,38,8,60,80],[160,46,12,80,110],[220,54,16,100,150]];
createEnemy=function(b){const e=_ce(b),z=player.zone-1,S=(b?BSTAT:MSTAT)[z],r=b?1:.9+Math.random()*.2;e.hp=e.maxHp=Math.round(S[0]*r);e.atk=Math.round(S[1]*r);e.def=S[2];e.gold=S[3];e.exp=S[4];return e;};
if(player.level===1&&player.exp===0){player.maxHp=60;player.hp=60;}updateHUD();
</script>
<script>
/* economy & difficulty */
SH.length=0;SH.push(['weapon','青銅短劍',12,60],['weapon','鋼鐵長劍',25,180],['weapon','戰斧',38,400],['weapon','精靈長弓',50,800],['weapon','元素神聖劍',70,1500],['weapon','龍牙大劍',95,2600],['weapon','星辰聖劍',125,4200],['weapon','神滅終焉劍',160,6500],['armor','皮甲',5,50],['armor','騎士鎧甲',12,200],['armor','秘銀鎧甲',22,600],['armor','聖騎士鎧甲',34,1400],['armor','龍鱗戰甲',48,2600],['armor','神話聖鎧',66,4500],['potion','藥水',40,25],['potion','大生命藥水',80,60],['potion','神癒藥水',9999,150]);
MSTAT.splice(0,4,[110,40,4,45,55],[190,58,10,70,80],[270,76,16,95,110],[360,96,22,120,150]);
BSTAT.splice(0,4,[700,75,14,300,300],[1000,92,22,450,450],[1400,112,30,600,600],[2000,135,40,1000,1000]);
if(player.level===1&&player.exp===0){player.gold=120;updateHUD();}
/* cross-map quests */
const B={};const _bu=buy;buy=function(i){const g=player.gold;_bu(i);if(player.gold<g)B[SH[i][1]]=(B[SH[i][1]]||0)+1;};
function rw(g,e){player.gold+=g;player.exp+=e;checkLevelUp();updateHUD();}
const _i3=interactNPC;interactNPC=function(role){const S=questState.sideQuests,u=()=>updateQuestHUD(),D=(n,l,c)=>showDialog(n,l,c);
if(role==='healer'){player.hp=Math.min(player.maxHp,player.hp+30);updateHUD();D('旅行的醫者',['我只能做簡單的包紮。（HP +30）','想完全恢復的話，請回村莊鐵匠鋪購買藥水。']);return;}
if(role==='blacksmith'){if(!S.b){S.b=1;D('鐵匠 Brock',['我的鍛造錘被北方風之森林的隱士借走了！','能幫我取回來嗎？我會付酬勞。'],()=>{alert('📜 任務：到風之森林找隱士取回鍛造錘');u();});return;}
if(S.b===2){S.b=3;D('鐵匠 Brock',['就是這把錘子！這是酬勞。'],()=>{rw(250,100);alert('✅ 任務完成：250 金幣、100 經驗');u();});return;}}
if(role==='hermit'){const L=[];if(S.b===1){S.b=2;L.push('Brock 的鍛造錘？在這裡，請帶給他。');alert('🔨 取得「鍛造錘」，帶回村莊給 Brock');}
if(!S.h){S.h=1;S.hb=B['大生命藥水']||0;L.push('我的傷還沒好⋯⋯能幫我到村莊鐵匠鋪買一瓶「大生命藥水」嗎？');alert('📜 任務：買「大生命藥水」給森林隱士');}
else if(S.h===1&&(B['大生命藥水']||0)>S.hb){S.h=2;rw(200,90);L.push('藥水！你救了我，這是謝禮（200 金幣、90 經驗）。');}
else if(S.h===1)L.push('藥水買到了嗎？記得是「大生命藥水」。');
if(!L.length)L.push(LORE.hermit[1]);D('森林隱士',L,u);return;}
if(role==='nomad'){const L=[];if(S.l===1){S.l=2;L.push('Lilith 的字典在我這裡，拿去吧！');alert('📖 取得「古老字典」，回去找 Lilith');}
if(!S.n){S.n=1;S.nb=B['皮甲']||0;L.push('另外，我想要一件皮甲抵擋沙暴，能幫我在村莊鐵匠鋪買嗎？');alert('📜 任務：買「皮甲」給沙漠商人');}
else if(S.n===1&&(B['皮甲']||0)>S.nb){S.n=2;rw(220,100);L.push('皮甲！太感謝了，這是酬謝（220 金幣、100 經驗）。');}
else if(S.n===1)L.push('皮甲還沒買到嗎？村莊鐵匠鋪有賣。');
if(!L.length)L.push(LORE.nomad[1]);D('沙漠商人',L,u);return;}
if(role==='explorer'&&(S.g===1||S.r===1)){const L=[];if(S.g===1){S.g=2;L.push('這是 Rose 婆婆的信？她還記得我愛吃的餅乾！','請回去告訴她，我一切安好。');}
if(S.r===1){S.r=2;L.push('你是為英靈找戒指的人嗎？這枚戒指是我在雪原撿到的，請交還給他。');alert('💍 取得「遺失的戒指」，帶回暗黑地牢');}D('冰原探險家',L,u);return;}
if(role==='spirit'){if(!S.r){S.r=1;D('聖騎士英靈',['我生前的戒指遺失在冰霜雪原⋯⋯','請向冰原探險家打聽，帶回來給我。'],()=>{alert('📜 任務：向冰原探險家尋找英靈的戒指');u();});return;}
if(S.r===1){D('聖騎士英靈',['戒指還沒找到嗎？去冰霜雪原問探險家。']);return;}
if(S.r===2){S.r=3;D('聖騎士英靈',['這是我的戒指！願它的力量與你同在。'],()=>{rw(300,120);alert('✅ 任務完成：300 金幣、120 經驗');u();});return;}}
return _i3(role);};
const _uq2=updateQuestHUD;updateQuestHUD=function(){_uq2();const S=questState.sideQuests,d=[];if(S.b===1)d.push('到森林取鍛造錘');if(S.b===2)d.push('錘子帶回 Brock');if(S.h===1)d.push('買大生命藥水給隱士');if(S.n===1)d.push('買皮甲給商人');if(S.r===1)d.push('向冰原探險家要戒指');if(S.r===2)d.push('戒指交還英靈');if(d.length)document.getElementById('quest-desc').innerText+='｜'+d.join('、');};
addEventListener('keydown',e=>{if((e.key==='q'||e.key==='Q')&&!document.querySelector('.modal.active')){const S=questState.sideQuests,x=(v,a)=>a[Math.min(v||0,a.length-1)],n=questState.bossesDefeated.filter(Boolean).length;
showDialog('任務日誌',['主線：奪回四顆元素水晶（'+n+'/4）　寶箱：'+chests+'/10\n送信 Rose→冰原探險家：'+x(S.g,['未接','進行中','回報 Rose','完成'])+'\n找字典 Lilith→沙漠商人：'+x(S.l,['未接','找商人','回報 Lilith','完成'])+'\n討伐 Marcus：'+x(S.m,['未接','進行中 '+Math.min(kills,5)+'/5','完成'])+'\n找鍛造錘 Brock→森林隱士：'+x(S.b,['未接','進行中','回報 Brock','完成'])+'\n買藥水→森林隱士：'+x(S.h,['未接','進行中','完成'])+'\n買皮甲→沙漠商人：'+x(S.n,['未接','進行中','完成'])+'\n找戒指 英靈→冰原探險家：'+x(S.r,['未接','進行中','回報英靈','完成'])]);}});
/* BGM */
const TK={v:{bpm:96,root:60,sc:[0,2,4,5,7,9,11],pr:[0,4,5,3],ld:'triangle',arp:1,perc:0,mel:[4,-1,5,7,9,-1,7,5,4,-1,2,4,5,-1,-1,-1,5,-1,7,9,11,-1,9,7,8,-1,7,5,4,-1,-1,-1]},
f:{bpm:84,root:62,sc:[0,2,3,5,7,9,10],pr:[0,5,3,4],ld:'triangle',arp:1,perc:0,mel:[2,-1,4,-1,6,5,4,-1,3,-1,2,-1,0,-1,-1,-1,4,-1,6,-1,8,7,6,-1,5,-1,4,2,3,-1,-1,-1]},
d:{bpm:104,root:62,sc:[0,1,4,5,7,8,10],pr:[0,1,0,4],ld:'sawtooth',arp:1,perc:1,mel:[7,-1,8,7,4,-1,3,4,7,-1,8,9,8,-1,7,-1,7,-1,8,7,4,-1,3,2,3,-1,4,-1,0,-1,-1,-1]},
i:{bpm:72,root:67,sc:[0,2,4,6,7,9,11],pr:[0,1,0,4],ld:'sine',arp:1,perc:0,mel:[9,-1,-1,11,12,-1,-1,11,9,-1,-1,7,9,-1,-1,-1,11,-1,-1,12,14,-1,-1,12,11,-1,9,-1,7,-1,-1,-1]},
g:{bpm:66,root:57,sc:[0,2,3,5,7,8,10],pr:[0,5,3,4],ld:'sawtooth',arp:0,perc:0,mel:[0,-1,-1,-1,2,-1,3,-1,4,-1,-1,-1,3,-1,2,-1,0,-1,-1,-1,2,-1,3,-1,5,-1,4,-1,2,-1,-1,-1]},
b:{bpm:150,root:57,sc:[0,2,3,5,7,8,11],pr:[0,3,4,0],ld:'square',arp:1,perc:1,mel:[7,7,-1,7,9,7,-1,5,7,7,-1,7,10,9,7,-1,8,8,-1,8,9,8,-1,7,5,-1,7,-1,4,-1,5,-1]},
x:{bpm:168,root:62,sc:[0,2,3,5,7,8,11],pr:[0,5,3,4],ld:'sawtooth',arp:1,perc:2,mel:[7,-1,8,7,5,-1,4,5,7,-1,8,9,10,9,8,7,7,-1,8,7,5,-1,4,2,4,-1,5,-1,0,-1,0,-1]}};
const BG={ac:null,tr:null,cur:'',st:0,next:0};
const mf=m=>440*2**((m-69)/12),nd=(T,d)=>T.root+T.sc[((d%7)+7)%7]+12*Math.floor(d/7);
function bgmInit(){if(BG.ac){BG.ac.resume();return;}const ac=BG.ac=new(window.AudioContext||window.webkitAudioContext)();BG.m=ac.createGain();BG.m.gain.value=0;BG.m.connect(ac.destination);
const d=ac.createDelay(1);d.delayTime.value=.32;const fb=ac.createGain();fb.gain.value=.32,lp=ac.createBiquadFilter();lp.frequency.value=2200;d.connect(lp);lp.connect(fb);fb.connect(d);d.connect(BG.m);BG.fx=ac.createGain();BG.fx.gain.value=.5;BG.fx.connect(d);
BG.nz=ac.createBuffer(1,ac.sampleRate/2,ac.sampleRate);const a=BG.nz.getChannelData(0);for(let i=0;i<a.length;i++)a[i]=Math.random()*2-1;}
function tone(f,t,d,ty,v,at,send){const o=BG.ac.createOscillator(),g=BG.ac.createGain();o.type=ty;o.frequency.value=f;g.gain.setValueAtTime(0,t);g.gain.linearRampToValueAtTime(v,t+at);g.gain.exponentialRampToValueAtTime(.0001,t+d);o.connect(g);g.connect(BG.m);if(send)g.connect(BG.fx);o.start(t);o.stop(t+d+.05);}
function hat(t,v){const s=BG.ac.createBufferSource(),g=BG.ac.createGain(),h=BG.ac.createBiquadFilter();s.buffer=BG.nz;h.type='highpass';h.frequency.value=7000;g.gain.setValueAtTime(.05*(v||1),t);g.gain.exponentialRampToValueAtTime(.001,t+.05);s.connect(h);h.connect(g);g.connect(BG.m);s.start(t);s.stop(t+.06);}
function kick(t){const o=BG.ac.createOscillator(),g=BG.ac.createGain();o.frequency.setValueAtTime(120,t);o.frequency.exponentialRampToValueAtTime(40,t+.12);g.gain.setValueAtTime(.22,t);g.gain.exponentialRampToValueAtTime(.001,t+.14);o.connect(g);g.connect(BG.m);o.start(t);o.stop(t+.15);}
function bgmStep(t,s){const T=BG.tr,sd=60/T.bpm/2,bar=(s>>3)%4,b=s%8,c=T.pr[bar];
if(b===0){[0,2,4].forEach(k=>tone(mf(nd(T,c+k)-12),t,sd*8,'triangle',.05,.25,1));tone(mf(nd(T,c)-24),t,sd*4,'sine',.14,.02);}if(b===4)tone(mf(nd(T,c)-24),t,sd*3,'sine',.12,.02);
if(T.arp)tone(mf(nd(T,c+[0,2,4,7,4,2,4,2][b])),t,sd*1.6,'triangle',.05,.01,1);
const m=T.mel[s%32];if(m>=0)tone(mf(nd(T,m)),t,sd*1.8,T.ld,T.ld==='sawtooth'||T.ld==='square'?.04:.08,.03,1);
if(T.perc){if(b%2===1)hat(t);if(b%4===0)kick(t);if(T.perc>1&&b===4)hat(t,2);}}
setInterval(()=>{if(!BG.ac)return;BG.m.gain.setTargetAtTime(soundEnabled?.5:0,BG.ac.currentTime,.1);
const bm=document.getElementById('battle-modal').classList.contains('active'),w=bm?(activeEnemy&&activeEnemy.bossIdx!==undefined?'x':'b'):'vfdig'[player.zone];
if(w!==BG.cur){BG.cur=w;BG.tr=TK[w];BG.st=0;BG.next=BG.ac.currentTime+.1;}
if(BG.tr)while(BG.next<BG.ac.currentTime+.25){bgmStep(BG.next,BG.st);BG.next+=60/BG.tr.bpm/2;BG.st++;}},80);
['keydown','click','touchstart'].forEach(ev=>addEventListener(ev,bgmInit));
</script>
<script>
buy=function(i){const [k,n,v,c]=SH[i],S=questState.sideQuests,q=(n==='皮甲'&&S.n===1)||(n==='大生命藥水'&&S.h===1);
if(player.gold<c){alert('金幣不足！\n'+n+' 需要 '+c+' 金幣，目前只有 '+player.gold);return;}
const weak=(k==='weapon'&&v<=player.weaponAtk)||(k==='armor'&&v<=player.armorDef);
if(weak&&!q){alert('你已裝備更強的'+(k==='weapon'?'武器':'防具')+'，不需要購買。');return;}
player.gold-=c;if(!weak){if(k==='weapon')player.weaponAtk=v;else if(k==='armor')player.armorDef=v;}
if(k==='potion')INV[n]=(INV[n]||0)+1;
B[n]=(B[n]||0)+1;updateHUD();renderShop();
alert('🛒 已購買【'+n+'】'+(weak?'（任務物品，不會取代目前裝備）':'')+'\n花費 '+c+' 金幣，剩餘 '+player.gold+' 金幣');};
</script>
<script>
bgmInit();let ui=0;const unlock=()=>{ui=1;if(BG.ac.state!=='running'){BG.ac.resume();try{const s=BG.ac.createBufferSource();s.buffer=BG.ac.createBuffer(1,1,22050);s.connect(BG.ac.destination);s.start(0);}catch(e){}}};
['pointerdown','mousedown','keydown','click','touchstart','touchend'].forEach(ev=>addEventListener(ev,unlock,true));
setInterval(()=>{if(ui&&BG.ac.state==='suspended')BG.ac.resume();},300);
setTimeout(()=>{if(BG.ac.state!=='running')alert('🎵 按任意鍵或點擊畫面即可開啟音樂');},1200);
</script>
<script>
BSTAT.splice(0,4,[500,62,12,300,300],[750,76,18,450,450],[1050,92,26,600,600],[1500,110,34,1000,1000]);
const INV={'藥水':2,'大生命藥水':0,'神癒藥水':0},PV=[['藥水',40],['大生命藥水',80],['神癒藥水',9999]];
const potN=()=>Object.values(INV).reduce((a,b)=>a+b,0);
function usePot(){const miss=player.maxHp-player.hp,o=PV.filter(p=>INV[p[0]]>0);if(!o.length){alert('背包沒有藥水！請回村莊鐵匠鋪購買。');return false;}if(miss<=0){alert('HP 已滿，不需要使用藥水。');return false;}const p=o.find(x=>x[1]>=miss)||o[o.length-1],b=player.hp;INV[p[0]]--;player.hp=Math.min(player.maxHp,player.hp+p[1]);updateHUD();alert('🧪 使用【'+p[0]+'】，HP '+b+' → '+player.hp+'（剩餘 '+INV[p[0]]+' 瓶）');return true;}
const pb=document.createElement('button');pb.id='potion-btn';pb.style.cssText='margin-top:10px;float:right;margin-left:8px';fb.before(pb);
pb.onclick=()=>{if(!activeEnemy||endLock)return;const t=timeLeft;clearInterval(battleTimer);if(usePot()){updateBattleBars();loadBattleQuestion();}else{startTimer();timeLeft=t;}};
setInterval(()=>{pb.innerText='🧪 藥水 ('+potN()+')';},300);
addEventListener('keydown',e=>{if((e.key==='h'||e.key==='H')&&!document.querySelector('.modal.active'))usePot();});
const _rs=renderShop;renderShop=function(){_rs();const hd=document.querySelector('#shop-modal .dialog-header');hd.innerText+='　背包藥水：'+potN()+'（按 H 在地圖上使用）';};
</script>
</body>
</html>
