<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Nuestra Conexión - Effy & Jonatan</title>
    <style>
        body { background-color: #94E0D6; color: #004d00; font-family: sans-serif; margin: 0; display: flex; flex-direction: column; align-items: center; min-height: 100vh; text-align: center; }
        .sidebar { position: fixed; left: 20px; top: 50%; transform: translateY(-50%); display: flex; flex-direction: column; gap: 15px; }
        .btn-side { border: none; padding: 15px 25px; border-radius: 50px; color: white; font-weight: bold; cursor: pointer; box-shadow: 0 4px 6px rgba(0,0,0,0.1); transition: 0.3s; }
        .te-extrano { background-color: #EB8FA4; }
        .motivate { background-color: #833EAD; }
        .btn-side:hover { transform: scale(1.1); }
        .main-content { max-width: 700px; padding: 40px 20px; }
        .caja-vida-container { background: rgba(255, 255, 255, 0.4); padding: 25px; border-radius: 20px; border: 2px solid #004d00; }
        textarea { width: 100%; height: 100px; border-radius: 10px; padding: 10px; border: 1px solid #004d00; resize: none; }
        .btn-interact { background-color: #004d00; color: white; padding: 12px 20px; border: none; border-radius: 8px; cursor: pointer; margin: 10px; font-weight: bold; }
        .historial-box { margin-top: 30px; background: white; color: #333; padding: 15px; border-radius: 10px; text-align: left; max-height: 400px; overflow-y: auto; width: 100%; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .log-entry { border-bottom: 1px solid #eee; padding: 8px 0; font-size: 0.9em; }
        .corazon-anim { font-size: 60px; position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%); animation: pulse 1s forwards; pointer-events: none; }
        @keyframes pulse { 0% { opacity: 1; transform: translate(-50%, -50%) scale(1); } 100% { opacity: 0; transform: translate(-50%, -50%) scale(5); } }
        .footer { font-style: italic; padding: 40px; color: #004d00; }
    </style>
</head>
<body>

    <div class="sidebar">
        <button class="btn-side te-extrano" onclick="registrarEvento('💖 Te extraño', 'Ella presionó que te extraña')">Te extraño</button>
        <button class="btn-side motivate" onclick="registrarEvento('🚀 Motivación', 'Vamoooos amor tu puedes')">Motívate</button>
    </div>

    <div class="main-content">
        <h1>Hola mi amor espero te guste esta pagina tanto como me gustas tu</h1>
        
        <div class="caja-vida-container">
            <p>Escribe algo para nuestra caja de vida:</p>
            <textarea id="inputMensaje" placeholder="Tu mensaje se guardará en nuestra historia..."></textarea>
            <br>
            <button class="btn-interact" onclick="enviarMensaje()">Caja de vida <3</button>
            
            <div style="margin-top: 20px;">
                <button class="btn-interact" onclick="registrarEvento('📹 Video', 'Interés en ver un video')">¿Quieres obtener un video?</button>
                <button class="btn-interact" onclick="registrarEvento('💬 Hablar', 'Desea hablar contigo')">¿Quieres hablar?</button>
            </div>
        </div>

        <h3>Huellas de nuestra conexión (En vivo)</h3>
        <div id="historial" class="historial-box">Cargando registros...</div>
    </div>

    <div class="footer">
        "Yo sé que esto llevará tiempo pero el hecho de que estemos por el momento separados no significa que estaremos desanclados, aquí estaré toda mi vida para ti."
    </div>

    <div id="efecto"></div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getDatabase, ref, push, onValue, serverTimestamp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

        // NOTA: Para que esto funcione, debes crear un proyecto gratis en firebase.google.com
        // y pegar tus credenciales aquí. Si no lo haces, funcionará solo localmente.
        const firebaseConfig = {
            databaseURL: "TU_URL_DE_FIREBASE_AQUI" 
        };

        const app = initializeApp(firebaseConfig);
        const db = getDatabase(app);
        const logsRef = ref(db, 'historial');

        // Función para enviar a la nube
        window.registrarEvento = function(tipo, contenido) {
            if(tipo.includes('extraño')) crearCorazon();
            if(tipo.includes('Motivación')) alert("Vamoooos amor tu puedes");

            push(logsRef, {
                tipo: tipo,
                contenido: contenido,
                fecha: new Date().toLocaleString(),
                timestamp: serverTimestamp()
            });
        };

        window.enviarMensaje = function() {
            const txt = document.getElementById('inputMensaje').value;
            if(!txt) return;
            registrarEvento('💬 Mensaje', txt);
            document.getElementById('inputMensaje').value = "";
            alert("Mensaje enviado a la nube ❤️");
        };

        // Escuchar cambios en tiempo real (Sincroniza todos los dispositivos)
        onValue(logsRef, (snapshot) => {
            const data = snapshot.val();
            const box = document.getElementById('historial');
            box.innerHTML = "";
            
            if (data) {
                // Convertir objeto a array y ordenar por tiempo
                Object.values(data).reverse().forEach(item => {
                    const entry = document.createElement('div');
                    entry.className = 'log-entry';
                    entry.innerHTML = `<small>${item.fecha}</small><br><strong>${item.tipo}:</strong> ${item.contenido}`;
                    box.appendChild(entry);
                });
            } else {
                box.innerHTML = "Aún no hay huellas guardadas.";
            }
        });

        function crearCorazon() {
            const div = document.getElementById('efecto');
            div.innerHTML = '<div class="corazon-anim">❤️</div>';
            setTimeout(() => div.innerHTML = "", 1000);
        }
    </script>
</body>
</html>
