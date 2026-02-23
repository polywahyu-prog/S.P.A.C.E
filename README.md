 <!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>S.P.A.C.E</title>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:'Inter',sans-serif;}
body{
  background:linear-gradient(135deg,#141e30,#243b55);
  color:white;
  display:flex;
  justify-content:center;
  align-items:center;
  height:100vh;
  overflow:hidden;
  transition:0.5s;
}
.dark-therapy{
  background:linear-gradient(135deg,#050510,#0f2027);
}

.container{
  width:95%;
  max-width:520px;
  background:rgba(255,255,255,0.08);
  backdrop-filter:blur(25px);
  border-radius:25px;
  padding:25px;
  box-shadow:0 0 40px rgba(0,0,0,0.6);
  overflow-y:auto;
  max-height:95vh;
}

h2{text-align:center;margin-bottom:15px;}

button{
  width:100%;
  padding:12px;
  margin:6px 0;
  border:none;
  border-radius:12px;
  background:rgba(255,255,255,0.15);
  color:white;
  cursor:pointer;
  transition:0.3s;
}
button:hover{transform:translateY(-2px);}

.result{
  margin-top:10px;
  padding:12px;
  border-radius:12px;
  background:rgba(255,255,255,0.1);
  display:none;
  line-height:1.6;
}

textarea{
  width:100%;
  height:80px;
  border-radius:10px;
  padding:8px;
  border:none;
  margin-top:8px;
}

.progress-container{
  background:rgba(255,255,255,0.1);
  border-radius:20px;
  height:15px;
  margin-top:10px;
  overflow:hidden;
}

.progress-bar{
  height:100%;
  width:0%;
  background:linear-gradient(90deg,#00f260,#0575e6);
  transition:0.5s;
}

.breath{
  width:140px;
  height:140px;
  border-radius:50%;
  margin:15px auto;
  background:rgba(255,255,255,0.2);
  display:flex;
  justify-content:center;
  align-items:center;
  transition:0.5s;
  font-weight:600;
  text-align:center;
}

@keyframes breathing{
  0%{transform:scale(1);background:#6a11cb;}
  25%{transform:scale(1.3);background:#2575fc;}
  50%{transform:scale(1.6);background:#ff6ec4;}
  75%{transform:scale(1.3);background:#2575fc;}
  100%{transform:scale(1);background:#6a11cb;}
}

.stats{
  font-size:13px;
  opacity:0.8;
  margin-top:8px;
}

.journal-entry{
  font-size:12px;
  margin-top:6px;
  opacity:0.7;
}
</style>
</head>

<body>
<div class="container">

<h2>S.P.A.C.E Mandar</h2>

<button onclick="selectEmotion('dihiraukan')">Indanga nalawelaweang - Saya tidak dihiraukan😕</button>
<button onclick="selectEmotion('egois')">Indangi uoloi sola egois - Saya tidak suka teman yang egois🥲</button>
<button onclick="selectEmotion('inginPerhatian')">Meloa' na itu rupa tau - Saya ingin diperhatikan 😃</button>

<div class="result" id="result"></div>
<button id="solutionBtn" style="display:none;" onclick="showSolution()">Apa yang bisa saya lakukan?</button>
<div class="result" id="solution"></div>

<h3 style="margin-top:15px;">Curhat yuk 😊</h3>
<textarea id="curhatText" placeholder="Tulis perasaanmu..."></textarea>
<button onclick="saveCurhat()">Simpan Curhat</button>

<div class="stats" id="stats"></div>
<div class="progress-container">
  <div class="progress-bar" id="progressBar"></div>
</div>
<div id="journalHistory"></div>

<h3>Latihan Pernafasan Dulu 🥰</h3>
<div class="breath" id="breathCircle">Tarik Nafas</div>
<button onclick="startBreathing()">Mulai</button>

<h3>Sound Therapy ☔</h3>
<button onclick="toggleRain()">Hujan Menenangkan</button>
<button onclick="toggleDark()">Deep Night Therapy Mode</button>

</div>

<script>
let emotionCount=JSON.parse(localStorage.getItem("emotionCount"))||0;
let journal=JSON.parse(localStorage.getItem("journal"))||[];
let selectedEmotion="";
let rainNoise;

function updateStats(){
  document.getElementById("stats").innerText=
  "Total refleksi emosional kamu: "+emotionCount;
  document.getElementById("progressBar").style.width=
  Math.min(emotionCount*10,100)+"%";
}
updateStats();

function renderJournal(){
  const container=document.getElementById("journalHistory");
  container.innerHTML="";
  journal.slice(-3).reverse().forEach(entry=>{
    const div=document.createElement("div");
    div.className="journal-entry";
    div.innerText=entry.date+" - "+entry.text;
    container.appendChild(div);
  });
}
renderJournal();

const emotions={
dihiraukan:"Kamu merasa diabaikan. Itu wajar, otak merespon seperti rasa sakit fisik.",
egois:"Teman egois bisa bikin stres. Perasaanmu valid dan penting untuk dihargai.",
inginPerhatian:"Kamu ingin diperhatikan. Kebutuhan ini alami dan manusiawi."
};

const solutions={
dihiraukan:"✨ Cobalah ungkapkan perasaanmu secara jujur pada teman dekat atau orang yang kamu percaya. Fokus pada kalimat 'Aku merasa…', bukan menyalahkan.<br>✨ Ambil waktu untuk self-care dan aktivitas yang membuatmu dihargai.<br>✨ Ingat, kamu berharga dan diperhatikan oleh orang-orang yang peduli.",
egois:"✨ Fokus pada batasan sehat, komunikasikan dengan tenang.<br>✨ Jangan biarkan perilaku egois merusak moodmu.<br>✨ Cari teman atau komunitas yang suportif, sehingga perasaanmu dihargai.",
inginPerhatian:"✨ Jangan ragu minta perhatian dari orang yang peduli.<br>✨ Buat aktivitas yang menyalurkan kebutuhan sosialmu.<br>✨ Ingat, meminta diperhatikan itu wajar dan menunjukkan kasih sayang pada diri sendiri."
};

function selectEmotion(type){
  selectedEmotion=type;
  document.getElementById("result").style.display="block";
  document.getElementById("result").innerText=emotions[type];
  document.getElementById("solutionBtn").style.display="block";
  emotionCount++;
  localStorage.setItem("emotionCount",emotionCount);
  updateStats();
}

function showSolution(){
  if(selectedEmotion){
    document.getElementById("solution").style.display="block";
    document.getElementById("solution").innerHTML=solutions[selectedEmotion];
  }
}

function saveCurhat(){
  const text=document.getElementById("curhatText").value;
  if(text.trim()==="") return;
  const date=new Date().toLocaleDateString();
  journal.push({date,text});
  localStorage.setItem("journal",JSON.stringify(journal));
  document.getElementById("curhatText").value="";
  renderJournal();
  alert("Terima kasih sudah berbagi. Perasaanmu penting dan layak didengar 🤍");
}

function startBreathing(){
  const circle=document.getElementById("breathCircle");
  circle.style.animation="breathing 6s infinite";
  circle.innerText="Tarik 4 - Tahan 4 - Hembus 6";
}

function createRainNoise(){
  const ctx=new (window.AudioContext||window.webkitAudioContext)();
  const bufferSize=2*ctx.sampleRate;
  const buffer=ctx.createBuffer(1,bufferSize,ctx.sampleRate);
  const output=buffer.getChannelData(0);
  for(let i=0;i<bufferSize;i++) output[i]=Math.random()*2-1;
  const source=ctx.createBufferSource();
  source.buffer=buffer;
  source.loop=true;
  const gain=ctx.createGain();
  gain.gain.value=0.2;
  source.connect(gain);
  gain.connect(ctx.destination);
  source.start(0);
  return source;
}

function toggleRain(){
  rainNoise=rainNoise? (rainNoise.stop(),null):createRainNoise();
}

function toggleDark(){
  document.body.classList.toggle("dark-therapy");
}
</script>

</body>
</html>
