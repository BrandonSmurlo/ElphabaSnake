---
layout: base
title: Snake
permalink: /snake/
---

<style>
    body {
        background-color: #0B0B0B; /* Dark Wicked background */
        color: #00B140; /* Elphaba green */
        font-family: "Verdana", sans-serif;
    }
    .wrap {
        margin-left: auto;
        margin-right: auto;
    }

    canvas {
        display: none;
        border-style: solid;
        border-width: 10px;
        border-color: #00B140; /* Elphaba green border */
    }
    canvas:focus {
        outline: none;
    }

    /* All screens style */
    #gameover p, #setting p, #menu p {
        font-size: 20px;
        color: #00B140; /* Text in Elphaba green */
    }

    #gameover a, #setting a, #menu a {
        font-size: 30px;
        display: block;
        color: #FFFFFF;
        text-decoration: none;
    }

    #gameover a:hover, #setting a:hover, #menu a:hover {
        cursor: pointer;
        color: #00FF00;
    }

    #gameover a:hover::before, #setting a:hover::before, #menu a:hover::before {
        content: "✦";
        margin-right: 10px;
    }

    #menu {
        display: block;
    }

    #gameover {
        display: none;
    }

    #setting {
        display: none;
    }

    #setting input {
        display: none;
    }

    #setting label {
        cursor: pointer;
    }

    #setting input:checked + label {
        background-color: #00B140;
        color: #000;
    }
</style>

<h2 style="text-align: center; color: #00B140;">Wicked Snake</h2>
<div class="container">
    <header class="pb-3 mb-4 border-bottom border-primary text-dark" style="text-align: center;">
        <p class="fs-4" style="color: #00B140;">Score: <span id="score_value">0</span></p>
    </header>
    <div class="container" style="text-align:center;">
        <!-- Main Menu -->
        <div id="menu" class="py-4 text-light">
            <p>Welcome to <strong>Wicked Snake</strong>, press <span style="background-color: #00B140; color: #000000;">space</span> to begin</p>
            <a id="new_game">new game</a>
            <a id="setting_menu">settings</a>
        </div>
        <!-- Game Over -->
        <div id="gameover" class="py-4 text-light">
            <p>Game Over, press <span style="background-color: #00B140; color: #000000;">space</span> to try again</p>
            <a id="new_game1">new game</a>
            <a id="setting_menu1">settings</a>
        </div>
        <!-- Play Screen -->
        <canvas id="snake" class="wrap" width="320" height="320" tabindex="1"></canvas>
        <!-- Settings Screen -->
        <div id="setting" class="py-4 text-light">
            <p>Settings Screen, press <span style="background-color: #00B140; color: #000000;">space</span> to go back to playing</p>
            <a id="new_game2">new game</a>
            <br>
            <p>Speed:
                <input id="speed1" type="radio" name="speed" value="100" />
                <label for="speed1">Normal</label>
                <input id="speed2" type="radio" name="speed" value="50" checked/>
                <label for="speed2">Fast</label>
                <input id="speed3" type="radio" name="speed" value="25" />
                <label for="speed3">Wicked Fast</label>
            </p>
            <p>Wall:
                <input id="wallon" type="radio" name="wall" value="1" checked/>
                <label for="wallon">On</label>
                <input id="walloff" type="radio" name="wall" value="0"/>
                <label for="walloff">Off</label>
            </p>
        </div>
    </div>
</div>

<script>
    (function(){
        const canvas = document.getElementById("snake");
        const ctx = canvas.getContext("2d");
        const ele_score = document.getElementById("score_value");
        const SCREEN_MENU = -1, SCREEN_SNAKE = 0, SCREEN_GAME_OVER = 1, SCREEN_SETTING = 2;
        const screen_menu = document.getElementById("menu");
        const screen_game_over = document.getElementById("gameover");
        const screen_setting = document.getElementById("setting");
        const button_new_game = document.getElementById("new_game");
        const BLOCK = 10;

        let snake, snake_dir, snake_next_dir, snake_speed, food, score, wall;

        const showScreen = (screen) => {
            screen_menu.style.display = screen === SCREEN_MENU ? "block" : "none";
            canvas.style.display = screen === SCREEN_SNAKE ? "block" : "none";
            screen_game_over.style.display = screen === SCREEN_GAME_OVER ? "block" : "none";
            screen_setting.style.display = screen === SCREEN_SETTING ? "block" : "none";
        };

        const mainLoop = () => {
            let _x = snake[0].x, _y = snake[0].y;
            snake_dir = snake_next_dir;
            switch(snake_dir) { case 0: _y--; break; case 1: _x++; break; case 2: _y++; break; case 3: _x--; break; }
            snake.pop(); snake.unshift({x: _x, y: _y});

            if (wall && (_x < 0 || _y < 0 || _x >= canvas.width / BLOCK || _y >= canvas.height / BLOCK)) return showScreen(SCREEN_GAME_OVER);
            snake.forEach((p, i) => i > 0 && snake[0].x === p.x && snake[0].y === p.y && showScreen(SCREEN_GAME_OVER));
            if (_x === food.x && _y === food.y) { score++; snake.push({...food}); ele_score.innerText = score; addFood(); }

            ctx.fillStyle = "#0B0B0B"; ctx.fillRect(0, 0, canvas.width, canvas.height);
            snake.forEach(p => drawBlock(p.x, p.y));
            drawWitchHat(food.x, food.y);
            setTimeout(mainLoop, snake_speed);
        };

        const newGame = () => {
            snake = [{x: 15, y: 15}]; snake_next_dir = 1; score = 0; ele_score.innerText = score; addFood(); mainLoop();
        };

        const addFood = () => {
            food = {x: Math.floor(Math.random() * (canvas.width / BLOCK)), y: Math.floor(Math.random() * (canvas.height / BLOCK))};
        };

        const drawBlock = (x, y) => { ctx.fillStyle = "#00B140"; ctx.fillRect(x * BLOCK, y * BLOCK, BLOCK, BLOCK); };
        const drawWitchHat = (x, y) => {
            ctx.fillStyle = "#FFFFFF";
            ctx.font = "14px Arial";
            ctx.fillText("▲", x * BLOCK, y * BLOCK + 8);
        };

        window.addEventListener("keydown", e => { if (e.code === "Space") newGame(); });
        button_new_game.onclick = newGame;

        showScreen(SCREEN_MENU);
    })();
</script>
