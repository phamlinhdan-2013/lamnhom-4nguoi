pip install streamlit
import streamlit as st

st.set_page_config(page_title="Snake Game on Streamlit", page_icon="🐍", layout="centered")

st.title("🐍 Trò Chơi Rắn Săn Mồi (Snake)")
st.write("Sử dụng các phím mũi tên (`⬆️ ⬇️ ⬅️ ➡️`) trên bàn phím để điều khiển rắn ăn mồi!")

# Mã HTML + JavaScript nhúng trực tiếp để xử lý game mượt mà theo thời gian thực
snake_game_html = """
<!DOCTYPE html>
<html>
<head>
    <style>
        body {
            background-color: #0e1117;
            color: white;
            text-align: center;
            font-family: sans-serif;
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        canvas {
            border: 4px solid #ffffff;
            background-color: #1a1c23;
            box-shadow: 0px 0px 20px rgba(255, 255, 255, 0.2);
        }
        #score {
            font-size: 24px;
            margin-bottom: 10px;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div id="score">Điểm số: 0</div>
    <canvas id="snakeGame" width="400" height="400"></canvas>

    <script>
        const canvas = document.getElementById("snakeGame");
        const ctx = canvas.getContext("2d");

        const box = 20;
        let score = 0;

        // Khởi tạo vị trí con rắn
        let snake = [];
        snake[0] = { x: 9 * box, y: 10 * box };

        // Khởi tạo vị trí thức ăn ngẫu nhiên
        let food = {
            x: Math.floor(Math.random() * 19) * box,
            y: Math.floor(Math.random() * 19) * box
        };

        let d = "";

        // Bắt sự kiện bàn phím
        document.addEventListener("keydown", direction);

        function direction(event) {
            if (event.keyCode == 37 && d != "RIGHT") {
                d = "LEFT";
            } else if (event.keyCode == 38 && d != "DOWN") {
                d = "UP";
            } else if (event.keyCode == 39 && d != "LEFT") {
                d = "RIGHT";
            } else if (event.keyCode == 40 && d != "UP") {
                d = "DOWN";
            }
        }

        // Kiểm tra va chạm với tường hoặc thân
        function collision(head, array) {
            for (let i = 0; i < array.length; i++) {
                if (head.x == array[i].x && head.y == array[i].y) {
                    return true;
                }
            }
            return false;
        }

        // Vẽ mọi thứ lên canvas
        function draw() {
            ctx.clearRect(0, 0, 400, 400);

            // Vẽ rắn
            for (let i = 0; i < snake.length; i++) {
                ctx.fillStyle = (i == 0) ? "#00FF7F" : "#32CD32";
                ctx.fillRect(snake[i].x, snake[i].y, box, box);
                
                ctx.strokeStyle = "#1a1c23";
                ctx.strokeRect(snake[i].x, snake[i].y, box, box);
            }

            // Vẽ mồi
            ctx.fillStyle = "#FF4500";
            ctx.fillRect(food.x, food.y, box, box);

            // Tọa độ đầu cũ
            let snakeX = snake[0].x;
            let snakeY = snake[0].y;

            // Hướng di chuyển
            if (d == "LEFT") snakeX -= box;
            if (d == "UP") snakeY -= box;
            if (d == "RIGHT") snakeX += box;
            if (d == "DOWN") snakeY += box;

            // Nếu rắn ăn mồi
            if (snakeX == food.x && snakeY == food.y) {
                score++;
                document.getElementById("score").innerText = "Điểm số: " + score;
                food = {
                    x: Math.floor(Math.random() * 19) * box,
                    y: Math.floor(Math.random() * 19) * box
                };
            } else {
                // Xóa đuôi rắn nếu không ăn mồi
                snake.pop();
            }

            // Tạo đầu mới cho rắn
            let newHead = { x: snakeX, y: snakeY };

            // Điều kiện kết thúc game (Game Over)
            if (snakeX < 0 || snakeX >= canvas.width || snakeY < 0 || snakeY >= canvas.height || collision(newHead, snake)) {
                clearInterval(game);
                ctx.fillStyle = "rgba(0, 0, 0, 0.75)";
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                ctx.fillStyle = "white";
                ctx.font = "30px Arial";
                ctx.fillText("GAME OVER", 100, 200);
                
                ctx.font = "15px Arial";
                ctx.fillText("Tải lại trang để chơi lại", 125, 240);
                return;
            }

            snake.unshift(newHead);
        }

        // Tốc độ khung hình game (100ms tương đương 10 khung hình/giây)
        let game = setInterval(draw, 100);
    </script>
</body>
</html>
"""

# Hiển thị giao diện game HTML vào bên trong ứng dụng Streamlit
st.components.v1.html(snake_game_html, height=480)
