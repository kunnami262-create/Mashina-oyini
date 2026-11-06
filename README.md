# Mashina-oyini
<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mashina O'yini</title>
    <style>
        body {
            margin: 0;
            background: #222;
            overflow: hidden;
            font-family: Arial, sans-serif;
            text-align: center;
            color: white;
        }
        canvas {
            background: #333;
            display: block;
            margin: 20px auto;
            border: 4px solid #fff;
            box-shadow: 0 0 20px rgba(0,0,0,0.8);
        }
        .controls {
            margin-top: 10px;
        }
        button {
            padding: 12px 24px;
            font-size: 18px;
            background: #ff4757;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
        }
        button:hover {
            background: #ff6b81;
        }
    </style>
</head>
<body>

    <h1>Kichik Mashina O'yini</h1>
    <p>Mashinani boshqarish uchun strelka (← →) tugmalaridan foydalaning!</p>
    
    <canvas id="gameCanvas" width="400" height="600"></canvas>
    
    <div class="controls">
        <button onclick="restartGame()">Qaytadan boshlash</button>
    </div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        let carX = 170;
        let carY = 480;
        let carWidth = 60;
        let carHeight = 100;
        let carSpeed = 6;

        let obstacleX = Math.random() * (canvas.width - 60);
        let obstacleY = -150;
        let obstacleWidth = 60;
        let obstacleHeight = 100;
        let obstacleSpeed = 5;

        let score = 0;
        let isGameOver = false;

        let rightPressed = false;
        let leftPressed = false;

        document.addEventListener("keydown", keyDownHandler);
        document.addEventListener("keyup", keyUpHandler);

        function keyDownHandler(e) {
            if (e.key === "Right" || e.key === "ArrowRight") {
                rightPressed = true;
            } else if (e.key === "Left" || e.key === "ArrowLeft") {
                leftPressed = true;
            }
        }

        function keyUpHandler(e) {
            if (e.key === "Right" || e.key === "ArrowRight") {
                rightPressed = false;
            } else if (e.key === "Left" || e.key === "ArrowLeft") {
                leftPressed = false;
            }
        }

        function drawCar(x, y, color) {
            ctx.fillStyle = color;
            ctx.fillRect(x, y, carWidth, carHeight);
            
            // Derazalar va g'ildiraklar
            ctx.fillStyle = "#111";
            ctx.fillRect(x + 10, y + 20, 40, 25); // Old oyna
            ctx.fillRect(x + 10, y + 65, 40, 20); // Orqa oyna
            
            ctx.fillRect(x - 6, y + 15, 6, 20); // Chap old g'ildirak
            ctx.fillRect(x + carWidth, y + 15, 6, 20); // O'ng old g'ildirak
            ctx.fillRect(x - 6, y + 70, 6, 20); // Chap orqa g'ildirak
            ctx.fillRect(x + carWidth, y + 70, 6, 20); // O'ng orqa g'ildirak
        }

        function update() {
            if (isGameOver) return;

            // Harakat
            if (rightPressed && carX < canvas.width - carWidth - 20) {
                carX += carSpeed;
            } else if (leftPressed && carX > 20) {
                carX -= carSpeed;
            }

            // Dushmanni harakatlantirish
            obstacleY += obstacleSpeed;
            if (obstacleY > canvas.height) {
                obstacleY = -120;
                obstacleX = Math.random() * (canvas.width - 100 - 40) + 20;
                score++;
                obstacleSpeed += 0.2; // Tezlikni oshirish
            }

            // To'qnashuvni tekshirish (Collision detection)
            if (
                carX < obstacleX + obstacleWidth &&
                carX + carWidth > obstacleX &&
                carY < obstacleY + obstacleHeight &&
                carY + carHeight > obstacleY
            ) {
                isGameOver = true;
            }
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Yo'l chiziqlari (fon)
            ctx.strokeStyle = "#fff";
            ctx.lineWidth = 4;
            ctx.setLineDash([20, 20]);
            ctx.beginPath();
            ctx.moveTo(canvas.width / 2, 0);
            ctx.lineTo(canvas.width / 2, canvas.height);
            ctx.stroke();
            ctx.setLineDash([]);

            // O'yinchi mashinasi (Ko'k)
            drawCar(carX, carY, "#00cec9");

            // Dushman mashinasi (Qizil)
            drawCar(obstacleX, obstacleY, "#ff7675");

            // Ballni chiqarish
            ctx.fillStyle = "white";
            ctx.font = "20px Arial";
            ctx.fillText("Ball: " + score, 50, 30);

            if (isGameOver) {
                ctx.fillStyle = "rgba(0, 0, 0, 0.75)";
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                ctx.fillStyle = "#ff4757";
                ctx.font = "bold 35px Arial";
                ctx.fillText("O'YIN TUGADI", canvas.width / 2, canvas.height / 2 - 20);

                ctx.fillStyle = "white";
                ctx.font = "18px Arial";
                ctx.fillText("Siz to'plagan ball: " + score, canvas.width / 2, canvas.height / 2 + 25);
            }
        }

        function loop() {
            update();
            draw();
            requestAnimationFrame(loop);
        }

        function restartGame() {
            carX = 170;
            obstacleY = -150;
            obstacleX = Math.random() * (canvas.width - 80);
            obstacleSpeed = 5;
            score = 0;
            isGameOver = false;
        }

        loop();
    </script>
</body>
</html>
