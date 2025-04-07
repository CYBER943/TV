<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>3D Animated Remote</title>
  <style>
    body {
      margin: 0;
      background: linear-gradient(to right, #2c3e50, #4ca1af);
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      font-family: Arial, sans-serif;
    }

    .container {
      display: flex;
      gap: 50px;
      align-items: center;
    }

    .remote {
      background: #1c1c1c;
      border-radius: 40px;
      padding: 30px 20px;
      box-shadow: inset 0 0 10px #000, 0 20px 40px rgba(0, 0, 0, 0.6);
      display: flex;
      flex-direction: column;
      gap: 15px;
      transform: perspective(600px) rotateX(15deg);
      transition: transform 0.3s ease-in-out;
    }

    .remote:hover {
      transform: perspective(600px) rotateX(5deg);
    }

    .row {
      display: flex;
      justify-content: center;
      gap: 10px;
      flex-wrap: wrap;
    }

    .btn {
      background: #444;
      color: white;
      border: none;
      border-radius: 50%;
      padding: 15px;
      width: 50px;
      height: 50px;
      font-weight: bold;
      font-size: 16px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
      transition: all 0.2s ease;
      cursor: pointer;
      position: relative;
    }

    .btn:active {
      transform: scale(0.95);
      background: #666;
    }

    .btn:after {
      content: '';
      position: absolute;
      width: 100%;
      height: 100%;
      top: 0;
      left: 0;
      background: rgba(0, 255, 0, 0.1);
      border-radius: 50%;
      opacity: 0;
      transition: opacity 0.3s ease;
    }

    .btn.clicked:after {
      opacity: 1;
      animation: glow 0.6s ease-out;
    }

    @keyframes glow {
      0% { box-shadow: 0 0 0px lime; }
      50% { box-shadow: 0 0 20px lime; }
      100% { box-shadow: 0 0 0px lime; }
    }

    .label-row {
      justify-content: space-evenly;
    }

    .btn.pill {
      border-radius: 20px;
      width: auto;
      padding: 10px 20px;
    }

    .title {
      text-align: center;
      color: #eee;
      margin-bottom: 10px;
      font-size: 18px;
      font-weight: bold;
    }

    .screen {
      background: #111;
      color: lime;
      padding: 10px;
      margin-top: 10px;
      text-align: center;
      font-size: 1.2rem;
      border-radius: 10px;
      box-shadow: inset 0 0 10px #0f0;
    }

    .tv {
      width: 400px;
      height: 250px;
      background: black;
      border: 10px solid #222;
      border-radius: 10px;
      box-shadow: 0 0 20px rgba(0,0,0,0.8);
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      position: relative;
    }

    .tv video {
      width: 100%;
      height: 100%;
      display: none;
    }

    .tv.on video {
      display: block;
    }

    .tv.off::before {
      content: "TV is OFF";
      color: white;
      font-size: 1.5rem;
      position: absolute;
    }

    .volume-bar {
      position: absolute;
      bottom: 10px;
      left: 10px;
      width: 80%;
      height: 8px;
      background: rgba(255, 255, 255, 0.2);
      border-radius: 4px;
      overflow: hidden;
      display: none;
    }

    .volume-fill {
      height: 100%;
      background: lime;
      transition: width 0.3s;
    }

    .tv.on .volume-bar {
      display: block;
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="remote">
      <div class="title">Smart Remote</div>
      <div class="screen" id="screen">Press any button</div>

      <div class="row label-row">
        <button class="btn pill" onclick="toggleTV()">Live</button>
        <button class="btn pill" onclick="nextChannel()">Ch+</button>
        <button class="btn pill" onclick="prevChannel()">Ch-</button>
        <button class="btn pill" onclick="toggleMute()">Mute</button>
      </div>

      <div class="row"><button class="btn">↑</button></div>
      <div class="row"><button class="btn">←</button><button class="btn">OK</button><button class="btn">→</button></div>
      <div class="row"><button class="btn">↓</button></div>

      <div class="row">
        <button class="btn" onclick="changeVolume(10)">Vol+</button>
        <button class="btn" onclick="changeVolume(-10)">Vol-</button>
      </div>

      <div class="row">
        <button class="btn">1</button><button class="btn">2</button><button class="btn">3</button>
      </div>
      <div class="row">
        <button class="btn">4</button><button class="btn">5</button><button class="btn">6</button>
      </div>
      <div class="row">
        <button class="btn">7</button><button class="btn">8</button><button class="btn">9</button>
      </div>
      <div class="row">
        <button class="btn">*</button><button class="btn">0</button><button class="btn">#</button>
      </div>

      <div class="row">
        <button class="btn">X</button>
        <button class="btn">O</button>
        <button class="btn">⏺</button>
      </div>
    </div>

    <div class="tv off" id="tv">
      <video id="tvVideo" src="" autoplay loop muted></video>
      <div class="volume-bar"><div class="volume-fill" id="volumeFill" style="width: 50%;"></div></div>
    </div>
  </div>

  <audio id="beep" src="https://www.soundjay.com/buttons/sounds/button-16.mp3"></audio>

  <script>
    const buttons = document.querySelectorAll('.btn');
    const screen = document.getElementById('screen');
    const beep = document.getElementById('beep');
    const tv = document.getElementById('tv');
    const video = document.getElementById('tvVideo');
    const volumeFill = document.getElementById('volumeFill');

    const channels = [
      { name: 'Nature World', src: 'https://www.w3schools.com/html/mov_bbb.mp4' },
      { name: 'Cinema Reel', src: 'https://www.w3schools.com/html/movie.mp4' },
      { name: 'Floral Loop', src: 'https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4' }
    ];

    let currentChannel = 0;
    let volume = 50;
    let isMuted = false;

    function toggleTV() {
      if (tv.classList.contains('off')) {
        screen.textContent = 'Booting up...';
        setTimeout(() => {
          tv.classList.remove('off');
          tv.classList.add('on');
          video.src = channels[currentChannel].src;
          video.muted = isMuted;
          video.play();
          screen.textContent = `Now Playing: ${channels[currentChannel].name}`;
        }, 1000);
      } else {
        tv.classList.add('off');
        tv.classList.remove('on');
        video.pause();
        screen.textContent = 'TV Turned OFF';
      }
    }

    function nextChannel() {
      if (!tv.classList.contains('on')) return;
      currentChannel = (currentChannel + 1) % channels.length;
      video.src = channels[currentChannel].src;
      video.play();
      screen.textContent = `Now Playing: ${channels[currentChannel].name}`;
    }

    function prevChannel() {
      if (!tv.classList.contains('on')) return;
      currentChannel = (currentChannel - 1 + channels.length) % channels.length;
      video.src = channels[currentChannel].src;
      video.play();
      screen.textContent = `Now Playing: ${channels[currentChannel].name}`;
    }

    function changeVolume(delta) {
      volume = Math.min(100, Math.max(0, volume + delta));
      volumeFill.style.width = volume + '%';
      screen.textContent = `Volume: ${volume}`;
    }

    function toggleMute() {
      isMuted = !isMuted;
      video.muted = isMuted;
      screen.textContent = isMuted ? 'Muted' : `Volume: ${volume}`;
    }

    buttons.forEach(btn => {
      btn.addEventListener('click', () => {
        btn.classList.add('clicked');
        if (!['Live', 'Ch+', 'Ch-', 'Vol+', 'Vol-', 'Mute'].includes(btn.innerText)) {
          screen.textContent = `Button \"${btn.innerText}\" pressed`;
        }
        beep.currentTime = 0;
        beep.play();
        setTimeout(() => btn.classList.remove('clicked'), 300);
      });
    });
  </script>
</body>
</html>
