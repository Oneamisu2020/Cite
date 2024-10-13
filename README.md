# Cite
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カードドロップゲーム</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
        }
        #game {
            width: 320px;
            height: 480px;
            border: 2px solid #333;
            background-color: #fff;
            position: relative;
            overflow: hidden;
        }
        .card {
            width: 30px;
            height: 30px;
            position: absolute;
            text-align: center;
            line-height: 30px;
            font-size: 20px;
            border: 1px solid #000;
            box-sizing: border-box;
        }
        .score {
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <h1>カードドロップゲーム</h1>
    <div class="score">スコア: <span id="score">0</span></div>
    <div id="game"></div>
    <button id="startButton">ゲームスタート</button>
    
    <script>
        const gameArea = document.getElementById('game');
        const scoreDisplay = document.getElementById('score');
        const startButton = document.getElementById('startButton');
        let score = 0;
        let gameInterval;
        
        function getRandomCard() {
            const suits = ['♠', '♥', '♦', '♣'];
            const value = Math.floor(Math.random() * 9) + 1; // 1-9の値
            const suit = suits[Math.floor(Math.random() * suits.length)];
            return { value, suit };
        }
        
        function createCard() {
            const card = getRandomCard();
            const cardElement = document.createElement('div');
            cardElement.className = 'card';
            cardElement.textContent = card.value + card.suit;
            cardElement.style.left = Math.random() * (gameArea.clientWidth - 30) + 'px';
            cardElement.dataset.value = card.value;
            cardElement.dataset.suit = card.suit;
            gameArea.appendChild(cardElement);
            
            moveCard(cardElement);
        }
        
        function moveCard(cardElement) {
            let position = 0;
            const dropInterval = setInterval(() => {
                if (position >= gameArea.clientHeight - 30) {
                    clearInterval(dropInterval);
                    gameArea.removeChild(cardElement);
                } else {
                    position += 5;
                    cardElement.style.top = position + 'px';
                }
            }, 100);
        }

        function checkForMatches() {
            const cards = document.querySelectorAll('.card');
            const cardValues = {};
            cards.forEach(card => {
                const value = parseInt(card.dataset.value);
                const suit = card.dataset.suit;
                const key = suit + value;
                cardValues[key] = (cardValues[key] || 0) + 1;
            });

            for (const key in cardValues) {
                if (cardValues[key] > 1) {
                    score += 10; // スコア加算
                    scoreDisplay.textContent = score;
                    const cardsToRemove = document.querySelectorAll(`.card[data-value="${key.slice(-1)}"][data-suit="${key.slice(0, -1)}"]`);
                    cardsToRemove.forEach(card => {
                        gameArea.removeChild(card);
                    });
                }
            }
        }

        function startGame() {
            score = 0;
            scoreDisplay.textContent = score;
            gameArea.innerHTML = ''; // 既存のカードをクリア
            gameInterval = setInterval(() => {
                createCard();
                checkForMatches();
            }, 1000);
        }

        startButton.addEventListener('click', () => {
            clearInterval(gameInterval); // 既存のゲームを停止
            startGame();
        });
    </script>
</body>
</html>
