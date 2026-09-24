<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>115學年度僑生幹部訓練營 - Kahoot! 50人同步搶答賽</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Canvas Confetti CDN -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- Ultra-reliable MQTT.js via Cloudflare CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mqtt/4.3.7/mqtt.min.js"></script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700;800&family=Noto+Sans+TC:wght@500;700;900&display=swap" rel="stylesheet">
    
    <style>
        * {
            -webkit-tap-highlight-color: transparent;
            touch-action: manipulation;
        }
        body {
            font-family: 'Fredoka', 'Noto Sans TC', sans-serif;
            background-color: #46178f;
            color: #ffffff;
            overflow-x: hidden;
            user-select: none;
        }

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
            opacity: 0.12;
            animation: float 20s infinite linear;
        }

        @keyframes float {
            0% { transform: translateY(105vh) rotate(0deg); }
            100% { transform: translateY(-10vh) rotate(360deg); }
        }

        /* Kahoot 3D Button Press Effect */
        .btn-kahoot {
            transition: transform 0.1s ease, filter 0.15s ease, box-shadow 0.1s ease;
            box-shadow: 0 6px 0 rgba(0,0,0,0.3);
        }
        .btn-kahoot:active:not(:disabled) {
            transform: translateY(4px);
            box-shadow: 0 2px 0 rgba(0,0,0,0.3);
        }

        .bg-kahoot-red { background-color: #e21b3c; }
        .bg-kahoot-blue { background-color: #1368ce; }
        .bg-kahoot-yellow { background-color: #d89e00; }
        .bg-kahoot-green { background-color: #26890c; }

        .timer-bar {
            transition: width 0.1s linear;
        }

        @keyframes popIn {
            0% { transform: scale(0.85); opacity: 0; }
            80% { transform: scale(1.03); }
            100% { transform: scale(1); opacity: 1; }
        }
        .pop-in {
            animation: popIn 0.35s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .podium-1 { height: 180px; animation: riseUp 0.8s ease-out 0.6s forwards; }
        .podium-2 { height: 130px; animation: riseUp 0.8s ease-out 0.3s forwards; }
        .podium-3 { height: 90px;  animation: riseUp 0.8s ease-out 0s forwards; }

        @keyframes riseUp {
            from { transform: scaleY(0); transform-origin: bottom; }
            to { transform: scaleY(1); transform-origin: bottom; }
        }

        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: rgba(0, 0, 0, 0.1); border-radius: 10px; }
        ::-webkit-scrollbar-thumb { background: rgba(255, 255, 255, 0.3); border-radius: 10px; }
    </style>
</head>
<body class="min-h-screen flex flex-col justify-between relative bg-purple-900 text-white select-none">

    <!-- Background Shapes -->
    <div class="bg-shapes" id="bgShapes"></div>

    <header class="relative z-10 w-full px-4 md:px-6 py-3 md:py-4 flex justify-between items-center bg-purple-950/50 backdrop-blur-md border-b border-purple-800/50">
        <div class="flex items-center gap-2 md:gap-3">
            <span class="bg-white text-purple-900 font-black px-2.5 py-0.5 md:px-3 md:py-1 rounded-lg text-lg md:text-xl tracking-wider shadow">NCCU</span>
            <div>
                <h1 class="text-base md:text-2xl font-bold tracking-wide leading-tight">115學年度僑生幹部訓練營</h1>
                <p id="roomPinDisplay" class="text-xs text-amber-300 font-bold hidden">PIN: <span id="pinValue" class="tracking-wider">------</span></p>
            </div>
        </div>
        <div class="flex items-center gap-2">
            <!-- Connection Status Badge -->
            <button onclick="reconnectBroker()" id="connStatusBadge" title="點擊嘗試重連伺服器" class="bg-amber-500/20 hover:bg-amber-500/30 text-amber-300 border border-amber-500/40 px-2.5 py-1 rounded-full text-xs font-bold flex items-center gap-1.5 transition cursor-pointer">
                <span id="connDot" class="w-2 h-2 rounded-full bg-amber-400 animate-pulse"></span>
                <span id="connText">準備中</span>
            </button>
            <span id="roleBadge" class="hidden bg-amber-400 text-purple-950 px-2.5 py-1 rounded-full text-xs font-black shadow">--</span>
            <button id="soundToggle" onclick="toggleSound()" class="bg-purple-800/80 hover:bg-purple-700 px-3 py-1.5 rounded-full text-xs md:text-sm font-semibold flex items-center gap-1.5 transition shadow">
                <span id="soundIcon">🔊</span> <span id="soundText" class="hidden sm:inline">音效開</span>
            </button>
        </div>
    </header>

    <main class="relative z-10 flex-grow flex items-center justify-center p-3 md:p-6 w-full max-w-5xl mx-auto">
        
        <!-- ==================== SCREEN 0: ROLE SELECT ==================== -->
        <div id="screenRole" class="w-full max-w-md bg-purple-800/85 backdrop-blur-xl p-6 md:p-8 rounded-3xl border border-purple-600/40 shadow-2xl text-center pop-in">
            <div class="mb-6">
                <span class="text-6xl inline-block mb-2">🎉</span>
                <h2 class="text-2xl md:text-3xl font-extrabold text-amber-300 tracking-wide">Kahoot! 50人搶答大賽</h2>
                <p class="text-purple-200 text-xs md:text-sm mt-2">請選擇您的身份進入遊戲：</p>
            </div>

            <div class="space-y-3.5">
                <button onclick="setupHostMode()" class="w-full p-4 md:p-5 rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 font-black text-lg md:text-xl flex items-center justify-center gap-3 transition transform active:scale-95 shadow-lg">
                    <span class="text-2xl">🖥️</span> 我是主持人（大螢幕投影）
                </button>
                <button onclick="showPlayerJoin()" class="w-full p-4 md:p-5 rounded-2xl bg-purple-700 hover:bg-purple-600 border-2 border-purple-400 font-bold text-lg md:text-xl flex items-center justify-center gap-3 transition transform active:scale-95 shadow-lg">
                    <span class="text-2xl">📱</span> 我是參賽者（手機回答）
                </button>
            </div>

            <div class="mt-6 pt-4 border-t border-purple-700/50 text-xs text-purple-300 leading-relaxed">
                💡 支援 50 人以上同時用手機掃碼連線搶答！第1名答對得 10 分，第2名 9 分...依此類推！
            </div>
        </div>

        <!-- ==================== SCREEN 1-HOST: HOST LOBBY ==================== -->
        <div id="screenHostLobby" class="hidden w-full max-w-4xl bg-purple-800/85 backdrop-blur-xl p-5 md:p-8 rounded-3xl border border-purple-600/40 shadow-2xl text-center pop-in">
            <div class="flex flex-col md:flex-row items-center justify-center gap-6 mb-6 bg-purple-950/50 p-5 rounded-2xl border border-purple-700/50">
                <!-- QR Code Display -->
                <div class="bg-white p-3 rounded-2xl shadow-md flex flex-col items-center">
                    <img id="qrCodeImg" src="" alt="QR Code" class="w-36 h-36 md:w-44 md:h-44 object-contain">
                    <span class="text-purple-950 text-xs font-black mt-1.5">📱 拿起手機相機掃碼加入</span>
                </div>
                
                <!-- PIN Display -->
                <div class="text-center md:text-left">
                    <span class="text-xs uppercase tracking-widest text-purple-300 font-bold">Game PIN</span>
                    <div id="bigPinDisplay" class="text-5xl md:text-7xl font-black text-amber-300 tracking-widest my-1 select-all cursor-pointer">
                        ------
                    </div>
                    <p id="hostStatusHint" class="text-amber-200 text-xs md:text-sm animate-pulse mt-1">建立雲端遊戲房間中...</p>
                </div>
            </div>

            <!-- Joined Players Counter & Grid -->
            <div class="bg-purple-950/60 rounded-2xl p-4 md:p-6 mb-6 border border-purple-700/50">
                <div class="flex justify-between items-center mb-3">
                    <span class="text-base md:text-lg font-bold">已加入玩家 (<span id="playerCount" class="text-amber-300 font-black">0</span> 人)</span>
                    <span class="text-xs text-emerald-400 font-bold flex items-center gap-1.5">
                        <span class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-ping"></span>
                        ● 雲端即時頻道連線中
                    </span>
                </div>
                <div id="hostPlayerGrid" class="flex flex-wrap gap-2.5 justify-center min-h-[120px] items-center max-h-[260px] overflow-y-auto p-2 bg-purple-900/40 rounded-xl border border-purple-800/40">
                    <span class="text-purple-400 text-xs md:text-sm italic">等待參賽者掃碼或輸入 PIN 加入...</span>
                </div>
            </div>

            <button id="startGameBtn" onclick="hostStartGame()" disabled class="w-full py-4 md:py-5 text-xl md:text-2xl font-black rounded-2xl bg-emerald-500 hover:bg-emerald-400 disabled:bg-gray-600 disabled:opacity-50 text-purple-950 transition transform active:scale-98 shadow-xl">
                🚀 開始遊戲 (START)
            </button>
        </div>

        <!-- ==================== SCREEN 1-PLAYER: PLAYER JOIN ==================== -->
        <div id="screenPlayerJoin" class="hidden w-full max-w-sm bg-purple-800/85 backdrop-blur-xl p-6 rounded-3xl border border-purple-600/40 shadow-2xl text-center pop-in">
            <div class="mb-5">
                <div class="inline-block p-3.5 bg-purple-700/60 rounded-full mb-2 shadow-inner">
                    <span class="text-4xl" id="avatarPreview">🎓</span>
                </div>
                <h2 class="text-2xl font-extrabold text-amber-300 tracking-wide">加入挑戰賽！</h2>
                <p class="text-purple-200 text-xs mt-1">請選擇頭像並輸入暱稱</p>
            </div>

            <!-- Avatar Selection -->
            <div class="flex justify-center gap-2 mb-5">
                <button onclick="setAvatar('🎓')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition bg-purple-700/60">🎓</button>
                <button onclick="setAvatar('🐯')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🐯</button>
                <button onclick="setAvatar('🦆')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🦆</button>
                <button onclick="setAvatar('🐱')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🐱</button>
                <button onclick="setAvatar('🚀')" class="avatar-btn text-2xl p-2 rounded-xl hover:bg-purple-700 transition">🚀</button>
            </div>

            <form onsubmit="handlePlayerJoin(event)" class="space-y-3.5">
                <div>
                    <input type="number" id="pinInput" required placeholder="6 位 Game PIN 碼" 
                        class="w-full px-4 py-3 text-center text-xl font-black rounded-2xl bg-purple-950/90 border-2 border-purple-500 focus:border-amber-400 focus:outline-none text-white placeholder-purple-400 shadow-inner mb-2.5">
                    <input type="text" id="nicknameInput" required maxlength="12" placeholder="請輸入你的暱稱..." 
                        class="w-full px-4 py-3.5 text-center text-lg font-bold rounded-2xl bg-purple-950/90 border-2 border-purple-500 focus:border-amber-400 focus:outline-none text-white placeholder-purple-400 shadow-inner">
                </div>
                
                <div id="playerErrorMsg" class="hidden text-amber-300 text-xs bg-purple-950/80 p-2.5 rounded-xl border border-amber-500/50 animate-pulse"></div>

                <button id="joinSubmitBtn" type="submit" 
                    class="w-full py-4 text-xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition transform active:scale-95 shadow-lg">
                    進入房間 Ready!
                </button>
            </form>
            
            <button onclick="switchScreen('screenRole')" class="mt-4 text-xs text-purple-300 hover:underline">← 返回角色選擇</button>
        </div>

        <!-- ==================== SCREEN PLAYER WAITING ==================== -->
        <div id="screenPlayerWaiting" class="hidden w-full max-w-sm bg-purple-800/85 backdrop-blur-xl p-6 md:p-8 rounded-3xl border border-purple-600/40 shadow-2xl text-center pop-in">
            <div class="my-4">
                <span class="text-6xl animate-bounce inline-block mb-3">⏳</span>
                <h2 class="text-2xl font-extrabold text-amber-300 mb-1">成功進房囉！</h2>
                <p id="playerWaitName" class="text-xl font-bold text-white mb-4">--</p>
                <div class="bg-purple-950/60 p-4 rounded-2xl border border-purple-700/50">
                    <p class="text-amber-200 text-xs md:text-sm font-semibold animate-pulse">請看大螢幕，等待主持人開始遊戲...</p>
                </div>
            </div>
        </div>

        <!-- ==================== SCREEN 2: READY COUNTDOWN ==================== -->
        <div id="screenGetReady" class="hidden text-center pop-in py-8">
            <h2 class="text-3xl md:text-5xl font-black text-amber-300 mb-4 tracking-wide">準備好搶答了嗎？</h2>
            <div id="countdownNumber" class="text-7xl md:text-9xl font-black text-white animate-bounce my-6">3</div>
            <p id="readyTip" class="text-lg text-purple-200 font-semibold">第一題即將登場！</p>
        </div>

        <!-- ==================== SCREEN 3: QUESTION & OPTIONS ==================== -->
        <div id="screenQuestion" class="hidden w-full max-w-4xl flex flex-col items-center">
            <div class="w-full flex justify-between items-center mb-3 px-1">
                <span id="questionProgress" class="bg-purple-800/90 px-3.5 py-1.5 rounded-xl text-sm md:text-base font-bold border border-purple-600">
                    問題 1 / 10
                </span>
                <!-- Host Answer Status & Early End Button -->
                <div class="flex items-center gap-2">
                    <span id="answeredStatusBadge" class="bg-purple-950/80 text-amber-300 px-3 py-1.5 rounded-xl text-xs md:text-sm font-bold border border-purple-600">
                        已回答: <span id="answeredCountText" class="text-white font-black">0</span> / <span id="totalPlayersText">0</span> 人
                    </span>
                    <button id="hostForceEndBtn" onclick="hostForceEndQuestion()" class="hidden bg-amber-400 hover:bg-amber-300 text-purple-950 font-black px-3 py-1.5 rounded-xl text-xs md:text-sm shadow transition transform active:scale-95">
                        ⏩ 提前開獎
                    </button>
                    <div class="flex items-center gap-1.5 bg-purple-800/90 px-3.5 py-1.5 rounded-xl border border-purple-600">
                        <span class="text-amber-400 font-bold text-xs md:text-sm">得分:</span>
                        <span id="userCurrentScore" class="text-sm md:text-lg font-black">0 分</span>
                    </div>
                </div>
            </div>

            <div class="w-full bg-purple-800/90 backdrop-blur-xl rounded-2xl md:rounded-3xl p-4 md:p-7 border border-purple-500/50 shadow-2xl mb-4 relative overflow-hidden">
                <div class="w-full bg-purple-950 h-2.5 md:h-3 rounded-full mb-4 overflow-hidden">
                    <div id="timerBar" class="bg-amber-400 h-full w-full timer-bar"></div>
                </div>

                <div class="flex items-center justify-between mb-1">
                    <span class="text-xs text-purple-300 font-bold tracking-wider uppercase">Question</span>
                    <span id="timerText" class="text-xl md:text-2xl font-black text-amber-300">15s</span>
                </div>

                <h2 id="questionText" class="text-xl md:text-3xl font-extrabold text-center text-white py-2 md:py-4 leading-relaxed">
                    載入題目中...
                </h2>
            </div>

            <div class="w-full grid grid-cols-2 gap-2.5 md:gap-4">
                <button onclick="submitAnswer(0)" id="btnOpt0" class="btn-kahoot bg-kahoot-red p-4 md:p-6 rounded-2xl flex items-center gap-3 text-left font-bold text-base md:text-2xl text-white min-h-[80px] md:min-h-[100px]">
                    <span class="bg-black/20 p-2.5 rounded-xl text-xl md:text-2xl flex items-center justify-center min-w-[42px]">▲</span>
                    <span id="optText0" class="flex-grow leading-tight">選項 A</span>
                </button>

                <button onclick="submitAnswer(1)" id="btnOpt1" class="btn-kahoot bg-kahoot-blue p-4 md:p-6 rounded-2xl flex items-center gap-3 text-left font-bold text-base md:text-2xl text-white min-h-[80px] md:min-h-[100px]">
                    <span class="bg-black/20 p-2.5 rounded-xl text-xl md:text-2xl flex items-center justify-center min-w-[42px]">◆</span>
                    <span id="optText1" class="flex-grow leading-tight">選項 B</span>
                </button>

                <button onclick="submitAnswer(2)" id="btnOpt2" class="btn-kahoot bg-kahoot-yellow p-4 md:p-6 rounded-2xl flex items-center gap-3 text-left font-bold text-base md:text-2xl text-white min-h-[80px] md:min-h-[100px]">
                    <span class="bg-black/20 p-2.5 rounded-xl text-xl md:text-2xl flex items-center justify-center min-w-[42px]">●</span>
                    <span id="optText2" class="flex-grow leading-tight">選項 C</span>
                </button>

                <button onclick="submitAnswer(3)" id="btnOpt3" class="btn-kahoot bg-kahoot-green p-4 md:p-6 rounded-2xl flex items-center gap-3 text-left font-bold text-base md:text-2xl text-white min-h-[80px] md:min-h-[100px]">
                    <span class="bg-black/20 p-2.5 rounded-xl text-xl md:text-2xl flex items-center justify-center min-w-[42px]">■</span>
                    <span id="optText3" class="flex-grow leading-tight">選項 D</span>
                </button>
            </div>
            
            <div id="playerSubmittedNotice" class="hidden mt-4 bg-emerald-600/90 text-white font-bold px-6 py-3 rounded-full text-sm animate-pulse border border-emerald-400 shadow-lg">
                ✓ 答案已成功送出！等待結算中...
            </div>
        </div>

        <!-- ==================== SCREEN 4: ANSWER RESULT FEEDBACK ==================== -->
        <div id="screenResult" class="hidden w-full max-w-md bg-purple-800/90 backdrop-blur-xl p-6 md:p-8 rounded-3xl border border-purple-500/50 shadow-2xl text-center pop-in">
            <div id="resultBgCard" class="p-6 rounded-2xl mb-4 transition-all">
                <div id="resultIcon" class="text-6xl md:text-7xl mb-2">🎉</div>
                <h2 id="resultTitle" class="text-3xl font-black mb-1">回答完成！</h2>
                <p id="resultPoints" class="text-xl md:text-2xl font-black text-amber-300">計算得分中...</p>
            </div>
            
            <div class="bg-purple-950/60 rounded-2xl p-4 mb-4 space-y-2 text-left border border-purple-700/50 text-sm">
                <div class="flex justify-between">
                    <span class="text-purple-300">正確答案：</span>
                    <span id="correctAnswerDisplay" class="font-bold text-emerald-400">--</span>
                </div>
                <div class="flex justify-between">
                    <span class="text-purple-300">本題獲得分數：</span>
                    <span id="thisRoundScore" class="font-bold text-amber-400">0 分</span>
                </div>
            </div>

            <!-- Option Selection Distribution Chart (Host Display) -->
            <div id="optionDistributionContainer" class="hidden bg-purple-950/70 p-4 rounded-2xl mb-4 border border-purple-700/50 text-left">
                <p class="text-xs text-purple-300 font-bold mb-2">全體選擇統計：</p>
                <div class="grid grid-cols-4 gap-2 text-center text-xs font-bold">
                    <div class="bg-kahoot-red/80 p-2 rounded-xl text-white">
                        <div>▲ A</div>
                        <div id="countOpt0" class="text-base font-black">0</div>
                    </div>
                    <div class="bg-kahoot-blue/80 p-2 rounded-xl text-white">
                        <div>◆ B</div>
                        <div id="countOpt1" class="text-base font-black">0</div>
                    </div>
                    <div class="bg-kahoot-yellow/80 p-2 rounded-xl text-white">
                        <div>● C</div>
                        <div id="countOpt2" class="text-base font-black">0</div>
                    </div>
                    <div class="bg-kahoot-green/80 p-2 rounded-xl text-white">
                        <div>■ D</div>
                        <div id="countOpt3" class="text-base font-black">0</div>
                    </div>
                </div>
            </div>

            <button id="showLeaderboardBtn" onclick="hostTriggerLeaderboard()" class="w-full py-4 text-lg font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition shadow-lg animate-pulse">
                查看即時排行榜 Top 5 ➔
            </button>
            <p id="playerWaitNextHint" class="hidden text-xs text-purple-200 mt-2">請看大螢幕，等待主持人進入下一題...</p>
        </div>

        <!-- ==================== SCREEN 5: LIVE LEADERBOARD (TOP 5) ==================== -->
        <div id="screenLeaderboard" class="hidden w-full max-w-2xl bg-purple-800/90 backdrop-blur-xl p-5 md:p-8 rounded-3xl border border-purple-500/50 shadow-2xl pop-in">
            <div class="text-center mb-5">
                <h2 class="text-2xl md:text-4xl font-extrabold text-amber-300">🏆 即時排行榜 Top 5</h2>
                <p class="text-purple-200 text-xs md:text-sm mt-1">搶答速度最快且答對者獲得高分（滿分 100 分）</p>
            </div>

            <div id="leaderboardList" class="space-y-2.5 mb-6"></div>

            <button id="nextQuestionBtn" onclick="hostTriggerNextQuestion()" class="w-full py-4 text-lg md:text-xl font-black rounded-2xl bg-emerald-500 hover:bg-emerald-400 text-purple-950 transition shadow-lg">
                下一題 ➔
            </button>
        </div>

        <!-- ==================== SCREEN 6: FINAL PODIUM ==================== -->
        <div id="screenPodium" class="hidden w-full max-w-2xl flex flex-col items-center pop-in">
            <h2 class="text-3xl md:text-5xl font-black text-amber-300 mb-1 text-center tracking-wider">🏆 遊戲結束 🏆</h2>
            <p class="text-purple-200 text-sm md:text-base mb-6 text-center">恭喜 115學年度僑生幹部訓練營 榮獲前三名者！</p>

            <div class="w-full flex justify-center items-end gap-2 md:gap-5 mb-6 px-2 min-h-[220px]">
                <!-- 2nd Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-1.5">
                        <span class="text-2xl md:text-3xl">🥈</span>
                        <div id="podiumName2" class="font-bold text-xs md:text-sm truncate max-w-[90px]">--</div>
                        <div id="podiumScore2" class="text-xs text-amber-300 font-bold">0 分</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-slate-400 to-slate-300 rounded-t-2xl podium-2 flex items-center justify-center text-purple-950 font-black text-2xl md:text-3xl shadow-lg border-t-2 border-white">2</div>
                </div>

                <!-- 1st Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-1.5">
                        <span class="text-3xl md:text-4xl animate-bounce inline-block">👑</span>
                        <div id="podiumName1" class="font-extrabold text-sm md:text-base text-amber-300 truncate max-w-[110px]">--</div>
                        <div id="podiumScore1" class="text-xs md:text-sm text-amber-300 font-black">0 分</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-amber-400 to-yellow-300 rounded-t-2xl podium-1 flex items-center justify-center text-purple-950 font-black text-3xl md:text-4xl shadow-xl border-t-2 border-white">1</div>
                </div>

                <!-- 3rd Place -->
                <div class="flex-1 flex flex-col items-center">
                    <div class="text-center mb-1.5">
                        <span class="text-2xl md:text-3xl">🥉</span>
                        <div id="podiumName3" class="font-bold text-xs md:text-sm truncate max-w-[90px]">--</div>
                        <div id="podiumScore3" class="text-xs text-amber-300 font-bold">0 分</div>
                    </div>
                    <div class="w-full bg-gradient-to-t from-amber-700 to-amber-600 rounded-t-2xl podium-3 flex items-center justify-center text-white font-black text-xl md:text-2xl shadow-lg border-t-2 border-white">3</div>
                </div>
            </div>

            <!-- Personal Result Card -->
            <div id="personalCard" class="w-full max-w-sm bg-purple-800/85 backdrop-blur-md rounded-2xl p-4 border border-purple-600/50 mb-5 text-center">
                <h3 class="text-purple-200 font-bold text-xs mb-2">個人比賽成績單</h3>
                <div class="grid grid-cols-3 gap-2">
                    <div class="bg-purple-950/60 p-2.5 rounded-xl">
                        <div class="text-xs text-purple-300">最終排名</div>
                        <div id="userFinalRank" class="text-lg font-black text-amber-300">#-</div>
                    </div>
                    <div class="bg-purple-950/60 p-2.5 rounded-xl">
                        <div class="text-xs text-purple-300">總得分</div>
                        <div id="userFinalScore" class="text-lg font-black text-amber-300">0 分</div>
                    </div>
                    <div class="bg-purple-950/60 p-2.5 rounded-xl">
                        <div class="text-xs text-purple-300">答對率</div>
                        <div id="userAccuracy" class="text-lg font-black text-emerald-400">0%</div>
                    </div>
                </div>
            </div>

            <button onclick="resetGame()" class="px-7 py-3.5 text-xl font-black rounded-2xl bg-amber-400 hover:bg-amber-300 text-purple-950 transition transform active:scale-95 shadow-xl">
                🔄 返回首頁
            </button>
        </div>

    </main>

    <!-- Footer -->
    <footer class="relative z-10 w-full py-2.5 text-center text-xs text-purple-300/80 bg-purple-950/40">
        115學年度僑生幹部訓練營 • Kahoot! 50人即時雲端搶答引擎
    </footer>

    <script>
        // Exact 10 NCCU Questions
        window.quizQuestions = [
            { question: "1. 政大統編是多少？", options: ["03807645", "03807564", "03807654", "03806574"], correct: 2 },
            { question: "2. 如果活動需要使用四維堂或雲岫聽的視聽服務團，最晚多久前申請？", options: ["活動前10天", "活動前14天", "活動前7天", "活動前15天"], correct: 1 },
            { question: "3. 如果要申請政大校內場地，主要要到哪個系統處理？", options: ["iNCCU 場地租借系統", "Google Classroom", "Moodle", "政大圖書館系統"], correct: 0 },
            { question: "4. 視聽服務團的義務服務時段是？", options: ["一整天", "16-21點", "12-20點", "18-22點"], correct: 3 },
            { question: "5. 租用遊覽車時、以下哪一項比較不用特別注意的？", options: ["司機年紀", "出廠10年內", "正規公司", "符合安全規定"], correct: 0 },
            { question: "6. 活動結束，如果需要繳交成果報告書通常應該什麼時候完成？", options: ["活動結束一週內", "活動結束後一個月內", "活動結束後兩週內", "下一學期再交"], correct: 2 },
            { question: "7. 如果社團需要開立收據/發票，抬頭要填什麼？", options: ["各自同學會", "國立政治大學", "國立政治大學生僑組", "國立政治大學學務處"], correct: 1 },
            { question: "8. 辦理活動時，每人餐費上限是多少？", options: ["120", "150", "100", "180"], correct: 2 },
            { question: "9. 投影機和投影幕可以去哪裡借？", options: ["四維堂", "藝文中心", "課外組", "生僑組"], correct: 2 },
            { question: "10. 以下哪個不是正確的器材借用流程？", options: ["生僑組蓋章", "表格下載/至課外組拿表單", "直接交給會長", "繳至該單位"], correct: 2 }
        ];

        // Multi-Broker Pool
        const BROKERS = [
            { name: 'EMQX', url: 'wss://broker.emqx.io:8084/mqtt' },
            { name: 'HiveMQ', url: 'wss://broker.hivemq.com:8884/mqtt' },
            { name: 'Mosquitto', url: 'wss://test.mosquitto.org:8081/mqtt' }
        ];

        // Global State
        window.isHost = false;
        window.currentRoomPin = null;
        window.currentBrokerIndex = 0;
        window.myPlayerId = 'p_' + Date.now().toString(36) + Math.random().toString(36).substr(2, 5);
        window.myPlayer = { nickname: '', avatar: '🎓', score: 0, correctCount: 0, lastPoints: 0 };
        window.userHasAnswered = false;
        window.soundEnabled = true;
        window.mqttClient = null;
        window.timerInterval = null;
        window.timeLeft = 15;

        window.roomState = {
            status: 'LOBBY',
            currentQ: 0,
            players: {},
            questionAnswers: [],
            currentAnswers: {}
        };

        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        function playSound(type) {
            if (!soundEnabled) return;
            try {
                if (audioCtx.state === 'suspended') audioCtx.resume();
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.connect(gain);
                gain.connect(audioCtx.destination);

                if (type === 'correct') {
                    osc.frequency.setValueAtTime(523.25, audioCtx.currentTime); // C5
                    osc.frequency.setValueAtTime(659.25, audioCtx.currentTime + 0.1); // E5
                    osc.frequency.setValueAtTime(783.99, audioCtx.currentTime + 0.2); // G5
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.5);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.5);
                } else if (type === 'wrong') {
                    osc.frequency.setValueAtTime(220, audioCtx.currentTime);
                    osc.frequency.setValueAtTime(180, audioCtx.currentTime + 0.15);
                    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.4);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.4);
                } else if (type === 'click') {
                    osc.frequency.setValueAtTime(440, audioCtx.currentTime);
                    gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
                    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.08);
                    osc.start();
                    osc.stop(audioCtx.currentTime + 0.08);
                }
            } catch (e) {
                console.log('Audio error', e);
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            document.getElementById('soundIcon').innerText = soundEnabled ? '🔊' : '🔇';
            document.getElementById('soundText').innerText = soundEnabled ? '音效開' : '音效關';
        }

        function setAvatar(emoji) {
            myPlayer.avatar = emoji;
            document.getElementById('avatarPreview').innerText = emoji;
            playSound('click');
        }

        function connectMQTT(pin, brokerIdx, onConnectedCallback) {
            window.currentBrokerIndex = brokerIdx % BROKERS.length;
            const broker = BROKERS[window.currentBrokerIndex];
            const clientId = 'nccu_kahoot_' + Math.random().toString(16).substr(2, 8);

            updateConnStatus('connecting', `連線雲端中 (${broker.name})...`);

            if (mqttClient) {
                try { mqttClient.end(true); } catch(e){}
            }

            mqttClient = mqtt.connect(broker.url, {
                clientId: clientId,
                keepalive: 30,
                clean: true,
                reconnectPeriod: 3000,
                connectTimeout: 5000
            });

            let connectTimer = setTimeout(() => {
                if (!mqttClient || !mqttClient.connected) {
                    console.warn('Broker timeout, trying next broker...');
                    connectMQTT(pin, window.currentBrokerIndex + 1, onConnectedCallback);
                }
            }, 6000);

            mqttClient.on('connect', () => {
                clearTimeout(connectTimer);
                updateConnStatus('connected', `已連線 (${broker.name})`);
                
                mqttClient.subscribe(`kahoot115/room/${pin}/#`, (err) => {
                    if (!err && onConnectedCallback) onConnectedCallback();
                });
            });

            mqttClient.on('message', (topic, message) => {
                try {
                    const payload = JSON.parse(message.toString());
                    handleIncomingMQTTMessage(topic, payload);
                } catch (e) {
                    console.error('MQTT JSON parse error', e);
                }
            });

            mqttClient.on('error', () => updateConnStatus('error', '連線重試中...'));
            mqttClient.on('offline', () => updateConnStatus('error', '網路離線'));
        }

        function reconnectBroker() {
            if (!currentRoomPin) return;
            connectMQTT(currentRoomPin, window.currentBrokerIndex + 1, () => {
                if (isHost) broadcastRoomState();
            });
        }

        function updateConnStatus(state, text) {
            const badge = document.getElementById('connStatusBadge');
            const dot = document.getElementById('connDot');
            const textEl = document.getElementById('connText');
            
            textEl.innerText = text;
            if (state === 'connected') {
                badge.className = 'bg-emerald-500/20 text-emerald-300 border border-emerald-500/40 px-2.5 py-1 rounded-full text-xs font-bold flex items-center gap-1.5 cursor-pointer';
                dot.className = 'w-2 h-2 rounded-full bg-emerald-400';
            } else if (state === 'connecting') {
                badge.className = 'bg-amber-500/20 text-amber-300 border border-amber-500/40 px-2.5 py-1 rounded-full text-xs font-bold flex items-center gap-1.5 cursor-pointer';
                dot.className = 'w-2 h-2 rounded-full bg-amber-400 animate-ping';
            } else {
                badge.className = 'bg-rose-500/20 text-rose-300 border border-rose-500/40 px-2.5 py-1 rounded-full text-xs font-bold flex items-center gap-1.5 cursor-pointer';
                dot.className = 'w-2 h-2 rounded-full bg-rose-400';
            }
        }

        function handleIncomingMQTTMessage(topic, payload) {
            if (topic.includes('/state')) {
                // Receive room state update
                if (!isHost) {
                    roomState = payload;
                    updateUIFromRoomState();
                }
            } else if (topic.includes('/join') && isHost) {
                // Host receives join request
                if (payload.playerId && payload.nickname) {
                    if (!roomState.players) roomState.players = {};
                    roomState.players[payload.playerId] = {
                        name: payload.nickname,
                        avatar: payload.avatar || '🎓',
                        score: 0,
                        correctCount: 0,
                        lastPoints: 0
                    };
                    updateHostLobbyPlayers();
                    broadcastRoomState();
                }
            } else if (topic.includes('/answers') && isHost) {
                // Host receives answer submission
                handleHostReceiveAnswer(payload);
            }
        }

        function broadcastRoomState() {
            if (!isHost || !mqttClient || !mqttClient.connected) return;
            mqttClient.publish(
                `kahoot115/room/${currentRoomPin}/state`,
                JSON.stringify(roomState),
                { retain: true, qos: 1 }
            );
            updateUIFromRoomState();
        }

        function setupHostMode() {
            isHost = true;
            currentRoomPin = Math.floor(100000 + Math.random() * 900000).toString();

            document.getElementById('roleBadge').innerText = '🖥️ 主持人';
            document.getElementById('roleBadge').classList.remove('hidden');
            document.getElementById('roomPinDisplay').classList.remove('hidden');
            document.getElementById('pinValue').innerText = currentRoomPin;
            document.getElementById('bigPinDisplay').innerText = currentRoomPin;

            switchScreen('screenHostLobby');

            connectMQTT(currentRoomPin, 0, () => {
                // Generate QR Code containing Broker Index
                const baseUrl = window.location.origin + window.location.pathname;
                const joinUrl = `${baseUrl}?pin=${currentRoomPin}&b=${window.currentBrokerIndex}`;
                document.getElementById('qrCodeImg').src = `https://api.qrserver.com/v1/create-qr-code/?size=220x220&data=${encodeURIComponent(joinUrl)}`;

                document.getElementById('hostStatusHint').innerText = '雲端房間建立成功！等待玩家加入...';
                document.getElementById('hostStatusHint').classList.remove('animate-pulse');
                document.getElementById('startGameBtn').disabled = false;
                broadcastRoomState();
            });
        }

        function showPlayerJoin() {
            switchScreen('screenPlayerJoin');
            const urlParams = new URLSearchParams(window.location.search);
            const pinParam = urlParams.get('pin');
            if (pinParam) {
                document.getElementById('pinInput').value = pinParam;
            }
        }

        function handlePlayerJoin(e) {
            e.preventDefault();
            const pin = document.getElementById('pinInput').value.trim();
            const nickname = document.getElementById('nicknameInput').value.trim();

            if (!pin || pin.length !== 6) {
                showPlayerError('請輸入 6 位數 Game PIN 碼');
                return;
            }
            if (!nickname) {
                showPlayerError('請輸入暱稱');
                return;
            }

            myPlayer.nickname = nickname;
            currentRoomPin = pin;

            const urlParams = new URLSearchParams(window.location.search);
            const brokerParam = parseInt(urlParams.get('b')) || 0;

            document.getElementById('joinSubmitBtn').disabled = true;
            document.getElementById('joinSubmitBtn').innerText = '連線雲端中...';

            connectMQTT(pin, brokerParam, () => {
                document.getElementById('roleBadge').innerText = '📱 參賽者';
                document.getElementById('roleBadge').classList.remove('hidden');
                document.getElementById('playerWaitName').innerText = `${myPlayer.avatar} ${nickname}`;

                switchScreen('screenPlayerWaiting');

                mqttClient.publish(`kahoot115/room/${pin}/join`, JSON.stringify({
                    playerId: myPlayerId,
                    nickname: nickname,
                    avatar: myPlayer.avatar
                }));
            });
        }

        function showPlayerError(msg) {
            const errEl = document.getElementById('playerErrorMsg');
            errEl.innerText = msg;
            errEl.classList.remove('hidden');
            setTimeout(() => errEl.classList.add('hidden'), 3000);
        }

        function updateHostLobbyPlayers() {
            const grid = document.getElementById('hostPlayerGrid');
            const players = Object.values(roomState.players || {});
            document.getElementById('playerCount').innerText = players.length;

            if (players.length === 0) {
                grid.innerHTML = '<span class="text-purple-400 text-xs md:text-sm italic">等待參賽者掃碼或輸入 PIN 加入...</span>';
                return;
            }

            grid.innerHTML = '';
            players.forEach(p => {
                const tag = document.createElement('span');
                tag.className = 'bg-amber-400 text-purple-950 font-extrabold px-3 py-1.5 rounded-xl text-sm md:text-base flex items-center gap-1.5 shadow pop-in';
                tag.innerHTML = `<span>${p.avatar || '🎓'}</span> <span>${p.name}</span>`;
                grid.appendChild(tag);
            });
        }

        function submitAnswer(idx) {
            if (userHasAnswered || roomState.status !== 'QUESTION') return;
            userHasAnswered = true;
            playSound('click');

            for (let i = 0; i < 4; i++) {
                const btn = document.getElementById(`btnOpt${i}`);
                if (i === idx) {
                    btn.classList.add('ring-4', 'ring-white', 'scale-95');
                } else {
                    btn.classList.add('opacity-40');
                }
                btn.disabled = true;
            }
            document.getElementById('playerSubmittedNotice').classList.remove('hidden');

            const payload = {
                type: 'SUBMIT_ANSWER',
                playerId: myPlayerId,
                selectedIndex: idx
            };

            if (isHost) {
                handleHostReceiveAnswer(payload);
            } else if (mqttClient && mqttClient.connected) {
                mqttClient.publish(`kahoot115/room/${currentRoomPin}/answers`, JSON.stringify(payload));
            }
        }

        // 10-Point Rank Scoring: 1st=10, 2nd=9, 3rd=8, 4th=7...
        function handleHostReceiveAnswer(payload) {
            if (roomState.status !== 'QUESTION') return;
            if (!roomState.players[payload.playerId]) return;
            if (!roomState.currentAnswers) roomState.currentAnswers = {};

            if (roomState.currentAnswers[payload.playerId] !== undefined) return;

            roomState.currentAnswers[payload.playerId] = payload.selectedIndex;

            const qData = quizQuestions[roomState.currentQ];
            if (payload.selectedIndex === qData.correct) {
                if (!roomState.questionAnswers) roomState.questionAnswers = [];
                roomState.questionAnswers.push(payload.playerId);
                
                const rank = roomState.questionAnswers.length;
                const points = Math.max(1, 11 - rank); // 1st=10, 2nd=9, 3rd=8... min 1 pt

                roomState.players[payload.playerId].score = (roomState.players[payload.playerId].score || 0) + points;
                roomState.players[payload.playerId].correctCount = (roomState.players[payload.playerId].correctCount || 0) + 1;
                roomState.players[payload.playerId].lastPoints = points;
            } else {
                roomState.players[payload.playerId].lastPoints = 0;
            }

            const totalPlayers = Object.keys(roomState.players || {}).length;
            const totalAnswered = Object.keys(roomState.currentAnswers).length;

            if (totalPlayers > 0 && totalAnswered >= totalPlayers) {
                clearInterval(timerInterval);
                setTimeout(() => {
                    if (roomState.status === 'QUESTION') {
                        roomState.status = 'RESULT';
                        broadcastRoomState();
                    }
                }, 400);
            } else {
                broadcastRoomState();
            }
        }

        function hostForceEndQuestion() {
            if (!isHost) return;
            clearInterval(timerInterval);
            roomState.status = 'RESULT';
            broadcastRoomState();
        }

        function updateTimerUI() {
            const timerBar = document.getElementById('timerBar');
            const timerText = document.getElementById('timerText');
            if (timerBar && timerText) {
                const pct = Math.max(0, (timeLeft / 15) * 100);
                timerBar.style.width = pct + '%';
                timerText.innerText = Math.ceil(timeLeft) + 's';
            }
        }

        function updateUIFromRoomState() {
            const status = roomState.status;

            if (status === 'LOBBY') {
                if (isHost) switchScreen('screenHostLobby');
                else if (myPlayer.nickname) switchScreen('screenPlayerWaiting');
                else switchScreen('screenRole');
                updateHostLobbyPlayers();
            } else if (status === 'COUNTDOWN') {
                switchScreen('screenGetReady');
                runCountdown();
            } else if (status === 'QUESTION') {
                renderQuestionScreen(roomState.currentQ);
            } else if (status === 'RESULT') {
                renderResultScreen();
            } else if (status === 'LEADERBOARD') {
                renderLeaderboardScreen();
            } else if (status === 'PODIUM') {
                renderPodiumScreen();
            }
        }

        function runCountdown() {
            let count = 3;
            const el = document.getElementById('countdownNumber');
            el.innerText = count;
            playSound('click');

            const interval = setInterval(() => {
                count--;
                if (count > 0) {
                    el.innerText = count;
                    playSound('click');
                } else {
                    clearInterval(interval);
                    if (isHost) {
                        roomState.status = 'QUESTION';
                        broadcastRoomState();
                    }
                }
            }, 1000);
        }

        function renderQuestionScreen(qIdx) {
            userHasAnswered = false;
            switchScreen('screenQuestion');
            document.getElementById('playerSubmittedNotice').classList.add('hidden');

            if (isHost) {
                document.getElementById('hostForceEndBtn').classList.remove('hidden');
            } else {
                document.getElementById('hostForceEndBtn').classList.add('hidden');
            }

            const qData = quizQuestions[qIdx];
            document.getElementById('questionProgress').innerText = `問題 ${qIdx + 1} / ${quizQuestions.length}`;
            document.getElementById('questionText').innerText = qData.question;
            
            const totalP = Object.keys(roomState.players || {}).length;
            const answeredP = Object.keys(roomState.currentAnswers || {}).length;
            document.getElementById('answeredCountText').innerText = answeredP;
            document.getElementById('totalPlayersText').innerText = totalP;

            const me = (roomState.players && roomState.players[myPlayerId]) ? roomState.players[myPlayerId] : myPlayer;
            document.getElementById('userCurrentScore').innerText = `${me.score || 0} 分`;

            for (let i = 0; i < 4; i++) {
                document.getElementById(`optText${i}`).innerText = qData.options[i];
                const btn = document.getElementById(`btnOpt${i}`);
                btn.disabled = false;
                btn.classList.remove('opacity-40', 'ring-4', 'ring-white', 'scale-95');
            }

            timeLeft = 15;
            updateTimerUI();

            clearInterval(timerInterval);
            timerInterval = setInterval(() => {
                timeLeft -= 0.1;
                if (timeLeft <= 0) {
                    timeLeft = 0;
                    clearInterval(timerInterval);
                    if (isHost) {
                        setTimeout(() => {
                            if (roomState.status === 'QUESTION') {
                                roomState.status = 'RESULT';
                                broadcastRoomState();
                            }
                        }, 300);
                    }
                }
                updateTimerUI();
            }, 100);
        }

        function renderResultScreen() {
            switchScreen('screenResult');

            const qData = quizQuestions[roomState.currentQ];
            const me = (roomState.players && roomState.players[myPlayerId]) ? roomState.players[myPlayerId] : myPlayer;
            const bgCard = document.getElementById('resultBgCard');
            const resultIcon = document.getElementById('resultIcon');
            const resultTitle = document.getElementById('resultTitle');

            document.getElementById('correctAnswerDisplay').innerText = qData.options[qData.correct];
            document.getElementById('thisRoundScore').innerText = `+${me.lastPoints || 0} 分`;
            document.getElementById('resultPoints').innerText = `累積總分: ${me.score || 0} 分`;

            if (isHost) {
                document.getElementById('showLeaderboardBtn').classList.remove('hidden');
                document.getElementById('playerWaitNextHint').classList.add('hidden');

                document.getElementById('optionDistributionContainer').classList.remove('hidden');
                const counts = [0, 0, 0, 0];
                Object.values(roomState.currentAnswers || {}).forEach(ansIdx => {
                    if (counts[ansIdx] !== undefined) counts[ansIdx]++;
                });
                for (let i = 0; i < 4; i++) {
                    document.getElementById(`countOpt${i}`).innerText = counts[i];
                }
            } else {
                document.getElementById('showLeaderboardBtn').classList.add('hidden');
                document.getElementById('playerWaitNextHint').classList.remove('hidden');
                document.getElementById('optionDistributionContainer').classList.add('hidden');
            }

            if (me.lastPoints > 0) {
                bgCard.className = 'p-6 rounded-2xl mb-4 transition-all bg-emerald-600 text-white shadow-xl';
                resultIcon.innerText = '🎉';
                resultTitle.innerText = '太棒了！答對囉！';
                playSound('correct');
            } else {
                bgCard.className = 'p-6 rounded-2xl mb-4 transition-all bg-rose-600 text-white shadow-xl';
                resultIcon.innerText = '❌';
                resultTitle.innerText = '答錯了，再接再厲！';
                playSound('wrong');
            }
        }

        function hostTriggerLeaderboard() {
            if (!isHost) return;
            playSound('click');
            roomState.status = 'LEADERBOARD';
            broadcastRoomState();
        }

        function renderLeaderboardScreen() {
            switchScreen('screenLeaderboard');
            const leaderboardList = document.getElementById('leaderboardList');
            leaderboardList.innerHTML = '';

            const sorted = Object.values(roomState.players || {}).sort((a, b) => b.score - a.score);
            const top5 = sorted.slice(0, 5);

            if (top5.length === 0) {
                leaderboardList.innerHTML = '<p class="text-center text-purple-300">尚無玩家數據</p>';
            } else {
                top5.forEach((p, idx) => {
                    const item = document.createElement('div');
                    item.className = 'flex items-center justify-between bg-purple-950/70 p-3.5 md:p-4 rounded-2xl border border-purple-700/60 shadow-md pop-in';
                    item.innerHTML = `
                        <div class="flex items-center gap-3">
                            <span class="w-8 h-8 rounded-xl bg-amber-400 text-purple-950 font-black flex items-center justify-center text-base shadow">#${idx + 1}</span>
                            <span class="text-2xl">${p.avatar || '🎓'}</span>
                            <span class="font-bold text-base md:text-lg text-white">${p.name}</span>
                        </div>
                        <span class="font-black text-amber-300 text-lg md:text-xl">${p.score || 0} 分</span>
                    `;
                    leaderboardList.appendChild(item);
                });
            }

            if (isHost) {
                document.getElementById('nextQuestionBtn').classList.remove('hidden');
            } else {
                document.getElementById('nextQuestionBtn').classList.add('hidden');
            }
        }

        async function hostStartGame() {
            if (!isHost) return;
            playSound('click');
            roomState.status = 'COUNTDOWN';
            roomState.currentQ = 0;
            roomState.questionAnswers = [];
            roomState.currentAnswers = {};
            broadcastRoomState();
        }

        function hostTriggerNextQuestion() {
            if (!isHost) return;
            playSound('click');
            if (roomState.currentQ >= quizQuestions.length - 1) {
                roomState.status = 'PODIUM';
            } else {
                roomState.status = 'QUESTION';
                roomState.currentQ += 1;
                roomState.questionAnswers = [];
                roomState.currentAnswers = {};
            }
            broadcastRoomState();
        }

        function renderPodiumScreen() {
            switchScreen('screenPodium');

            const allPlayers = Object.values(roomState.players || {}).sort((a, b) => b.score - a.score);

            const p1 = allPlayers[0] || { name: '--', score: 0, avatar: '🎓' };
            const p2 = allPlayers[1] || { name: '--', score: 0, avatar: '🎓' };
            const p3 = allPlayers[2] || { name: '--', score: 0, avatar: '🎓' };

            document.getElementById('podiumName1').innerText = `${p1.avatar} ${p1.name}`;
            document.getElementById('podiumScore1').innerText = `${p1.score} 分`;

            document.getElementById('podiumName2').innerText = `${p2.avatar} ${p2.name}`;
            document.getElementById('podiumScore2').innerText = `${p2.score} 分`;

            document.getElementById('podiumName3').innerText = `${p3.avatar} ${p3.name}`;
            document.getElementById('podiumScore3').innerText = `${p3.score} 分`;

            const meIndex = allPlayers.findIndex(p => p.name === myPlayer.nickname);
            const me = allPlayers[meIndex] || { score: 0, correctCount: 0 };
            
            document.getElementById('userFinalRank').innerText = meIndex >= 0 ? `#${meIndex + 1}` : '#-';
            document.getElementById('userFinalScore').innerText = `${me.score || 0} 分`;
            document.getElementById('userAccuracy').innerText = `${Math.round(((me.correctCount || 0) / quizQuestions.length) * 100)}%`;

            if (typeof confetti === 'function') {
                confetti({ particleCount: 180, spread: 100, origin: { y: 0.6 } });
            }
            playSound('correct');
        }

        function resetGame() {
            playSound('click');
            location.reload();
        }

        function switchScreen(screenId) {
            const screens = ['screenRole', 'screenHostLobby', 'screenPlayerJoin', 'screenPlayerWaiting', 'screenGetReady', 'screenQuestion', 'screenResult', 'screenLeaderboard', 'screenPodium'];
            screens.forEach(id => {
                const el = document.getElementById(id);
                if (el) {
                    if (id === screenId) el.classList.remove('hidden');
                    else el.classList.add('hidden');
                }
            });
        }
    </script>
</body>
</html>
