<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Official GST Portal | Spin & Win</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;800&display=swap" rel="stylesheet">
    <style>
        :root {
            --navy: #1a2332;
            --gold: #ff8a00;
            --white: #ffffff;
        }

        body { margin: 0; font-family: 'Inter', sans-serif; background: #e0f2fe; overflow-x: hidden; }

        /* Ticker */
        .ticker-wrap { background: var(--navy); color: white; padding: 10px; position: sticky; top: 0; z-index: 100; overflow: hidden; }
        .ticker { display: flex; white-space: nowrap; animation: move 20s linear infinite; }
        @keyframes move { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        .container { max-width: 1200px; margin: auto; display: flex; flex-wrap: wrap; padding: 20px; }
        
        /* Main Section */
        .main-content { flex: 2; background: white; padding: 30px; border-radius: 30px; min-width: 320px; box-shadow: 0 10px 30px rgba(0,0,0,0.1); }
        .hero-title { font-size: 32px; font-weight: 800; color: var(--navy); margin-bottom: 20px; text-align: center; }

        /* --- SPINNER AREA --- */
        #spinner-section { 
            text-align: center; 
            background: #000; 
            padding: 40px 20px; 
            border-radius: 20px; 
            margin-bottom: 30px;
            scroll-margin-top: 80px; /* For smooth scroll offset */
        }

        .wheel-wrapper {
            position: relative;
            width: 320px;
            height: 320px;
            margin: 0 auto 20px;
        }

        /* The Triangle Pointer */
        .pointer {
            position: absolute;
            top: -10px;
            left: 50%;
            transform: translateX(-50%);
            width: 0; height: 0;
            border-left: 15px solid transparent;
            border-right: 15px solid transparent;
            border-top: 30px solid white;
            z-index: 10;
            filter: drop-shadow(0 2px 5px rgba(0,0,0,0.5));
        }

        canvas {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            border: 8px solid #333;
            box-shadow: 0 0 20px rgba(255,138,0,0.3);
        }

        .spin-btn {
            background: var(--gold);
            color: white;
            border: none;
            padding: 15px 50px;
            font-size: 18px;
            font-weight: 800;
            border-radius: 50px;
            cursor: pointer;
            transition: 0.3s;
            text-transform: uppercase;
        }
        .spin-btn:disabled { background: #555; }

        /* Form */
        .form-box { background: #f1f5f9; padding: 20px; border-radius: 15px; margin-top: 20px; }
        .input-group { display: flex; gap: 10px; margin-bottom: 10px; }
        input { flex: 1; padding: 12px; border: 1px solid #ccc; border-radius: 8px; }

        /* Popup */
        #popup {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9);
            z-index: 1000;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            color: white;
            text-align: center;
        }

        .cta-btn { 
            padding: 15px 30px; border-radius: 10px; color: white; 
            text-decoration: none; font-weight: bold; margin: 10px; display: inline-block;
        }

        @media (max-width: 600px) {
            .wheel-wrapper { width: 280px; height: 280px; }
            .input-group { flex-direction: column; }
        }
    </style>
</head>
<body>

<div class="ticker-wrap">
    <div class="ticker">🚀 GST Registration @ ₹2500 | 💎 ITR Filing @ ₹1000 | ✅ MSME Certificate Free | 📞 Call: 8448073713</div>
</div>

<div class="container">
    <main class="main-content">
        <h1 class="hero-title">Official Tax Compliance Portal</h1>

        <div class="form-box">
            <p style="margin-top:0; font-weight:bold;">Enter details to enable Spinner:</p>
            <div class="input-group">
                <input type="text" id="name" placeholder="Business Name">
                <input type="tel" id="phone" placeholder="Mobile Number">
            </div>
        </div>

        <div id="spinner-section">
            <div class="wheel-wrapper">
                <div class="pointer"></div>
                <canvas id="wheelCanvas" width="500" height="500"></canvas>
            </div>
            <button class="spin-btn" id="spinBtn" onclick="startSpin()">Spin Now</button>
        </div>
    </main>
</div>

<div id="popup">
    <h2 style="color:var(--gold); font-size: 40px;">CONGRATULATIONS!</h2>
    <p id="win-msg" style="font-size: 24px;"></p>
    <div>
        <a href="tel:8448073713" class="cta-btn" style="background:#2563eb">CALL EXPERT</a>
        <a href="https://wa.me/918448073713" class="cta-btn" style="background:#25D366">WHATSAPP</a>
    </div>
    <p onclick="location.reload()" style="margin-top:30px; cursor:pointer; text-decoration:underline;">Close & Reset</p>
</div>

<script>
    const canvas = document.getElementById("wheelCanvas");
    const ctx = canvas.getContext("2d");
    const sectors = [
        { label: "GST REG ₹2500", color: "#111" },
        { label: "ITR ₹1000", color: "#333" },
        { label: "TRADE LIC ₹2000", color: "#111" },
        { label: "TDS FILING ₹500", color: "#333" },
        { label: "MSME AMEND ₹50", color: "#111" },
        { label: "HUF PAN ₹500", color: "#333" },
        { label: "TAN NO ₹800", color: "#111" },
        { label: "FLAT 10% OFF", color: "#ff8a00" }
    ];

    const numSectors = sectors.length;
    const arc = 2 * Math.PI / numSectors;
    let ang = 0;
    let spinVar = null;

    // Draw Wheel
    function drawWheel() {
        ctx.clearRect(0, 0, 500, 500);
        sectors.forEach((s, i) => {
            const angle = ang + i * arc;
            ctx.beginPath();
            ctx.fillStyle = s.color;
            ctx.moveTo(250, 250);
            ctx.arc(250, 250, 240, angle, angle + arc);
            ctx.lineTo(250, 250);
            ctx.fill();
            ctx.stroke();

            // Text
            ctx.save();
            ctx.translate(250, 250);
            ctx.rotate(angle + arc / 2);
            ctx.textAlign = "right";
            ctx.fillStyle = "#fff";
            ctx.font = "bold 22px Inter";
            ctx.fillText(s.label, 220, 10);
            ctx.restore();
        });
    }

    function startSpin() {
        const name = document.getElementById('name').value;
        const phone = document.getElementById('phone').value;

        if(!name || !phone) {
            alert("Please enter Name and Phone to Spin!");
            return;
        }

        // AUTO-SCROLL to spinner
        document.getElementById('spinner-section').scrollIntoView({ behavior: 'smooth' });

        const btn = document.getElementById('spinBtn');
        btn.disabled = true;

        let speed = Math.random() * 0.4 + 0.4; // Initial speed
        let friction = 0.985; // Slow down effect

        function animate() {
            if (speed < 0.002) {
                // Stop & Show Result
                const index = Math.floor(numSectors - (ang % (2 * Math.PI)) / arc) % numSectors;
                document.getElementById('win-msg').innerText = "You Won: " + sectors[index].label;
                setTimeout(() => {
                    document.getElementById('popup').style.display = 'flex';
                }, 500);
                return;
            }
            speed *= friction;
            ang += speed;
            drawWheel();
            requestAnimationFrame(animate);
        }
        animate();
    }

    drawWheel(); // Initial draw
</script>

</body>
</html>
