---
layout: page
title: Snake
permalink: /snake/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Witch Snake Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #3e1746; /* Witch-themed dark purple */
            color: #e8e4f1; /* Pale white text */
            font-family: Arial, sans-serif;
            cursor: url('wand-cursor.png'), auto; /* Custom cursor style */
        }
        h1 {
            font-size: 3rem;
            margin-bottom: 20px;
        }
        .button {
            padding: 15px 30px;
            font-size: 20px;
            cursor: pointer;
            background-color: #5fbd57; /* Green wicked witch theme */
            color: white;
            border: none;
            border-radius: 8px;
            transition: transform 0.3s, background-color 0.3s;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
            margin: 10px;
        }
        .button:hover {
            transform: scale(1.1);
            background-color: #4e9f48; /* Darker green */
        }
        .button:active {
            transform: scale(1);
        }
        .hidden {
            display: none;
        }
        canvas {
            margin-top: 20px;
            background: black;
            display: none;
        }
        #score {
            position: absolute;
            top: 20px;
            left: 20px;
            background-color: #6a5acd; /* Witchy purple */
            padding: 10px 20px;
            border-radius: 12px;
            font-size: 24px; /* Larger font */
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
        }
    </style>
</head>
<body>
    <!-- Score Display -->
    <div id="score">Score: 0</div>

    <!-- Main Page -->
    <div id="mainPage">
        <h1>BEST SNAKE GAME IN 4TH</h1>
        <button id="startGame" class="button">🧙‍♀️ Play Snake Game</button>
        <button id="openSettings" class="button">⚙️ Settings</button>
    </div>

    <!-- Settings Page -->
    <div id="settingsPage" class="hidden">
        <h1>Change Snake Speed:</h1>
        <button id="slow" class="button">🐢 Slow</button>
        <button id="medium" class="button">⚡ Medium</button>
        <button id="fast" class="button">🚀 Fast</button>
        <button id="backToMain" class="button">⬅️ Back</button>
    </div>

    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const startButton = document.getElementById("startGame");
        const openSettingsButton = document.getElementById("openSettings");
        const settingsPage = document.getElementById("settingsPage");
        const mainPage = document.getElementById("mainPage");
        const slowButton = document.getElementById("slow");
        const mediumButton = document.getElementById("medium");
        const fastButton = document.getElementById("fast");
        const backToMainButton = document.getElementById("backToMain");
        const scoreDisplay = document.getElementById("score");

        // Game variables
        let snake = [{ x: 200, y: 200 }];
        let food = { x: 100, y: 100 };
        let dx = 10;
        let dy = 0;
        let score = 0;
        let gameInterval;
        let gameSpeed = 100; // Default speed (medium)
        const hatHeight = 10; // Height of the witch hat
        const hatColor = 'purple'; // Color of the witch hat

        // Function to draw the snake
        function drawSnake() {
            ctx.fillStyle = "lime";
            snake.forEach((segment, index) => {
                ctx.fillRect(segment.x, segment.y, 10, 10);
                if (index === 0) { // Draw witch hat on the frontmost segment
                    ctx.fillStyle = hatColor;
                    ctx.beginPath();
                    ctx.moveTo(segment.x, segment.y - hatHeight);
                    ctx.lineTo(segment.x + 5, segment.y - hatHeight - 15);
                    ctx.lineTo(segment.x + 10, segment.y - hatHeight);
                    ctx.closePath();
                    ctx.fill();
                }
            });
        }

        // Function to move the snake
        function moveSnake() {
            const head = { x: snake[0].x + dx, y: snake[0].y + dy };
            snake.unshift(head);

            // Check if snake eats the food
            if (head.x === food.x && head.y === food.y) {
                score++;
                scoreDisplay.textContent = `Score: ${score}`;
                generateFood();
            } else {
                snake.pop();
            }
        }

        // Function to draw the food
        function drawFood() {
            ctx.fillStyle = "green"; // Witch-themed food
            ctx.fillRect(food.x, food.y, 10, 10);
        }

        // Function to generate food
        function generateFood() {
            food.x = Math.floor(Math.random() * 40) * 10;
            food.y = Math.floor(Math.random() * 40) * 10;

            // Ensure food does not spawn on the snake
            snake.forEach((segment) => {
                if (segment.x === food.x && segment.y === food.y) {
                    generateFood();
                }
            });
        }

        // Function to check for collisions
        function checkCollision() {
            const head = snake[0];

            // Check wall collisions
            if (head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height) {
                endGame();
            }

            // Check self collisions
            for (let i = 1; i < snake.length; i++) {
                if (head.x === snake[i].x && head.y === snake[i].y) {
                    endGame();
                }
            }
        }

        // Function to update the game
        function updateGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            drawFood();
            moveSnake();
            drawSnake();
            checkCollision();
        }

        // Function to end the game
        function endGame() {
            clearInterval(gameInterval);

            const playAgain = confirm(`Game Over! Your score was ${score}. \nWould you like to play again?`);
            if (playAgain) {
                resetGame();
                startGame();
            } else {
                resetGame();
                mainPage.style.display = "flex";
            }
        }

        // Function to reset the game
        function resetGame() {
            snake = [{ x: 200, y: 200 }];
            food = { x: 100, y: 100 };
            dx = 10;
            dy = 0;
            score = 0;
            scoreDisplay.textContent = `Score: ${score}`; // Reset the displayed score
            canvas.style.display = "none";
            mainPage.style.display = "flex";
        }

        // Function to start the game
        function startGame() {
            canvas.style.display = "block";
            mainPage.style.display = "none";
            generateFood();
            gameInterval = setInterval(updateGame, gameSpeed);
        }

        // Speed control event listeners
        slowButton.addEventListener("click", () => gameSpeed = 200); // Slow speed
        mediumButton.addEventListener("click", () => gameSpeed = 100); // Medium speed
        fastButton.addEventListener("click", () => gameSpeed = 50); // Fast speed

        // Navigation between pages
        openSettingsButton.addEventListener("click", () => {
            mainPage.style.display = "none";
            settingsPage.style.display = "flex";
        });
        backToMainButton.addEventListener("click", () => {
            settingsPage.style.display = "none";
            mainPage.style.display = "flex";
        });

        // Event listener for arrow key controls
        document.addEventListener("keydown", (event) => {
            if (event.key === "ArrowUp" && dy === 0) {
                dx = 0;
                dy = -10;
            } else if (event.key === "ArrowDown" && dy === 0) {
                dx = 0;
                dy = 10;
            } else if (event.key === "ArrowLeft" && dx === 0) {
                dx = -10;
                dy = 0;
            } else if (event.key === "ArrowRight" && dx === 0) {
                dx = 10;
                dy = 0;
            }
        });

        // Start game on button click
        startButton.addEventListener("click", startGame);
    </script>
</body>
</html>
