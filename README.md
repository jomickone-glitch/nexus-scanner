<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>NEXUS SCANNER V44</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        :root { --blue: #00f3ff; --gold: #d4af37; --bg: #050505; }
        body { background: var(--bg); color: var(--blue); font-family: 'Rajdhani', sans-serif; display: flex; flex-direction: column; align-items: center; padding: 20px; height: 100vh; margin: 0; box-sizing: border-box; }
        .header { text-align: center; margin-bottom: 20px; border-bottom: 1px solid var(--blue); width: 100%; padding-bottom: 10px; }
        .frame { width: 100%; max-width: 400px; height: 250px; border: 1px solid var(--blue); position: relative; display: flex; justify-content: center; align-items: center; background: rgba(0,243,255,0.05); border-radius: 8px; overflow: hidden; box-shadow: 0 0 15px rgba(0,243,255,0.2); }
        #preview { width: 100%; height: 100%; object-fit: cover; display: none; }
        .controls { width: 100%; max-width: 400px; margin-top: 20px; display: flex; flex-direction: column; gap: 15px; }
        input { background: rgba(255,255,255,0.05); border: 1px solid var(--gold); color: white; padding: 12px; border-radius: 4px; font-family: inherit; outline: none; }
        .btn { background: none; border: 1px solid var(--blue); color: var(--blue); padding: 15px; font-weight: bold; cursor: pointer; text-transform: uppercase; letter-spacing: 2px; transition: 0.3s; }
        .btn:active { background: var(--blue); color: black; }
        .status { font-size: 12px; color: var(--gold); margin-top: 10px; display: none; text-align: center; font-weight: bold; }
    </style>
</head>
<body>
    <div class="header"><h1>NEXUS SCANNER</h1><span style="font-size: 10px; color: var(--gold)">PROTOCOL V44 ACTIVE</span></div>
    
    <div class="frame" onclick="document.getElementById('cam').click()">
        <span id="plh">[ PULSAR PARA ESCANEAR ]</span>
        <img id="preview">
    </div>
    
    <input type="file" id="cam" accept="image/*" capture="environment" style="display:none">
    
    <div class="controls">
        <input type="text" id="req" placeholder="Escribe el comando de rediseño...">
        <button class="btn" id="sendBtn" onclick="uploadAndSend()">🚀 EJECUTAR PROTOCOLO</button>
        <div id="status" class="status">SINCRONIZANDO CON EL KERNEL...</div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();
        let selectedFile = null;

        // API KEY de ImgBB (Para saltar el límite de 4KB)
        const IMGBB_KEY = "7026727282e70e3001e3e7f4c93540d5"; 

        document.getElementById('cam').onchange = function(e) {
            selectedFile = e.target.files[0];
            if (!selectedFile) return;
            document.getElementById('preview').src = URL.createObjectURL(selectedFile);
            document.getElementById('preview').style.display = 'block';
            document.getElementById('plh').style.display = 'none';
        };

        async function uploadAndSend() {
            const prompt = document.getElementById('req').value;
            if (!selectedFile || !prompt) return alert("Falta imagen o comando");

            const status = document.getElementById('status');
            const btn = document.getElementById('sendBtn');
            
            status.style.display = 'block';
            btn.disabled = true;
            btn.innerText = "PROCESANDO...";

            try {
                // 1. Subir a ImgBB para obtener una URL corta
                status.innerText = "SUBIENDO IMAGEN AL PUENTE...";
                const formData = new FormData();
                formData.append("image", selectedFile);
                
                const response = await fetch(`https://api.imgbb.com/1/upload?key=${IMGBB_KEY}`, {
                    method: "POST",
                    body: formData
                });
                
                const result = await response.json();
                
                if (result.success) {
                    const imageUrl = result.data.url;
                    status.innerText = "SINCRONIZANDO CON AXLEPH...";
                    
                    // 2. Enviar la URL al bot (esto solo pesa unos bytes, NO SE BLOQUEA)
                    const payload = JSON.stringify({ 
                        action: "nexus_render", 
                        image_url: imageUrl, 
                        request: prompt 
                    });
                    
                    tg.sendData(payload);
                } else {
                    throw new Error("Fallo en la subida al puente.");
                }
            } catch (err) {
                alert("ERROR: " + err.message);
                btn.disabled = false;
                btn.innerText = "REINTENTAR";
            }
        }
    </script>
</body>
</html>
