# Hexrun.html
Welcome to HEX RUN: Cyber  Cat Full Edition!  Enter the neon grid... and run for your life.  Jump the firewalls... dodge the glitches... and outsmart the system.  Simple controls. Endless speed.  Maximum chaos.  How far can YOU run?  HEX RUN - press start, if you dare."
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hex Run Web</title>
  <style>
    body {
      margin: 0;
      background: black;
      color: white;
      font-family: monospace;
      overflow: hidden;
    }
    canvas {
      display: block;
      margin: auto;
      background: black;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="800" height="400"></canvas>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    // Game state
    let running = true;
    let speed = 4;
    let gravity = 0.6;
    let jumpStrength = -12;
    let score = 0;

    // Player
    const player = {
      x: 100,
      y: 300,
      w: 20,
      h: 20,
      dy: 0,
      jumping: false
    };

    // Obstacles & drones
    const obstacles = [];
    const drones = [];

    function spawnObstacle() {
      obstacles.push({
        x: canvas.width,
        y: 320,
        w: 20,
        h: 40
      });
    }

    function spawnDrone() {
      drones.push({
        x: canvas.width,
        y: 200 + Math.random() * 100 - 50, // float in mid-air
        w: 20,
        h: 20
      });
    }

    // Background stars
    const stars = [];
    for (let i = 0; i < 100; i++) {
      stars.push({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        r: Math.random() * 2
      });
    }

    // Input
    document.addEventListener("keydown", (e) => {
      if (e.key === "ArrowUp" && !player.jumping) {
        player.dy = jumpStrength;
        player.jumping = true;
      } else if (e.key === "p") {
        running = !running;
        if (running) gameLoop();
      } else if (e.key === "ArrowRight") {
        speed += 1;
      } else if (e.key === "ArrowLeft") {
        if (speed > 1) speed -= 1;
      }
    });

    function update() {
      // Player physics
      player.y += player.dy;
      player.dy += gravity;

      if (player.y > 300) {
        player.y = 300;
        player.dy = 0;
        player.jumping = false;
      }

      // Obstacles
      obstacles.forEach(o => o.x -= speed);
      if (obstacles.length && obstacles[0].x + obstacles[0].w < 0) {
        obstacles.shift();
        score++;
      }

      // Drones
      drones.forEach(d => d.x -= speed);
      if (drones.length && drones[0].x + drones[0].w < 0) {
        drones.shift();
        score++;
      }

      // Collision check
      [...obstacles, ...drones].forEach(obj => {
        if (player.x < obj.x + obj.w &&
            player.x + player.w > obj.x &&
            player.y < obj.y + obj.h &&
            player.y + player.h > obj.y) {
          running = false;
          alert("Game Over! Score: " + score);
          document.location.reload();
        }
      });

      // Random spawns
      if (Math.random() < 0.02) spawnObstacle();
      if (Math.random() < 0.01) spawnDrone();
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Background stars
      ctx.fillStyle = "white";
      stars.forEach(s => {
        ctx.beginPath();
        ctx.arc(s.x, s.y, s.r, 0, Math.PI * 2);
        ctx.fill();
        s.x -= 0.2;
        if (s.x < 0) s.x = canvas.width;
      });

      // Ground
      ctx.fillStyle = "green";
      ctx.fillRect(0, 340, canvas.width, 5);

      // Player
      ctx.fillStyle = "cyan";
      ctx.fillRect(player.x, player.y, player.w, player.h);

      // Obstacles (walls)
      ctx.fillStyle = "red";
      obstacles.forEach(o => ctx.fillRect(o.x, o.y, o.w, o.h));

      // Drones
      ctx.fillStyle = "orange";
      drones.forEach(d => {
        ctx.beginPath();
        ctx.arc(d.x + d.w / 2, d.y + d.h / 2, d.w / 2, 0, Math.PI * 2);
        ctx.fill();
      });

      // Score
      ctx.fillStyle = "white";
      ctx.fillText("Score: " + score, 10, 20);
      ctx.fillText("Speed: " + speed, 10, 40);
    }

    function gameLoop() {
      if (!running) return;
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
