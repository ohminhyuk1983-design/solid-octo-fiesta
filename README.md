<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>연습용 가짜 돈 바카라</title>
    <style>
        body { font-family: Arial, sans-serif; background-color: #0c5a30; color: white; text-align: center; margin: 0; padding: 20px; }
        .table { border: 2px solid #fff; border-radius: 50px; max-width: 600px; margin: 20px auto; padding: 20px; background-color: #064422; }
        .zone { display: inline-block; width: 25%; margin: 10px; padding: 15px; border: 1px dashed #fff; cursor: pointer; border-radius: 10px; }
        .zone:hover { background-color: #086633; }
        .selected { background-color: #d4af37 !important; color: black; font-weight: bold; }
        .cards { font-size: 24px; margin: 15px 0; min-height: 35px; }
        button { padding: 10px 20px; font-size: 16px; font-weight: bold; background-color: #fff; border: none; border-radius: 5px; cursor: pointer; }
        button:hover { background-color: #ddd; }
        #result { font-size: 20px; font-weight: bold; margin: 20px 0; color: #ffeb3b; }
    </style>
</head>
<body>

    <h1>🃏 연습용 바카라 (가짜 돈)</h1>
    <h2>보유 자산: $<span id="balance">10000</span></h2>

    <div class="table">
        <div id="zone-player" class="zone" onclick="selectZone('Player')">플레이어<br>(1:1)</div>
        <div id="zone-tie" class="zone" onclick="selectZone('Tie')">타이<br>(1:8)</div>
        <div id="zone-banker" class="zone" onclick="selectZone('Banker')">뱅커<br>(1:0.95)</div>
        
        <p>베팅 금액: <input type="number" id="bet-amount" value="1000" min="100" step="100" style="width: 80px; padding: 5px; text-align: center;"></p>
        <button onclick="playRound()">게임 시작 (딜)</button>
    </div>

    <div class="table">
        <h3>플레이어 카드: <span id="p-cards" class="cards">-</span> (점수: <span id="p-score">0</span>)</h3>
        <h3>뱅커 카드: <span id="b-cards" class="cards">-</span> (점수: <span id="b-score">0</span>)</h3>
        <div id="result">베팅할 구역과 금액을 선택하세요!</div>
    </div>

    <script>
        let balance = 10000;
        let selectedZone = '';
        const shapes = ['♠', '♥', '♦', '♣'];
        const values = ['A', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K'];

        function selectZone(zone) {
            ['Player', 'Tie', 'Banker'].forEach(z => document.getElementById(`zone-${z.toLowerCase()}`).classList.remove('selected'));
            selectedZone = zone;
            document.getElementById(`zone-${zone.toLowerCase()}`).classList.add('selected');
        }

        function getCard() {
            const shape = shapes[Math.floor(Math.random() * shapes.length)];
            const value = values[Math.floor(Math.random() * values.length)];
            let score = values.indexOf(value) + 1;
            if (score >= 10) score = 0; 
            return { display: shape + value, score: score };
        }

        function calculateScore(cards) {
            return cards.reduce((sum, card) => sum + card.score, 0) % 10;
        }

        function playRound() {
            const betInput = document.getElementById('bet-amount');
            const bet = parseInt(betInput.value);

            if (!selectedZone) { alert('베팅 구역을 선택해주세요!'); return; }
            if (isNaN(bet) || bet <= 0 || bet > balance) { alert('올바른 베팅 금액을 입력해주세요.'); return; }

            balance -= bet;
            document.getElementById('balance').innerText = balance;

            // 기본 2장씩 배분
            let pCards = [getCard(), getCard()];
            let bCards = [getCard(), getCard()];

            let pScore = calculateScore(pCards);
            let bScore = calculateScore(bCards);

            // 내추럴(8, 9점)이 아닌 경우 추가 카드 룰 적용 (간단화 버전)
            if (pScore < 8 && bScore < 8) {
                if (pScore <= 5) {
                    pCards.push(getCard());
                    pScore = calculateScore(pCards);
                }
                if (bScore <= 5) {
                    bCards.push(getCard());
                    bScore = calculateScore(bCards);
                }
            }

            // 화면 업데이트
            document.getElementById('p-cards').innerText = pCards.map(c => c.display).join(' ');
            document.getElementById('b-cards').innerText = bCards.map(c => c.display).join(' ');
            document.getElementById('p-score').innerText = pScore;
            document.getElementById('b-score').innerText = bScore;

            // 결과 판정
            let winner = '';
            if (pScore > bScore) winner = 'Player';
            else if (bScore > pScore) winner = 'Banker';
            else winner = 'Tie';

            let msg = `결과: ${winner} 승리! `;
            if (selectedZone === winner) {
                let payout = 0;
                if (winner === 'Player') payout = bet * 2;
                else if (winner === 'Banker') payout = bet * 1.95;
                else if (winner === 'Tie') payout = bet * 9;
                
                balance += Math.floor(payout);
                msg += `🎉 축하합니다! $${Math.floor(payout - bet)}을 획득했습니다.`;
            } else {
                msg += `💸 아쉽습니다! 베팅금을 잃었습니다.`;
            }

            document.getElementById('balance').innerText = balance;
            document.getElementById('result').innerText = msg;
            
            if (balance <= 0) {
                alert('자산이 모두 소진되어 $10,000로 무료 충전됩니다!');
                balance = 10000;
                document.getElementById('balance').innerText = balance;
            }
        }
    </script>
</body>
</html>