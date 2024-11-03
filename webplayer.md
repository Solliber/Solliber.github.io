<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Web Player with Dark Mode</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://vjs.zencdn.net/7.21.1/video-js.css" rel="stylesheet" />
    <style>
        body.dark-mode {
            background-color: #121212;
            color: #ffffff;
        }
        #playerContainer {
            max-width: 800px;
            margin: 20px auto;
            background-color: #000;
            border-radius: 8px;
            overflow: hidden;
            transition: background-color 0.3s;
        }
        .custom-controls {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: #333;
            padding: 10px;
            color: #fff;
        }
        .custom-controls button,
        .custom-controls select,
        .custom-controls input {
            margin: 5px;
        }
        .progress-bar-container {
            flex-grow: 1;
            margin: 0 10px;
            height: 5px;
            background-color: #666;
            cursor: pointer;
        }
        .progress-bar {
            width: 0%;
            height: 100%;
            background-color: #28a745;
        }
    </style>
</head>
<body>
    <div id="playerContainer" class="container-fluid">
        <video id="videoPlayer" class="video-js vjs-default-skin w-100" controls preload="auto" height="450">
            <track id="subtitleTrack" label="English" kind="subtitles" srclang="en" default>
            Your browser does not support the video tag.
        </video>
        <div class="custom-controls">
            <button class="btn btn-success" onclick="playVideo()">Play</button>
            <button class="btn btn-danger" onclick="pauseVideo()">Pause</button>
            <button class="btn btn-primary" onclick="seekBackward()"><< 10s</button>
            <button class="btn btn-primary" onclick="seekForward()">10s >></button>
            <button class="btn btn-warning" onclick="toggleFullScreen()">Full Screen</button>
            <button class="btn btn-secondary" onclick="toggleDarkMode()">Dark Mode</button>
            <input type="range" id="volumeControl" class="form-range w-25" min="0" max="1" step="0.1" onchange="changeVolume(this.value)" />
            <select id="speedControl" class="form-select w-25" onchange="changePlaybackSpeed(this.value)">
                <option value="0.5">0.5x</option>
                <option value="1" selected>1x (Normal)</option>
                <option value="1.5">1.5x</option>
                <option value="2">2x</option>
            </select>
        </div>
        <div class="custom-controls">
            <button class="btn btn-info" onclick="previousVideo()">Previous</button>
            <button class="btn btn-info" onclick="nextVideo()">Next</button>
            <select id="chapterSelect" class="form-select" onchange="goToChapter(this.value)">
                <option value="">Select Chapter</option>
            </select>
        </div>
        <div class="progress-bar-container" onclick="seek(event)">
            <div class="progress-bar" id="progressBar"></div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <script src="https://vjs.zencdn.net/7.21.1/video.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/hls.js@latest"></script>
    <script>
        var player = videojs('videoPlayer');
        const progressBar = document.getElementById('progressBar');
        const chapters = [
            { time: 0, title: 'Intro' },
            { time: 60, title: 'Chapter 1' },
            { time: 180, title: 'Chapter 2' },
            { time: 300, title: 'Chapter 3' }
        ];
        const playlist = [
            { title: 'Video 1', url: 'http://example.com/video1.mp4' },
            { title: 'Video 2', url: 'http://example.com/video2.mp4' },
            { title: 'Video 3', url: 'http://example.com/video3.mp4' }
        ];
        let currentVideoIndex = 0;

        player.on('timeupdate', updateProgressBar);
        player.on('loadstart', function () {
            player.tech().setAttribute('crossorigin', 'anonymous');
        });

        function playVideo() {
            player.play();
        }

        function pauseVideo() {
            player.pause();
        }

        function seekForward() {
            player.currentTime(player.currentTime() + 10);
        }

        function seekBackward() {
            player.currentTime(Math.max(0, player.currentTime() - 10));
        }

        function toggleFullScreen() {
            if (player.isFullscreen()) {
                player.exitFullscreen();
            } else {
                player.requestFullscreen();
            }
        }

        function changeVolume(value) {
            player.volume(value);
        }

        function changePlaybackSpeed(speed) {
            player.playbackRate(speed);
        }

        function updateProgressBar() {
            const currentTime = player.currentTime();
            const duration = player.duration();
            if (duration > 0) {
                const percent = (currentTime / duration) * 100;
                progressBar.style.width = percent + '%';
            }
        }

        function seek(event) {
            const containerWidth = event.target.clientWidth;
            const clickX = event.offsetX;
            const newTime = (clickX / containerWidth) * player.duration();
            player.currentTime(newTime);
        }

        function toggleDarkMode() {
            document.body.classList.toggle('dark-mode');
        }

        function goToChapter(time) {
            if (time) {
                player.currentTime(time);
                player.play();
            }
        }

        function populateChapters() {
            const chapterSelect = document.getElementById('chapterSelect');
            chapters.forEach(chapter => {
                const option = document.createElement('option');
                option.value = chapter.time;
                option.textContent = chapter.title;
                chapterSelect.appendChild(option);
            });
        }

        function previousVideo() {
            if (currentVideoIndex > 0) {
                currentVideoIndex--;
                loadVideo(playlist[currentVideoIndex].url);
            }
        }

        function nextVideo() {
            if (currentVideoIndex < playlist.length - 1) {
                currentVideoIndex++;
                loadVideo(playlist[currentVideoIndex].url);
            }
        }

        function loadVideo(url) {
            player.src({ type: 'video/mp4', src: url });
            player.play();
        }

        // Load initial video and populate chapters
        loadVideo(playlist[currentVideoIndex].url);
        populateChapters();
    </script>
</body>
</html>
