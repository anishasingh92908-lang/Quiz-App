<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Ultimate Fun Quiz</title>
  <style>
      :root {
  --bg: #fff;
  --text: #333;
  --primary: #00bcd4;
  --correct: #4CAF50;
  --incorrect: #f44336;
}

body.dark {
  --bg: #1e1e1e;
  --text: #eee;
  --primary: #ff9800;
}

body {
  margin: 0;
  padding: 0;
  background: var(--bg);
  color: var(--text);
  font-family: 'Comic Sans MS', cursive;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  transition: background 0.5s, color 0.5s;
}

#theme-toggle {
  position: absolute;
  top: 20px;
  right: 20px;
  background: var(--primary);
  color: white;
  border: none;
  padding: 10px;
  border-radius: 50%;
  cursor: pointer;
}

.container {
  background: var(--bg);
  padding: 30px;
  border-radius: 20px;
  width: 90%;
  max-width: 600px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  text-align: center;
}

.status-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  font-weight: bold;
}

.timer-wrapper {
  width: 100px;
  height: 10px;
  background: #ddd;
  border-radius: 5px;
  overflow: hidden;
}

#timer-bar {
  height: 100%;
  background: var(--primary);
  width: 100%;
  transition: width 1s linear;
}

#answers button {
  background-color: #fce38a;
  border: none;
  width: 100%;
  padding: 12px;
  margin: 8px 0;
  border-radius: 10px;
  cursor: pointer;
  font-size: 16px;
  transition: 0.3s;
}

#answers button:hover {
  background-color: #f9d423;
}

.correct {
  background-color: var(--correct) !important;
  color: white;
}

.incorrect {
  background-color: var(--incorrect) !important;
  color: white;
}

#next-btn {
  margin-top: 20px;
  padding: 12px 20px;
  background-color: var(--primary);
  border: none;
  color: white;
  font-size: 16px;
  border-radius: 10px;
  cursor: pointer;
  display: none;
}

#high-score {
  margin-top: 10px;
  font-weight: bold;
}
  </style>
</head>
<body>
  <button id="theme-toggle">🌙</button>
  <div class="container">
    <h1>🎉 Ultimate Fun Quiz</h1>
    <div class="quiz-box">
      <div class="status-bar">
        <span id="progress">Question 1/10</span>
        <div class="timer-wrapper">
          <div id="timer-bar"></div>
        </div>
      </div>
      <h2 id="question">Loading question...</h2>
      <div id="answers"></div>
      <button id="next-btn">Next</button>
    </div>
    <p id="high-score">🏆 High Score: 0</p>
  </div>
  <script>
      const questions = [
  { question: "🌐 What does HTML stand for?", answers: ["Hyper Text Markup Language", "Home Tool Markup Language", "Hyperlinks Text Management Language", "Hyper Tool Made Language"], correct: 0 },
  { question: "🎨 Which language styles web pages?", answers: ["HTML", "Python", "CSS", "Node.js"], correct: 2 },
  { question: "📜 What does JS stand for?", answers: ["JustScript", "JavaSource", "JavaScript", "JScript"], correct: 2 },
  { question: "🏢 Who developed JavaScript?", answers: ["Microsoft", "Google", "Netscape", "Apple"], correct: 2 },
  { question: "⚛ JS framework?", answers: ["Laravel", "React", "Django", "Flask"], correct: 1 },
  { question: "🧵 CSS stands for?", answers: ["Colorful Style Sheets", "Creative Style System", "Cascading Style Sheets", "Computer Style Sheets"], correct: 2 },
  { question: "🔗 HTML tag for hyperlink?", answers: ["<link>", "<a>", "<href>", "<url>"], correct: 1 },
  { question: "💬 JS comment symbol?", answers: ["//", "<!-- -->", "#", "/* */"], correct: 0 },
  { question: "🔁 JS loop?", answers: ["for", "repeat", "iterate", "loop"], correct: 0 },
  { question: "🔒 Declare constant in JS?", answers: ["let", "var", "const", "static"], correct: 2 }
];

let currentIndex = 0;
let score = 0;
let timer;
let timeLeft = 15;
let shuffled = [];

const questionEl = document.getElementById("question");
const answersEl = document.getElementById("answers");
const nextBtn = document.getElementById("next-btn");
const progressEl = document.getElementById("progress");
const timerBar = document.getElementById("timer-bar");
const highScoreEl = document.getElementById("high-score");

function shuffleQuestions() {
  shuffled = [...questions].sort(() => Math.random() - 0.5);
}

function startQuiz() {
  shuffleQuestions();
  currentIndex = 0;
  score = 0;
  nextBtn.innerText = "Next";
  showQuestion();
}

function showQuestion() {
  resetState();
  let q = shuffled[currentIndex];
  questionEl.innerText = q.question;
  progressEl.innerText = Question ${currentIndex + 1}/${shuffled.length};
  timeLeft = 15;
  startTimer();

  q.answers.forEach((answer, i) => {
    const btn = document.createElement("button");
    btn.innerText = answer;
    btn.addEventListener("click", () => selectAnswer(i));
    answersEl.appendChild(btn);
  });
}


function resetState() {
  clearInterval(timer);
  nextBtn.style.display = "none";
  answersEl.innerHTML = "";
  timerBar.style.width = "100%";
}

function startTimer() {
  timer = setInterval(() => {
    timeLeft--;
    timerBar.style.width = ${(timeLeft / 15) * 100}%;

    if (timeLeft <= 0) {
      clearInterval(timer);
      showCorrect();
      nextBtn.style.display = "block";
    }
  }, 1000);
}

function selectAnswer(index) {
  clearInterval(timer);
  const correctIndex = shuffled[currentIndex].correct;
  const buttons = answersEl.querySelectorAll("button");

  buttons.forEach((btn, i) => {
    btn.disabled = true;
    if (i === correctIndex) btn.classList.add("correct");
    else if (i === index) btn.classList.add("incorrect");
  });

  if (index === correctIndex) {
    score++;
    playSound("correct");
  } else {
    playSound("wrong");
  }

  nextBtn.style.display = "block";
}

function showCorrect() {
  const correctIndex = shuffled[currentIndex].correct;
  const buttons = answersEl.querySelectorAll("button");
  buttons.forEach((btn, i) => {
    btn.disabled = true;
    if (i === correctIndex) btn.classList.add("correct");
  });
  playSound("wrong");
}

nextBtn.addEventListener("click", () => {
  currentIndex++;
  if (currentIndex < shuffled.length) {
    showQuestion();
  } else {
    showScore();
  }
});

function showScore() { 
  resetState();
  questionEl.innerText = 🎉 You scored ${score} out of ${shuffled.length}!;
  nextBtn.innerText = "Play Again";
  nextBtn.style.display = "block";
  updateHighScore();
  triggerConfetti();
  nextBtn.onclick = startQuiz;
}


function updateHighScore() {
  let high = localStorage.getItem("highScore");

  if (!high || score > parseInt(high)) {
    localStorage.setItem("highScore", score);
    highScoreEl.innerText = 🏆 High Score: ${score};
  } else {
    highScoreEl.innerText = 🏆 High Score: ${high};
  }
}


function triggerConfetti() {
  document.body.style.transition = "background 0.5s";
  document.body.style.background = "linear-gradient(135deg, #f6d365, #fda085)";
  setTimeout(() => {
    document.body.style.background = "";
  }, 1500);
}

function playSound(type) {
  const audio = new Audio();
  if (type === "correct") {
    audio.src = "https://www.soundjay.com/buttons/sounds/button-4.mp3";
  } else {
    audio.src = "https://www.soundjay.com/buttons/sounds/button-10.mp3";
  }
  audio.play();
}

startQuiz();
  </script>
</body>
</html>
