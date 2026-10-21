---
layout: default
title: Breakout & High Scores
---

<div class="nav-banner">
  <a href="index.html" class="brand">Elouan Léran <span>| ENS Rennes</span></a>
  <nav>
    <a href="index.html">Accueil</a>
    <a href="enseignement.html">Enseignement</a>
    <a href="recherche.html">Recherche</a>
    <a href="musique.html">Musique</a>
    <a href="sport.html">Sport</a>
    <a href="bonus.html" class="active">Bonus</a>
  </nav>
</div>

<div class="wrapper-content">

  <h1>Breakout</h1>
  <p>Utilise les flèches <b>Gauche / Droite</b> ou la <b>souris</b> pour déplacer la raquette. Détruis toutes les briques pour remporter la partie !</p>

  <div style="display: flex; gap: 30px; flex-wrap: wrap; align-items: flex-start; margin-top: 25px;">
    
    <!-- Zone de Jeu Canvas -->
    <div class="card" style="flex: 1; min-width: 320px; text-align: center; padding: 20px;">
      <canvas id="breakoutCanvas" width="480" height="320" style="background: #090d16; border-radius: 6px; border: 1px solid #334155; max-width: 100%; height: auto;"></canvas>
      
      <div style="margin-top: 15px; display: flex; justify-content: center; gap: 15px; align-items: center;">
        <button id="startBtn" onclick="startGame()" style="background: #0284c7; color: white; border: none; padding: 8px 18px; border-radius: 6px; cursor: pointer; font-weight: 600; font-size: 0.95em;">Lancer la partie</button>
        <span style="font-weight: bold; color: #38bdf8;">Score : <span id="currentScore">0</span></span>
      </div>
    </div>

    <!-- Tableau des Scores -->
    <div class="card" style="width: 280px;">
      <h3 style="margin-top: 0; color: #f8fafc; border-bottom: 1px solid #334155; padding-bottom: 8px;">🏆 Top 10 High Scores</h3>
      <ol id="leaderboard" style="padding-left: 20px; color: #cbd5e1; font-size: 0.95em; line-height: 1.8;">
        <li>Chargement...</li>
      </ol>
    </div>

  </div>

</div>

<!-- Moteur du Jeu Breakout & Système de Score -->
<script>
  const canvas = document.getElementById("breakoutCanvas");
  const ctx = canvas.getContext("2d");

  // Configuration du jeu
  let ballRadius = 6;
  let x = canvas.width / 2;
  let y = canvas.height - 30;
  let dx = 2.5;
  let dy = -2.5;

  let paddleHeight = 10;
  let paddleWidth = 75;
  let paddleX = (canvas.width - paddleWidth) / 2;

  let rightPressed = false;
  let leftPressed = false;

  let rowCount = 5;
  let columnCount = 7;
  let brickWidth = 55;
  let brickHeight = 14;
  let brickPadding = 8;
  let brickOffsetTop = 30;
  let brickOffsetLeft = 25;

  let score = 0;
  let gameInterval = null;
  let isRunning = false;

  let bricks = [];
  function initBricks() {
    bricks = [];
    for (let c = 0; c < columnCount; c++) {
      bricks[c] = [];
      for (let r = 0; r < rowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, status: 1 };
      }
    }
  }

  // Événements clavier & souris
  document.addEventListener("keydown", keyDownHandler, false);
  document.addEventListener("keyup", keyUpHandler, false);
  document.addEventListener("mousemove", mouseMoveHandler, false);

  function keyDownHandler(e) {
    if (e.key === "Right" || e.key === "ArrowRight") rightPressed = true;
    else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = false;
  }

  function keyUpHandler(e) {
    if (e.key === "Right" || e.key === "ArrowRight") rightPressed = false;
    else if (e.key === "Left" || e.key === "ArrowLeft") leftPressed = false;
  }

  function mouseMoveHandler(e) {
    let relativeX = e.clientX - canvas.offsetLeft;
    if (relativeX > 0 && relativeX < canvas.width) {
      paddleX = relativeX - paddleWidth / 2;
    }
  }

  // Détection des collisions
  function collisionDetection() {
    for (let c = 0; c < columnCount; c++) {
      for (let r = 0; r < rowCount; r++) {
        let b = bricks[c][r];
        if (b.status === 1) {
          if (x > b.x && x < b.x + brickWidth && y > b.y && y < b.y + brickHeight) {
            dy = -dy;
            b.status = 0;
            score += 10;
            document.getElementById("currentScore").innerText = score;
            if (score === rowCount * columnCount * 10) {
              alert("Bravo ! Tu as détruit toutes les briques !");
              endGame();
            }
          }
        }
      }
    }
  }

  function drawBall() {
    ctx.beginPath();
    ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
    ctx.fillStyle = "#38bdf8";
    ctx.fill();
    ctx.closePath();
  }

  function drawPaddle() {
    ctx.beginPath();
    ctx.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
    ctx.fillStyle = "#f8fafc";
    ctx.fill();
    ctx.closePath();
  }

  function drawBricks() {
    const colors = ["#ef4444", "#f97316", "#eab308", "#10b981", "#06b6d4"];
    for (let c = 0; c < columnCount; c++) {
      for (let r = 0; r < rowCount; r++) {
        if (bricks[c][r].status === 1) {
          let brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
          let brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
          bricks[c][r].x = brickX;
          bricks[c][r].y = brickY;
          ctx.beginPath();
          ctx.rect(brickX, brickY, brickWidth, brickHeight);
          ctx.fillStyle = colors[r % colors.length];
          ctx.fill();
          ctx.closePath();
        }
      }
    }
  }

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBricks();
    drawBall();
    drawPaddle();
    collisionDetection();

    if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) {
      dx = -dx;
    }
    if (y + dy < ballRadius) {
      dy = -dy;
    } else if (y + dy > canvas.height - ballRadius) {
      if (x > paddleX && x < paddleX + paddleWidth) {
        dy = -dy;
      } else {
        endGame();
        return;
      }
    }

    if (rightPressed && paddleX < canvas.width - paddleWidth) paddleX += 5;
    else if (leftPressed && paddleX > 0) paddleX -= 5;

    x += dx;
    y += dy;
  }

  function startGame() {
    if (isRunning) return;
    initBricks();
    score = 0;
    document.getElementById("currentScore").innerText = score;
    x = canvas.width / 2;
    y = canvas.height - 30;
    dx = 2.5;
    dy = -2.5;
    paddleX = (canvas.width - paddleWidth) / 2;
    isRunning = true;
    gameInterval = setInterval(draw, 10);
  }

  function endGame() {
    clearInterval(gameInterval);
    isRunning = false;
    setTimeout(() => {
      let name = prompt(`Game Over ! Ton score est de ${score}. Entre ton pseudo/nom pour le classement :`);
      if (name) {
        saveScore(name, score);
      }
    }, 100);
  }

  // --- GESTION DU TABLEAU DES SCORES (LocalStorage + API) ---
  function getScores() {
    let scores = JSON.parse(localStorage.getItem("breakout_scores") || "[]");
    return scores;
  }

  function displayScores() {
    let scores = getScores();
    let html = "";
    if (scores.length === 0) {
      html = "<li>Aucun score enregistré. Soyez le premier !</li>";
    } else {
      scores.sort((a, b) => b.score - a.score);
      scores.slice(0, 10).forEach(s => {
        html += `<li><b>${s.name}</b> : ${s.score} pts</li>`;
      });
    }
    document.getElementById("leaderboard").innerHTML = html;
  }

  function saveScore(name, score) {
    let scores = getScores();
    scores.push({ name: name, score: score });
    scores.sort((a, b) => b.score - a.score);
    localStorage.setItem("breakout_scores", JSON.stringify(scores));
    displayScores();
  }

  // Charger les scores au chargement
  displayScores();
  initBricks();
  drawBricks();
  drawPaddle();
  drawBall();
</script>
