<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Flappy Bird Clone</title>
  <style>
    body { margin: 0; background: #70c5ce; overflow: hidden; }
    canvas { display: block; margin: auto; background: #70c5ce; }
  </style>
</head>
<body>
<canvas id="gameCanvas" width="400" height="600"></canvas>
<script>
  const canvas = document.getElementById('gameCanvas');
  const ctx = canvas.getContext('2d');

  let bird = { x: 50, y: 150, width: 20, height: 20, velocity: 0 };
  const gravity = 0.5;
  const jump = -8;
  let pipes = [];
  let frame = 0;
  let score = 0;
  let gameOver = false;

  function resetGame() {
    bird = { x: 50, y: 150, width: 20, height: 20, velocity: 0 };
    pipes = [];
    frame = 0;
    score = 0;
    gameOver = false;
  }

  function createPipe() {
    const gap = 120;
    const minHeight = 20;
    const maxHeight = canvas.height - gap - minHeight;
    const topHeight = Math.floor(Math.random() * (maxHeight - minHeight + 1)) + minHeight;
    const bottomY = topHeight + gap;
    pipes.push({
      x: canvas.width,
      top: { y: 0, height: topHeight },
      bottom: { y: bottomY, height: canvas.height - bottomY },
      width: 40
    });
  }

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw the bird
    ctx.fillStyle = 'yellow';
    ctx.fillRect(bird.x, bird.y, bird.width, bird.height);

    // Draw the pipes
    ctx.fillStyle = 'green';
    pipes.forEach(pipe => {
      // Top pipe
      ctx.fillRect(pipe.x, pipe.top.y, pipe.width, pipe.top.height);
      // Bottom pipe
      ctx.fillRect(pipe.x, pipe.bottom.y, pipe.width, pipe.bottom.height);
    });

    // Draw score
    ctx.fillStyle = 'black';
    ctx.font = '20px Arial';
    ctx.fillText("Score: " + score, 10, 25);

    // Display Game Over
    if (gameOver) {
      ctx.fillStyle = 'red';
      ctx.font = '40px Arial';
      ctx.fillText("Game Over", canvas.width / 2 - 100, canvas.height / 2);
      ctx.font = '20px Arial';
      ctx.fillText("Click to Restart", canvas.width / 2 - 70, canvas.height / 2 + 30);
    }
  }

  function update() {
    if (gameOver) return;

    bird.velocity += gravity;
    bird.y += bird.velocity;

    // Create new pipe every 90 frames (roughly every 1.5 seconds at 60fps)
    if (frame % 90 === 0) {
      createPipe();
    }

    // Move pipes to the left
    pipes.forEach(pipe => {
      pipe.x -= 2;
    });

    // Remove off-screen pipes and update score
    pipes = pipes.filter(pipe => {
      if (pipe.x + pipe.width < 0) {
        score++;
        return false;
      }
      return true;
    });

    // Check for collisions with pipes
    for (let pipe of pipes) {
      // Collision with top pipe
      if (
        bird.x < pipe.x + pipe.width &&
        bird.x + bird.width > pipe.x &&
        bird.y < pipe.top.height
      ) {
        gameOver = true;
      }
      // Collision with bottom pipe
      if (
        bird.x < pipe.x + pipe.width &&
        bird.x + bird.width > pipe.x &&
        bird.y + bird.height > pipe.bottom.y
      ) {
        gameOver = true;
      }
    }

    // Check if bird hits the ground or flies off the top
    if (bird.y + bird.height > canvas.height || bird.y < 0) {
      gameOver = true;
    }

    frame++;
  }

  function loop() {
    update();
    draw();
    requestAnimationFrame(loop);
  }

  // Make the bird flap on key press or mouse click/touch
  document.addEventListener('keydown', function(e) {
    if (e.code === 'Space' || e.code === 'ArrowUp') {
      if (!gameOver) {
        bird.velocity = jump;
      }
    }
  });

  document.addEventListener('click', function() {
    if (gameOver) {
      resetGame();
    } else {
      bird.velocity = jump;
    }
  });

  canvas.addEventListener('touchstart', function(e) {
    e.preventDefault();
    if (gameOver) {
      resetGame();
    } else {
      bird.velocity = jump;
    }
  });

  // Start the game loop
  loop();
</script>
</body>
</html>
