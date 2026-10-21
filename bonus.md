---
layout: default
title: Arcade & High Scores
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

  <h1>Espace Arcade</h1>
  <p>Do your best :p </p>

  <div style="display: flex; gap: 30px; flex-wrap: wrap; align-items: flex-start;">
    
    <!-- Zone de Jeu -->
    <div class="card" style="flex: 1; min-width: 300px; text-align: center;">
      <canvas id="gameCanvas" width="320" height="320" style="background: #000; border-radius: 6px; border: 1px solid #334155;"></canvas>
      <div style="margin-top: 15px;">
        <button id="startBtn" style="background: #0284c7; color: white; border: none; padding: 8px 16px; border-radius: 4px; cursor: pointer; font-weight: 600;">Lancer la partie</button>
      </div>
    </div>

    <!-- Tableau des Scores -->
    <div class="card" style="width: 280px;">
      <h3 style="margin-top: 0; color: #f8fafc;">🏆 Top 10 High Scores</h3>
      <ol id="leaderboard" style="padding-left: 20px; color: #cbd5e1;">
        <li>Chargement des scores...</li>
      </ol>
    </div>

  </div>

</div>
