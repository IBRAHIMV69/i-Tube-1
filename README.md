<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>YouTube Video Search & URL Buttons</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(to right, #000, #b21f1f, #000);
            color: #fff;
            margin: 0;
            padding: 0;
        }
        .balance, .search-bar, .url-buttons {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 16px;
            background: rgba(0, 0, 0, 0.6);
            border-radius: 8px;
            margin: 16px;
        }
        .video-task {
            border-radius: 20px;
            overflow: hidden;
            background: #fff;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        iframe {
            border: none;
            width: 100%;
            height: 180px;
        }
        .search-bar input {
            width: 70%;
            padding: 8px;
            border-radius: 4px;
            border: none;
        }
        .search-bar button, .url-buttons a {
            padding: 8px 16px;
            border-radius: 4px;
            border: none;
            background: linear-gradient(to right, #000, #b21f1f, #000);
            color: #fff;
            cursor: pointer;
            text-decoration: none;
            text-align: center;
            margin-left: 8px;
        }
        .search-bar button:hover, .url-buttons a:hover {
            background: linear-gradient(to right, #000, #d32f2f, #000);
        }
        .description {
            margin: 16px;
            font-size: 14px;
            text-align: center;
            color: #ddd;
        }
    </style>
</head>
<body>
    <div class="balance">
        <p>Balance: $<span id="balance">1.00</span></p>
    </div>

    <div class="search-bar">
        <input type="text" id="searchInput" placeholder="Enter video title...">
        <button onclick="searchVideo()">Search</button>
    </div>
    
    <div class="video-container">
        <iframe id="videoIframe" src="" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>
    
    <!-- Short Description -->
    <p class="description">Get $2 for every visiting sites of advertisers.</p>

    <!-- URL Buttons Section -->
    <div class="url-buttons">
        <a href="https://example.com/page1" target="_blank" id="url1" onclick="markUrlVisited(0)">Earn $2</a>
        <a href="https://example.com/page2" target="_blank" id="url2" onclick="markUrlVisited(1)">Earn $2</a>
        <a href="https://example.com/page3" target="_blank" id="url3" onclick="markUrlVisited(2)">Earn $2</a>
        <a href="https://example.com/page4" target="_blank" id="url4" onclick="markUrlVisited(3)">Earn $2</a>
    </div>

    <script>
        // List of videos with titles and corresponding YouTube IDs
        const videoList = [
            { title: "112233", id: "qzQvUKavtL0" },
            { title: "Incredible Science", id: "AQO98HXS9o0" },
            { title: "Gurnani", id: "i9ddLGUqrlU" }
        ];

        let balance = parseFloat(localStorage.getItem('balance')) || 1.00;
        let urlsVisited = JSON.parse(localStorage.getItem('urlsVisited')) || [null, null, null, null]; // Track URL visits and their timestamps

        // Update the balance display
        document.getElementById('balance').textContent = balance.toFixed(2);

        // Function to unlock the next button after a visit
        function unlockNextButton(index) {
            if (index < 3 && !urlsVisited[index + 1]) {
                document.getElementById(`url${index + 2}`).style.pointerEvents = 'auto'; // Unlock the next button
                document.getElementById(`url${index + 2}`).style.opacity = '1'; // Make the next button visible
            }
        }

        // Function to lock the buttons and mark visits
        function lockButtons() {
            for (let i = 0; i < urlsVisited.length; i++) {
                const timestamp = urlsVisited[i];
                const currentTime = new Date().getTime();
                
                if (timestamp) {
                    const timeDifference = currentTime - timestamp;
                    const oneHourInMillis = 60 * 60 * 1000;

                    // Check if 1 hour has passed since the URL was visited
                    if (timeDifference < oneHourInMillis) {
                        document.getElementById(`url${i + 1}`).style.pointerEvents = 'none'; // Lock the button
                        document.getElementById(`url${i + 1}`).style.opacity = '0.5'; // Make the button look locked
                    } else {
                        document.getElementById(`url${i + 1}`).style.pointerEvents = 'auto'; // Unlock the button
                        document.getElementById(`url${i + 1}`).style.opacity = '1'; // Make the button visible
                    }
                } else {
                    document.getElementById(`url${i + 1}`).style.pointerEvents = 'auto'; // Unlock the button if not visited
                    document.getElementById(`url${i + 1}`).style.opacity = '1'; // Make the button visible
                }
            }
        }

        function loadVideo(videoId) {
            if (balance >= 1) {
                balance -= 1;
                localStorage.setItem('balance', balance.toFixed(2));
                document.getElementById('balance').textContent = balance.toFixed(2);
                const iframe = document.getElementById('videoIframe');
                iframe.src = `https://www.youtube.com/embed/${videoId}?enablejsapi=1&autoplay=1`;
            } else {
                alert("Insufficient balance. Visit the URL buttons to earn more balance.");
            }
        }

        function searchVideo() {
            const searchQuery = document.getElementById('searchInput').value.trim().toLowerCase();
            const video = videoList.find(v => v.title.toLowerCase() === searchQuery);

            if (video) {
                loadVideo(video.id);
            } else {
                alert("Video not found. Please try another title.");
            }
        }

        function markUrlVisited(index) {
            if (!urlsVisited[index]) {
                urlsVisited[index] = new Date().getTime(); // Store the timestamp of the visit
                localStorage.setItem('urlsVisited', JSON.stringify(urlsVisited)); // Save to localStorage
                addBalance(2.00); // Add $2 to the balance for visiting a URL
                unlockNextButton(index); // Unlock the next button after the current one is visited
                lockButtons(); // Lock visited buttons
            } else {
                alert("You have already visited this URL.");
            }
        }

        function addBalance(amount) {
            balance += amount;
            localStorage.setItem('balance', balance.toFixed(2));
            document.getElementById('balance').textContent = balance.toFixed(2);
        }

        // Initialize the buttons on page load
        lockButtons();
    </script>
</body>
</html>
