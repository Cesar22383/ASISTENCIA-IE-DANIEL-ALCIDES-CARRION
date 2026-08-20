<!DOCTYPE html>
<html lang="es">

<head>

<meta charset="UTF-8">

<meta name="viewport"
content="width=device-width, initial-scale=1.0">

<title>I.E. Daniel Alcides Carrión</title>

<style>

/* =====================================================
   CONFIGURACIÓN GENERAL
   ===================================================== */

* {
box-sizing: border-box;
}

html,
body {

margin: 0;
padding: 0;

width: 100%;
height: 100%;

overflow: hidden;

font-family: Arial, sans-serif;

background: #4a86e8;

}


/* =====================================================
   PANTALLA PRINCIPAL
   ===================================================== */

.pantalla {

width: 100%;
height: 100vh;

display: flex;
flex-direction: column;

align-items: center;
justify-content: center;

padding: 20px;

text-align: center;

}


/* =====================================================
   TÍTULO
   ===================================================== */

.titulo {

color: black;

font-size: 30px;

font-weight: bold;

margin-bottom: 30px;

}


/* =====================================================
   TEXTO DNI
   ===================================================== */

.dni-label {

color: white;

font-size: 30px;

font-weight: bold;

margin-bottom: 12px;

}


/* =====================================================
   CAJA DNI
   ===================================================== */

#dni {

width: 300px;

max-width: 85vw;

height: 55px;

border: 4px solid white;

border-radius: 10px;

background: white;

color: #222;

font-size: 28px;

font-weight: bold;

text-align: center;

outline: none;

}


/* EFECTO AL SELECCIONAR LA CAJA */

#dni:focus {

border-color: #ffd966;

box-shadow:
0 0 15px #ffd966;

}


/* =====================================================
   BOTÓN CONTINUAR
   ===================================================== */

#btnContinuar {

width: 300px;

max-width: 85vw;

height: 55px;

margin-top: 18px;

border: none;

border-radius: 10px;

background: #174bb5;

color: white;

font-size: 24px;

font-weight: bold;

cursor: pointer;

box-shadow:
0 4px 8px rgba(0,0,0,0.25);

}


/* EFECTO DEL BOTÓN */

#btnContinuar:hover {

background: #123d96;

}


/* CUANDO SE PRESIONA */

#btnContinuar:active {

transform: scale(0.97);

}


/* =====================================================
   MENSAJE CARGANDO
   ===================================================== */

.cargando {

color: white;

font-size: 20px;

font-weight: bold;

margin-top: 18px;

}


/* =====================================================
   RESULTADO
   ===================================================== */

#resultado {

width: 90%;

max-width: 850px;

margin-top: 35px;

color: white;

display: none;

}


/* =====================================================
   NOMBRE
   ===================================================== */

.nombre {

font-size: 32px;

font-weight: bold;

margin-bottom: 20px;

}


/* =====================================================
   GRADO Y SECCIÓN
   ===================================================== */

.datos {

display: flex;

justify-content: space-between;

font-size: 25px;

font-weight: bold;

margin: 10px 0;

}


/* =====================================================
   MENSAJE
   ===================================================== */

.mensaje {

width: 100%;

margin-top: 25px;

padding: 18px;

border-radius: 5px;

color: white;

font-size: 30px;

font-weight: bold;

}


/* ASISTENCIA REGISTRADA */

.encontrado {

background: #00a651;

}


/* ERROR */

.error {

background: #555;

}


/* =====================================================
   CELULARES
   ===================================================== */

@media (max-width: 700px) {

.titulo {

font-size: 24px;

margin-bottom: 25px;

}

.dni-label {

font-size: 25px;

}

#dni {

width: 280px;

height: 52px;

font-size: 25px;

}

#btnContinuar {

width: 280px;

height: 52px;

font-size: 22px;

}

.nombre {

font-size: 25px;

}

.datos {

font-size: 20px;

}

.mensaje {

font-size: 22px;

}

}

</style>

</head>


<body>


<div class="pantalla">


<!-- =================================================
     INICIO
     ================================================= -->

<div id="inicio">


<div class="titulo">

I.E. DANIEL ALCIDES CARRIÓN

</div>


<div class="dni-label">

DNI

</div>


<input
id="dni"
type="tel"
inputmode="numeric"
maxlength="8"
autocomplete="off"
autofocus
placeholder="Ingrese DNI"
>


<!-- =================================================
     BOTÓN CONTINUAR
     ================================================= -->

<button
id="btnContinuar"
type="button">

CONTINUAR

</button>


<div
id="cargando"
class="cargando">
</div>


</div>


<!-- =================================================
     RESULTADO
     ================================================= -->

<div id="resultado">


<div
id="nombre"
class="nombre">
</div>


<div class="datos">

<span id="grado"></span>

<span id="seccion"></span>

</div>


<div
id="mensaje"
class="mensaje">
</div>


</div>


</div>


<script>

/* =====================================================
   CONFIGURACIÓN SUPABASE
   ===================================================== */

const SUPABASE_URL =
"https://glgmttpaqheuvqplbjrl.supabase.co";


const SUPABASE_KEY =
"sb_publishable_MX3PWni7cZ3hF7JqtFXb9A_Ee0iwIIb";


/* =====================================================
   ELEMENTOS
   ===================================================== */

const dniInput =
document.getElementById("dni");


const btnContinuar =
document.getElementById("btnContinuar");


const cargando =
document.getElementById("cargando");


const inicio =
document.getElementById("inicio");


const resultado =
document.getElementById("resultado");


const nombre =
document.getElementById("nombre");


const grado =
document.getElementById("grado");


const seccion =
document.getElementById("seccion");


const mensaje =
document.getElementById("mensaje");


/* =====================================================
   BOTÓN CONTINUAR
   ===================================================== */

btnContinuar.addEventListener(
"click",
function() {

continuar();

});


/* =====================================================
   DETECTAR ENTER
   ===================================================== */

dniInput.addEventListener(
"keydown",
function(event) {

if (event.key === "Enter") {

event.preventDefault();

continuar();

}

});


/* =====================================================
   FUNCIÓN CONTINUAR
   ===================================================== */

function continuar() {

const dni =
dniInput.value.trim();


/* VERIFICAR QUE SEAN 8 DÍGITOS */

if (!/^\d{8}$/.test(dni)) {

cargando.innerText =
"INGRESE UN DNI VÁLIDO DE 8 DÍGITOS";

dniInput.focus();

return;

}


/* BUSCAR ESTUDIANTE */

buscarEstudiante(dni);

}


/* =====================================================
   BUSCAR ESTUDIANTE
   ===================================================== */

async function buscarEstudiante(dni) {

cargando.innerText =
"CONSULTANDO...";


btnContinuar.disabled = true;


try {


const respuesta =
await fetch(

SUPABASE_URL +
"/rest/v1/estudiantes" +
"?select=dni,nombre,grado,seccion" +
"&dni=eq." +
encodeURIComponent(dni),

{

method: "GET",

headers: {

"apikey":
SUPABASE_KEY,

"Authorization":
"Bearer " +
SUPABASE_KEY

}

}

);


/* VERIFICAR RESPUESTA */

if (!respuesta.ok) {

throw new Error(
"Error HTTP " +
respuesta.status
);

}


const datos =
await respuesta.json();


cargando.innerText =
"";


/* DNI NO EXISTE */

if (
!datos ||
datos.length === 0
) {

mostrarNoEncontrado();

return;

}


/*
   EL ESTUDIANTE EXISTE.
   REGISTRAMOS LA ASISTENCIA.
*/

await registrarAsistencia(
datos[0]
);


}

catch (error) {

console.error(error);

cargando.innerText =
"";

mostrarError();

}

finally {

btnContinuar.disabled =
false;

}

}


/* =====================================================
   REGISTRAR ASISTENCIA
   ===================================================== */

async function registrarAsistencia(
estudiante
) {

try {


const ahora =
new Date();


/* =================================================
   FECHA DE PERÚ
   ================================================= */

const fechaPeru =
new Intl.DateTimeFormat(
"en-CA",
{

timeZone:
"America/Lima",

year:
"numeric",

month:
"2-digit",

day:
"2-digit"

}
).format(ahora);


/* =================================================
   HORA DE PERÚ
   ================================================= */

const horaPeru =
new Intl.DateTimeFormat(
"en-GB",
{

timeZone:
"America/Lima",

hour:
"2-digit",

minute:
"2-digit",

second:
"2-digit",

hour12:
false

}
).format(ahora);


/* =================================================
   DÍA DE LA SEMANA
   ================================================= */

const diaPeru =
new Intl.DateTimeFormat(
"es-PE",
{

timeZone:
"America/Lima",

weekday:
"long"

}
).format(ahora);


/* =================================================
   DATOS PARA ASISTENCIA
   ================================================= */

const registro = {

dni:
estudiante.dni,

fecha:
fechaPeru,

dia:
diaPeru,

hora:
horaPeru,

estado:
"PRESENTE"

};


/* =================================================
   INSERTAR EN SUPABASE
   ================================================= */

const respuesta =
await fetch(

SUPABASE_URL +
"/rest/v1/asistencia",

{

method:
"POST",

headers: {

"apikey":
SUPABASE_KEY,

"Authorization":
"Bearer " +
SUPABASE_KEY,

"Content-Type":
"application/json",

"Prefer":
"return=minimal"

},

body:
JSON.stringify(registro)

}

);


/* VERIFICAR REGISTRO */

if (!respuesta.ok) {

const errorTexto =
await respuesta.text();

console.error(
"Error al registrar:",
errorTexto
);

throw new Error(
"NO SE PUDO REGISTRAR"
);

}


/* MOSTRAR RESULTADO */

mostrarEstudiante(

estudiante,

fechaPeru,

horaPeru

);


}

catch (error) {

console.error(error);

throw error;

}

}


/* =====================================================
   MOSTRAR ESTUDIANTE
   ===================================================== */

function mostrarEstudiante(
estudiante,
fecha,
hora
) {


inicio.style.display =
"none";


resultado.style.display =
"block";


nombre.innerText =
estudiante.nombre || "";


grado.innerText =
estudiante.grado
? "Grado: " +
estudiante.grado
: "";


seccion.innerText =
estudiante.seccion
? "Sección: " +
estudiante.seccion
: "";


mensaje.className =
"mensaje encontrado";


mensaje.innerText =
"ASISTENCIA REGISTRADA";


/* REGRESAR DESPUÉS DE 2 SEGUNDOS */

setTimeout(
regresarInicio,
2000
);

}


/* =====================================================
   DNI NO ENCONTRADO
   ===================================================== */

function mostrarNoEncontrado() {


inicio.style.display =
"none";


resultado.style.display =
"block";


nombre.innerText =
"DNI NO ENCONTRADO";


grado.innerText =
"";


seccion.innerText =
"";


mensaje.className =
"mensaje error";


mensaje.innerText =
"VERIFIQUE EL DNI";


setTimeout(
regresarInicio,
2000
);

}


/* =====================================================
   ERROR
   ===================================================== */

function mostrarError() {


inicio.style.display =
"none";


resultado.style.display =
"block";


nombre.innerText =
"ERROR";


grado.innerText =
"";


seccion.innerText =
"";


mensaje.className =
"mensaje error";


mensaje.innerText =
"NO SE PUDO REGISTRAR LA ASISTENCIA";


setTimeout(
regresarInicio,
3000
);

}


/* =====================================================
   VOLVER AL INICIO
   ===================================================== */

function regresarInicio() {


resultado.style.display =
"none";


inicio.style.display =
"block";


dniInput.value =
"";


cargando.innerText =
"";


dniInput.focus();

}


/* =====================================================
   ENFOCAR DNI AL CARGAR
   ===================================================== */

window.addEventListener(
"load",
function() {

dniInput.focus();

});

</script>


</body>

</html>
