<!DOCTYPE html>
<html lang="vi">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Chúc 20/10 - Trái Tim Vũ Trụ</title>
<style>
  body, html {
    margin: 0; padding: 0; overflow: hidden;
    background: #000011;
    height: 100vh;
    color: #fff;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    text-align: center;
  }
  canvas {
    display: block;
    position: absolute;
    top:0; left:0;
    width: 100%; height: 100%;
  }
  #message {
    position: absolute;
    width: 100%;
    bottom: 50px;
    font-size: 2.5em;
    font-weight: bold;
    text-shadow: 0 0 10px #ff6699, 0 0 20px #ff3366;
  }
</style>
</head>
<body>

<canvas id="canvas"></canvas>
<div id="message">Chúc Mừng 20/10 - Ngày Phụ Nữ Việt Nam!</div>

<script>
  const canvas = document.getElementById('canvas');
  const ctx = canvas.getContext('2d');
  let W, H;

  function resize() {
    W = window.innerWidth;
    H = window.innerHeight;
    canvas.width = W;
    canvas.height = H;
  }
  resize();
  window.addEventListener('resize', resize);

  // Sao rơi
  class Star {
    constructor() {
      this.reset();
    }
    reset() {
      this.x = Math.random() * W;
      this.y = Math.random() * H;
      this.size = Math.random() * 1.5 + 0.5;
      this.speed = Math.random() * 1 + 0.2;
      this.opacity = Math.random() * 0.8 + 0.2;
    }
    update() {
      this.y += this.speed;
      if (this.y > H) this.reset();
    }
    draw() {
      ctx.beginPath();
      ctx.fillStyle = `rgba(255, 255, 255, ${this.opacity})`;
      ctx.shadowColor = 'white';
      ctx.shadowBlur = 5;
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fill();
      ctx.shadowBlur = 0;
    }
  }

  // Pháo hoa
  class FireworkParticle {
    constructor(x, y, angle, speed, color) {
      this.x = x;
      this.y = y;
      this.angle = angle;
      this.speed = speed;
      this.vx = Math.cos(angle) * speed;
      this.vy = Math.sin(angle) * speed;
      this.gravity = 0.05;
      this.alpha = 1;
      this.decay = Math.random() * 0.02 + 0.015;
      this.color = color;
      this.size = Math.random() * 2 + 1;
    }
    update() {
      this.vy += this.gravity;
      this.x += this.vx;
      this.y += this.vy;
      this.alpha -= this.decay;
      if (this.alpha < 0) this.alpha = 0;
    }
    draw() {
      ctx.beginPath();
      ctx.fillStyle = `rgba(${this.color.r}, ${this.color.g}, ${this.color.b}, ${this.alpha})`;
      ctx.shadowColor = `rgba(${this.color.r}, ${this.color.g}, ${this.color.b}, ${this.alpha})`;
      ctx.shadowBlur = 8;
      ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
      ctx.fill();
      ctx.shadowBlur = 0;
    }
  }

  // Hình trái tim
  function drawHeart(cx, cy, size, glow) {
    ctx.save();
    ctx.translate(cx, cy);
    ctx.scale(size, size);
    ctx.beginPath();
    ctx.moveTo(0, -1);
    ctx.bezierCurveTo(0.5, -1.5, 1.5, -1, 1.5, -0.25);
    ctx.bezierCurveTo(1.5, 0.5, 0, 1.4, 0, 1.4);
    ctx.bezierCurveTo(0, 1.4, -1.5, 0.5, -1.5, -0.25);
    ctx.bezierCurveTo(-1.5, -1, -0.5, -1.5, 0, -1);
    ctx.closePath();
    ctx.fillStyle = 'deeppink';
    ctx.shadowColor = 'deeppink';
    ctx.shadowBlur = glow;
    ctx.fill();
    ctx.restore();
  }

  // Tạo sao rơi
  const stars = [];
  for (let i = 0; i < 120; i++) {
    stars.push(new Star());
  }

  // Pháo hoa
  let fireworks = [];
  function createFirework() {
    // Nơi nổ gần trái tim
    const cx = W / 2 + (Math.random() * 200 - 100);
    const cy = H / 2 - 50 + (Math.random() * 100 - 50);
    const colors = [
      {r:255,g:69,b:0}, {r:255,g:105,b:180}, {r:135,g:206,b:250}, {r:255,g:215,b:0}, {r:0,g:255,b:127}
    ];
    const color = colors[Math.floor(Math.random() * colors.length)];
    const particleCount = 30 + Math.floor(Math.random()*20);
    for (let i = 0; i < particleCount; i++) {
      const angle = Math.random() * 2 * Math.PI;
      const speed = Math.random() * 4 + 1;
      fireworks.push(new FireworkParticle(cx, cy, angle, speed, color));
    }
  }

  let fireworkTimer = 0;

  function animate() {
    ctx.clearRect(0, 0, W, H);

    // Vẽ sao rơi
    for (let star of stars) {
      star.update();
      star.draw();
    }

    // Vẽ trái tim phát sáng
    drawHeart(W/2, H/2, 100, 30);

    // Vẽ pháo hoa
    for (let i = fireworks.length -1; i >= 0; i--) {
      let p = fireworks[i];
      p.update();
      if (p.alpha <= 0) {
        fireworks.splice(i, 1);
      } else {
        p.draw();
      }
    }

    // Tạo pháo hoa mới sau mỗi 60 frames (~1 giây ở 60fps)
    fireworkTimer++;
    if (fireworkTimer > 60) {
      createFirework();
      fireworkTimer = 0;
    }

    requestAnimationFrame(animate);
  }

  animate();
</script>

</body>
</html>
