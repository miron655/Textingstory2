textingstory2 
<!DOCTYPE html>
<html lang="ro">
<head>
<meta charset="UTF-8">
<title>TextingStory Fake - Chat Real</title>
<style>
  body { background: white; font-family: Arial, sans-serif; display: flex; justify-content: center; padding: 20px; }
  #container { width: 100%; max-width: 400px; }
  #unlockBox { text-align: center; margin-bottom: 10px; }
  #unlockBox input { padding: 6px; width: 70%; border-radius: 6px; border: 1px solid #ccc; }
  #unlockBox button { padding: 6px 10px; border-radius: 6px; border: none; cursor: pointer; }
  #chatApp { display: none; }
  .header { display: flex; justify-content: space-between; margin-bottom: 10px; font-weight: bold; cursor: pointer; user-select: none; }
  #chat { display: flex; gap: 10px; border: 1px solid #ccc; border-radius: 10px; padding: 6px; background: #f9f9f9; min-height: 300px; overflow-y: auto; }
  .column { display: flex; flex-direction: column; gap: 4px; width: 50%; }
  #leftColumn { align-items: flex-start; }   /* Burgher în stânga */
  #rightColumn { align-items: flex-end; }    /* Cartofi în dreapta */
  .message { display: flex; align-items: center; padding: 6px 10px; border-radius: 20px; width: fit-content; max-width: 90%; margin-bottom: 2px; }
  .cartofi { background: #4CAF50; color: white; flex-direction: row-reverse; justify-content: flex-end; text-align: right; }
  .burgher { background: #FFEB3B; color: black; flex-direction: row; justify-content: flex-start; text-align: left; }
  .avatar { font-size: 20px; margin: 0 6px; }
  .input-area { display: flex; gap: 6px; margin-top: 10px; }
  input[type="text"] { flex: 1; padding: 6px; border-radius: 6px; border: 1px solid #ccc; }
  button { padding: 6px 10px; border-radius: 6px; border: none; cursor: pointer; }
</style>
</head>
<body>
<div id="container">
  <div id="unlockBox">
    <div>Introdu codul pentru acces:</div>
    <input type="text" id="codeInput" placeholder="Cod aici">
    <button onclick="checkCode()">Deblochează</button>
    <div id="unlockMsg" style="color:red; margin-top:5px;"></div>
    <div style="margin-top:10px; font-size:12px;">[Amprentă simulată]</div>
  </div>

  <div id="chatApp">
    <div class="header">
      <div id="nameBurgher" onclick="changeWriter('Ɓurgher🍔')">Ɓurgher🍔</div>
      <div id="nameCartofi" onclick="changeWriter('Cartofi🍟')">Cartofi🍟</div>
    </div>

    <div id="chat">
      <div id="leftColumn" class="column"></div>
      <div id="rightColumn" class="column"></div>
    </div>

    <div class="input-area">
      <input type="text" id="textInput" placeholder="Scrie ceva..." autofocus>
      <button onclick="sendMessage()">Trimite</button>
    </div>
  </div>
</div>

<script>
let currentWriter = "Cartofi🍟";
const correctCode = "banana1234";
const maxAttempts = 3;
const lockDuration = 5 * 60 * 60 * 1000; // 5 ore

// Inițializare localStorage
if(!localStorage.getItem('attempts')) localStorage.setItem('attempts', 0);
if(!localStorage.getItem('lockTime')) localStorage.setItem('lockTime', 0);
if(!localStorage.getItem('chatMessages')) localStorage.setItem('chatMessages', JSON.stringify([]));

function checkLock() {
  const lockTime = parseInt(localStorage.getItem('lockTime'));
  const now = Date.now();
  if(now < lockTime) {
    const remaining = Math.ceil((lockTime - now)/1000/60);
    document.getElementById('unlockMsg').innerText = `Blocat! Mai încearcă peste ${remaining} minute.`;
    return true;
  }
  return false;
}

function checkCode() {
  if(checkLock()) return;

  const input = document.getElementById("codeInput").value.trim();
  const msg = document.getElementById("unlockMsg");

  if(input === correctCode){
    document.getElementById("unlockBox").style.display = "none";
    document.getElementById("chatApp").style.display = "block";
    localStorage.setItem('attempts', 0);
    loadSavedMessages();
  } else {
    let attempts = parseInt(localStorage.getItem('attempts')) + 1;
    localStorage.setItem('attempts', attempts);
    if(attempts >= maxAttempts) {
      const lockTime = Date.now() + lockDuration;
      localStorage.setItem('lockTime', lockTime);
      msg.innerText = "Ai greșit de 3 ori! Încearcă peste 5 ore.";
    } else {
      msg.innerText = `Cod greșit! Mai ai ${maxAttempts - attempts} încercări.`;
    }
  }
}

function changeWriter(name) {
  currentWriter = name;
  document.getElementById("textInput").placeholder = `Scrie ca ${name}`;
  document.getElementById("textInput").focus();
}

function displayMessage(text, sender) {
  const leftCol = document.getElementById("leftColumn");
  const rightCol = document.getElementById("rightColumn");

  const msgDiv = document.createElement("div");
  msgDiv.className = "message " + (sender === "Cartofi🍟" ? "cartofi" : "burgher");

  const avatar = document.createElement("div");
  avatar.className = "avatar";
  avatar.innerText = (sender === "Cartofi🍟") ? "🍟" : "🍔";

  const msgText = document.createElement("div");
  msgText.innerText = text;

  msgDiv.appendChild(avatar);
  msgDiv.appendChild(msgText);

  if(sender === "Cartofi🍟") rightCol.appendChild(msgDiv);
  else leftCol.appendChild(msgDiv);

  document.getElementById("chat").scrollTop = document.getElementById("chat").scrollHeight;
}

function saveMessage(text, writer) {
  const messages = JSON.parse(localStorage.getItem('chatMessages'));
  messages.push({text, writer});
  localStorage.setItem('chatMessages', JSON.stringify(messages));
}

function loadSavedMessages() {
  const messages = JSON.parse(localStorage.getItem('chatMessages'));
  messages.forEach(msg => displayMessage(msg.text, msg.writer));
}

function sendMessage() {
  const inp = document.getElementById("textInput");
  if(inp.value.trim() === "") return;
  displayMessage(inp.value, currentWriter);
  saveMessage(inp.value, currentWriter);
  inp.value = "";
  inp.focus();
}

document.getElementById("textInput").addEventListener("keydown", function(e){
  if(e.key === "Enter") sendMessage();
});
</script>
</body>
</html>
