<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Fortune Spin</title>
  <style>
    body {
      margin: 0;
      min-height: 100vh;
      display: grid;
      place-items: center;
      background: #0f172a;
      color: #fff;
      font-family: system-ui, sans-serif;
      overflow: hidden;
    }
    .wrap { text-align: center; }
    canvas { background: #1e293b; border-radius: 16px; display: block; margin: 0 auto 14px; }
    button {
      padding: 12px 24px;
      font-size: 16px;
      font-weight: 700;
      border: 0;
      border-radius: 10px;
      background: #f59e0b;
      color: #0f172a;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="wrap">
    <canvas id="game" width="340" height="340"></canvas>
    <button id="spinBtn">SPIN & SCATTER</button>
  </div>
  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");
    const btn = document.getElementById("spinBtn");

    const slices = ["2X", "SCATTER", "100", "BONUS", "500", "WILD"];
    const colors = ["#ef4444", "#3b82f6", "#10b981", "#eab308", "#8b5cf6", "#ec4899"];

    let angle = 0;
    let vel = 0;
    let spinning = false;
    let particles = [];

    function spawnScatter(x, y) {
      for (let i = 0; i < 36; i++) {
        const a = (Math.PI * 2 * i) / 36 + Math.random() * 0.2;
        const spd = 2 + Math.random() * 5;
        particles.push({
          x, y,
          vx: Math.cos(a) * spd,
          vy: Math.sin(a) * spd,
          size: 4 + Math.random() * 5,
          color: colors[i % colors.length],
          life: 60
        });
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      const cx = canvas.width / 2;
      const cy = canvas.height / 2;
      const r = 120;
      const step = (Math.PI * 2) / slices.length;

      ctx.save();
      ctx.translate(cx, cy);
      ctx.rotate(angle);

      slices.forEach((label, i) => {
        ctx.beginPath();
        ctx.moveTo(0, 0);
        ctx.arc(0, 0, r, i * step, (i + 1) * step);
        ctx.closePath();
        ctx.fillStyle = colors[i];
        ctx.fill();

        ctx.save();
        ctx.rotate(i * step + step / 2);
        ctx.fillStyle = "#fff";
        ctx.font = "bold 14px sans-serif";
        ctx.fillText(label, r * 0.52, 5);
        ctx.restore();
      });
      ctx.restore();

      // Pointer at top
      ctx.fillStyle = "#f8fafc";
      ctx.beginPath();
      ctx.moveTo(cx, cy - r - 12);
      ctx.lineTo(cx - 10, cy - r + 8);
      ctx.lineTo(cx + 10, cy - r + 8);
      ctx.closePath();
      ctx.fill();

      // Scatter particles
      particles.forEach(p => {
        p.x += p.vx;
        p.y += p.vy;
        p.vy += 0.12;
        p.life--;
        ctx.fillStyle = p.color;
        ctx.fillRect(p.x, p.y, p.size, p.size);
      });
      particles = particles.filter(p => p.life > 0);

      if (spinning) {
        angle += vel;
        vel *= 0.985;
        if (vel < 0.004) {
          spinning = false;
          spawnScatter(cx, cy);
        }
      }

      requestAnimationFrame(draw);
    }

    btn.addEventListener("click", () => {
      if (!spinning) {
        vel = 0.28 + Math.random() * 0.15;
        spinning = true;
      }
    });

    draw();
  </script>
</body>
</html>
