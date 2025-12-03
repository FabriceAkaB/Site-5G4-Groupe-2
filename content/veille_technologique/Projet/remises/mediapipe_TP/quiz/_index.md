+++
title = "Quiz MediaPipe"
weight = 4
+++

<style>
.quiz-container {
  max-width: 760px;
  margin: 0 auto;
  padding: 2rem;
  border: 1px solid #d9e2ec;
  border-radius: 14px;
  background: #fdfefe;
  box-shadow: 0 10px 25px rgba(15, 23, 42, 0.08);
}
.quiz-intro {
  margin-bottom: 1.5rem;
  line-height: 1.6;
  color: #1f2933;
}
.quiz-intro h2 {
  margin: 0 0 0.35rem;
  font-size: 1.5rem;
  color: #102a43;
}
.quiz-intro p {
  margin: 0 0 0.5rem;
}
.quiz-rules {
  margin: 0;
  padding-left: 1.2rem;
  color: #486581;
  font-size: 0.95rem;
}
.quiz-question-grid {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}
.quiz-card {
  padding: 1.25rem;
  border: 1px solid #e2e8f0;
  border-radius: 12px;
  background: #fff;
  transition: border-color 0.2s ease, box-shadow 0.2s ease;
}
.quiz-card:hover {
  border-color: #cbd5f5;
  box-shadow: 0 6px 15px rgba(79, 117, 208, 0.18);
}
.quiz-card-correct {
  border-color: #38a169;
  box-shadow: 0 6px 18px rgba(56, 161, 105, 0.25);
}
.quiz-card-wrong {
  border-color: #e53e3e;
  box-shadow: 0 6px 18px rgba(229, 62, 62, 0.2);
}
.quiz-card-pending {
  border-color: #f7c948;
}
.quiz-question-title {
  margin: 0 0 0.75rem;
  font-size: 1.1rem;
  line-height: 1.4;
  color: #0f172a;
}
.quiz-question-num {
  font-weight: 600;
  color: #4c51bf;
}
.quiz-options {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}
.quiz-option {
  display: flex;
  gap: 0.35rem;
  font-size: 0.98rem;
  color: #243b53;
}
.quiz-card-status {
  margin-top: 0.65rem;
  font-size: 0.9rem;
  font-weight: 600;
  color: #486581;
}
.quiz-actions {
  margin-top: 1.5rem;
  display: flex;
  gap: 0.75rem;
}
.quiz-actions button {
  flex: 1;
  padding: 0.85rem 1rem;
  border-radius: 10px;
  border: none;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}
.quiz-actions button[type=submit] {
  background: linear-gradient(120deg, #4c6ef5, #2d6a4f);
  color: #fff;
  box-shadow: 0 10px 20px rgba(76, 110, 245, 0.25);
}
.quiz-actions button[type=button] {
  background: #edf2ff;
  color: #2f3e9e;
  border: 1px solid #c3dafe;
}
.quiz-actions button:hover {
  transform: translateY(-1px);
}
.quiz-feedback {
  margin-top: 1.8rem;
  padding: 1.1rem 1.3rem;
  border-radius: 10px;
  background: #f0fff4;
  border: 1px solid #c6f6d5;
  color: #22543d;
  box-shadow: inset 0 0 0 1px rgba(255, 255, 255, 0.4);
}
.quiz-feedback ul {
  margin: 0.5rem 0 0;
  padding-left: 1.1rem;
}
.quiz-feedback li {
  margin-bottom: 0.4rem;
}
</style>

<section class="quiz-container">
<div class="quiz-intro">
<h2>Quiz MediaPipe Hands</h2>
<p>5 questions essentielles pour verifier la bonne comprehension des notes de cours.</p>
<ul class="quiz-rules">
<li>Reponds a toutes les questions avant de valider.</li>
<li>Tu obtiendras un retour detaille question par question.</li>
<li>Utilise <em>Reinitialiser</em> pour retenter un perfect.</li>
</ul>
</div>

<form id="mediapipe-quiz" class="quiz-question-grid">
<div class="quiz-card" data-question="q1">
<p class="quiz-question-title"><span class="quiz-question-num">Q1.</span> Quel est le role principal du module <em>Palm Detection</em> dans MediaPipe Hands ?</p>
<ul class="quiz-options">
<li><label class="quiz-option"><input type="radio" name="q1" value="a"> Fournir les coordonnees 3D exactes de chaque doigt.</label></li>
<li><label class="quiz-option"><input type="radio" name="q1" value="b"> Calculer les angles articulaires de la main robotique.</label></li>
<li><label class="quiz-option"><input type="radio" name="q1" value="c"> Detecter rapidement la paume, zone stable pour lancer le pipeline.</label></li>
<li><label class="quiz-option"><input type="radio" name="q1" value="d"> Filtrer les landmarks avec un algorithme de Kalman.</label></li>
</ul>
<p class="quiz-card-status"></p>
</div>

<div class="quiz-card" data-question="q2">
<p class="quiz-question-title"><span class="quiz-question-num">Q2.</span> MediaPipe repose sur un <em>graph processing framework</em>. Que signifie cette approche ?</p>
<ul class="quiz-options">
<li><label class="quiz-option"><input type="radio" name="q2" value="a"> Chaque frame est envoyee directement a TensorFlow pour recursion.</label></li>
<li><label class="quiz-option"><input type="radio" name="q2" value="b"> Une suite de calculators traite les images via un graphe optimise.</label></li>
<li><label class="quiz-option"><input type="radio" name="q2" value="c"> Le modele applique uniquement des filtres convolutionnels 3D.</label></li>
<li><label class="quiz-option"><input type="radio" name="q2" value="d"> L'algorithme ne fonctionne que si un GPU est disponible.</label></li>
</ul>
<p class="quiz-card-status"></p>
</div>

<div class="quiz-card" data-question="q3">
<p class="quiz-question-title"><span class="quiz-question-num">Q3.</span> Quelle affirmation correspond a une limitation de MediaPipe Hands ?</p>
<ul class="quiz-options">
<li><label class="quiz-option"><input type="radio" name="q3" value="a"> Les landmarks incluent une profondeur absolue en centimetres.</label></li>
<li><label class="quiz-option"><input type="radio" name="q3" value="b"> Le module perd en precision lorsque la scene est mal eclairee.</label></li>
<li><label class="quiz-option"><input type="radio" name="q3" value="c"> Il ne peut analyser qu'une main gauche.</label></li>
<li><label class="quiz-option"><input type="radio" name="q3" value="d"> Il requiert obligatoirement un capteur de profondeur.</label></li>
</ul>
<p class="quiz-card-status"></p>
</div>

<div class="quiz-card" data-question="q4">
<p class="quiz-question-title"><span class="quiz-question-num">Q4.</span> Lequel des modules suivants combine visage, pose et mains ?</p>
<ul class="quiz-options">
<li><label class="quiz-option"><input type="radio" name="q4" value="a"> MediaPipe Pose</label></li>
<li><label class="quiz-option"><input type="radio" name="q4" value="b"> MediaPipe Holistic</label></li>
<li><label class="quiz-option"><input type="radio" name="q4" value="c"> MediaPipe Face Mesh</label></li>
<li><label class="quiz-option"><input type="radio" name="q4" value="d"> MediaPipe Tasks for Web</label></li>
</ul>
<p class="quiz-card-status"></p>
</div>

<div class="quiz-card" data-question="q5">
<p class="quiz-question-title"><span class="quiz-question-num">Q5.</span> Quel parametrage aide a faire tourner MediaPipe Hands sur un Raspberry Pi 4 ?</p>
<ul class="quiz-options">
<li><label class="quiz-option"><input type="radio" name="q5" value="a"> Capturer en 4K a 60 FPS pour ecouter les doigts.</label></li>
<li><label class="quiz-option"><input type="radio" name="q5" value="b"> Desactiver totalement le calcul sur CPU.</label></li>
<li><label class="quiz-option"><input type="radio" name="q5" value="c"> Abaisser la resolution a 640x480 avec un eclairage stable.</label></li>
<li><label class="quiz-option"><input type="radio" name="q5" value="d"> Utiliser exclusivement la version Holistic.</label></li>
</ul>
<p class="quiz-card-status"></p>
</div>

<div class="quiz-actions">
<button type="submit">Valider mes reponses</button>
<button type="button" id="quiz-reset">Reinitialiser</button>
</div>
</form>

<div id="quiz-feedback" class="quiz-feedback" aria-live="polite" hidden></div>
<p  class="quiz-intro">J'ai implémenté ce quiz pour le fun avec un llm chatgpt</p>
</section>

<script>
(function() {
const quiz = document.getElementById("mediapipe-quiz");
if (!quiz) return;
const feedback = document.getElementById("quiz-feedback");
const resetButton = document.getElementById("quiz-reset");
const cards = Array.from(document.querySelectorAll(".quiz-card"));

const answerKey = [
  { name: "q1", answer: "c", explanation: "Palm Detection repere la paume pour amorcer la pipeline." },
  { name: "q2", answer: "b", explanation: "MediaPipe enchaine des calculators dans un graphe optimise." },
  { name: "q3", answer: "b", explanation: "Une lumiere faible ou un contre-jour fait perdre la precision." },
  { name: "q4", answer: "b", explanation: "MediaPipe Holistic fusionne visage, pose et mains." },
  { name: "q5", answer: "c", explanation: "Sur Raspberry Pi 4, une resolution 640x480 et un eclairage stable aident a tenir 8-12 FPS." }
];
function resetStates() {
  cards.forEach(card => {
    card.classList.remove("quiz-card-correct", "quiz-card-wrong", "quiz-card-pending");
    const status = card.querySelector(".quiz-card-status");
    if (status) {
      status.textContent = "";
    }
  });
}

function renderFeedback(obj) {
  feedback.removeAttribute("hidden");
  feedback.style.background = obj.background;
  feedback.style.borderColor = obj.border;
  feedback.style.color = obj.color;
  feedback.innerHTML = obj.content;
}

quiz.addEventListener("submit", function(event) {
  event.preventDefault();
  resetStates();
  let score = 0;
  const unanswered = [];

  const details = answerKey.map((item, index) => {
    const card = cards[index];
    const status = card ? card.querySelector(".quiz-card-status") : null;
    const selected = quiz.querySelector(`input[name="${item.name}"]:checked`);
    const value = selected ? selected.value : null;

    if (!value) {
      unanswered.push(index);
      if (card) {
        card.classList.add("quiz-card-pending");
        if (status) status.textContent = "Question en attente de reponse.";
      }
      return `Question ${index + 1} : repondre avant de valider.`;
    }

    const isCorrect = value === item.answer;
    if (card) {
      card.classList.add(isCorrect ? "quiz-card-correct" : "quiz-card-wrong");
      if (status) status.textContent = isCorrect ? "Bonne reponse." : "A revoir.";
    }
    if (isCorrect) score += 1;
    return `Question ${index + 1} : ${isCorrect ? "Correct" : "Incorrect"} - ${item.explanation}`;
  });

  if (unanswered.length) {
    renderFeedback({
      background: "#fffaf0",
      border: "#f6ad55",
      color: "#7b341e",
      content: `<p><strong>Petit rappel :</strong> reponds a toutes les questions avant la correction.</p>`
    });
    return;
  }

  renderFeedback({
    background: "#f0fff4",
    border: "#c6f6d5",
    color: "#22543d",
    content: `<p><strong>Resultat :</strong> ${score} / ${answerKey.length}</p><ul>${details.map(d => `<li>${d}</li>`).join("")}</ul>`
  });
});

if (resetButton) {
  resetButton.addEventListener("click", function() {
    quiz.reset();
    resetStates();
    feedback.innerHTML = "";
    feedback.setAttribute("hidden", "hidden");
  });
}
})();
</script>
