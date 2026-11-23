
<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Vision Finder AI</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #050817;
  color: white;
  font-family: Arial, sans-serif;
}

header {
  padding: 18px;
  display: flex;
  gap: 12px;
  align-items: center;
}

.logo {
  width: 48px;
  height: 48px;
  border-radius: 15px;
  background: linear-gradient(135deg,#2563eb,#a855f7);
  display: grid;
  place-items: center;
  font-size: 25px;
}

h1 {
  font-size: 20px;
  margin: 0;
}

header p {
  color: #9ca3af;
  margin: 5px 0 0;
  font-size: 13px;
}

.camera-box {
  margin: 10px 14px;
  position: relative;
  border-radius: 22px;
  overflow: hidden;
  background: #111827;
}

video {
  width: 100%;
  display: block;
  aspect-ratio: 4 / 3;
  object-fit: cover;
}

.live {
  position: absolute;
  top: 14px;
  left: 14px;
  background: #ef4444;
  padding: 8px 13px;
  border-radius: 30px;
  font-weight: bold;
}

.status {
  position: absolute;
  top: 14px;
  right: 14px;
  background: #111827dd;
  padding: 8px 12px;
  border-radius: 30px;
  font-size: 13px;
}

.scan {
  position: absolute;
  bottom: 14px;
  left: 10%;
  width: 80%;
  text-align: center;
  background: #111827dd;
  padding: 12px;
  border-radius: 30px;
  font-size: 14px;
}

.controls {
  display: flex;
  gap: 8px;
  padding: 0 14px;
}

button {
  border: 0;
  border-radius: 14px;
  padding: 13px 12px;
  font-size: 14px;
  font-weight: bold;
  color: white;
  background: #6d28d9;
}

.secondary {
  background: #1f2937;
  border: 1px solid #374151;
}

.section {
  padding: 20px 14px 10px;
}

.section-title {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 8px;
}

.section h2 {
  font-size: 19px;
  margin: 0;
}

.section p {
  color: #9ca3af;
  font-size: 13px;
}

.auto {
  background: #6d28d9;
  padding: 9px 12px;
  border-radius: 30px;
  font-size: 13px;
}

.gallery {
  display: grid;
  grid-template-columns: repeat(2,1fr);
  gap: 12px;
  padding: 0 14px 20px;
}

.card {
  background: #111827;
  border: 1px solid #293044;
  border-radius: 16px;
  overflow: hidden;
}

.card img {
  width: 100%;
  aspect-ratio: 1 / 1;
  object-fit: cover;
  display: block;
}

.card p {
  margin: 10px;
  font-size: 13px;
  color: #d1d5db;
}

.note {
  margin: 0 14px 20px;
  padding: 14px;
  background: #111827;
  border-radius: 14px;
  color: #9ca3af;
  font-size: 13px;
  line-height: 1.5;
}

footer {
  display: flex;
  justify-content: space-around;
  padding: 18px 8px;
  border-top: 1px solid #1f2937;
  color: #9ca3af;
  font-size: 13px;
}
</style>
</head>

<body>

<header>
  <div class="logo">📷</div>
  <div>
    <h1>Vision Finder <span style="color:#a855f7">AI</span></h1>
    <p>Live Camera · Smart Search</p>
  </div>
</header>

<div class="camera-box">
  <video id="camera" autoplay playsinline muted></video>
  <div class="live">● LIVE</div>
  <div class="status" id="status">Camera Off</div>
  <div class="scan" id="scanText">
    Start camera to begin
  </div>
</div>

<div class="controls">
  <button onclick="startCamera()">▶ Start</button>
  <button class="secondary" onclick="switchCamera()">↻ Switch</button>
  <button class="secondary" onclick="stopCamera()">■ Stop</button>
</div>

<section class="section">
  <div class="section-title">
    <h2>🔍 Live Image Search</h2>
    <button class="auto" id="autoBtn" onclick="toggleAuto()">
      ⚡ Auto: OFF
    </button>
  </div>
  <p>ক্যামেরার দৃশ্যের মতো মিল থাকা ছবি</p>
</section>

<div class="gallery" id="gallery">
  <div class="card">
    <img src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=500" alt="Shoe">
    <p>Similar Shoes</p>
  </div>

  <div class="card">
    <img src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=500" alt="Running shoe">
    <p>Running Shoes</p>
  </div>

  <div class="card">
    <img src="https://images.unsplash.com/photo-1495555961986-6d4c1ecb7be3?w=500" alt="Fashion shoe">
    <p>Fashion Shoes</p>
  </div>

  <div class="card">
    <img src="https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=500" alt="Sports shoe">
    <p>Sports Shoes</p>
  </div>
</div>

<div class="note" id="message">
  Demo images shown. Real internet image search will be connected later.
</div>

<footer>
  <span>⌂ Home</span>
  <span>◷ History</span>
  <span>☆ Favorites</span>
  <span>ⓘ About</span>
</footer>

<script>
let stream = null;
let facingMode = "environment";
let autoMode = false;
let timer = null;

const video = document.getElementById("camera");

async function startCamera() {
  try {
    stopCamera();

    stream = await navigator.mediaDevices.getUserMedia({
      video: {
        facingMode: facingMode
      },
      audio: false
    });

    video.srcObject = stream;
    await video.play();

    document.getElementById("status").textContent = "Camera Active";
    document.getElementById("scanText").textContent = "Live camera running";

  } catch (error) {
    document.getElementById("status").textContent = "Camera Error";
    document.getElementById("scanText").textContent = "Allow camera permission";
    alert("Camera permission দিন। HTTPS website ব্যবহার করুন।");
  }
}

function stopCamera() {
  if (stream) {
    stream.getTracks().forEach(track => track.stop());
    stream = null;
  }

  video.srcObject = null;
  document.getElementById("status").textContent = "Camera Off";
  document.getElementById("scanText").textContent = "Start camera to begin";
}

async function switchCamera() {
  facingMode = facingMode === "environment" ? "user" : "environment";

  if (stream) {
    await startCamera();
  }
}

function toggleAuto() {
  autoMode = !autoMode;

  document.getElementById("autoBtn").textContent =
    autoMode ? "⚡ Auto: ON" : "⚡ Auto: OFF";

  if (autoMode) {
    document.getElementById("scanText").textContent =
      "Auto mode enabled — search coming soon";

    timer = setInterval(() => {
      if (stream) {
        document.getElementById("scanText").textContent =
          "Camera running · visual search pending";
      }
    }, 4000);

  } else {
    clearInterval(timer);
    document.getElementById("scanText").textContent =
      stream ? "Live camera running" : "Start camera to begin";
  }
}
</script>

</body>
</html>
