<!DOCTYPE html>
<html lang="ro">
<head>
    <meta charset="UTF-8">
    <title>Joc Divizibilitate</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; text-align: center; background: #e8eff1; padding: 20px; }
        #game-container { background: white; max-width: 600px; margin: auto; padding: 30px; border-radius: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.1); }
        .option { display: block; width: 100%; padding: 12px; margin: 10px 0; border: 2px solid #3498db; border-radius: 8px; cursor: pointer; background: white; font-size: 18px; transition: 0.3s; }
        .option:hover { background: #ebf5fb; }
        #score { font-size: 22px; color: #2c3e50; font-weight: bold; margin-bottom: 10px; }
        #feedback { margin-top: 15px; padding: 15px; border-radius: 8px; display: none; font-weight: bold; }
        .correct { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
        .wrong { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
        #next-btn { margin-top: 15px; padding: 10px 25px; background: #2c3e50; color: white; border: none; border-radius: 5px; cursor: pointer; display: none; }
    </style>
</head>
<body>
    <div id="game-container">
        <h1>🔢 Provocarea Divizibilității</h1>
        <div id="score">Scor: 0 / 20</div>
        <div id="question-area">
            <p id="question-text" style="font-size: 24px; font-weight: bold;"></p>
            <div id="options"></div>
            <div id="feedback"></div>
            <button id="next-btn">Următoarea Întrebare➡️</button>
        </div>
    </div>

    <script>
        const questions = [
            { q: "Este adevărat că 7 | 147?", a: ["Adevărat", "Fals"], c: 0, r: "Da, 147 : 7 = 21" },
            { q: "Care număr este divizibil cu 9 (n ⋮ 9)?", a: ["123", "171", "199"], c: 1, r: "171 (1+7+1=9)" },
            { q: "Este adevărat că 13 | 169?", a: ["Adevărat", "Fals"], c: 0, r: "Da, 13 x 13 = 169" },
            { q: "10³ este divizibil cu 10?", a: ["Da (1000 ⋮ 10)", "Nu"], c: 0, r: "Orice putere a lui 10 se divide cu 10" },
            { q: "Care număr verifică 11 | n?", a: ["121", "131", "151"], c: 0, r: "121 (11 x 11 = 121)" },
            { q: "3 | 102?", a: ["Da", "Nu"], c: 0, r: "Da (1+0+2=3)" },
            { q: "Alege numărul care verifică n ⋮ 5:", a: ["192", "195", "199"], c: 1, r: "195 se termină în 5" },
            { q: "Este adevărat că 10^7 ⋮ 10?", a: ["Da", "Nu"], c: 0, r: "Da, baza este 10" },
            { q: "17 | 170?", a: ["Adevărat", "Fals"], c: 0, r: "Da, 17 x 10 = 170" },
            { q: "Care număr este par (n ⋮ 2)?", a: ["147", "188", "191"], c: 1, r: "188 se termină în cifră pară" },
            { q: "9 | 108?", a: ["Adevărat", "Fals"], c: 0, r: "Da (1+0+8=9)" },
            { q: "Este adevărat că 11 | 143?", a: ["Da", "Nu"], c: 0, r: "Da, 11 x 13 = 143" },
            { q: "Care număr este divizibil și cu 2 și cu 3?", a: ["12", "15", "20"], c: 0, r: "12 este par și 1+2=3" },
            { q: "7 | 112?", a: ["Da", "Nu"], c: 0, r: "Da, 112 : 7 = 16" },
            { q: "10² ⋮ 10?", a: ["Da", "Nu"], c: 0, r: "Da, 100 : 10 = 10" },
            { q: "Care număr verifică n ⋮ 5 și n ⋮ 2?", a: ["15", "25", "150"], c: 2, r: "150 se termină în 0" },
            { q: "19 | 190?", a: ["Da", "Nu"], c: 0, r: "Da, 19 x 10 = 190" },
            { q: "3 | 111?", a: ["Da", "Nu"], c: 0, r: "Da (1+1+1=3)" },
            { q: "10 | 10^1?", a: ["Adevărat", "Fals"], c: 0, r: "Da, orice număr se divide cu el însuși" },
            { q: "9 | 198?", a: ["Da", "Nu"], c: 0, r: "Da (1+9+8=18, iar 18 ⋮ 9)" }
        ];

        let currentQ = 0;
        let score = 0;

        const qText = document.getElementById('question-text');
        const optionsDiv = document.getElementById('options');
        const feedbackDiv = document.getElementById('feedback');
        const nextBtn = document.getElementById('next-btn');
        const scoreDiv = document.getElementById('score');

        function showQuestion() {
            feedbackDiv.style.display = "none";
            nextBtn.style.display = "none";
            
            if (currentQ >= questions.length) {
                document.getElementById('question-area').innerHTML = "<h2>Joc Terminat! 🎉</h2><p style='font-size:20px'>Ai obținut " + score + " puncte din 20.</p>";
                return;
            }

            const q = questions[currentQ];
            // AICI AM SCHIMBAT PUNCTUL CU PARANTEZA )
            qText.innerText = (currentQ + 1) + ") " + q.q;
            optionsDiv.innerHTML = "";
            
            q.a.forEach((opt, i) => {
                const btn = document.createElement('button');
                btn.className = "option";
                btn.innerText = opt;
                btn.onclick = () => check(i);
                optionsDiv.appendChild(btn);
            });
        }

        function check(idx) {
            const q = questions[currentQ];
            const allBtns = document.querySelectorAll('.option');
            allBtns.forEach(b => b.disabled = true);

            feedbackDiv.style.display = "block";
            if (idx === q.c) {
                score++;
                feedbackDiv.className = "correct";
                feedbackDiv.innerText = "CORECT! " + q.r;
            } else {
                feedbackDiv.className = "wrong";
                feedbackDiv.innerText = "GREȘIT! Răspunsul corect era: " + q.a[q.c] + ". (" + q.r + ")";
            }

            scoreDiv.innerText = "Scor: " + score + " / 20";
            nextBtn.style.display = "inline-block";
        }

        nextBtn.onclick = () => {
            currentQ++;
            showQuestion();
        };

        showQuestion();
    </script>
</body>
</html>
