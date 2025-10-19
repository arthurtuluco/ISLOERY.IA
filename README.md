<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ISLODERY - Gerador de Vídeos e Fotos</title>
<style>
  body { margin:0; font-family: Arial, sans-serif; background:#111; color:#fff; display:flex; flex-direction:column; align-items:center; justify-content:center; height:100vh; }
  #videoPreview { width: 90%; max-width: 400px; border: 2px solid #ffd700; border-radius: 12px; margin-bottom: 20px; }
  .controls { display:flex; gap:10px; flex-wrap: wrap; justify-content:center; }
  button { padding:10px 16px; border:none; border-radius:8px; cursor:pointer; font-weight:bold; }
  #startRec { background:#28a745; color:#fff; }
  #stopRec { background:#dc3545; color:#fff; }
  #takePhoto { background:#ffc107; color:#111; }
  #downloadLink { display:block; margin-top:15px; color:#00f; text-decoration:underline; }
</style>
</head>
<body>

<h1>ISLODERY - Vídeos e Fotos</h1>
<video id="videoPreview" autoplay muted></video>

<div class="controls">
  <button id="startRec">Gravar Vídeo</button>
  <button id="stopRec">Parar Gravação</button>
  <button id="takePhoto">Tirar Foto</button>
</div>

<a id="downloadLink" href="#" download style="display:none;">Baixar</a>

<script>
let mediaStream;
let recorder;
let chunks = [];

const videoPreview = document.getElementById('videoPreview');
const startRecBtn = document.getElementById('startRec');
const stopRecBtn = document.getElementById('stopRec');
const takePhotoBtn = document.getElementById('takePhoto');
const downloadLink = document.getElementById('downloadLink');

// Acessar câmera e microfone
async function initMedia() {
  try {
    mediaStream = await navigator.mediaDevices.getUserMedia({ video:true, audio:true });
    videoPreview.srcObject = mediaStream;
  } catch(err) {
    alert("Erro ao acessar câmera/microfone: " + err);
  }
}
initMedia();

// Iniciar gravação
startRecBtn.addEventListener('click', () => {
  if(!mediaStream) return alert("Sem câmera disponível!");
  chunks = [];
  recorder = new MediaRecorder(mediaStream);
  recorder.ondataavailable = e => chunks.push(e.data);
  recorder.onstop = () => {
    const blob = new Blob(chunks, {type:'video/webm'});
    const url = URL.createObjectURL(blob);
    downloadLink.href = url;
    downloadLink.style.display = 'block';
    downloadLink.textContent = 'Baixar Vídeo';
  };
  recorder.start();
  alert("Gravação iniciada!");
});

// Parar gravação
stopRecBtn.addEventListener('click', () => {
  if(recorder && recorder.state !== "inactive") {
    recorder.stop();
    alert("Gravação finalizada!");
  }
});

// Tirar foto
takePhotoBtn.addEventListener('click', () => {
  if(!mediaStream) return;
  const canvas = document.createElement('canvas');
  canvas.width = videoPreview.videoWidth;
  canvas.height = videoPreview.videoHeight;
  canvas.getContext('2d').drawImage(videoPreview, 0,0);
  const dataURL = canvas.toDataURL('image/png');
  downloadLink.href = dataURL;
  downloadLink.style.display = 'block';
  downloadLink.download = 'foto.png';
  downloadLink.textContent = 'Baixar Foto';
});
</script>

</body>
</html>
