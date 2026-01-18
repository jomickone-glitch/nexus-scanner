<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>NEXUS INDUSTRIAL V5</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <style>
        :root { --neon: #00f3ff; --gold: #d4af37; --dark: #0a0a0a; }
        body { background: var(--dark); color: white; font-family: 'Segoe UI', sans-serif; display: flex; flex-direction: column; align-items: center; padding: 20px; }
        .app-container { width: 100%; max-width: 500px; border: 1px solid rgba(0,243,255,0.2); background: rgba(255,255,255,0.03); border-radius: 15px; padding: 20px; backdrop-filter: blur(10px); }
        .viewport { width: 100%; height: 300px; border: 2px dashed var(--neon); border-radius: 10px; display: flex; flex-direction: column; justify-content: center; align-items: center; overflow: hidden; position: relative; cursor: pointer; }
        #preview { width: 100%; height: 100%; object-fit: cover; display: none; }
        .status-bar { font-size: 10px; color: var(--gold); text-transform: uppercase; margin-top: 10px; letter-spacing: 2px; }
        input[type="text"] { width: 100%; background: #151515; border: 1px solid var(--neon); color: white; padding: 15px; margin-top: 20px; border-radius: 8px; box-sizing: border-box; }
        .btn-deploy { width: 100%; background: linear-gradient(45deg, var(--neon), #00aaff); color: black; border: none; padding: 18px; margin-top: 20px; font-weight: bold; border-radius: 8px; cursor: pointer; text-transform: uppercase; }
        .loading-overlay { position: fixed; top:0; left:0; width:100%; height:100%; background: rgba(0,0,0,0.8); display:none; justify-content:center; align-items:center; z-index:100; flex-direction: column; }
    </style>
</head>
<body>
    <div class="loading-overlay" id="loader">
        <div style="color: var(--neon);">SINCRONIZANDO UPLINK HD...</div>
    </div>

    <div class="app-container">
        <div style="text-align:center; margin-bottom: 20px;">
            <h2 style="margin:0; color: var(--neon);">NEXUS INDUSTRIAL</h2>
            <div style="font-size: 10px; color: var(--gold);">BANDB ARQUITECTURA V5</div>
        </div>

        <div class="viewport" onclick="document.getElementById('fileInput').click()">
            <span id="label">[ ESCANEAR ESPACIO HD ]</span>
            <img id="preview">
        </div>
        <input type="file" id="fileInput" accept="image/*" style="display:none">

        <input type="text" id="userRequest" placeholder="Ej: Cocina minimalista madera roble...">
        
        <button class="btn-deploy" onclick="deployNexus()">Generar Visualización Pro</button>
        <div class="status-bar" id="status">Sistema Listo - Encriptación BANDB Activa</div>
    </div>

    <script>
        const tg = window.Telegram.WebApp;
        tg.expand();

        // CREDENCIALES INDUSTRIALES (Cloudinary)
        const CLOUDINARY_URL = "https://api.cloudinary.com/v1_1/dxf63mqqs/image/upload";
        const UPLOAD_PRESET = "unsigned_nexus"; // Preset configurado para subida directa

        let currentFile = null;

        document.getElementById('fileInput').onchange = (e) => {
            currentFile = e.target.files[0];
            if (currentFile) {
                document.getElementById('preview').src = URL.createObjectURL(currentFile);
                document.getElementById('preview').style.display = 'block';
                document.getElementById('label').style.display = 'none';
            }
        };

        async function deployNexus() {
            const req = document.getElementById('userRequest').value;
            if (!currentFile || !req) return alert("Faltan datos técnicos");

            document.getElementById('loader').style.display = 'flex';

            try {
                // SUBIDA INDUSTRIAL (Mantiene resolución original)
                const formData = new FormData();
                formData.append("file", currentFile);
                formData.append("upload_preset", UPLOAD_PRESET);

                const res = await fetch(CLOUDINARY_URL, { method: "POST", body: formData });
                const data = await res.json();

                if (data.secure_url) {
                    // Solo mandamos la URL (pequeña) a Telegram
                    tg.sendData(jsonString = JSON.stringify({
                        action: "nexus_render",
                        image_url: data.secure_url,
                        request: req
                    }));
                }
            } catch (e) {
                alert("Fallo en Uplink: " + e);
            } finally {
                document.getElementById('loader').style.display = 'none';
            }
        }
    </script>
</body>
</html>
