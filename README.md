<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>政大社團知識王 - Kahoot! 挑戰賽</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Canvas Confetti CDN for winner celebration -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Noto+Sans+TC:wght@500;700;900&display=swap" rel="stylesheet">
    
    <!-- Custom Styles & Animations -->
    <style>
        body {
            font-family: 'Fredoka', 'Noto Sans TC', sans-serif;
            background-color: #46178f;
            color: #ffffff;
            overflow-x: hidden;
            user-select: none;
        }

        /* Animated Background Floating Shapes */
        .bg-shapes {
            position: fixed;
            top: 0;
            left: 0;
            width: 100vw;
            height: 100vh;
            z-index: 0;
            pointer-events: none;
            overflow: hidden;
        }

        .shape {
            position: absolute;
            opacity: 0.15;
            animation: float 20s infinite linear;
        }

        @keyframes float {
            0% { transform: translateY(105vh) rotate(0deg); }
            100% { transform: translateY(-10vh) rotate(360deg); }
        }

        /* Kahoot Option Button Gradients & Shadows */
        .btn-kahoot {
            transition: transform 0.1s ease, filter 0.2s ease, box-shadow 0.1s ease;
            box-shadow: 0 8px 0 rgba(0,0,0,0.25);
        }
        .btn-kahoot:active {
            transform: translateY(4px);
            box-shadow: 0 4px 0 rgba(0,0,0,0.25);
        }

        .bg-kahoot-red { background-color: #e21b3c; }
        .bg-kahoot-blue { background-color: #1368ce; }
        .bg-kahoot-yellow { background-color: #d89e00; }
        .bg-kahoot-green { background-color: #26890c; }

        .hover-kahoot-red:hover { background-color: #d01735; }
        .hover-kahoot-blue:hover { background-color: #105cb8; }
        .hover-kahoot-yellow:hover { background-color: #c28e00; }
        .hover-kahoot-green:hover { background-color: #207a0a; }

        /* Timer Bar Animation */
        .timer-bar {
            transition: width 0.1s linear;
        }

        /* Custom Bounce Animations */
        @keyframes popIn {
            0% { transform: scale(0.8); opacity: 0; }
            80% { transform: scale(1.05); }
            100% { transform: scale(1); opacity: 1; }
        }
        .pop-in {
            animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        /* Podium Animations */
        .podium-1 { height: 180px; animation: riseUp 0.8s ease-out 0.6s forwards; }
        .podium-2 { height: 130px; animation: riseUp 0.8s ease-out 0.3s forwards; }
        .podium-3 { height: 90px;  animation: riseUp 0.8s ease-out 0s forwards; }

        @keyframes riseUp {
            from { transform: scaleY(0); transform-origin: bottom; }
            to { transform: scaleY(1); transform-origin: bottom; }
        }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative bg-purple-900 text-white">

    <!-- Background Floating Geometric Shapes -->
    <div class="bg-shapes" id="bgShapes"></div>

    <!-- Top Header Bar -->
    <header class="relative z-10 w-full px-6 py-4 flex justify-between items-center bg-purple-950/40 backdrop-blur-md border-b border-purple-800/50">
        <div class="flex items-center gap-3">
            <span class="bg-white text-purple-900 font-black px-3 py-1 rounded-lg text-xl tracking-wider shadow-md">NCCU</span>
            <h1 class="text-xl md:text-2xl font-bold tracking-wide">政大社團知識王</h1>
        </div>
        <div class="flex items-center gap-4">
            <button id="soundToggle" onclick="toggleSound()" class="bg-purple-800 hover:bg-purple-700 px-3 py-1.5 rounded-full text-sm font-semibold flex items-center gap-2 transition shadow">
                <span id="soundIcon">🔊</span> <span id="soundText" class="hidden sm:inline">音效開</span>
            </button>
        </div>
    </header>

    <!-- Main Dynamic Content Container -->
    <main class="relative z-10 flex-grow flex items-center justify-center p-4 md:p-6">
        
        <!-- ==================== SCREEN 1: NICKNAME & LOBBY ==================== -->
        <div id="screenLobby" class="w-full max-w-md bg-purple-800/80 backdrop-blur-xl p-8 rounded-3xl border border-purple-600/40 shadow-2xl text-center pop-in">
            <div class="mb-6">
                <div class="inline-block p-4 bg-purple-700/60 rounded-full mb-3 shadow-inner">
                    <span class="text-5xl" id="avatarPreview">🎓</span>
                </div>
                <h2 class="text-3xl font-extrabold text-amber-300 tracking-wider">加入遊戲！</h2>
                <p class="text-purple-200 text-sm mt-1">請輸入你的暱稱，準備與校友/同學們一較高下！</p>
            </div>

            <!-- Avatar Selection -->
            <div class="flex justify-center gap-2 mb-6">
                <button onclick="setAvatar('🎓')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition bg-purple-700/50">🎓</button>
                <button onclick="setAvatar('🐯')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🐯</button>
                <button onclick="setAvatar('🦆')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🦆</button>
                <button onclick="setAvatar('🐱')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🐱</button>
                <button onclick="setAvatar('🚀')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🚀</button>
            </div>

            <form onsubmit="handleJoinLobby(event)" class="space-y-4">
                <div>
                    <input type="text" id="nicknameInput" required maxlength="12" placeholder="請輸入暱稱..." 
                        class="w-full px-5 py-4 text-center text-xl font-bold rounded-2xl bg-purple-950/80 border-2 border-purple-500 focus:border-amber-400 focus:outline-none text-white placeholder-purple-400 shadow-inner">
                </div>
                
                <button type="submit" 
                    class="w-full py-4 text-2xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition transform hover:scale-[1.02] active:scale-[0.98] shadow-lg">
                    進入大廳 Ready!
                </button>
            </form>

            <div class="mt-6 pt-4 border-t border-purple-700/50 text-xs text-purple-300 flex justify-around">
                <span>⚡ 10 題政大知識搶答</span>
                <span>⏱️ 答題越快分數越高</span>
            </div>
        </div>

        <!-- ==================== SCREEN 2: READY COUNTDOWN ==================== -->
        <div id="screenGetReady" class="hidden text-center pop-in">
            <h2 class="text-4xl md:text-6xl font-black text-amber-300 mb-6 tracking-wide">準備好搶答了嗎？</h2>
            <div id="countdownNumber" class="text-8xl md:text-9xl font-black text-white animate-bounce my-8">3</div>
            <p id="readyTip" class="text-xl text-purple-200 font-semibold">第一題即將登場！</p>
        </div>

        <!-- ==================== SCREEN 3: QUESTION & OPTIONS ==================== -->
        <div id="screenQuestion" class="hidden w-full max-w-4xl flex flex-col items-center">
            
            <!-- Question Header Info -->
            <div class="w-full flex justify-between items-center mb-4 px-2">
                <span id="questionProgress" class="bg-purple-800/80 px-4 py-2 rounded-xl text-lg font-bold border border-purple-600">
                    問題 1 / 10
                </span>
                <div class="flex items-center gap-2 bg-purple-800/80 px-4 py-2 rounded-xl border border-purple-600">
                    <span class="text-amber-400 font-bold">目前分數:</span>
                    <span id="userCurrentScore" class="text-xl font-black">0</span>
                </div>
            </div>

            <!-- Question Box & Timer Bar -->
            <div class="w-full bg-purple-800/90 backdrop-blur-xl rounded-3xl p-6 md:p-8 border border-purple-500/50 shadow-2xl mb-6 relative overflow-hidden">
                <!-- Countdown Timer Bar -->
                <div class="w-full bg-purple-950 h-3 rounded-full mb-6 overflow-hidden">
                    <div id="timerBar" class="bg-amber-400 h-full w-full timer-bar"></div>
                </div>

                <div class="flex items-center justify-between mb-2">
                    <span class="text-sm text-purple-300 font-bold tracking-wider uppercase">Question</span>
                    <span id="timerText" class="text-2xl font-black text-amber-300">15s</span>
                </div>

                <!-- Main Question Title -->
                <h2 id="questionText" class="text-2xl md:text-4xl font-extrabold text-center text-white py-4 leading-relaxed">
                    載入題目中...
                </h2>
            </div>

            <!-- 4 Kahoot Options Grid -->
            <div class="w-full grid grid-cols-1 md:grid-cols-2 gap-4">
                <!-- Option A: Red Triangle -->
                <button onclick="submitAnswer(0)" id="btnOpt0" class="btn-kahoot bg-kahoot-red hover-kahoot-red p-5 rounded-2xl flex items-center gap-4 text-left font-bold text-xl md:text-2xl text-white">
                    <span class="bg-black/20 p-3 rounded-xl text-2xl flex items-center justify-center min-w-[50px]">▲</span>
                    <span id="optText0" class="flex-grow">選項 A</span>
                </button>

                <!-- Option B: Blue Diamond -->
                <button onclick="submitAnswer(1)" id="btnOpt1" class="btn-kahoot bg-kahoot-blue hover-kahoot-blue p-5 rounded-2xl flex items-center gap-4 text-left font-bold text-xl md:text-2xl text-white">
                    <span class="bg-black/20 p-3 rounded-xl text-2xl flex items-center justify-center min-w-[50px]">◆</span>
                    <span id="optText1" class="flex-grow">選項 B</span>
                </button>

                <!-- Option C: Yellow Circle -->
                <button onclick="submitAnswer(2)" id="btnOpt2" class="btn-kahoot bg-kahoot-yellow hover-kahoot-yellow p-5 rounded-2xl flex items-center gap-4 text-left font-bold text-xl md:text-2xl text-white">
                    <span class="bg-black/20 p-3 rounded-xl text-2xl flex items-center justify-center min-w-[50px]">●</span>
                    <span id="optText2" class="flex-grow">選項 C</span>
                </button>

                <!-- Option D: Green Square -->
                <button onclick="submitAnswer(3)" id="btnOpt3" class="btn-kahoot bg-kahoot-green hover-kahoot-green p-5 rounded-2xl flex items-center gap-4 text-left font-bold text-xl md:text-2xl text-white">
                    <span class="bg-black/20 p-3 rounded-xl text-2xl flex items-center justify-center min-w-[50px]">■</span>
                    <span id="optText3" class="flex-grow">選項 D</span>
                </button>
            </div>
        </div>

        <!-- ==================== SCREEN 4: ANSWER RESULT FEEDBACK ==================== -->
        <div id="screenResult" class="hidden w-full max-w-lg bg-purple-800/90 backdrop-blur-xl p-8 rounded-3xl border border-purple-500/50 shadow-2xl text-center pop-in">
            <div id="resultIcon" class="text-7xl mb-4">🎉</div>
            <h2 id="resultTitle" class="text-4xl font-extrabold mb-2">回答正確！</h2>
            <p id="resultPoints" class="text-2xl font-bold text-amber-300 mb-4">+950 分</p>
            
            <div class="bg-purple-950/60 rounded-2xl p-4 mb-6 space-y-2 text-left border border-purple-700/50">
                <div class="flex justify-between text-sm">
                    <span class="text-purple-300">正確答案：</span>
                    <span id="correctAnswerDisplay" class="font-bold text-emerald-400">--</span>
                </div>
                <div class="flex justify-between text-sm">
                    <span class="text-purple-300">連續答對 (Streak)：</span>
                    <span id="streakDisplay" class="font-bold text-amber-400">🔥 0</span>
                </div>
            </div>

            <button onclick="showLeaderboard()" class="w-full py-4 text-xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition shadow-lg">
                查看即時排行榜 ➔
            </button>
        </div>

        <!-- ==================== SCREEN 5: LIVE LEADERBOARD (TOP 5) ==================== -->
        <div id="screenLeaderboard" class="hidden w-full max-w-2xl bg-purple-800/90 backdrop-blur-xl p-6 md:p-8 rounded-3xl border border-purple-500/50 shadow-2xl pop-in">
            <div class="text-center mb-6">
                <h2 class="text-3xl md:text-4xl font-extrabold text-amber-300">🏆 即時排行榜 Top 5</h2>
                <p class="text-purple-200 text-sm mt-1">累積得分最高的前五名玩家</p>
            </div>

            <!-- Leaderboard List -->
            <div id="leaderboardList" class="space-y-3 mb-8">
                <!-- Dynamic Leaderboard Items generated via JS -->
            </div>

            <button id="nextQuestionBtn" onclick="nextQuestion()" class="w-full py-4 text-xl font-black rounded-2xl bg-emerald-500 hover:bg-emerald-400 text-purple-950 transition shadow-lg">
                下一題 ➔
            </button>
        </div>

        <!-- ==================== SCREEN 6: FINAL PODIUM (WINNERS) ==================== -->
        <div id="screenPodium" class="hidden w-full max-w-3xl flex flex-col items-center pop-in">
            <h2 class="text-4xl md:text-6xl font-black text-amber-300 mb-2 text-center tracking-wider">🏆 遊戲結束 🏆</h2>
            <p class="text-purple-200 text-lg mb-8 text-center">恭喜政大社團知識王的前三名獲勝者！</p>

            <!-- 3D Style Podium Container -->
            <div class="w-full flex justify-center items-end gap-2 md:gap-6 mb-8 px-4 min-h-[260px]">
                
                <!-- 2nd Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-2">
                        <span class="text-3xl">🥈</span>
                        <div id="podiumName2" class="font-bold text-sm md:text-base truncate max-w-[100px]">--</div>
                        <div id="podiumScore2" class="text-xs text-amber-300 font-bold">0 pts</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-slate-400 to-slate-300 rounded-t-2xl podium-2 flex items-center justify-center text-purple-950 font-black text-3xl shadow-lg border-t-2 border-white">
                        2
                    </div>
                </div>

                <!-- 1st Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-2">
                        <span class="text-4xl md:text-5xl animate-bounce inline-block">👑</span>
                        <div id="podiumName1" class="font-extrabold text-base md:text-lg text-amber-300 truncate max-w-[120px]">--</div>
                        <div id="podiumScore1" class="text-sm text-amber-300 font-black">0 pts</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-amber-400 to-yellow-300 rounded-t-2xl podium-1 flex items-center justify-center text-purple-950 font-black text-4xl shadow-xl border-t-2 border-white">
                        1
                    </div>
                </div>

                <!-- 3rd Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-2">
                        <span class="text-3xl">🥉</span>
                        <div id="podiumName3" class="font-bold text-sm md:text-base truncate max-w-[100px]">--</div>
                        <div id="podiumScore3" class="text-xs text-amber-300 font-bold">0 pts</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-amber-700 to-amber-600 rounded-t-2xl podium-3 flex items-center justify-center text-white font-black text-2xl shadow-lg border-t-2 border-white">
                        3
                    </div>
                </div>

            </div>

            <!-- Player Personal Summary Card -->
            <div class="w-full max-w-md bg-purple-800/80 backdrop-blur-md rounded-2xl p-5 border border-purple-600/50 mb-6 text-center">
                <h3 class="text-purple-200 font-bold text-sm mb-2">你的比賽成績卡</h3>
                <div class="grid grid-cols-3 gap-2">
                    <div class="bg-purple-950/60 p-3 rounded-xl">
                        <div class="text-xs text-purple-300">最終排名</div>
                        <div id="userFinalRank" class="text-xl font-black text-amber-300">#-</div>
                    </div>
                    <div class="bg-purple-950/60 p-3 rounded-xl">
                        <div class="text-xs text-purple-300">總得分</div>
                        <div id="userFinalScore" class="text-xl font-black text-amber-300">0</div>
                    </div>
                    <div class="bg-purple-950/60 p-3 rounded-xl">
                        <div class="text-xs text-purple-300">答對率</div>
                        <div id="userAccuracy" class="text-xl font-black text-emerald-400">0%</div>
                    </div>
                </div>
            </div>

            <button onclick="resetGame()" class="px-8 py-4 text-2xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition transform hover:scale-105 shadow-xl">
                🔄 再玩一次
            </button>
        </div>

    </main>

    <!-- Footer -->
    <footer class="relative z-10 w-full py-3 text-center text-xs text-purple-300/80 bg-purple-950/30">
        國立政治大學社團活動知識問答 • Inspired by Kahoot! Style
    </footer>

    <!-- JS Logic -->
    <script>
        
        // 10 Quiz Questions Data
        const quizQuestions = [
            {
                question: "1. 政大統編是多少？",
                options: ["03807645", "03807564", "03807654", "03806574"],
                correct: 2 // Option C
            },
            {
                question: "2. 如果活動需要使用四維堂或雲岫廳的視聽服務團，最晚多久前申請？",
                options: ["活動前10天", "活動前14天", "活動前7天", "活動前15天"],
                correct: 1 // Option B
            },
            {
                question: "3. 如果要申請政大校內場地，主要要到哪個系統處理？",
                options: ["iNCCU 場地租借系統", "Google Classroom", "Moodle", "政大圖書館系統"],
                correct: 0 // Option A
            },
            {
                question: "4. 視聽服務團的義務服務時段是？",
                options: ["一整天", "16-21點", "12-20點", "18-22點"],
                correct: 3 // Option D
            },
            {
                question: "5. 租用遊覽車時、以下哪一項比較不用特別注意的？",
                options: ["司機年紀", "出廠10年內", "正規公司", "符合安全規定"],
                correct: 0 // Option A
            },
            {
                question: "6. 活動結束，如果需要繳交成果報告書通常應該什麼時候完成？",
                options: ["活動結束一週內", "活動結束後一個月內", "活動結束後兩週內", "下一學期再交"],
                correct: 2 // Option C
            },
            {
                question: "7. 如果社團需要開立收據/發票，抬頭要填什麼？",
                options: ["各自同學會", "國立政治大學", "國立政治大學生僑組", "國立政治大學學務處"],
                correct: 1 // Option B
            },
            {
                question: "8. 辦理活動時，每人餐費上限是多少？",
                options: ["120", "150", "100", "180"],
                correct: 2 // Option C
            },
            {
                question: "9. 投影機和投影幕可以去哪裡借？",
                options: ["四維堂", "藝文中心", "課外組", "生僑組"],
                correct: 2 // Option C
            },
            {
                question: "10. 以下哪個不是正確的器材借用流程？",
                options: ["生僑組蓋章", "表格下載/至課外組拿表單", "直接交給會長", "繳至該單位"],
                correct: 2 // Option C
            }
        ];

        // Simulated AI Bot Competitors (To mimic Kahoot live competition feel)
        let bots = [
            { id: 'b1', name: '指南山人 🏔️', score: 0, streak: 0, accuracy: 0.85 },
            { id: 'b2', name: '醉夢湖鴨 🦆', score: 0, streak: 0, accuracy: 0.80 },
            { id: 'b3', name: '四維堂阿伯 👴', score: 0, streak: 0, accuracy: 0.75 },
            { id: 'b4', name: '水岸腳踏車 🚲', score: 0, streak: 0, accuracy: 0.70 },
            { id: 'b5', name: '政大貓咪 🐱', score: 0, streak: 0, accuracy: 0.90 }
        ];

        // Game State Variables
        let userPlayer = {
            nickname: '',
            avatar: '🎓',
            score: 0,
            streak: 0,
            correctCount: 0
        };

        let currentQuestionIndex = 0;
        let timerInterval = null;
        let timeLeft = 15; // 15 seconds per question
        let soundEnabled = true;
        let questionStartTime = 0;
        let userHasAnswered = false;

        // Web Audio API for Kahoot Sound FX (No external audio file needed)
        const AudioContext = window.AudioContext || window.webkitAudioContext;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                audioCtx = new AudioContext();
            }
        }

        function playSound(type) {
            if (!soundEnabled) return;
            initAudio();
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
            }

            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);

            const now = audioCtx.currentTime;

            if (type === 'click') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(400, now);
                osc.frequency.exponentialRampToValueAtTime(800, now + 0.08);
                gain.gain.setValueAtTime(0.2, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.08);
                osc.start(now);
                osc.stop(now + 0.08);
            } else if (type === 'correct') {
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(523.25, now); // C5
                osc.frequency.setValueAtTime(659.25, now + 0.1); // E5
                osc.frequency.setValueAtTime(783.99, now + 0.2); // G5
                osc.frequency.setValueAtTime(1046.50, now + 0.3); // C6
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.5);
                osc.start(now);
                osc.stop(now + 0.5);
            } else if (type === 'wrong') {
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(220, now);
                osc.frequency.setValueAtTime(180, now + 0.15);
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.4);
                osc.start(now);
                osc.stop(now + 0.4);
            } else if (type === 'count') {
                osc.type = 'sine';
                osc.frequency.setValueAtTime(600, now);
                gain.gain.setValueAtTime(0.15, now);
                gain.gain.linearRampToValueAtTime(0.01, now + 0.05);
                osc.start(now);
                osc.stop(now + 0.05);
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            document.getElementById('soundIcon').innerText = soundEnabled ? '🔊' : '🔇';
            document.getElementById('soundText').innerText = soundEnabled ? '音效開' : '音效關';
        }

        function setAvatar(emoji) {
            userPlayer.avatar = emoji;
            document.getElementById('avatarPreview').innerText = emoji;
            playSound('click');
        }

        // Initialize Background Shapes
        function createBgShapes() {
            const container = document.getElementById('bgShapes');
            const shapeTypes = ['▲', '◆', '●', '■'];
            for (let i = 0; i < 20; i++) {
                const el = document.createElement('div');
                el.className = 'shape font-black text-2xl text-purple-300';
                el.innerText = shapeTypes[i % 4];
                el.style.left = Math.random() * 100 + 'vw';
                el.style.animationDuration = (12 + Math.random() * 15) + 's';
                el.style.animationDelay = (Math.random() * 10) + 's';
                el.style.fontSize = (20 + Math.random() * 30) + 'px';
                container.appendChild(el);
            }
        }
        createBgShapes();

        function handleJoinLobby(e) {
            e.preventDefault();
            const nickname = document.getElementById('nicknameInput').value.trim();
            if (!nickname) return;

            userPlayer.nickname = nickname;
            playSound('click');

            // Switch to Get Ready Screen
            switchScreen('screenGetReady');
            startReadyCountdown();
        }

        function startReadyCountdown() {
            let count = 3;
            const countEl = document.getElementById('countdownNumber');
            countEl.innerText = count;

            const timer = setInterval(() => {
                count--;
                if (count > 0) {
                    countEl.innerText = count;
                    playSound('count');
                } else if (count === 0) {
                    countEl.innerText = 'GO!';
                    playSound('correct');
                } else {
                    clearInterval(timer);
                    startQuiz();
                }
            }, 900);
        }

        function startQuiz() {
            currentQuestionIndex = 0;
            userPlayer.score = 0;
            userPlayer.streak = 0;
            userPlayer.correctCount = 0;
            
            // Reset Bots
            bots.forEach(bot => {
                bot.score = 0;
                bot.streak = 0;
            });

            loadQuestion();
        }

        function loadQuestion() {
            userHasAnswered = false;
            switchScreen('screenQuestion');

            const qData = quizQuestions[currentQuestionIndex];
            document.getElementById('questionProgress').innerText = `問題 ${currentQuestionIndex + 1} / ${quizQuestions.length}`;
            document.getElementById('questionText').innerText = qData.question;
            document.getElementById('userCurrentScore').innerText = userPlayer.score;

            // Load options
            for (let i = 0; i < 4; i++) {
                document.getElementById(`optText${i}`).innerText = qData.options[i];
                const btn = document.getElementById(`btnOpt${i}`);
                btn.disabled = false;
                btn.classList.remove('opacity-50', 'ring-4', 'ring-white');
            }

            // Start Timer (15 seconds)
            timeLeft = 15;
            questionStartTime = Date.now();
            updateTimerUI();

            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                timeLeft -= 0.1;
                if (timeLeft <= 0) {
                    timeLeft = 0;
                    clearInterval(timerInterval);
                    if (!userHasAnswered) {
                        timeOutAnswer();
                    }
                }
                updateTimerUI();
            }, 100);
        }

        function updateTimerUI() {
            const percentage = (timeLeft / 15) * 100;
            document.getElementById('timerBar').style.width = `${percentage}%`;
            document.getElementById('timerText').innerText = `${Math.ceil(timeLeft)}s`;
        }

        function submitAnswer(selectedIndex) {
            if (userHasAnswered) return;
            userHasAnswered = true;
            clearInterval(timerInterval);

            const qData = quizQuestions[currentQuestionIndex];
            const isCorrect = (selectedIndex === qData.correct);
            const timeTaken = (Date.now() - questionStartTime) / 1000;

            // Kahoot score formula: Max 1000 pts scaled by time speed + streak bonus
            let pointsEarned = 0;
            if (isCorrect) {
                const speedRatio = Math.max(0, (15 - timeTaken) / 15);
                pointsEarned = Math.round(500 + (speedRatio * 500));
                userPlayer.streak++;
                userPlayer.correctCount++;
                if (userPlayer.streak > 1) {
                    pointsEarned += Math.min( userPlayer.streak * 50, 250); // Streak Bonus
                }
                userPlayer.score += pointsEarned;
                playSound('correct');
            } else {
                userPlayer.streak = 0;
                playSound('wrong');
            }

            // Simulate Bot Responses for this question
            simulateBotAnswers(qData.correct);

            // Highlight selected button
            for (let i = 0; i < 4; i++) {
                const btn = document.getElementById(`btnOpt${i}`);
                btn.disabled = true;
                if (i !== selectedIndex) {
                    btn.classList.add('opacity-50');
                }
            }

            // Show feedback screen after short delay
            setTimeout(() => {
                showAnswerResult(isCorrect, pointsEarned, qData.options[qData.correct]);
            }, 800);
        }

        function timeOutAnswer() {
            userHasAnswered = true;
            userPlayer.streak = 0;
            playSound('wrong');
            simulateBotAnswers(quizQuestions[currentQuestionIndex].correct);

            const qData = quizQuestions[currentQuestionIndex];
            showAnswerResult(false, 0, qData.options[qData.correct]);
        }

        function simulateBotAnswers(correctIndex) {
            bots.forEach(bot => {
                const isCorrect = Math.random() < bot.accuracy;
                if (isCorrect) {
                    const randomTime = 2 + Math.random() * 8; // 2 to 10 seconds answer time
                    const speedRatio = Math.max(0, (15 - randomTime) / 15);
                    let pts = Math.round(500 + (speedRatio * 500));
                    bot.streak++;
                    if (bot.streak > 1) pts += 100;
                    bot.score += pts;
                } else {
                    bot.streak = 0;
                }
            });
        }

        function showAnswerResult(isCorrect, points, correctAnswerText) {
            switchScreen('screenResult');

            const iconEl = document.getElementById('resultIcon');
            const titleEl = document.getElementById('resultTitle');
            const pointsEl = document.getElementById('resultPoints');

            if (isCorrect) {
                iconEl.innerText = '🎉';
                titleEl.innerText = '回答正確！';
                titleEl.className = 'text-4xl font-extrabold mb-2 text-emerald-400';
                pointsEl.innerText = `+${points} 分`;
            } else {
                iconEl.innerText = '❌';
                titleEl.innerText = '答錯囉！';
                titleEl.className = 'text-4xl font-extrabold mb-2 text-red-400';
                pointsEl.innerText = '+0 分';
            }

            document.getElementById('correctAnswerDisplay').innerText = correctAnswerText;
            document.getElementById('streakDisplay').innerText = `🔥 ${userPlayer.streak}`;
        }

        function showLeaderboard() {
            switchScreen('screenLeaderboard');
            playSound('click');

            // Combine user and bots into all-players list
            const allPlayers = [
                { id: 'user', name: `${userPlayer.avatar} ${userPlayer.nickname} (你)`, score: userPlayer.score, isUser: true },
                ...bots.map(b => ({ id: b.id, name: b.name, score: b.score, isUser: false }))
            ];

            // Sort by score descending
            allPlayers.sort((a, b) => b.score - a.score);

            const listEl = document.getElementById('leaderboardList');
            listEl.innerHTML = '';

            // Render Top 5
            const top5 = allPlayers.slice(0, 5);
            top5.forEach((player, index) => {
                const rank = index + 1;
                const item = document.createElement('div');
                
                const isUserClass = player.isUser 
                    ? 'bg-amber-400 text-purple-950 font-black border-2 border-white scale-[1.02]' 
                    : 'bg-purple-950/70 text-white font-bold border border-purple-700/50';

                item.className = `p-4 rounded-2xl flex justify-between items-center transition shadow ${isUserClass}`;
                item.innerHTML = `
                    <div class="flex items-center gap-3 truncate">
                        <span class="w-8 h-8 rounded-full bg-purple-900/40 flex items-center justify-center text-sm font-black">
                            ${rank}
                        </span>
                        <span class="text-lg truncate">${player.name}</span>
                    </div>
                    <span class="text-xl font-black">${player.score} pts</span>
                `;
                listEl.appendChild(item);
            });

            // Update Next button text if it's last question
            const nextBtn = document.getElementById('nextQuestionBtn');
            if (currentQuestionIndex >= quizQuestions.length - 1) {
                nextBtn.innerText = '🏆 查看頒獎台結果！';
                nextBtn.className = 'w-full py-4 text-xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition shadow-lg';
            } else {
                nextBtn.innerText = '下一題 ➔';
                nextBtn.className = 'w-full py-4 text-xl font-black rounded-2xl bg-emerald-500 hover:bg-emerald-400 text-purple-950 transition shadow-lg';
            }
        }

        function nextQuestion() {
            currentQuestionIndex++;
            if (currentQuestionIndex < quizQuestions.length) {
                loadQuestion();
            } else {
                showPodium();
            }
        }

        function showPodium() {
            switchScreen('screenPodium');

            // Combine and sort final scores
            const allPlayers = [
                { name: `${userPlayer.avatar} ${userPlayer.nickname}`, score: userPlayer.score, isUser: true },
                ...bots.map(b => ({ name: b.name, score: b.score, isUser: false }))
            ].sort((a, b) => b.score - a.score);

            // Top 3 Podium
            const p1 = allPlayers[0] || { name: '--', score: 0 };
            const p2 = allPlayers[1] || { name: '--', score: 0 };
            const p3 = allPlayers[2] || { name: '--', score: 0 };

            document.getElementById('podiumName1').innerText = p1.name;
            document.getElementById('podiumScore1').innerText = `${p1.score} pts`;

            document.getElementById('podiumName2').innerText = p2.name;
            document.getElementById('podiumScore2').innerText = `${p2.score} pts`;

            document.getElementById('podiumName3').innerText = p3.name;
            document.getElementById('podiumScore3').innerText = `${p3.score} pts`;

            // User Final Stats
            const userRank = allPlayers.findIndex(p => p.isUser) + 1;
            document.getElementById('userFinalRank').innerText = `#${userRank}`;
            document.getElementById('userFinalScore').innerText = userPlayer.score;
            
            const accuracy = Math.round((userPlayer.correctCount / quizQuestions.length) * 100);
            document.getElementById('userAccuracy').innerText = `${accuracy}%`;

            // Fire Confetti Cannon
            if (typeof confetti === 'function') {
                confetti({
                    particleCount: 120,
                    spread: 80,
                    origin: { y: 0.6 }
                });
            }
            playSound('correct');
        }

        function resetGame() {
            playSound('click');
            switchScreen('screenLobby');
        }

        // Screen Switcher Helper
        function switchScreen(screenId) {
            const screens = ['screenLobby', 'screenGetReady', 'screenQuestion', 'screenResult', 'screenLeaderboard', 'screenPodium'];
            screens.forEach(id => {
                const el = document.getElementById(id);
                if (id === screenId) {
                    el.classList.remove('hidden');
                } else {
                    el.classList.add('hidden');
                }
            });
        }
    </script>
</body>
</html>
