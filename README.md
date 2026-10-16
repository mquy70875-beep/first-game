# first-game<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Game Hứng Bóng Của Tôi</title>
    <style>
        body { margin: 0; background: #222; display: flex; justify-content: center; align-items: center; height: 100vh; color: white; font-family: Arial, sans-serif; overflow: hidden; }
        canvas { background: #111; border: 2px solid #fff; display: block; }
        #score { position: absolute; top: 20px; font-size: 24px; }
    </style>
</head>
<body>
    <div id="score">Điểm: 0</div>
    <canvas id="gameCanvas" width="400" height="500"></canvas>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        const scoreElement = document.getElementById("score");

        let score = 0;
        let gameOver = false;

        // Cấu hình thanh hứng và quả bóng ban đầu
        let paddle = { x: 150, y: 460, width: 100, height: 15, speed: 8 };
        let ball = { x: 200, y: 50, radius: 10, dx: 4, dy: 4 };

        let rightPressed = false;
        let leftPressed = false;

        // Hàm Reset lại toàn bộ trạng thái game để chơi lại
        function resetGame() {
            score = 0;
            gameOver = false;
            scoreElement.innerText = "Điểm: 0";
            paddle = { x: 150, y: 460, width: 100, height: 15, speed: 8 };
            ball = { x: 200, y: 50, radius: 10, dx: 4, dy: 4 };
            draw(); // Chạy lại vòng lặp game
        }

        // Lắng nghe sự kiện bàn phím
        document.addEventListener("keydown", (e) => {
            if(e.key === "Right" || e.key === "ArrowRight") rightPressed = true;
            if(e.key === "Left" || e.key === "ArrowLeft") leftPressed = true;
            
            // Nếu đang Game Over và người chơi bấm phím Space (Dấu cách)
            if(gameOver && e.key === " ") {
                resetGame();
            }
        });
        
        document.addEventListener("keyup", (e) => {
            if(e.key === "Right" || e.key === "ArrowRight") rightPressed = false;
            if(e.key === "Left" || e.key === "ArrowLeft") leftPressed = false;
        });

        document.addEventListener("mousemove", (e) => {
            const relativeX = e.clientX - canvas.offsetLeft;
            if(relativeX > 0 && relativeX < canvas.width) {
                paddle.x = relativeX - paddle.width / 2;
            }
        });

        // Thêm sự kiện click chuột/chạm màn hình để chơi lại cho tiện
        canvas.addEventListener("click", () => {
            if (gameOver) {
                resetGame();
            }
        });

        function draw() {
            if (gameOver) {
                ctx.fillStyle = "red";
                ctx.font = "30px Arial";
                ctx.fillText("GAME OVER", 110, 220);
                
                // Vẽ thêm dòng hướng dẫn chơi lại bên dưới chữ Game Over
                ctx.fillStyle = "white";
                ctx.font = "16px Arial";
                ctx.fillText("Bấm phím SPACE hoặc Click vào đây để CHƠI LẠI", 40, 280);
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Vẽ bóng
            ctx.beginPath();
            ctx.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();

            // Vẽ thanh hứng
            ctx.beginPath();
            ctx.rect(paddle.x, paddle.y, paddle.width, paddle.height);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();

            // Di chuyển bóng & Xử lý va chạm tường
            if(ball.x + ball.dx > canvas.width - ball.radius || ball.x + ball.dx < ball.radius) ball.dx = -ball.dx;
            if(ball.y + ball.dy < ball.radius) ball.dy = -ball.dy;
            
            // Xử lý khi bóng chạm đáy hoặc chạm thanh hứng
            if(ball.y + ball.dy > canvas.height - ball.radius) {
                if(ball.x > paddle.x && ball.x < paddle.x + paddle.width) {
                    ball.dy = -ball.dy;
                    score++;
                    scoreElement.innerText = "Điểm: " + score;
                    ball.dy *= 1.05; 
                    ball.dx *= 1.05;
                } else {
                    gameOver = true;
                }
            }

            // Di chuyển thanh hứng bằng phím
            if(rightPressed && paddle.x < canvas.width - paddle.width) paddle.x += paddle.speed;
            if(leftPressed && paddle.x > 0) paddle.x -= paddle.speed;

            ball.x += ball.dx;
            ball.y += ball.dy;

            requestAnimationFrame(draw);
        }

        draw();
    </script>
</body>
</html>
