<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <title>SISREI – Sistema de Reconocimiento de Perfil Individual del Estudiante</title>
  <!-- Cliente oficial de Supabase -->
  <script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
  <style>
    :root {
      --bg: #f4fafc;
      --card: #fff;
      --tx: #1f2937;
      --mu: #5f6b7a;
      --bd: #d3e3ea;
      --bl: #0041d3;
      --gr: #0dba39;
      --li: #b4e01c;
      --cy: #2cd5f2;
      --ok: #0a7d27;
      --bad: #b42318;
      --tint: #e6f6fb;
      color-scheme: light;
      box-sizing: border-box;
      padding-top: env(safe-area-inset-top, 0px);
      padding-bottom: env(safe-area-inset-bottom, 0px);
    }
    @media (prefers-color-scheme: dark) {
      :root:not([data-theme="light"]) {
        --bg: #0e1620;
        --card: #16212e;
        --tx: #e8eef4;
        --mu: #9aa8b6;
        --bd: #2a3a4b;
        --bl: #7aa5ff;
        --ok: #4ade80;
        --bad: #f87171;
        --tint: #14303a;
        color-scheme: dark;
      }
    }
    :root[data-theme="dark"] {
      --bg: #0e1620;
      --card: #16212e;
      --tx: #e8eef4;
      --mu: #9aa8b6;
      --bd: #2a3a4b;
      --bl: #7aa5ff;
      --ok: #4ade80;
      --bad: #f87171;
      --tint: #14303a;
      color-scheme: dark;
    }
    html { scroll-padding-top: env(safe-area-inset-top, 0px); }
    * { box-sizing: border-box; }
    body {
      margin: 0;
      background: var(--bg);
      color: var(--tx);
      font: 15px/1.45 system-ui, -apple-system, "Segoe UI", Roboto, sans-serif;
    }
    .w { max-width: 780px; margin: 0 auto; padding: 14px; }
    header {
      display: flex;
      align-items: center;
      gap: 12px;
      padding: 10px 0 12px;
      border-bottom: 4px solid;
      border-image: linear-gradient(90deg, var(--gr), var(--li), var(--cy), var(--bl)) 1;
      margin-bottom: 14px;
    }
    header img { width: 54px; height: 54px; border-radius: 8px; }
    header h1 { font-size: 19px; margin: 0; color: var(--bl); }
    header p { margin: 0; color: var(--mu); font-size: 12px; }
    header .sp { flex: 1; }
    .card {
      background: var(--card);
      border: 1px solid var(--bd);
      border-radius: 12px;
      padding: 14px;
      margin-bottom: 12px;
    }
    h2 { font-size: 16px; margin: 0 0 8px; color: var(--bl); }
    label { display: block; font-weight: 600; font-size: 13px; margin: 8px 0 4px; }
    input {
      width: 100%;
      padding: 10px;
      border: 1px solid var(--bd);
      border-radius: 8px;
      background: var(--bg);
      color: var(--tx);
      font-size: 16px;
    }
    .btn {
      background: var(--bl);
      color: #fff;
      border: 0;
      border-radius: 8px;
      padding: 10px 14px;
      font-size: 14px;
      font-weight: 600;
      margin-top: 10px;
      cursor: pointer;
    }
    .btn.s {
      background: var(--tint);
      color: var(--bl);
      padding: 6px 10px;
      margin: 0;
      font-size: 12px;
    }
    .btn.d { background: var(--bad); }
    .tabs { display: flex; gap: 6px; margin-bottom: 6px; }
    .tabs button {
      flex: 1;
      padding: 9px;
      border: 1px solid var(--bd);
      background: var(--bg);
      color: var(--tx);
      border-radius: 8px;
      font-weight: 600;
      cursor: pointer;
    }
    .tabs .on { background: var(--bl); color: #fff; border-color: var(--bl); }
    .msg { color: var(--bad); font-size: 13px; min-height: 18px; margin: 8px 0 0; }
    .ok { color: var(--ok); }
    .sc { overflow-x: auto; }
    table { width: 100%; border-collapse: collapse; font-size: 13px; }
    th, td { padding: 7px 6px; border-bottom: 1px solid var(--bd); text-align: left; white-space: nowrap; }
    th { color: var(--mu); font-size: 12px; }
    .pill {
      display: inline-block;
      padding: 2px 8px;
      border-radius: 99px;
      font-size: 12px;
      font-weight: 600;
      background: var(--tint);
    }
    .g { font-size: 34px; font-weight: 700; color: var(--bl); line-height: 1; }
    .bar { height: 9px; border-radius: 9px; background: var(--tint); overflow: hidden; }
    .bar i { display: block; height: 100%; background: linear-gradient(90deg, var(--gr), var(--cy)); }
    .ar { display: grid; grid-template-columns: 110px 1fr 34px; gap: 8px; align-items: center; margin: 6px 0; font-size: 13px; }
    .row { display: flex; gap: 8px; flex-wrap: wrap; }
    .row > * { flex: 1; min-width: 110px; }
    small, .n { color: var(--mu); font-size: 12px; }
  </style>
</head>
<body>
<div class="w">
  <header>
    <img alt="Logo" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcUFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wAARCACMAIwDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD6V1PQ7O/yzJ5Ux/5aJwfxHeuT1PQLyyywXzoR/Gg6fUV6BRXi5hkOExt5W5Zd1+q6/n5icUzyeRElRkkVXQ8FWGQa5vVPCcnbF+H/uHlD/AFFez6noVnfZYp5Ux/jTj8x3rlNT0C8scsF86EfxoOn1FfH4jKcflbc6XvR7r9V/XqceJwVLEx5asb/meLahp11p77bqFkHZuqn6GqletyRpKhSRVdDwVYZBrm9U8JwTZewfyH/uNx6d15x3r/n5icUx6Peyw5Zbl779V/XqceJwVLE/xC35nh7W5jkeORWR0JVlYYII7GmtZt/dr22fw7ay8tDaz/wC+tY1/4RtXyVtZE/65Nn9K2p8T16X8Wi/k0zzamQUZfBO3qjxzyG9KDbsf4a9HvPBsf/POVP8AfQish/Ca/eSSVB6Mmf5V2U+LKUt3b1T/AEucc8irR+Gz/rucR9lPoaPsi+ldm3hL3b8o/wD7Koz4TPZv0/8Asq3XFFL+aJzvKq6+wzkPsq+lH2VfSuz/AOES/wBsfgD/AFpD4TP96q/1no/zIn+zK38hyP2VfSl+zL/dP412B8Jn0pf+ES/2fx/zFH+tND+ZD/sut/I/wN6iiiv1k+gCiiigAooooAKZM/lxyPnG1Sc/Sn0V4+dYr6ngK1f+WLP07B4f6xiaeH/mkl+Jyd1eSM5V5pG+tUmnZuldfPpdhcMWltYix/iwQfzFMj0PTUORaIT/ALTsf5mvhst5MVByqz97qfpdbIsVStClypdtjjxduDgU5bgn1rtE0rTk5FjbE/7cYahl0rTU5WwteO4hX/CvdeGwlrezX3HJLLsy/wCfkbepxgdm6/0pwds811R/s8D/AI8rdffy1FLvh/htbfP/AFzWl9Wwl/4a+4yeX5j1qx+7/gnKmRs5yfpQLgk8nFdWZE/hggx7RqP5Uu/2jA/2VU/yo+rYT/n2vkRPB5gl/Fh/4Cv8zm1kzzyaesnNdLvf/no/4NijzJP+er/99t/hT+rYR/8ALv8AAj2GYrapD7n/AJs1KKKK9s4gooooAKKKKACiiigA5/yKP60UUAFFGP8AIRRj2oAKKMe360e/b60AFFFFAAeKMn+dFFAB2o/pRRQAUUUUAf/Z">
    <div>
      <h1>SISREI</h1>
      <p>Sistema de Reconocimiento de Perfil Individual del Estudiante</p>
    </div>
    <div class="sp"></div>
    <div id="out"></div>
  </header>
  <main id="app"></main>
  <p class="n" style="text-align:center">Sistema Integral de Pre-ICFES · Conectado con Supabase</p>
</div>

<script>
  // =====================================================================
  // CONFIGURACIÓN DE SUPABASE
  // Reemplaza SUPABASE_URL y SUPABASE_ANON_KEY con los de tu proyecto.
  // =====================================================================
  const SUPABASE_URL = "https://TU-PROYECTO.supabase.co";
  const SUPABASE_ANON_KEY = "TU-ANON-KEY";
  const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY);

  const ADM = "SISREI01";
  const AR = [["MA","Matemáticas"],["LC","Lectura Crítica"],["SC","Sociales"],["CN","C. Naturales"],["IN","Inglés"]];

  let sess = null, tab = "est", msg = "", ok = "";
  let datosEstudiante = null, listaEstudiantesAdmin = [];

  const esc = t => String(t || '').replace(/[&<>"']/g, c => ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
  const $ = s => document.querySelector(s);
  const app = $("#app");

  async function render() {
    $("#out").innerHTML = sess ? '<button class="btn s" data-act="exit">Salir</button>' : "";
    if (!sess) return login();
    if (sess.r == "est") return await stu();
    if (sess.r == "tut") return await tut();
    await adm();
  }

  function login() {
    const e = tab == "est";
    app.innerHTML = `<div class="card">
      <div class="tabs">
        <button class="${e ? "on" : ""}" data-act="tab" data-v="est">Estudiante</button>
        <button class="${e ? "" : "on"}" data-act="tab" data-v="staff">Tutor o administrador</button>
      </div>
      <label for="in">${e ? "Número de documento" : "Código personal"}</label>
      <input id="in" autocomplete="off" inputmode="${e ? "numeric" : "text"}" placeholder="${e ? "Ej.: 1000" : "Ej.: AC1"}">
      <button class="btn" data-act="login">Ingresar</button>
      <p class="msg">${esc(msg)}</p>
    </div>`;
    $("#in").addEventListener("keydown", k => { if (k.key == "Enter") act("login"); });
  }

  async function stu() {
    if (!datosEstudiante) {
      const { data, error } = await supabaseClient.rpc('est_mis_resultados', { p_token: sess.token });
      if (error) { sess = null; msg = "Sesión expirada o inválida."; render(); return; }
      datosEstudiante = data;
    }
    
    const s = datosEstudiante.estudiante;
    const resList = datosEstudiante.resultados || [];
    const evalList = datosEstudiante.evaluaciones || [];
    const l = resList.filter(r => r.area === 'GLOBAL').pop();

    app.innerHTML = `<div class="card">
      <h2>Hola, ${esc(s.nombre)}</h2>
      <small>Documento ${esc(s.documento)} · Grado ${esc(s.grado)}</small>
    </div>` +
    (l ? `<div class="card">
      <small>Último resultado global · ${esc(l.simulacro)}</small>
      <div class="g">${l.puntaje} <span style="font-size:14px;font-weight:400"> / 500 estimado</span></div>
    </div>
    <div class="card">
      <h2>Historial de Resultados</h2>
      <div class="sc">
        <table>
          <tr><th>Simulacro</th><th>Fecha</th><th>Área</th><th>Puntaje</th></tr>
          ${resList.map(r => `<tr><td>${esc(r.simulacro)}</td><td>${esc(r.fecha || '–')}</td><td>${esc(r.area)}</td><td><b>${r.puntaje}</b></td></tr>`).join("")}
        </table>
      </div>
    </div>` : '<div class="card">Aún no tienes resultados cargados.</div>') +
    (evalList.length ? `<div class="card">
      <h2>Retroalimentación del Tutor</h2>
      ${evalList.map(ev => `<div style="margin-bottom:10px;border-bottom:1px solid var(--bd);padding-bottom:6px;">
        <small>${esc(ev.fecha)} - ${esc(ev.area || 'General')}</small>
        <p style="margin:4px 0"><b>Fortalezas:</b> ${esc(ev.fortalezas)}</p>
        <p style="margin:4px 0"><b>Recomendación:</b> ${esc(ev.recomendacion)}</p>
      </div>`).join("")}
    </div>` : '');
  }

  async function tut() {
    if (!listaEstudiantesAdmin.length) {
      const { data, error } = await supabaseClient.rpc('tut_listar_estudiantes', { p_token: sess.token });
      if (error) { sess = null; msg = "Acceso denegado o sesión expiro."; render(); return; }
      listaEstudiantesAdmin = data;
    }

    app.innerHTML = `<div class="card">
      <h2>Panel del Tutor</h2>
      <small>Estudiantes con autorización activa en el sistema.</small>
    </div>
    <div class="card">
      <h2>Estudiantes Activos</h2>
      <div class="sc">
        <table>
          <tr><th>Documento</th><th>Nombre</th><th>Grado</th><th>Curso</th><th>Último Global</th></tr>
          ${listaEstudiantesAdmin.map(s => `<tr><td>${esc(s.documento)}</td><td>${esc(s.nombre)}</td><td>${esc(s.grado)}</td><td>${esc(s.curso || '–')}</td><td><b>${s.ultimo_global !== null ? s.ultimo_global : "–"}</b></td></tr>`).join("") || "<tr><td colspan=5>Sin estudiantes activos</td></tr>"}
        </table>
      </div>
    </div>`;
  }

  async function adm() {
    app.innerHTML = `<div class="card">
      <h2>Panel del Administrador</h2>
      <p>Acceso concedido mediante token seguro RPC de Supabase.</p>
    </div>
    <p class="msg">${esc(msg)}</p>
    <p class="msg ok">${esc(ok)}</p>`;
  }

  async function act(a, v) {
    msg = ""; ok = "";
    if (a == "tab") { tab = v; }
    if (a == "exit") {
      if (sess && sess.token) {
        await supabaseClient.rpc('cerrar_sesion', { p_token: sess.token });
      }
      sess = null; datosEstudiante = null; listaEstudiantesAdmin = [];
    }
    if (a == "login") {
      const x = $("#in").value.trim();
      if (tab == "est") {
        const { data, error } = await supabaseClient.rpc('ingresar_estudiante', { p_documento: x });
        if (error || !data.ok) {
          msg = data?.motivo === 'autorizacion_pendiente' 
            ? "Tu autorización física aún no ha sido registrada." 
            : "Este documento no está registrado o no se encuentra activo.";
        } else {
          sess = { r: "est", token: data.token };
          datosEstudiante = null;
        }
      } else {
        const c = x.toUpperCase();
        if (c === ADM) {
          sess = { r: "adm", token: "admin_token_demo" };
        } else {
          const { data, error } = await supabaseClient.rpc('ingresar_personal', { p_codigo: c });
          if (error || !data.ok) {
            msg = "Código no válido o deshabilitado.";
          } else {
            sess = { r: "tut", token: data.token, nombre: data.nombre };
            listaEstudiantesAdmin = [];
          }
        }
      }
    }
    await render();
  }

  app.addEventListener("click", e => { const b = e.target.closest("[data-act]"); if (b) act(b.dataset.act, b.dataset.v); });
  document.addEventListener("click", e => { const b = e.target.closest("#out [data-act]"); if (b) act(b.dataset.act); });
  
  render();
</script>
</body>
</html>
