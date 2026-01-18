<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NEXUS INTERFACE</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --neon-blue: #00f3ff;
            --neon-gold: #d4af37;
            --glass-bg: rgba(10, 15, 30, 0.85);
            --glass-border: rgba(0, 243, 255, 0.3);
            --danger: #ff2a2a;
        }

        body {
            background-color: #050505;
            background-image: 
                linear-gradient(rgba(0, 243, 255, 0.03) 1px, transparent 1px),
                linear-gradient(90deg, rgba(0, 243, 255, 0.03) 1px, transparent 1px);
            background-size: 30px 30px;
            color: var(--neon-blue);
            font-family: 'Rajdhani', sans-serif;
            margin: 0;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            height: 100vh;
            overflow: hidden;
            box-sizing: border-box;
        }

        /* TÍTULO Y HEADER */
        .header {
            text-align: center;
            margin-bottom: 20px;
            text-shadow: 0 0 10px var(--neon-blue);
            animation: flicker 2s infinite;
        }
        h1 { margin: 0; font-weight: 700; letter-spacing: 3px; font-size: 24px; }
        .subtitle { font-size: 12px; color: var(--neon-gold); letter-spacing: 1px; text-transform: uppercase; }

        /* CONTENEDOR DE VISUALIZACIÓN */
        .scanner-frame {
            width: 100%;
            max-width: 400px;
            height: 300px;
            border: 2px solid var(--neon-blue);
            background: rgba(0, 0, 0, 0.5);
            position: relative;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 243, 255, 0.2);
            margin-bottom: 20px;
        }

        /* RETÍCULA Y EFECTOS */
        .scanner-frame::before {
            content: '';
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: linear-gradient(to bottom, transparent 50%, rgba(0, 243, 255, 0.1) 51%, transparent 51%);
            background-size: 100% 4px;
            pointer-events: none;
            z-index: 2;
        }

        .scan-line {
            position: absolute;
            top: 0; left: 0; width: 100%; height: 2px;
            background: var(--neon-gold);
            box-shadow: 0 0 10px var(--neon-gold);
            animation: scan 3s linear infinite;
            z-index: 3;
            display: none;
        }

        #preview {
            max-width: 100%;
            max-height: 100%;
            display: none;
            z-index: 1;
        }

        .placeholder-text {
            color: rgba(0, 243, 255, 0.5);
            text-transform: uppercase;
            letter-spacing: 2px;
            text-align: center;
        }

        /* CONTROLES */
        .controls {
            width: 100%;
            max-width: 400px;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        /* INPUTS ESTILO GLASSMORPHISM */
        input[type="text"] {
            background: rgba(255, 255, 255, 0.05);
            border: 1px solid var(--neon-gold);
            color: #fff;
            padding: 15px;
            border-radius: 5px;
            font-family: 'Rajdhani', sans-serif;
            font-size: 16px;
            outline: none;
            transition: 0.3s;
        }
        input[type="text"]:focus {
            box-shadow: 0 0 15px rgba(212, 175, 55, 0.3);
            background: rgba(255, 255, 255, 0.1);
        }

        /* BOTONES */
        .btn {
            background: transparent;
            border: 1px solid var(--neon-blue);
            color: var(--neon-blue);
            padding: 15px;
            font-family: 'Rajdhani', sans-serif;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            transition: 0.3s;
            position: relative;
            overflow: hidden;
            text-align: center;
        }

        .btn:hover {
            background: var(--neon-blue);
            color: #000;
            box-shadow: 0 0 20px var(--neon-blue);
        }

        .btn-gold {
            border-color: var(--neon-gold);
            color: var(--neon-gold);
        }
        .btn-gold:hover {
            background: var(--neon-gold);
            color: #000;
            box-shadow: 0 0 20px var(--neon-gold);
        }

        /* ANIMACIONES */
        @keyframes scan { 0% { top: 0; } 50% { top: 100%; } 100% { top: 0; } }
        @keyframes flicker { 0%, 19%, 21%, 23%, 25%, 54%, 56%, 100% { opacity: 1; } 20%, 24%, 55% { opacity: 0.5; } }

        .hidden { display: none !important; }
    </style>
</head>
<body>

    <div class="header">
        <h1>NEXUS V.44</h1>
        <div class="subtitle">Sistema de Realidad Aumentada</div>
    </div>

    <div class="scanner-frame" onclick="document.getElementById('cameraInput').click()">
        <div class="scan-line" id="scanLine"></div>
        <div class="placeholder-text" id="placeholder">
            [ PULSA PARA ACTIVAR SENSOR ]<br>
            <span style="font-size: 10px; opacity: 0.7">SOPORTE IMAGEN ÓPTICO</span>
        </div>
        <img id="preview" alt="Preview">
    </div>

    <input type="file" id="cameraInput" accept="image/*" capture="environment" style="display:none">

    <div class="controls">
        <input type="text" id="promptInput" placeholder="EJ: CONVERTIR EN BASE LUNAR..." autocomplete="off">
        
        <div id="actionButtons" class="hidden">
            <div class="btn btn-gold" onclick="processData()">⚡ EJECUTAR NEXUS ⚡</div>
            <div class="btn" onclick="reset()" style="margin-top: 10px; border-color: #555; color: #777; font-size: 12px;">CANCELAR OPERACIÓN</div>
        </div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand(); // Pantalla completa en Telegram

        const input = document.getElementById('cameraInput');
        const preview = document.getElementById('preview');
        const placeholder = document.getElementById('placeholder');
        const scanLine = document.getElementById('scanLine');
        const promptInput = document.getElementById('promptInput');
        const actionButtons = document.getElementById('actionButtons');

        let compressedImageBase64 = "";

        // 1. MANEJO DE LA CÁMARA E IMAGEN
        input.addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;

            // Mostrar estado de carga
            placeholder.innerHTML = "PROCESANDO MATERIA...";
            
            const reader = new FileReader();
            reader.onload = function(event) {
                const img = new Image();
                img.onload = function() {
                    // COMPRESIÓN AGRESIVA (Necesaria para Telegram WebApp Data Limit 4096 bytes)
                    // Nota: Enviamos una versión "Miniatura" para referencia, 
                    // o intentamos ajustar la calidad al máximo.
                    const canvas = document.createElement('canvas');
                    let width = img.width;
                    let height = img.height;
                    
                    // Redimensionar a algo muy pequeño para que pase por el cable (Telegram Limitation)
                    // Si necesitas HD, la estrategia cambia a subir a servidor externo.
                    // Aquí ajustamos para demostración rápida.
                    const MAX_SIZE = 400; 
                    if (width > height) {
                        if (width > MAX_SIZE) { height *= MAX_SIZE / width; width = MAX_SIZE; }
                    } else {
                        if (height > MAX_SIZE) { width *= MAX_SIZE / height; height = MAX_SIZE; }
                    }

                    canvas.width = width;
                    canvas.height = height;
                    const ctx = canvas.getContext('2d');
                    ctx.drawImage(img, 0, 0, width, height);

                    // Convertir a Base64 con baja calidad para asegurar transmisión
                    compressedImageBase64 = canvas.toDataURL('image/jpeg', 0.6);

                    // Mostrar preview
                    preview.src = compressedImageBase64;
                    preview.style.display = "block";
                    placeholder.style.display = "none";
                    scanLine.style.display = "block"; // Activar animación
                    actionButtons.classList.remove('hidden');
                }
                img.src = event.target.result;
            }
            reader.readAsDataURL(file);
        });

        // 2. ENVIAR DATOS A AXLEPH
        function processData() {
            const prompt = promptInput.value;
            if(!prompt) { 
                tg.showPopup({title: "ERROR DE INPUT", message: "Debes introducir un comando para Nexus."});
                return; 
            }

            if(!compressedImageBase64) {
                tg.showPopup({title: "ERROR VISUAL", message: "No se ha detectado imagen."});
                return;
            }

            // Crear el paquete de datos JSON
            const data = {
                action: "nexus_render",
                image: compressedImageBase64,
                request: prompt
            };

            // Convertir a string
            const jsonString = JSON.stringify(data);

            // Verificación de seguridad de tamaño (Telegram Límite)
            // Si es muy grande, avisamos al usuario (aunque el resize arriba debería evitarlo)
            if (jsonString.length > 4096) {
               // Intento de emergencia: comprimir más
               tg.showPopup({title: "ADVERTENCIA", message: "La imagen es compleja. Se reducirá la calidad para la transmisión cuántica."});
            }

            // ENVIAR AL BOT
            tg.sendData(jsonString);
        }

        function reset() {
            input.value = "";
            preview.src = "";
            preview.style.display = "none";
            placeholder.style.display = "block";
            placeholder.innerHTML = "[ PULSA PARA ACTIVAR SENSOR ]<br><span style='font-size: 10px; opacity: 0.7'>SOPORTE IMAGEN ÓPTICO</span>";
            scanLine.style.display = "none";
            actionButtons.classList.add('hidden');
            compressedImageBase64 = "";
            promptInput.value = "";
        }
    </script>
</body>
</html>
