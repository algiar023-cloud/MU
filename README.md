<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VibeStream - Modern Music</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;700;800&display=swap');

        :root {
            --black: #000000;
            --white: #ffffff;
            --gray: #f0f0f0;
            --accent: #333333;
        }

        body {
            background-color: var(--white);
            font-family: 'Plus Jakarta Sans', sans-serif;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            display: flex;
            gap: 40px;
            width: 90%;
            max-width: 1000px;
            padding: 40px;
            border: 4px solid var(--black);
            box-shadow: 15px 15px 0px var(--black);
        }

        /* Player Side */
        .player-section { flex: 1; }

        .album-art {
            width: 100%;
            height: 300px;
            background: var(--black);
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 80px;
            margin-bottom: 20px;
        }

        .song-details h1 { font-size: 2rem; margin: 0; text-transform: uppercase; }
        .song-details p { color: #666; margin: 5px 0 20px 0; }

        /* Controls */
        .progress-container { width: 100%; margin-bottom: 20px; }
        input[type="range"] {
            width: 100%;
            accent-color: var(--black);
            cursor: pointer;
        }

        .controls {
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 30px;
        }

        .btn {
            background: none;
            border: 2px solid var(--black);
            padding: 15px;
            font-weight: bold;
            cursor: pointer;
            transition: 0.2s;
        }

        .btn-play {
            background: var(--black);
            color: white;
            width: 60px;
            height: 60px;
            border-radius: 50%;
        }

        .btn:hover { transform: translateY(-3px); box-shadow: 4px 4px 0 var(--black); }

        /* Queue Side */
        .queue-section {
            width: 300px;
            border-left: 2px solid var(--black);
            padding-left: 20px;
        }

        .queue-list { list-style: none; padding: 0; max-height: 400px; overflow-y: auto; }
        .queue-list li {
            padding: 10px;
            border: 1px solid var(--black);
            margin-bottom: 10px;
            cursor: pointer;
            font-size: 14px;
        }

        .queue-list li.active { background: var(--black); color: white; }

        .upload-area {
            margin-top: 20px;
            border: 2px dashed var(--black);
            padding: 15px;
            text-align: center;
            cursor: pointer;
        }
    </style>
</head>
<body>

<div class="container">
    <div class="player-section">
        <div class="album-art" id="art">♪</div>
        <div class="song-details">
            <h1 id="title">Ready to Play</h1>
            <p id="artist">Upload your music online</p>
        </div>
        
        <div class="progress-container">
            <input type="range" id="progress" value="0">
        </div>

        <div class="controls">
            <button class="btn" onclick="prevSong()">PREV</button>
            <button class="btn btn-play" id="playBtn" onclick="togglePlay()">▶</button>
            <button class="btn" onclick="nextSong()">NEXT</button>
        </div>
    </div>

    <div class="queue-section">
        <h3>QUEUE</h3>
        <ul id="playlist" class="queue-list"></ul>
        <label class="upload-area">
            UPLOAD MP3
            <input type="file" id="fileInput" hidden accept="audio/*" multiple>
        </label>
    </div>
</div>

<audio id="audio"></audio>

<script>
    const audio = document.getElementById('audio');
    const playBtn = document.getElementById('playBtn');
    const fileInput = document.getElementById('fileInput');
    const playlist = document.getElementById('playlist');
    const progress = document.getElementById('progress');

    let songs = [];
    let currentIndex = 0;

    fileInput.onchange = (e) => {
        const files = Array.from(e.target.files);
        files.forEach(file => {
            const url = URL.createObjectURL(file);
            songs.push({ title: file.name, url: url });
        });
        updatePlaylist();
        if (audio.paused) loadSong(0);
    };

    function updatePlaylist() {
        playlist.innerHTML = songs.map((s, i) => 
            `<li class="${i === currentIndex ? 'active' : ''}" onclick="loadSong(${i})">${s.title}</li>`
        ).join('');
    }

    function loadSong(index) {
        currentIndex = index;
        audio.src = songs[index].url;
        document.getElementById('title').innerText = songs[index].title;
        audio.play();
        playBtn.innerText = "⏸";
        updatePlaylist();
    }

    function togglePlay() {
        if (audio.paused) { audio.play(); playBtn.innerText = "⏸"; }
        else { audio.pause(); playBtn.innerText = "▶"; }
    }

    function nextSong() { if (currentIndex < songs.length - 1) loadSong(currentIndex + 1); }
    function prevSong() { if (currentIndex > 0) loadSong(currentIndex - 1); }

    audio.ontimeupdate = () => {
        progress.value = (audio.currentTime / audio.duration) * 100 || 0;
    };

    progress.oninput = () => {
        audio.currentTime = (progress.value / 100) * audio.duration;
    };
</script>
</body>
</html>
