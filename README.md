# Skin-Price
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SkinPrice - Comparador de Skins</title>
<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial}
body{background:#0f1115;color:white;padding:40px}
h1{text-align:center;margin-bottom:30px;color:#00ffcc}
.container{max-width:1000px;margin:auto}
.search-box{display:flex;gap:10px;margin-bottom:30px}
.search-box input{flex:1;padding:12px;border-radius:8px;border:none;background:#1a1d24;color:white}
.search-box button{padding:12px 20px;border:none;border-radius:8px;background:#00ffcc;color:black;font-weight:bold;cursor:pointer}
.table-container{background:#1a1d24;border-radius:12px;overflow:hidden}
table{width:100%;border-collapse:collapse}
th,td{padding:15px;text-align:center}
th{background:#14161c;color:#00ffcc}
tr:nth-child(even){background:#171a21}
.price-up{color:#00ff88;font-weight:bold}
.price-down{color:#ff4d4d;font-weight:bold}
.badge{padding:6px 10px;border-radius:20px;font-size:12px;font-weight:bold}
.opportunity{background:#00ff88;color:black}
.no-opportunity{background:#ff4d4d}
</style>
</head>
<body>

<h1>🔥 SkinPrice - Comparador de Skins</h1>

<div class="container">
  <div class="search-box">
    <input type="text" id="skinInput" placeholder="Ej: AK-47 | Redline (Field-Tested)">
    <button onclick="buscarSkin()">Buscar</button>
  </div>

  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Marketplace</th>
          <th>Precio</th>
          <th>Diferencia</th>
          <th>Estado</th>
        </tr>
      </thead>
      <tbody id="tablaResultados">
      </tbody>
    </table>
  </div>
</div>

<script>
async function buscarSkin() {
  const nombre = document.getElementById("skinInput").value;
  if(!nombre) return alert("Escribe el nombre exacto");

  // Cambia esto por tu URL de Replit cuando tengamos backend
  const backendURL = "https://tu-backend-replit.repl.co";

  try {
    const res = await fetch(`${backendURL}/api/steam?name=${encodeURIComponent(nombre)}`);
    const data = await res.json();

    if(!data.success) {
      alert("Skin no encontrada");
      return;
    }

    const precioSteam = parseFloat(data.lowest_price.replace("€","").replace(",","."));

    // Simulación de otros mercados
    const precioSkinport = (precioSteam * 0.92).toFixed(2);
    const precioBuff = (precioSteam * 0.90).toFixed(2);

    mostrarResultados(precioSteam, precioSkinport, precioBuff);

  } catch(err) {
    alert("Error obteniendo datos");
  }
}

function mostrarResultados(steam, skinport, buff) {
  const tabla = document.getElementById("tablaResultados");
  tabla.innerHTML = "";

  const mercados = [
    {nombre:"Steam Market", precio:steam},
    {nombre:"Skinport", precio:skinport},
    {nombre:"Buff", precio:buff}
  ];

  mercados.forEach(m => {
    const diferencia = ((steam - m.precio) / steam * 100).toFixed(2);
    const rentable = m.precio < steam;

    tabla.innerHTML += `
      <tr>
        <td>${m.nombre}</td>
        <td>€${m.precio}</td>
        <td class="${rentable ? 'price-up':'price-down'}">
          ${rentable ? '+' : ''}${Math.abs(diferencia)}%
        </td>
        <td>
          <span class="badge ${rentable ? 'opportunity':'no-opportunity'}">
            ${rentable ? 'Oportunidad':'No rentable'}
          </span>
        </td>
      </tr>
    `;
  });
}
</script>

</body>
</html>
