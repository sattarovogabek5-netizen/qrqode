<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QR Kod Skaneri</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5-qrcode/2.3.8/html5-qrcode.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        .container {
            width: 100%;
            max-width: 500px;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 25px;
            text-align: center;
        }

        .header h1 {
            font-size: 24px;
            margin-bottom: 5px;
        }

        .header p {
            opacity: 0.9;
            font-size: 14px;
        }

        .scanner-container {
            padding: 20px;
        }

        #reader {
            border-radius: 15px;
            overflow: hidden;
            border: 3px solid #667eea;
            position: relative;
        }

        #reader video {
            border-radius: 12px;
        }

        /* Skaner ramkasi */
        #reader__scan_region {
            border-radius: 15px !important;
        }

        .result-container {
            margin-top: 20px;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 15px;
            display: none;
        }

        .result-container.show {
            display: block;
        }

        .result-title {
            font-size: 18px;
            font-weight: bold;
            color: #667eea;
            margin-bottom: 10px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .result-content {
            background: white;
            padding: 15px;
            border-radius: 10px;
            word-break: break-all;
            font-size: 14px;
            color: #333;
        }

        .button-group {
            margin-top: 15px;
            display: flex;
            gap: 10px;
        }

        .btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
        }

        .btn-primary {
            background: #667eea;
            color: white;
        }

        .btn-primary:hover {
            background: #5568d3;
            transform: translateY(-2px);
        }

        .btn-secondary {
            background: #e0e0e0;
            color: #333;
        }

        .btn-secondary:hover {
            background: #d0d0d0;
        }

        .status {
            text-align: center;
            padding: 15px;
            color: #666;
            font-size: 14px;
        }

        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📷 QR Kod Skaneri</h1>
            <p>QR kodni kamerangizga tutib skanerlang</p>
        </div>

        <div class="scanner-container">
            <div id="reader"></div>
            
            <div class="result-container" id="result-container">
                <div class="result-title">
                    <span>✅</span>
                    <span>Skanerlangan Ma'lumot:</span>
                </div>
                <div class="result-content" id="result-content"></div>
                <div class="button-group">
                    <button class="btn btn-primary" onclick="sendToBot()">
                        📤 Botga Yuborish
                    </button>
                    <button class="btn btn-secondary" onclick="scanAgain()">
                        🔄 Qayta Skanerlash
                    </button>
                </div>
            </div>

            <div class="status" id="status">
                <div class="loading"></div>
                <p>Kamera yuklanmoqda...</p>
            </div>
        </div>
    </div>

    <script>
        let tg = window.Telegram.WebApp;
        let scannedData = null;
        let html5QrcodeScanner = null;

        // Telegram WebApp ni kengaytirish
        tg.expand();

        // QR kod skanerini boshlash
        function startScanner() {
            html5QrcodeScanner = new Html5QrcodeScanner(
                "reader",
                { 
                    fps: 10,
                    qrbox: { width: 250, height: 250 },
                    aspectRatio: 1.0,
                    facingMode: "environment", // Orqa kamera
                    rememberLastUsedCamera: false
                }
            );

            html5QrcodeScanner.render(onScanSuccess, onScanError);
            
            document.getElementById('status').style.display = 'none';
        }

        // Muvaffaqiyatli skanerlash
        function onScanSuccess(decodedText, decodedResult) {
            scannedData = decodedText;
            
            // Skanerini to'xtatish
            html5QrcodeScanner.clear();
            
            // Natijani ko'rsatish
            document.getElementById('result-content').textContent = decodedText;
            document.getElementById('result-container').classList.add('show');
            document.getElementById('reader').style.display = 'none';
            
            // Vibratsiya (agar mavjud bo'lsa)
            if (tg.HapticFeedback) {
                tg.HapticFeedback.notificationOccurred('success');
            }
        }

        // Xatolik yuz berganda
        function onScanError(error) {
            // Xatoliklarni e'tiborsiz qoldiramiz (doimiy skanerlash uchun)
        }

        // Botga yuborish
        function sendToBot() {
            if (scannedData) {
                tg.sendData(scannedData);
                tg.close();
            }
        }

        // Qayta skanerlash
        function scanAgain() {
            scannedData = null;
            document.getElementById('result-container').classList.remove('show');
            document.getElementById('reader').style.display = 'block';
            startScanner();
        }

        // Skaner ishga tushirish
        window.addEventListener('load', function() {
            setTimeout(startScanner, 500);
        });

        // Orqaga tugmasi
        tg.BackButton.show();
        tg.BackButton.onClick(function() {
            tg.close();
        });
    </script>
</body>
</html>
