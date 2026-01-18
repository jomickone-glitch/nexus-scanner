<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NEXUS SCANNER</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { --blue: #00f3ff; --gold: #d4af37; --bg: #050505; }
        body { background: var(--bg); color: var(--blue); font-family: 'Rajdhani', sans-serif; display: flex; flex-direction: column; align-items: center; padding: 20px; height: 100vh; margin: 0; box-sizing: border-box; }
        .header { text-align: center; margin-bottom: 20px; border-bottom: 1px solid var(--blue); width: 100%; padding-bottom: 10px; }
        .frame { width: 100%; max-width: 400px; height: 250px; border: 1px solid var(--blue); position: relative; display: flex; justify-content: center; align-items: center; background: rgba(0,243,255,0.05); border-radius: 8px; overflow: hidden; }
        #preview { width: 100%; height: 100%; object-fit: cover; display: none; }
        .controls { width: 100%; max-width: 400px; margin-top: 20px; display: flex; flex-direction: column; gap: 15px; }
        input { background: rgba(255,255,255,0.05); border: 1px solid var(--gold); color: white; padding: 12px; border-radius: 4px; font-family: inherit; }
        .btn { background: none; border: 1px solid var(--blue); color: var(--blue); padding: 15px; font-weight: bold; cursor: pointer; text-transform: uppercase; letter-spacing: 2px; }
        .btn:active { background: var(--blue); color: black; }
        .loading { font-size: 12px; color: var(--gold); margin-top: 5px; display: none; }
    </style>
</head>
<body>
    <div class="header"><h1>NEXUS V44</h1></div>
    <div class="frame" onclick="document.getElementById('cam').click()">
        <span id="plh">[ ACTIVAR ESCÁNER ÓPTICO ]</span>
        <img id="preview">
    </div>
    <input type="file" id="cam" accept="image/*" capture="environment" style="display:none">
    <div class="controls">
        <input type="text" id="req" placeholder="Comando de diseño...">
        <button class="btn" onclick="send()">Ejecutar Protocolo</button>
        <div id="status" class="loading">ENVIANDO DATOS A AXLEPH...</div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        let b64 = "";

        document.getElementById('cam').onchange = function(e) {
            const reader = new FileReader();
            reader.onload = function(ev) {
                const img = new Image();
                img.onload = function() {
                    const canvas = document.createElement('canvas');
                    // Reducción agresiva para no superar el límite de 4KB de Telegram
                    const scale = Math.min(200 / img.width, 200 / img.height);
                    canvas.width = img.width * scale;
                    canvas.height = img.height * scale;
                    canvas.getContext('2d').drawImage(img, 0, 0, canvas.width, canvas.height);
                    b64 = canvas.toDataURL('image/jpeg', 0.5);
                    document.getElementById('preview').src = b64;
                    document.getElementById('preview').style.display = 'block';
                    document.getElementById('plh').style.display = 'none';
                };
                img.src = ev.target.result;
            };
            reader.readAsDataURL(e.target.files[0]);
        };

        function send() {
            const prompt = document.getElementById('req').value;
            if (!b64 || !prompt) return alert("Falta imagen o comando");
            document.getElementById('status').style.display = 'block';
            
            const payload = JSON.stringify({ action: "nexus_render", image: b64, request: prompt });
            
            // Si el payload es mayor a 4096 bytes, Telegram lo bloqueará
            if (payload.length > 4096) {
                alert("Imagen demasiado pesada. Intenta con otra o reduce el zoom.");
                document.getElementById('status').style.display = 'none';
                return;
            }
            tg.sendData(payload);
        }
    </script>
</body>
</html>
