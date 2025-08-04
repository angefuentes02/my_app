<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Test con Corrección Manual Detallada</title>

<link rel="manifest" href="manifest.json" />
<meta name="theme-color" content="#317EFB"/>

<style>
  body { font-family: Arial, sans-serif; padding: 20px; background: #f4f4f4; }
  h1 { text-align: center; }
  .question, .review-question { margin: 6px 0; background: #fff; padding: 8px; border-radius: 4px; box-shadow: 0 1px 3px #ccc; }
  .number { display: inline-block; width: 30px; font-weight: bold; }
  .options label { margin-right: 15px; }
  .button-container { margin-top: 20px; text-align: center; }
  button { padding: 10px 20px; font-size: 16px; margin: 5px; cursor: pointer; }
  #timer { font-size: 18px; font-weight: bold; margin: 10px 0; text-align: center; }
  #result { margin-top: 20px; font-weight: bold; white-space: pre-line; text-align: center; }
  #reviewPanel { margin-top: 20px; display: none; max-height: 60vh; overflow-y: auto; }
</style>

<script>
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', function() {
      navigator.serviceWorker.register('sw.js').then(function(registration) {
        console.log('ServiceWorker registrado con éxito:', registration.scope);
      }).catch(function(error) {
        console.log('Error registrando ServiceWorker:', error);
      });
    });
  }
</script>

</head>
<body>

<h1>Test con Corrección Manual Detallada</h1>
<div id="timer">Tiempo: 00:00:00</div>

<form id="testForm"></form>

<div class="button-container">
  <label for="penalty">Penalización por fallo:</label>
  <select id="penalty">
    <option value="0">0</option>
    <option value="0.25">0.25</option>
    <option value="0.33">0.33</option>
    <option value="0.5" selected>0.5</option>
  </select>
  <br>
  <button id="evaluateBtn" type="button">Evaluar respuestas</button>
  <button id="calcScoreBtn" type="button" style="display:none;">Calcular nota</button>
</div>

<div id="reviewPanel"></div>
<div id="result"></div>

<script>
  const testForm = document.getElementById('testForm');
  const reviewPanel = document.getElementById('reviewPanel');
  const resultDiv = document.getElementById('result');
  const evaluateBtn = document.getElementById('evaluateBtn');
  const calcScoreBtn = document.getElementById('calcScoreBtn');
  const penaltySelect = document.getElementById('penalty');
  const timerDisplay = document.getElementById('timer');

  // Cronómetro
  let seconds = 0;
  let timerInterval = setInterval(() => {
    seconds++;
    const hrs = String(Math.floor(seconds / 3600)).padStart(2, '0');
    const mins = String(Math.floor((seconds % 3600) / 60)).padStart(2, '0');
    const secs = String(seconds % 60).padStart(2, '0');
    timerDisplay.textContent = `Tiempo: ${hrs}:${mins}:${secs}`;
  }, 1000);

  // Generar preguntas para responder (A, B, C, Anulada)
  for(let i = 1; i <= 100; i++) {
    const div = document.createElement('div');
    div.className = 'question';
    div.innerHTML = `
      <span class="number">${i}.</span>
      <span class="options">
        <label><input type="radio" name="q${i}" value="A"> A</label>
        <label><input type="radio" name="q${i}" value="B"> B</label>
        <label><input type="radio" name="q${i}" value="C"> C</label>
        <label><input type="radio" name="q${i}" value="Anulada"> Anular</label>
      </span>
    `;
    testForm.appendChild(div);
  }

  evaluateBtn.onclick = () => {
    // Generar panel para corregir marcando bien, mal, anulada o no respondida para cada pregunta
    reviewPanel.innerHTML = '<h3>Marca el estado de cada respuesta:</h3>';
    reviewPanel.style.display = 'block';
    resultDiv.textContent = '';
    calcScoreBtn.style.display = 'inline-block';
    evaluateBtn.style.display = 'none';

    // Detener cronómetro porque se termina la respuesta y comienza corrección
    clearInterval(timerInterval);

    // Recoger respuestas dadas
    const formData = new FormData(testForm);

    for(let i = 1; i <= 100; i++) {
      const userAnswer = formData.get(`q${i}`) || 'Sin responder';
      const div = document.createElement('div');
      div.className = 'review-question';
      div.innerHTML = `
        <span class="number">${i}.</span>
        <span>Respuesta dada: <strong>${userAnswer}</strong></span><br>
        <label><input type="radio" name="state${i}" value="bien" required> Bien (acierto)</label>
        <label><input type="radio" name="state${i}" value="mal"> Mal (fallo)</label>
        <label><input type="radio" name="state${i}" value="anulada"> Anulada</label>
        <label><input type="radio" name="state${i}" value="no"> No contestada</label>
      `;
      reviewPanel.appendChild(div);
    }
  };

  calcScoreBtn.onclick = () => {
    const penalty = parseFloat(penaltySelect.value);
    let correct = 0, wrong = 0, annulled = 0, unanswered = 0;

    // Vamos a leer los estados marcados
    for(let i = 1; i <= 100; i++) {
      const radios = document.getElementsByName(`state${i}`);
      let val = null;
      for (const r of radios) {
        if(r.checked) {
          val = r.value;
          break;
        }
      }
      if(!val) {
        alert(`Marca el estado para la pregunta ${i}`);
        return;
      }
      if(val === 'bien') correct++;
      else if(val === 'mal') wrong++;
      else if(val === 'anulada') annulled++;
      else unanswered++;
    }

    const rawScore = correct - (wrong * penalty);
    const finalScore = Math.max(0, (rawScore / 100) * 10).toFixed(2);

    const hrs = String(Math.floor(seconds / 3600)).padStart(2, '0');
    const mins = String(Math.floor((seconds % 3600) / 60)).padStart(2, '0');
    const secs = String(seconds % 60).padStart(2, '0');

    resultDiv.textContent = `
Aciertos: ${correct}
Fallos: ${wrong}
Anuladas: ${annulled}
No contestadas: ${unanswered}
Penalización por fallo: ${penalty}
Nota final: ${finalScore} / 10
Tiempo total: ${hrs}:${mins}:${secs}
    `;

    calcScoreBtn.style.display = 'none';
  };
</script>

</body>
</html>
