<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>The Blaze Java | Postulaciones</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
body{
    margin:0;
    font-family:Arial, sans-serif;
    background:radial-gradient(circle at top,#111,#000);
    color:#e4e6eb;
}
.container{max-width:1200px;margin:auto;padding:40px 20px}
h1{text-align:center;margin-bottom:40px}
.cards{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:25px}

.card{
    background:linear-gradient(180deg,#0f1115,#0a0b0e);
    border-radius:16px;
    padding:25px;
    box-shadow:0 0 40px rgba(0,0,0,.8);
    display:flex;
    flex-direction:column;
    justify-content:space-between
}
.icon{
    width:50px;height:50px;
    background:#7c6a00;
    border-radius:12px;
    display:flex;align-items:center;justify-content:center;
    margin-bottom:20px
}
.card h2{margin:0 0 10px}
.card p{color:#9da1a9;font-size:14px}

.alert{
    margin:20px 0;
    padding:12px;
    border-radius:10px;
    background:rgba(240,71,71,.15);
    color:#f04747;
    display:flex;
    gap:8px;
    font-size:14px
}

button{
    padding:12px;
    border-radius:10px;
    border:none;
    font-weight:bold;
    cursor:pointer;
    font-size:15px
}
.btn-open{background:linear-gradient(135deg,#43b581,#2ea043);color:white}
.btn-closed{background:#2b2d31;color:#72767d;cursor:not-allowed}
.admin{margin-top:10px;background:#5865f2;color:white}

/* MODAL */
.modal{
    position:fixed;inset:0;
    background:rgba(0,0,0,.8);
    display:none;
    align-items:center;
    justify-content:center;
    z-index:10
}
.modal-content{
    background:#1c1f26;
    padding:30px;
    border-radius:16px;
    max-width:600px;
    width:90%;
    text-align:left;
    max-height:80vh;
    overflow-y:auto; /* Scroll interno */
}
.modal h3{margin-top:0}
label{display:block;margin-top:15px;font-size:14px}
input, textarea{
    width:100%;
    padding:10px;
    margin-top:5px;
    border-radius:8px;
    border:none;
    background:#2b2d31;
    color:white;
}
textarea{resize:vertical;}
.modal button{margin-top:20px;width:100%;background:#5865f2;color:white;cursor:pointer}

/* Botón revisar postulaciones */
#btn-ver-postulaciones {
  background:#5865f2;
  color:white;
  border-radius:10px;
  padding:12px;
  cursor:pointer;
  width:100%;
  max-width:300px;
  margin:20px auto;
  display:block;
}
#btn-ver-postulaciones:hover{opacity:0.85;}

/* Botón eliminar dentro del modal */
.btn-eliminar{
  background:#f04747;
  color:white;
  padding:6px 12px;
  border-radius:6px;
  font-size:12px;
  margin-top:5px;
  cursor:pointer;
}
.btn-eliminar:hover{opacity:0.8;}
</style>
</head>

<body>

<div class="container">
<h1>Postulaciones The Blaze Java</h1>

<div class="cards">

<!-- SOPORTE -->
<div class="card">
<div>
<div class="icon">📄</div>
<h2>Rango SOPORTE</h2>
<p>Postulación para formar parte del equipo de soporte del clan.</p>
<div class="alert" id="alert-soporte"></div>
</div>
<button id="btn-soporte"></button>
<button class="admin" onclick="adminToggle('soporte')">Admin: Abrir / Cerrar</button>
</div>

</div>

<!-- Botón para que admins vean postulaciones -->
<button id="btn-ver-postulaciones" onclick="mostrarPostulaciones()">Ver postulaciones enviadas</button>

<!-- MODAL -->
<div class="modal" id="modal">
<div class="modal-content">
<h3 id="modalTitle"></h3>
<div id="questions"></div>
<button onclick="cerrar()">Enviar Postulación</button>
</div>
</div>

<script>
const preguntas = {
soporte:[
"Tu nombre de Discord:",
"¿Por qué quieres ser soporte?",
"¿Cuánto tiempo podrías dedicar al clan?",
"¿Tienes experiencia en soporte o atención a jugadores?"
]
};

const adminPassword = "1.21 #3 parcelas";

function estado(r){ return localStorage.getItem(r) === "abierto"; }

function actualizar(r){
  const btn = document.getElementById("btn-"+r);
  const alert = document.getElementById("alert-"+r);

  if(estado(r)){
    alert.innerHTML = "🟢 Postulación ABIERTA";
    alert.style.background = "rgba(67,181,129,.15)";
    alert.style.color = "#43b581";
    btn.textContent = "Postularse";
    btn.className = "btn-open";
    btn.onclick = () => abrir(r);
  }else{
    alert.innerHTML = "🔒 Postulación CERRADA";
    alert.style.background = "rgba(240,71,71,.15)";
    alert.style.color = "#f04747";
    btn.textContent = "Cerrada";
    btn.className = "btn-closed";
    btn.onclick = null;
  }
}

function adminToggle(r){
  const pass = prompt("Introduce la contraseña de administrador para cambiar el estado de: " + r.toUpperCase());
  if(pass === adminPassword){
    localStorage.setItem(r, estado(r) ? "cerrado" : "abierto");
    actualizar(r);
    alert("Estado cambiado correctamente.");
  } else {
    alert("Contraseña incorrecta. No tienes permiso.");
  }
}

function abrir(r){
  document.getElementById("modal").style.display = "flex";
  document.getElementById("modalTitle").textContent = "Postulación - " + r.toUpperCase();
  const q = document.getElementById("questions");
  q.innerHTML = "";
  preguntas[r].forEach(p=>{
      if(p.toLowerCase().includes("por qué") || p.toLowerCase().includes("experiencia") || p.toLowerCase().includes("cuánto tiempo")){
          q.innerHTML += `<label>${p}</label><textarea required></textarea>`;
      } else {
          q.innerHTML += `<label>${p}</label><input type="text" required>`;
      }
  });
}

function cerrar() {
  const modalTitle = document.getElementById("modalTitle").textContent.toLowerCase().replace("postulación - ", "");
  const inputs = document.querySelectorAll("#questions input, #questions textarea");
  const respuestas = [];
  inputs.forEach(input => respuestas.push(input.value.trim()));

  if(!respuestas[0]){
    alert("Debes ingresar tu nombre de Discord.");
    return;
  }

  let actuales = JSON.parse(localStorage.getItem("postulaciones_" + modalTitle)) || [];
  actuales.push(respuestas);
  localStorage.setItem("postulaciones_" + modalTitle, JSON.stringify(actuales));

  document.getElementById("modal").style.display = "none";
  alert("Postulación enviada con éxito.");
}

function mostrarPostulaciones() {
  const pass = prompt("Ingresa la contraseña de administrador para ver las postulaciones:");
  if(pass !== adminPassword){ alert("Contraseña incorrecta."); return; }

  let todasPostulaciones = "";
  ["soporte"].forEach(rango => {
    const data = JSON.parse(localStorage.getItem("postulaciones_" + rango)) || [];
    if(data.length > 0) {
      todasPostulaciones += `<h3>Postulaciones para ${rango.toUpperCase()}</h3>`;
      data.forEach((respuestas, i) => {
        todasPostulaciones += `<div style="border:1px solid #555; padding:10px; margin-bottom:10px; border-radius:10px;">`;
        todasPostulaciones += `<b>Postulación #${i+1}</b><br>`;
        preguntas[rango].forEach((preg, idx) => {
          todasPostulaciones += `<i>${preg}</i> ${respuestas[idx] || "No respondido"}<br>`;
        });
        todasPostulaciones += `<button class="btn-eliminar" onclick="eliminarPostulacion('${rango}', ${i})">Eliminar</button>`;
        todasPostulaciones += "</div>";
      });
    }
  });

  if(!todasPostulaciones) {
    todasPostulaciones = "<p>No hay postulaciones guardadas.</p>";
  }

  document.getElementById("modalTitle").textContent = "Postulaciones enviadas";
  document.getElementById("questions").innerHTML = todasPostulaciones;
  document.getElementById("modal").style.display = "flex";
}

function eliminarPostulacion(rango, index){
  if(!confirm("¿Seguro que quieres eliminar esta postulación?")) return;
  let data = JSON.parse(localStorage.getItem("postulaciones_" + rango)) || [];
  data.splice(index, 1);
  localStorage.setItem("postulaciones_" + rango, JSON.stringify(data));
  mostrarPostulaciones(); // refresca la lista
}

// Inicialización
["soporte"].forEach(r=>{
  if(!localStorage.getItem(r)) localStorage.setItem(r,"abierto");
  actualizar(r);
});
</script>

</body>
</html>
