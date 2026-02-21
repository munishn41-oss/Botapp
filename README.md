# Botapp
Мини игра кликер
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>ЗВОНОК ИЗ ТЬМЫ · TG</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
            font-family: 'Courier New', monospace;
        }

        body {
            min-height: 100vh;
            background: #050304;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 8px;
        }

        /* ТЕЛЕФОН */
        .phone {
            max-width: 380px;
            width: 100%;
            aspect-ratio: 9 / 18;
            background: radial-gradient(circle at 30% 30%, #2f262d, #0c080b);
            border-radius: 54px 54px 54px 54px;
            box-shadow: 
                0 30px 40px black,
                inset 0 -6px 10px #000,
                inset 0 6px 12px #ffccf050,
                0 0 0 4px #523e4b,
                0 0 0 9px #1d141b;
            padding: 20px 14px 25px 14px;
            position: relative;
        }

        .speaker {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 18px;
        }
        .speaker span {
            width: 14px;
            height: 14px;
            background: #352b33;
            border-radius: 30px;
            box-shadow: inset 0 -3px 5px black, 0 0 10px #d69ec9;
            border: 1px solid #ad84a0;
        }

        .led {
            width: 14px;
            height: 14px;
            background: #5f0033;
            border-radius: 50%;
            position: absolute;
            top: 32px;
            right: 32px;
            box-shadow: 0 0 15px #ff3399;
            animation: ledBlink 1.8s infinite;
        }
        @keyframes ledBlink {
            0% { opacity: 0.3; background: #440022; }
            50% { opacity: 1; background: #ff0066; box-shadow: 0 0 25px #ff66b2; }
            100% { opacity: 0.3; background: #440022; }
        }

        /* ЭКРАН */
        .screen {
            width: 100%;
            height: 68%;
            background: #140f14;
            border-radius: 32px 32px 24px 24px;
            padding: 12px 10px;
            box-shadow: 
                inset 0 0 40px black,
                0 0 0 3px #825d74,
                0 0 30px #ff80bf60;
            border: 1px solid #be90b0;
            position: relative;
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }

        .screen::before {
            content: "";
            position: absolute;
            inset: 0;
            background: repeating-linear-gradient(0deg, rgba(255,200,255,0.02) 0px, rgba(0,0,0,0.05) 2px, transparent 4px);
            pointer-events: none;
            z-index: 5;
        }

        .story-window {
            flex: 1;
            background: #0e070c;
            border: 3px solid #7f5270;
            border-radius: 20px 20px 14px 14px;
            padding: 20px 16px;
            color: #ffe4f2;
            text-shadow: 0 0 8px #ff66b2, 0 0 18px #b30070;
            font-size: 17px;
            font-weight: bold;
            line-height: 1.5;
            box-shadow: inset 0 0 30px #39022b;
            overflow-y: auto;
            word-break: break-word;
            scrollbar-width: thin;
            scrollbar-color: #f09bd8 #2c1424;
            z-index: 10;
        }

        /* КНОПКИ */
        .controls {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 12px;
            margin-top: 20px;
            margin-bottom: 8px;
        }

        .ctrl-btn {
            background: #362a34;
            border: none;
            padding: 18px 16px;
            border-radius: 80px;
            font-size: 24px;
            font-weight: bold;
            color: #ffe1f5;
            text-shadow: 0 0 12px #ffaae6;
            box-shadow: 
                0 7px 0 #1f121b,
                0 14px 25px black,
                inset 0 2px 18px #ffc0f0;
            flex: 1 1 120px;
            min-width: 120px;
            touch-action: manipulation;
            cursor: pointer;
            border: 1px solid #ca9bbb;
            transition: 0.05s linear;
        }

        .ctrl-btn:active {
            transform: translateY(7px);
            box-shadow: 
                0 2px 0 #1f121b,
                0 8px 18px black,
                inset 0 2px 12px #ffb6e6;
            background: #4f3147;
        }

        #btnCall {
            flex: 0.8 1 180px;
            background: #4e2f45;
            font-size: 26px;
            border-color: #ecb6db;
        }

        /* МЕНЮ */
        .menu-overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.95);
            backdrop-filter: blur(6px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.3s;
            padding: 20px;
        }

        .menu-card {
            max-width: 340px;
            width: 100%;
            background: #1f141f;
            border: 4px solid #c67eaf;
            border-radius: 60px;
            padding: 34px 24px;
            box-shadow: 0 0 70px #ff44bb, inset 0 0 40px #3e002b;
            text-align: center;
        }

        .menu-title {
            font-size: 44px;
            color: #ffbfe6;
            text-shadow: 0 0 25px #ff00aa;
            margin-bottom: 35px;
            font-weight: bold;
        }

        .menu-btn {
            background: #332530;
            border: 3px solid #d89ec6;
            color: #ffd9f2;
            font-size: 32px;
            padding: 20px 12px;
            margin: 20px 0;
            border-radius: 80px;
            box-shadow: 0 7px 0 #472f40, 0 15px 30px black;
            touch-action: manipulation;
            cursor: pointer;
            font-weight: bold;
        }

        .menu-btn:active {
            transform: translateY(7px);
            box-shadow: 0 2px 0 #472f40, 0 10px 25px black;
        }

        .volume-slider {
            width: 100%;
            margin: 20px 0;
        }
        .volume-slider input {
            width: 100%;
            height: 14px;
            background: #5b3851;
            border-radius: 20px;
            accent-color: #ff77b0;
        }

        .crack {
            position: absolute;
            top: 15%; left: 12%;
            width: 40%; height: 7%;
            border-left: 3px solid #ffc0e070;
            transform: rotate(20deg);
            filter: blur(2px);
            pointer-events: none;
        }
        .crack2 {
            top: 70%; left: 50%;
            transform: rotate(-35deg);
            border-bottom: 3px dashed #ffb0da90;
        }
    </style>
</head>
<body>
    <div class="menu-overlay" id="menuOverlay">
        <div class="menu-card">
            <div class="menu-title">ТРУБКА</div>
            <div class="menu-btn" id="menuStart">▶ НАЧАТЬ</div>
            <div class="menu-btn" id="menuSoundToggle">🔊 ЗВУК ВКЛ</div>
            <div class="volume-slider">
                <input type="range" id="volumeSlider" min="0" max="1" step="0.05" value="0.5">
            </div>
            <div style="color:#b388a8; margin-top:15px;">Telegram хоррор</div>
        </div>
    </div>

    <div class="phone">
        <div class="speaker">
            <span></span><span></span><span></span><span></span>
        </div>
        <div class="led"></div>

        <div class="screen">
            <div class="story-window" id="storyText">
                Нажми СТАРТ...
            </div>
            <div class="crack"></div>
            <div class="crack crack2"></div>
        </div>

        <div class="controls">
            <button class="ctrl-btn" id="btnLeft">⬅️ ТЬМА</button>
            <button class="ctrl-btn" id="btnRight">СВЕТ ➡️</button>
        </div>
        <div style="display: flex; justify-content: center;">
            <button class="ctrl-btn" id="btnCall">📞 ЗВОНИТЬ</button>
        </div>
    </div>

    <script>
        // Telegram WebApp API
        const tg = window.Telegram.WebApp;
        tg.expand(); // Разворачиваем на весь экран
        tg.enableClosingConfirmation(); // Подтверждение при закрытии
        
        // Устанавливаем тему Telegram
        document.body.style.background = tg.backgroundColor;
        
        // ========== ПОЛНЫЙ СЮЖЕТ ==========
        const game = {
            chapter: 1,
            gameOver: false,
            finalEnding: false,
            storyFlags: {}
        };

        const chapterText = {
            1: {
                desc: "ГЛАВА 1: ТИШИНА ПЕРЕД ЗВОНКОМ\nТы находишь старый телефон на чердаке.",
                left: "Тьма сгущается. Ты слышишь шаги за спиной.",
                right: "Свет падает на альбом. Фото размыто, но там ТЫ.",
                call: "Ты набираешь номер... 'Приходи в подвал'."
            },
            2: {
                desc: "ГЛАВА 2: ГОЛОСА ИЗ СТЕН\nТы в подвале. Экран мерцает.",
                left: "Зеркало. Твоё отражение улыбается.",
                right: "Радио: 'Осталось 10 минут'.",
                call: "В ответ детский плач."
            },
            3: {
                desc: "ГЛАВА 3: ПОСЛЕДНИЙ ЗВОНОК\nВыбери судьбу.",
                left: "ТЬМА поглощает тебя. (ПЛОХАЯ КОНЦОВКА)",
                right: "СВЕТ ведёт к выходу. Ты почти спасён...",
                call: "ЗВОНОК: 'Ты уже мёртв'."
            }
        };

        const endings = {
            bad: '💀 ПЛОХАЯ КОНЦОВКА: Ты остаёшься в подвале навсегда.',
            good: '✨ ХОРОШАЯ КОНЦОВКА: Ты выбегаешь на улицу. Ты спасён.',
            secret: '📞 ТАЙНАЯ КОНЦОВКА: Ты стал голосом в трубке.'
        };

        // Элементы
        const storyDiv = document.getElementById('storyText');
        const btnLeft = document.getElementById('btnLeft');
        const btnRight = document.getElementById('btnRight');
        const btnCall = document.getElementById('btnCall');
        const menuOverlay = document.getElementById('menuOverlay');
        const menuStart = document.getElementById('menuStart');
        const menuSoundToggle = document.getElementById('menuSoundToggle');
        const volumeSlider = document.getElementById('volumeSlider');

        // ========== ЗВУК ==========
        let audioCtx = null;
        let soundEnabled = true;
        let masterVolume = 0.5;

        function initAudio() {
            if (audioCtx) return audioCtx;
            try {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            } catch (e) {
                return null;
            }
            return audioCtx;
        }

        function playSound(type) {
            if (!soundEnabled) return;
            const ctx = initAudio();
            if (!ctx) return;
            if (ctx.state === 'suspended') ctx.resume();

            const now = ctx.currentTime;
            const vol = masterVolume;

            if (type === 'scream') {
                const osc = ctx.createOscillator();
                const gain = ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.value = 800 + Math.random() * 500;
                gain.gain.setValueAtTime(vol * 0.5, now);
                gain.gain.exponentialRampToValueAtTime(0.001, now + 0.7);
                osc.connect(gain);
                gain.connect(ctx.destination);
                osc.start(now);
                osc.stop(now + 0.7);
                
                // Отправляем данные в Telegram (для статистики)
                tg.sendData(JSON.stringify({event: 'scream', chapter: game.chapter}));
                return;
            }
            
            if (type === 'ring') {
                for (let i = 0; i < 3; i++) {
                    const osc = ctx.createOscillator();
                    const gain = ctx.createGain();
                    osc.type = 'square';
                    osc.frequency.value = 700 + i * 50;
                    gain.gain.setValueAtTime(0, now + i * 0.4);
                    gain.gain.linearRampToValueAtTime(vol * 0.2, now + i * 0.4 + 0.03);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + i * 0.4 + 0.3);
                    osc.connect(gain);
                    gain.connect(ctx.destination);
                    osc.start(now + i * 0.4);
                    osc.stop(now + i * 0.4 + 0.3);
                }
                return;
            }
        }

        function setStory(text, sound = null, screamChance = 0) {
            storyDiv.innerText = text;
            if (sound) playSound(sound);
            
            if (Math.random() < screamChance) {
                setTimeout(() => {
                    playSound('scream');
                    if (navigator.vibrate) navigator.vibrate([200, 100, 200]);
                    storyDiv.innerText += "\n\n❌❗ ЧТО-ТО РЯДОМ ❗❌";
                    
                    // Отправляем скример в бот
                    tg.sendData(JSON.stringify({event: 'jumpscare', chapter: game.chapter}));
                }, 600);
            }
            
            // Вибрация
            tg.HapticFeedback.impactOccurred('heavy');
        }

        // Действия
        function applyAction(actionType) {
            if (game.gameOver) {
                setStory("Игра окончена. Начни заново.");
                return;
            }

            if (game.finalEnding) {
                setStory("Всё кончено. Открой меню.");
                return;
            }

            let text = "";
            let sound = null;
            let scream = 0.0;

            if (game.chapter === 1) {
                if (actionType === 'left') {
                    text = chapterText[1].left;
                    sound = "steps";
                    scream = 0.2;
                } else if (actionType === 'right') {
                    text = chapterText[1].right;
                    sound = "whisper";
                } else if (actionType === 'call') {
                    text = chapterText[1].call + "\n(ГЛАВА 2)";
                    sound = "ring";
                    game.chapter = 2;
                }
            }
            else if (game.chapter === 2) {
                if (actionType === 'left') {
                    text = chapterText[2].left;
                    sound = "scream";
                    scream = 0.5;
                } else if (actionType === 'right') {
                    text = chapterText[2].right;
                } else if (actionType === 'call') {
                    text = chapterText[2].call + "\n(ГЛАВА 3)";
                    sound = "ring";
                    game.chapter = 3;
                }
            }
            else if (game.chapter === 3) {
                game.finalEnding = true;
                let endingType = 'bad';
                
                if (actionType === 'left') {
                    text = endings.bad;
                    endingType = 'bad';
                    sound = "scream";
                    scream = 1.0;
                } else if (actionType === 'right') {
                    text = endings.good;
                    endingType = 'good';
                } else if (actionType === 'call') {
                    text = endings.secret;
                    endingType = 'secret';
                    sound = "scream";
                }
                
                // Отправляем результат в бота
                tg.sendData(JSON.stringify({
                    ending: endingType,
                    chapter: 3,
                    flags: game.storyFlags
                }));
            }

            if (!game.finalEnding && game.chapter <= 3) {
                text = chapterText[game.chapter].desc + "\n\n" + text;
            }

            setStory(text, sound, scream);
            if (game.finalEnding) game.gameOver = true;
        }

        // Привязка кнопок
        function bindButton(btn, action) {
            if (!btn) return;
            btn.addEventListener('click', (e) => { e.preventDefault(); action(); });
            btn.addEventListener('touchstart', (e) => { e.preventDefault(); action(); }, { passive: false });
        }

        bindButton(btnLeft, () => applyAction('left'));
        bindButton(btnRight, () => applyAction('right'));
        bindButton(btnCall, () => applyAction('call'));

        // Меню
        function resetGame() {
            game.chapter = 1;
            game.gameOver = false;
            game.finalEnding = false;
            game.storyFlags = {};
            setStory(chapterText[1].desc + "\n\nВыбери ТЬМА, СВЕТ или ЗВОНИТЬ.", 'whisper');
            
            // Сообщаем боту о старте
            tg.sendData(JSON.stringify({event: 'game_start'}));
        }

        menuStart.addEventListener('click', (e) => {
            e.preventDefault();
            menuOverlay.style.opacity = '0';
            setTimeout(() => {
                menuOverlay.style.display = 'none';
            }, 300);
            resetGame();
            playSound('ring');
        });

        menuSoundToggle.addEventListener('click', (e) => {
            e.preventDefault();
            soundEnabled = !soundEnabled;
            menuSoundToggle.innerText = soundEnabled ? '🔊 ЗВУК ВКЛ' : '🔇 ЗВУК ВЫКЛ';
        });

        volumeSlider.addEventListener('input', (e) => {
            masterVolume = parseFloat(e.target.value);
        });

        // Закрываем меню при старте
        tg.ready();
        setStory("☠️ ТРУБКА ЖДЁТ ☠️\nОткрой меню и нажми СТАРТ");
    </script>
</body>
</html>
