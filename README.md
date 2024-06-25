<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Valentina na Floresta</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #87CEEB;
            margin: 0;
        }
        canvas {
            border: 2px solid #000;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const characterSize = 50;
        const treeWidth = 30, treeHeight = 60;
        const houseWidth = 80, houseHeight = 80;
        const bugWidth = 20, bugHeight = 20;

        let valentinaX = canvas.width / 2 - characterSize / 2;
        let valentinaY = canvas.height / 2 - characterSize / 2;
        let trees = [];
        let houses = [];
        let bugs = [];
        let foundSerena = false;
        const valentinaSpeed = 5;

        function createTree() {
            return {
                x: Math.random() * canvas.width,
                y: -treeHeight,
                speed: 3 + Math.random() * 2
            };
        }

        function createHouse() {
            return {
                x: Math.random() * canvas.width,
                y: -houseHeight,
                speed: 2 + Math.random() * 1
            };
        }

        function createBug() {
            return {
                x: Math.random() * canvas.width,
                y: -bugHeight,
                speed: 4 + Math.random() * 2
            };
        }

        function drawCharacter() {
            ctx.fillStyle = '#FF69B4'; // Rosa para a roupa de coelho
            ctx.fillRect(valentinaX, valentinaY, characterSize, characterSize);

            // Desenhar franja
            ctx.fillStyle = '#000'; // Cor da franja
            ctx.fillRect(valentinaX, valentinaY, characterSize, characterSize / 3);
        }

        function drawTrees() {
            ctx.fillStyle = '#228B22';
            for (let tree of trees) {
                ctx.fillRect(tree.x, tree.y, treeWidth, treeHeight);
            }
        }

        function drawHouses() {
            ctx.fillStyle = '#8B4513';
            for (let house of houses) {
                ctx.fillRect(house.x, house.y, houseWidth, houseHeight);
            }
        }

        function drawBugs() {
            ctx.fillStyle = '#FFD700';
            for (let bug of bugs) {
                ctx.fillRect(bug.x, bug.y, bugWidth, bugHeight);
            }
        }

        function drawSerena() {
            ctx.fillStyle = '#A0522D'; // Parda
            ctx.fillRect(canvas.width / 2 - houseWidth / 2, canvas.height / 2 - houseHeight / 2, houseWidth, houseHeight);

            // Desenhar cabelo de Serena
            ctx.fillStyle = '#000';
            ctx.fillRect(canvas.width / 2 - houseWidth / 2, canvas.height / 2 - houseHeight / 2, houseWidth, houseHeight / 3);
        }

        function moveObjects(objects, objectHeight) {
            for (let obj of objects) {
                obj.y += obj.speed;
            }
            return objects.filter(obj => obj.y < canvas.height);
        }

        function drawEverything() {
            // Clear the canvas
            ctx.fillStyle = '#87CEEB';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw trees, houses, and bugs
            drawTrees();
            drawHouses();
            drawBugs();

            // Draw Valentina
            drawCharacter();

            // Draw Serena if found
            if (foundSerena) {
                drawSerena();
            }
        }

        function moveCharacter() {
            if (keys['ArrowUp']) valentinaY -= valentinaSpeed;
            if (keys['ArrowDown']) valentinaY += valentinaSpeed;
            if (keys['ArrowLeft']) valentinaX -= valentinaSpeed;
            if (keys['ArrowRight']) valentinaX += valentinaSpeed;

            valentinaX = Math.max(0, Math.min(canvas.width - characterSize, valentinaX));
            valentinaY = Math.max(0, Math.min(canvas.height - characterSize, valentinaY));
        }

        function gameLoop() {
            if (Math.random() < 0.05) trees.push(createTree());
            if (Math.random() < 0.01) houses.push(createHouse());
            if (Math.random() < 0.05) bugs.push(createBug());

            trees = moveObjects(trees, treeHeight);
            houses = moveObjects(houses, houseHeight);
            bugs = moveObjects(bugs, bugHeight);

            moveCharacter();

            // Check collision with houses
            for (let house of houses) {
                if (valentinaX < house.x + houseWidth &&
                    valentinaX + characterSize > house.x &&
                    valentinaY < house.y + houseHeight &&
                    valentinaY + characterSize > house.y) {
                    foundSerena = true;
                }
            }

            drawEverything();
            if (!foundSerena) {
                requestAnimationFrame(gameLoop);
            }
        }

        const keys = {};
        window.addEventListener('keydown', (e) => {
            keys[e.key] = true;
        });
        window.addEventListener('keyup', (e) => {
            keys[e.key] = false;
        });

        gameLoop();
    </script>
</body>
</html>
