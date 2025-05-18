// ==UserScript==
// @name         Blacket Game injecter non malicius
// @author       QdPieDrury on most platforms, and MrQCat on roblox
// @namespace    http://tampermonkey.net/
// @version      3.0-nodev
// @description  Getting sick of having to manually host games and competitions outside of blacket? Now you can—this script adds a play game feature, but **without** the dev panel!
// @match        https://blacket.org/leaderboard/
// @match        https://blacket.org/stats/
// @match        https://blacket.org/clans/discover/
// @match        https://blacket.org/market/
// @match        https://blacket.org/blooks/
// @match        https://blacket.org/inventory/
// @match        https://blacket.org/settings/
// @match        https://blacket.org
// @icon         https://res.cloudinary.com/blooket/image/upload/v1613003832/Blooks/purpleAstronaut.svg
// @grant        none
// ==/UserScript==
(function () {
  'use strict';

  // btw i found this cool text! कंप्यूटर वायरस क्या है
  // --- CONFIGURABLE BLOOKS ---
  const blooks = [
    { name: "Red", color: "#eb4d4b" },
    { name: "Blue", color: "#3498db" },
    { name: "Green", color: "#22a96e" },
    { name: "Yellow", color: "#f7ca18" },
    { name: "Purple", color: "#8f44ad" },
    { name: "Orange", color: "#ff9900" },
    { name: "Pink", color: "#e056fd" },
    { name: "Teal", color: "#1abc9c" },
    { name: "Gray", color: "#7f8fa6" }
  ];

  // --- LOAD FIREBASE ---
  const scripts = [
    "https://www.gstatic.com/firebasejs/10.12.0/firebase-app-compat.js",
    "https://www.gstatic.com/firebasejs/10.12.0/firebase-database-compat.js"
  ];
  let loaded = 0;
  scripts.forEach(src => {
    const s = document.createElement('script');
    s.src = src;
    s.onload = () => {
      loaded++;
      if (loaded === scripts.length) initFirebaseGame();
    };
    document.head.appendChild(s);
  });

  function initFirebaseGame() {
    const firebaseConfig = {
      apiKey: "AIzaSyCh3yY8p7grlPaIYmLkH-KztMlVd2U0ooo",
      authDomain: "chatmonkey-2-tp.firebaseapp.com",
      databaseURL: "https://chatmonkey-2-tp-default-rtdb.firebaseio.com",
      projectId: "chatmonkey-2-tp",
      storageBucket: "chatmonkey-2-tp.firebasestorage.app",
      messagingSenderId: "544692021129",
      appId: "1:544692021129:web:fb97cc8b881a02ebdf6358"
    };
    if (!window.firebase?.apps?.length) firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    // --- GUI STYLE ---
    const style = document.createElement('style');
    style.textContent = `
      .blacket-gui {
        position: fixed;
        bottom: 8px !important;
        right: 8px !important;
        width: 270px !important;
        background: #2e2f4f;
        color: white;
        border: 2px solid #4c5cff;
        border-radius: 12px;
        z-index: 9999;
        padding: 13px 10px 13px 10px;
        font-family: 'Segoe UI', sans-serif;
        box-shadow: 0 2px 8px #00000033;
        min-height: 0;
        min-width: 0;
      }
      .blacket-gui button {
        width: 100%;
        padding: 8px;
        margin: 4px 0;
        border: none;
        background: #4c5cff;
        color: white;
        border-radius: 8px;
        font-weight: bold;
        cursor: pointer;
        transition: background 0.3s;
        font-size: 14px;
      }
      .blacket-gui button:disabled {
        background: #222244;
        opacity: 0.6;
        cursor: not-allowed;
      }
      .blacket-gui button:hover:not(:disabled) {
        background: #3a45cc;
      }
      .blacket-gui input {
        width: 100%;
        padding: 7px;
        margin: 4px 0 8px 0;
        border: 2px solid #4c5cff;
        border-radius: 6px;
        background: #1f2037;
        color: white;
        font-size: 14px;
      }
      .blacket-gui .timer {
        font-size: 14px;
        text-align: right;
        margin-bottom: 8px;
        font-weight: bold;
        color: #ffd700;
      }
      .feedback {
        margin-top: 7px;
        font-weight: bold;
        text-align: center;
      }
      .blook-select-grid {
        display: grid;
        grid-template-columns: repeat(3, 1fr);
        gap: 7px;
        margin: 10px 0 6px 0;
      }
      .blook-box {
        border-radius: 44% 46% 41% 51%/51% 49% 55% 45%;
        width: 50px;
        height: 50px;
        box-shadow: 0 3px 9px #0002;
        display: flex;
        align-items: center;
        justify-content: center;
        font-weight: bold;
        font-size: 13px;
        color: #fff;
        cursor: pointer;
        border: 2px solid #fff0;
        transition: border 0.2s, box-shadow 0.2s;
        user-select: none;
      }
      .blook-box.selected {
        border: 2px solid #ffd700 !important;
        box-shadow: 0 0 0 3px #ffd70044;
      }
      .blook-box.unavailable {
        opacity: 0.3;
        pointer-events: none;
        filter: grayscale(0.7);
      }
      .player-blook-list {
        margin: 8px 0 0 0;
        font-size: 13px;
      }
      .player-blook-indicator {
        display: inline-block;
        width: 16px;
        height: 16px;
        border-radius: 40%;
        margin-right: 4px;
        vertical-align: middle;
        border: 1px solid #fff3;
      }
    `;
    document.head.appendChild(style);

    // --- MAIN GUI ---
    const gui = document.createElement('div');
    gui.className = 'blacket-gui';
    gui.innerHTML = `
      <h2 style="text-align:center;">🎮 Blacket Game</h2>
      <div id="mainMenu">
        <button id="playBtn">▶️ Play</button>
        <button id="hostBtn">📡 Host</button>
      </div>
      <div id="playPanel" style="display:none;"></div>
      <div id="hostPanel" style="display:none;"></div>
    `;
    document.body.appendChild(gui);

    const questionSet = [
      { q: "What is the primary currency in Blacket?", options: ["Tokens", "Coins", "Bucks", "Credits"], a: 0 },
      { q: "Who is the creator of Blacket?", options: ["someone", "QdPieDrury", "pig_55", "josh16"], a: 0 },
      { q: "Where can you sell your blooks?", options: ["Market", "Bazaar", "Shop", "Inventory"], a: 1 },
      { q: "Which section lets you see your blooks?", options: ["Market", "Stats", "Inventory", "Clans"], a: 2 },
      { q: "Which page shows global rankings?", options: ["Leaderboard", "Blooks", "Stats", "Settings"], a: 0 },
      { q: "Which tab lets you join groups?", options: ["Stats", "Clans", "Bazaar", "Market"], a: 1 },
      { q: "What color is a Mystical border?", options: ["Red", "Rainbow", "Blue", "Black"], a: 1 },
      { q: "What happens if you duplicate a blook?", options: ["You get awarded", "You're gonna get banned", "Lose it", "Upgrade it"], a: 1 },
      { q: "What page shows your trading activity?", options: ["Market", "Inventory", "Bazaar", "Settings"], a: 2 },
      { q: "What is a 'Blook'?", options: ["Character", "Skin", "Item", "Pet"], a: 0 },
      { q: "Can you change your Blacket name?", options: ["Yes", "No"], a: 0 },
      { q: "What page would you visit to see your stats?", options: ["Inventory", "Stats", "Settings", "Clans"], a: 1 },
      { q: "Which area lets you see blooks available?", options: ["Inventory", "Market", "Leaderboard", "Bazaar"], a: 1 },
      { q: "Can you see other users’ scores?", options: ["Yes", "No"], a: 0 },
      { q: "Are Mystical blooks common?", options: ["Yes", "No"], a: 1 },
      { q: "Is there a marketplace in Blacket?", options: ["Yes", "No"], a: 0 },
      { q: "What is the purpose of the 'Bazaar'?", options: ["Sell blooks", "Join games", "Chat", "Settings"], a: 0 },
      { q: "What’s shown on the leaderboard?", options: ["Top players", "Blooks", "Boxes", "Tokens"], a: 0 },
      { q: "What is the home screen color?", options: ["Blue", "Purple", "Dark", "White"], a: 2 },
      { q: "Where can you host games?", options: ["Leaderboard", "Custom GUI", "Bazaar", "Clans"], a: 1 },
      { q: "How do you join a game?", options: ["Enter code", "Click Play", "Use nickname", "Ask a friend"], a: 0 },
      { q: "Is Blacket related to Blooket?", options: ["Yes", "No"], a: 0 },
      { q: "Are there limited-time blooks?", options: ["Yes", "No"], a: 0 },
      { q: "Where do Mysticals usually appear?", options: ["Events", "Bazaar", "Shop", "Stats"], a: 0 },
      { q: "Which button changes your Blacket GUI?", options: ["Settings", "Themes", "Inventory", "Clans"], a: 0 },
      { q: "Can you open boxes in Blacket?", options: ["Yes", "No"], a: 0 },
      { q: "Are boxes free in Blacket?", options: ["Yes", "No"], a: 1 },
      { q: "How do you level up?", options: ["Gain XP", "Buy tokens", "Sell blooks", "Complete quizzes"], a: 0 },
      { q: "Which is NOT a page in Blacket?", options: ["Arcade", "Market", "Clans", "Inventory"], a: 0 },
      { q: "Can you trade blooks in Blacket?", options: ["No", "Yes"], a: 0 },
      { q: "What’s shown in the Inventory tab?", options: ["Owned blooks", "XP", "Stats", "Themes"], a: 0 },
      { q: "Which is more rare: Epic or Mystical?", options: ["Mystical", "Epic"], a: 0 },
      { q: "Where are blook categories shown?", options: ["Inventory", "Market", "Bazaar", "Stats"], a: 0 },
      { q: "What’s the default background color?", options: ["Black", "Blue", "Dark Gray", "White"], a: 2 },
      { q: "Can you preview blooks?", options: ["Yes", "No"], a: 0 },
      { q: "Where do new players go first?", options: ["Leaderboard", "Stats", "Inventory", "Market"], a: 2 },
      { q: "How long is a Blacket game?", options: ["3 min", "1 min", "2 min", "5 min"], a: 0 },
      { q: "What do you need to join a clan?", options: ["Invite", "Tokens", "XP", "Nothing"], a: 0 },
      { q: "Which is the rarest blook type?", options: ["Common", "Rare", "Mystical", "Epic"], a: 2 },
      { q: "What color are Epic blooks?", options: ["Purple", "Yellow", "Green", "Blue"], a: 0 },
      { q: "Can you have duplicate blooks?", options: ["No", "Yes"], a: 1 },
      { q: "What happens if you try to sell a Mystical?", options: ["Allowed", "Not allowed", "Get banned", "Lose it"], a: 1 },
      { q: "What color border do common blooks have?", options: ["Gray", "Green", "Blue", "White"], a: 0 },
      { q: "What color is a Legendary blook's border?", options: ["Yellow", "Red", "Gold", "Rainbow"], a: 2 },
      { q: "Which is a legendary blook?", options: ["Phoenix", "Unicorn", "Dragon", "All"], a: 3 },
      { q: "How can you get a blook?", options: ["Buy", "Open box", "Trade", "All"], a: 3 },
      { q: "What is the minimum tokens for a trade?", options: ["0", "1", "10", "100"], a: 1 }
    ];

    // --- DOM HANDLERS ---
    const mainMenu = document.getElementById('mainMenu');
    const playPanel = document.getElementById('playPanel');
    const hostPanel = document.getElementById('hostPanel');

    // --- PLAY (JOIN GAME) ---
    document.getElementById('playBtn').onclick = () => {
      mainMenu.style.display = 'none';
      playPanel.style.display = 'block';
      playPanel.innerHTML = `
        <h3>Join Game</h3>
        <input id="gameCode" placeholder="Game Code" />
        <input id="nickname" placeholder="Enter your full Blacket name" />
        <button id="joinGame">Join</button>
        <button id="backFromPlay">🔙 Back</button>
      `;
      document.getElementById('backFromPlay').onclick = () => {
        playPanel.style.display = 'none';
        mainMenu.style.display = 'block';
      };
      document.getElementById('joinGame').onclick = () => {
        const code = document.getElementById('gameCode').value.trim();
        const name = document.getElementById('nickname').value.trim();
        if (!code || !name) return alert("Enter code and nickname.");
        joinGame(code, name);
      };
    };

    // --- HOST ---
    document.getElementById('hostBtn').onclick = () => {
      mainMenu.style.display = 'none';
      hostPanel.style.display = 'block';
      // Generate random 6-digit code
      const code = Math.floor(100000 + Math.random() * 900000).toString();
      const ref = db.ref("games/" + code);
      // Initial game state: phase=intermission, time
      const gameState = { created: Date.now(), players: {}, phase: "intermission", timer: 180, startedAt: null };
      ref.set(gameState);
      let timerInterval;
      let gameEnded = false;

      function renderHostPanel(data) {
        const players = data.players || {};
        const phase = data.phase || "intermission";
        const timeLeft = data.timer !== undefined ? data.timer : 180;
        let timerHtml = `<div class="timer" id="hostTimer">⏱ ${formatTime(timeLeft * 1000)}</div>`;
        let playerListHtml = "";
        let startBtnHtml = "";

        if (phase === "intermission") {
          playerListHtml = `<div><strong>Players in intermission:</strong></div>`;
          playerListHtml += `<div class="player-blook-list">`;
          for (const [n, pdata] of Object.entries(players)) {
            if (pdata.blook) {
              const blookObj = blooks.find(b=>b.name===pdata.blook);
              playerListHtml += `
                <span>
                  <span class="player-blook-indicator" style="background:${blookObj ? blookObj.color : "#888"}"></span>
                  ${n} <span style="color:${blookObj ? blookObj.color : "#fff"}">${blookObj ? blookObj.name : "?"}</span>
                </span><br>
              `;
            } else {
              playerListHtml += `<span>${n} <span style="color:#888">(no blook)</span></span><br>`;
            }
          }
          playerListHtml += `</div>`;
          if (Object.keys(players || {}).length >= 1) {
            startBtnHtml = `<button id="startGameBtn">Start Game</button>`;
          }
        } else {
          playerListHtml = renderLeaderboard(players);
        }

        hostPanel.innerHTML = `
          <h3>Hosting Game: ${code}</h3>
          ${timerHtml}
          ${playerListHtml}
          ${startBtnHtml}
          <button id="stopHosting">🛑 Stop Hosting</button>
          <button id="backFromHost">🔙 Back</button>
        `;

        if (document.getElementById("startGameBtn")) {
          document.getElementById("startGameBtn").onclick = () => {
            const startedAt = Date.now();
            ref.update({ phase: "started", startedAt, timer: 180 });
          };
        }
        document.getElementById("stopHosting").onclick = () => {
          endGame();
        };
        document.getElementById("backFromHost").onclick = () => {
          endGame();
        };
      }

      function formatTime(ms) {
        const s = Math.floor(ms / 1000);
        const min = Math.floor(s / 60);
        const sec = s % 60;
        return `${min}:${sec.toString().padStart(2, "0")}`;
      }

      function endGame() {
        if (gameEnded) return;
        gameEnded = true;
        ref.remove();
        clearInterval(timerInterval);
        location.reload();
      }

      function startHostTimer(startedAt) {
        clearInterval(timerInterval);
        timerInterval = setInterval(() => {
          const now = Date.now();
          const elapsed = Math.floor((now - startedAt) / 1000);
          const timeLeft = Math.max(0, 180 - elapsed);
          ref.update({ timer: timeLeft });
          if (timeLeft <= 0) {
            ref.update({ phase: "ended", timer: 0 });
            clearInterval(timerInterval);
          }
        }, 1000);
      }

      ref.on("value", snap => {
        const data = snap.val() || {};
        renderHostPanel(data);

        if (data.phase === "started") {
          if (data.startedAt) startHostTimer(data.startedAt);
        } else {
          clearInterval(timerInterval);
        }
        if (data.phase === "ended") {
          hostPanel.innerHTML = `<h3>Game ended.</h3>${renderLeaderboard(data.players || {})}<button onclick="location.reload()">Restart</button>`;
          clearInterval(timerInterval);
        }
      });

      setTimeout(() => {
        ref.update({ phase: "ended", timer: 0 });
      }, 182000);

      function renderLeaderboard(players) {
        const sorted = Object.entries(players || {}).sort((a, b) => (b[1].score||0) - (a[1].score||0));
        return "<h4>Leaderboard</h4>" + sorted.map(([n, s]) =>
          `<span>${n} (${s.blook ? `<span style="color:${(blooks.find(b=>b.name===s.blook)||{}).color||"#fff"}">${s.blook}</span>` : "?"}): <b>${s.score || 0}</b></span><br>`).join("");
      }
    };

    // --- JOIN GAME / PLAYER FLOW ---
    function joinGame(code, name) {
      const ref = db.ref("games/" + code);
      const playerRef = db.ref("games/" + code + "/players/" + name);

      let localBlook = null;
      let answered = false;

      ref.on("value", snap => {
        const data = snap.val();
        // --- New check: Does the game exist & is not ended? ---
        if (!data) {
          playPanel.innerHTML = `<h3 style="color:red;">Game code not found.</h3><button onclick="location.reload()">Back</button>`;
          return;
        }
        if (data.phase === "ended") {
          playPanel.innerHTML = `<h3 style="color:red;">Game has ended.</h3><button onclick="location.reload()">Back</button>`;
          return;
        }
        // --- End new check ---

        const phase = data.phase || "intermission";
        if (phase === "intermission") {
          renderBlookSelection();
        } else if (phase === "started") {
          if (!answered) {
            answered = true;
            startGame(ref, playerRef, name, data.timer || 180, data.startedAt || Date.now());
          }
        }
      });

      function renderBlookSelection() {
        playPanel.innerHTML = `
          <div style="text-align:center;font-weight:bold">Pick your Blook</div>
          <div class="blook-select-grid" id="blookGrid"></div>
          <div style="margin:7px 0 0 0;font-size:13px;color:#ffd700" id="blookStatus"></div>
        `;
        const blookGrid = document.getElementById("blookGrid");
        blooks.forEach(blook => {
          const div = document.createElement('div');
          div.className = "blook-box";
          div.style.background = blook.color;
          div.textContent = blook.name;
          if (localBlook === blook.name) div.classList.add("selected");
          div.onclick = () => {
            localBlook = blook.name;
            playerRef.update({ blook: localBlook });
            document.getElementById("blookStatus").textContent = `Selected: ${blook.name}`;
            Array.from(blookGrid.children).forEach(child => child.classList.remove("selected"));
            div.classList.add("selected");
          };
          blookGrid.appendChild(div);
        });
      }

      window.addEventListener("beforeunload", () => {
        playerRef.remove();
      });
    }

    function startGame(gameRef, playerRef, playerName, serverTimeLeft, startedAt) {
      let score = 0;
      let index = 0;
      const started = startedAt || Date.now();
      const totalTime = 180 * 1000;
      let timerTimeout;
      function formatTime(ms) {
        const s = Math.floor(ms / 1000);
        const min = Math.floor(s / 60);
        const sec = s % 60;
        return `${min}:${sec.toString().padStart(2, "0")}`;
      }
      function updateTimer() {
        const now = Date.now();
        const left = totalTime - (now - started);
        if (left <= 0) return end();
        document.getElementById("timerDisplay").textContent = "⏱ " + formatTime(left);
        timerTimeout = setTimeout(updateTimer, 1000);
      }
      function showNext() {
        const now = Date.now();
        if (now - started >= totalTime) return end();
        if (index >= questionSet.length) index = 0;
        const q = questionSet[index++];
        playPanel.innerHTML = `
          <div class="timer" id="timerDisplay">⏱ Loading...</div>
          <h4>${q.q}</h4>
          <div id="options"></div>
          <div class="feedback" id="feedbackText"></div>
        `;
        q.options.forEach((opt, i) => {
          const btn = document.createElement("button");
          btn.textContent = opt;
          btn.onclick = () => {
            const correct = Array.isArray(q.a) ? q.a.includes(i) : i === q.a;
            document.getElementById("feedbackText").textContent = correct ? "✔️ Correct!" : "❌ Wrong!";
            if (correct) score++;
            playerRef.update({ score });
            setTimeout(showNext, 1000);
          };
          document.getElementById("options").appendChild(btn);
        });
      }
      function end() {
        clearTimeout(timerTimeout);
        playPanel.innerHTML = `<h3>Time's up!</h3><p>Score: ${score}</p><button onclick="location.reload()">Restart</button>`;
      }
      showNext();
      updateTimer();
    }
  }
})();
