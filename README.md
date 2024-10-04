<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Космический путешественник</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #000;
        }
        #gameCanvas {
            display: block;
        }
        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            font-family: Arial, sans-serif;
            font-size: 20px;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <div id="score">Счет: 0</div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        let ship = {
            x: canvas.width / 2,
            y: canvas.height - 30,
            width: 30,
            height: 30,
            speed: 5
        };

        let asteroids = [];
        let stars = [];
        let score = 0;

        function drawShip() {
            ctx.fillStyle = 'lime';
            ctx.beginPath();
            ctx.moveTo(ship.x, ship.y);
            ctx.lineTo(ship.x - 15, ship.y + 30);
            ctx.lineTo(ship.x + 15, ship.y + 30);
            ctx.closePath();
            ctx.fill();
        }

        function drawAsteroid(asteroid) {
            ctx.fillStyle = 'gray';
            ctx.beginPath();
            ctx.arc(asteroid.x, asteroid.y, asteroid.size, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawStar(star) {
            ctx.fillStyle = 'yellow';
            ctx.beginPath();
            ctx.arc(star.x, star.y, star.size, 0, Math.PI * 2);
            ctx.fill();
        }

        function createAsteroid() {
            return {
                x: Math.random() * canvas.width,
                y: -20,
                size: Math.random() * 20 + 10,
                speed: Math.random() * 3 + 1
            };
        }

        function createStar() {
            return {
                x: Math.random() * canvas.width,
                y: -10,
                size: Math.random() * 3 + 2,
                speed: Math.random() * 2 + 1
            };
        }

        function updateGame() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            drawShip();

            asteroids.forEach((asteroid, index) => {
                asteroid.y += asteroid.speed;
                drawAsteroid(asteroid);

                if (asteroid.y > canvas.height) {
                    asteroids.splice(index, 1);
                }

                if (collision(ship, asteroid)) {
                    alert('Игра окончена! Ваш счет: ' + score);
                    resetGame();
                }
            });

            stars.forEach((star, index) => {
                star.y += star.speed;
                drawStar(star);

                if (star.y > canvas.height) {
                    stars.splice(index, 1);
                }

                if (collision(ship, star)) {
                    score += 10;
                    scoreElement.textContent = 'Счет: ' + score;
                    stars.splice(index, 1);
                }
            });

            if (Math.random() < 0.02) asteroids.push(createAsteroid());
            if (Math.random() < 0.05) stars.push(createStar());

            requestAnimationFrame(updateGame);
        }

        function collision(a, b) {
            return !(
                ((a.y + a.height) < (b.y)) ||
                (a.y > (b.y + b.size)) ||
                ((a.x + a.width) < b.x) ||
                (a.x > (b.x + b.size))
            );
        }

        function resetGame() {
            ship.x = canvas.width / 2;
            ship.y = canvas.height - 30;
            asteroids = [];
            stars = [];
            score = 0;
            scoreElement.textContent = 'Счет: 0';
        }

        document.addEventListener('mousemove', (e) => {
            ship.x = e.clientX - ship.width / 2;
        });

        updateGame();
    </script>
</body>
</html>
