<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DataMatrix Kod Skanerlash</title>
    
    <!-- Barcha kerakli kutubxonalar -->
    <script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/jsqr@1.4.0/dist/jsQR.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/datamatrix-scanner@1.0.0/dist/datamatrix-scanner.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/quagga/0.12.1/quagga.min.js"></script>
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            color: #333;
            min-height: 100vh;
            padding: 20px;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        
        header {
            text-align: center;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 30px;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
        }
        
        h1 {
            color: #1a2a6c;
            margin-bottom: 15px;
            font-size: 2.5rem;
        }
        
        .subtitle {
            color: #b21f1f;
            font-size: 1.2rem;
            margin-bottom: 20px;
        }
        
        .app-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
        }
        
        @media (max-width: 900px) {
            .app-grid {
                grid-template-columns: 1fr;
            }
        }
        
        .scanner-section, .info-section {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
        }
        
        .section-title {
            color: #1a2a6c;
            margin-bottom: 20px;
            font-size: 1.5rem;
            border-bottom: 2px solid #fdbb2d;
            padding-bottom: 10px;
        }
        
        .scanner-container {
            position: relative;
            margin-bottom: 20px;
        }
        
        #reader {
            width: 100%;
            height: 300px;
            border: 3px dashed #1a2a6c;
            border-radius: 15px;
            overflow: hidden;
            background-color: #f8f9fa;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }
        
        .scanner-frame {
            position: absolute;
            width: 80%;
            height: 80%;
            border: 2px solid #b21f1f;
            border-radius: 10px;
            box-shadow: 0 0 0 2000px rgba(0, 0, 0, 0.3);
        }
        
        .file-upload {
            margin: 15px 0;
            text-align: center;
        }
        
        .file-upload label {
            display: inline-block;
            padding: 10px 20px;
            background: #2196F3;
            color: white;
            border-radius: 5px;
            cursor: pointer;
            transition: background 0.3s;
        }
        
        .file-upload label:hover {
            background: #1976D2;
        }
        
        .file-upload input {
            display: none;
        }
        
        .controls {
            display: flex;
            gap: 15px;
            margin-top: 15px;
            flex-wrap: wrap;
        }
        
        button {
            padding: 12px 25px;
            border: none;
            border-radius: 50px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        #startButton {
            background-color: #4CAF50;
            color: white;
        }
        
        #stopButton {
            background-color: #f44336;
            color: white;
        }
        
        #copyButton {
            background-color: #2196F3;
            color: white;
        }
        
        #saveButton {
            background-color: #fdbb2d;
            color: #333;
        }
        
        #clearHistory {
            background-color: #9C27B0;
            color: white;
        }
        
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        button:active {
            transform: translateY(0);
        }
        
        button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }
        
        .result-section {
            margin-top: 30px;
        }
        
        #result {
            background-color: #f8f9fa;
            border: 1px solid #e0e0e0;
            border-radius: 10px;
            padding: 15px;
            min-height: 120px;
            word-break: break-all;
            font-family: monospace;
            white-space: pre-wrap;
            max-height: 200px;
            overflow-y: auto;
        }
        
        .status {
            margin-top: 15px;
            padding: 10px;
            border-radius: 8px;
            text-align: center;
            font-weight: 500;
        }
        
        .success {
            background-color: #e8f5e9;
            color: #2e7d32;
            border: 1px solid #c8e6c9;
        }
        
        .error {
            background-color: #ffebee;
            color: #c62828;
            border: 1px solid #ffcdd2;
        }
        
        .info {
            background-color: #e3f2fd;
            color: #1565c0;
            border: 1px solid #bbdefb;
        }
        
        .applications {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        
        .app-card {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 20px;
            border-left: 4px solid #1a2a6c;
        }
        
        .app-card h3 {
            color: #1a2a6c;
            margin-bottom: 10px;
        }
        
        .app-card p {
            color: #555;
            line-height: 1.5;
        }
        
        .history-section {
            margin-top: 30px;
        }
        
        #historyList {
            list-style-type: none;
            max-height: 200px;
            overflow-y: auto;
            border: 1px solid #e0e0e0;
            border-radius: 10px;
            padding: 10px;
        }
        
        #historyList li {
            padding: 10px;
            border-bottom: 1px solid #e0e0e0;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        
        #historyList li:last-child {
            border-bottom: none;
        }
        
        .history-time {
            color: #777;
            font-size: 0.9rem;
        }
        
        .history-actions {
            display: flex;
            gap: 5px;
        }
        
        .history-actions button {
            padding: 5px 10px;
            font-size: 0.8rem;
        }
        
        .stats {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }
        
        .stat-card {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 15px;
            text-align: center;
            border-left: 4px solid #4CAF50;
        }
        
        .stat-value {
            font-size: 1.5rem;
            font-weight: bold;
            color: #1a2a6c;
        }
        
        .stat-label {
            font-size: 0.9rem;
            color: #666;
        }
        
        footer {
            margin-top: 40px;
            text-align: center;
            color: white;
            padding: 20px;
        }
        
        @media (max-width: 600px) {
            header {
                padding: 20px;
            }
            
            h1 {
                font-size: 1.8rem;
            }
            
            #reader {
                height: 250px;
            }
            
            .controls {
                flex-direction: column;
            }
            
            button {
                width: 100%;
                justify-content: center;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>DataMatrix Kod Skanerlash</h1>
            <p class="subtitle">Farmatsevtika, sanoat va logistikada ishlatiladigan 2D kodlar</p>
        </header>
        
        <div class="app-grid">
            <div class="scanner-section">
                <h2 class="section-title">DataMatrix Skanerlash</h2>
                
                <div class="scanner-container">
                    <div id="reader">
                        <div class="scanner-frame"></div>
                        <p id="scannerPlaceholder">Skanerlashni boshlash uchun "Skanerlashni Boshlash" tugmasini bosing</p>
                    </div>
                </div>
                
                <div class="file-upload">
                    <label for="fileInput">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M14 2H6C4.9 2 4 2.9 4 4V20C4 21.1 4.9 22 6 22H18C19.1 22 20 21.1 20 20V8L14 2ZM16 18H8V16H16V18ZM16 14H8V12H16V14ZM13 9V3.5L18.5 9H13Z" fill="currentColor"/>
                        </svg>
                        Rasm Yuklash
                    </label>
                    <input type="file" id="fileInput" accept="image/*">
                </div>
                
                <div class="controls">
                    <button id="startButton">
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M12 15C13.6569 15 15 13.6569 15 12C15 10.3431 13.6569 9 12 9C10.3431 9 9 10.3431 9 12C9 13.6569 10.3431 15 12 15Z" fill="currentColor"/>
                            <path fill-rule="evenodd" clip-rule="evenodd" d="M22 12C22 17.5228 17.5228 22 12 22C6.47715 22 2 17.5228 2 12C2 6.47715 6.47715 2 12 2C17.5228 2 22 6.47715 22 12ZM20 12C20 16.4183 16.4183 20 12 20C7.58172 20 4 16.4183 4 12C4 7.58172 7.58172 4 12 4C16.4183 4 20 7.58172 20 12Z" fill="currentColor"/>
                        </svg>
                        Skanerlashni Boshlash
                    </button>
                    <button id="stopButton" disabled>
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <rect x="6" y="6" width="12" height="12" rx="1" fill="currentColor"/>
                        </svg>
                        To'xtatish
                    </button>
                    <button id="copyButton" disabled>
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M16 12.9V17.1C16 20.6 14.6 22 11.1 22H6.9C3.4 22 2 20.6 2 17.1V12.9C2 9.4 3.4 8 6.9 8H11.1C14.6 8 16 9.4 16 12.9Z" fill="currentColor"/>
                            <path d="M17.1 2H12.9C9.45001 2 8.05001 3.37 8.01001 6.75H11.1C15.3 6.75 17.25 8.7 17.25 12.9V15.99C20.63 15.95 22 14.55 22 11.1V6.9C22 3.4 20.6 2 17.1 2Z" fill="currentColor"/>
                        </svg>
                        Nusxa Olish
                    </button>
                    <button id="saveButton" disabled>
                        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <path d="M14 2H6C4.9 2 4 2.9 4 4V20C4 21.1 4.9 22 6 22H18C19.1 22 20 21.1 20 20V8L14 2ZM16 18H8V16H16V18ZM16 14H8V12H16V14ZM13 9V3.5L18.5 9H13Z" fill="currentColor"/>
                        </svg>
                        Saqlash
                    </button>
                </div>
                
                <div id="statusMessage" class="status info">
                    Kamera ruxsatini berishni unutmang
                </div>
                
                <div class="stats">
                    <div class="stat-card">
                        <div class="stat-value" id="totalScans">0</div>
                        <div class="stat-label">Jami Skanerlar</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="successfulScans">0</div>
                        <div class="stat-label">Muvaffaqiyatli</div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-value" id="todayScans">0</div>
                        <div class="stat-label">Bugun</div>
                    </div>
                </div>
                
                <div class="result-section">
                    <h2 class="section-title">Skanerlash Natijasi</h2>
                    <div id="result">Hali hech narsa skanerlanmadi...</div>
                </div>
                
                <div class="history-section">
                    <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px;">
                        <h2 class="section-title" style="margin: 0;">Skanerlash Tarixi</h2>
                        <button id="clearHistory" style="padding: 8px 15px; font-size: 0.9rem;">
                            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <path d="M6 19C6 20.1 6.9 21 8 21H16C17.1 21 18 20.1 18 19V7H6V19ZM19 4H15.5L14.5 3H9.5L8.5 4H5V6H19V4Z" fill="currentColor"/>
                            </svg>
                            Tozalash
                        </button>
                    </div>
                    <ul id="historyList">
                        <!-- Tarix bu yerda ko'rsatiladi -->
                    </ul>
                </div>
            </div>
            
            <div class="info-section">
                <h2 class="section-title">DataMatrix Kodlar Haqida</h2>
                
                <div class="applications">
                    <div class="app-card">
                        <h3>Farmatsevtika</h3>
                        <p>Dori-darmonlarni identifikatsiya qilish, seriya raqamlari, ishlab chiqarilgan sanalar va boshqa ma'lumotlarni saqlash.</p>
                    </div>
                    
                    <div class="app-card">
                        <h3>Sanoat</h3>
                        <p>Qismlar katalogizatsiyasi, ishlab chiqarish jarayonini kuzatish, sifat nazorati va tarkibiy qismlarni kuzatish.</p>
                    </div>
                    
                    <div class="app-card">
                        <h3>Logistika</h3>
                        <p>Yuk tashish hujjatlari, inventarizatsiya boshqaruvi, buyurtmalarni kuzatish va omborxona boshqaruvi.</p>
                    </div>
                    
                    <div class="app-card">
                        <h3>Elektronika</h3>
                        <p>Elektron komponentlarni belgilash, seriya raqamlari, ishlab chiqaruvchi ma'lumotlari va boshqa texnik ma'lumotlar.</p>
                    </div>
                </div>
                
                <div style="margin-top: 30px;">
                    <h2 class="section-title">DataMatrix Afzalliklari</h2>
                    <ul style="padding-left: 20px; line-height: 1.6;">
                        <li>Kichik o'lchamda ko'p ma'lumot saqlash imkoniyati</li>
                        <li>Xatolarni tuzatish kodi tufayli shikastlangan kodlarni o'qish qobiliyati</li>
                        <li>360 graduslik o'qish imkoniyati</li>
                        <li>Turli sirtlarda (qog'oz, metall, plastmassada) ishlash qobiliyati</li>
                        <li>Yuqori ishonchlilik darajasi</li>
                    </ul>
                </div>
                
                <div style="margin-top: 30px;">
                    <h2 class="section-title">Qo'llanma</h2>
                    <ol style="padding-left: 20px; line-height: 1.6;">
                        <li>"Skanerlashni Boshlash" tugmasini bosing</li>
                        <li>Kamera ruxsatini berishingiz so'raladi - "Ruxsat berish" tugmasini bosing</li>
                        <li>DataMatrix kodni kameraga qaratib, to'g'ri o'qilgunicha ushlab turing</li>
                        <li>Yoki "Rasm Yuklash" tugmasi bilan kompyuteringizdan rasm yuklang</li>
                        <li>Natijani ko'ring yoki "Nusxa Olish" tugmasi bilan nusxalang</li>
                    </ol>
                </div>
            </div>
        </div>
        
        <footer>
            &copy; 2023 DataMatrix Kod Skanerlash Dasturi | Barcha kerakli kutubxonalar bilan jihozlangan
        </footer>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const startButton = document.getElementById('startButton');
            const stopButton = document.getElementById('stopButton');
            const copyButton = document.getElementById('copyButton');
            const saveButton = document.getElementById('saveButton');
            const clearHistoryButton = document.getElementById('clearHistory');
            const fileInput = document.getElementById('fileInput');
            const resultDiv = document.getElementById('result');
            const statusMessage = document.getElementById('statusMessage');
            const scannerPlaceholder = document.getElementById('scannerPlaceholder');
            const historyList = document.getElementById('historyList');
            const totalScansElement = document.getElementById('totalScans');
            const successfulScansElement = document.getElementById('successfulScans');
            const todayScansElement = document.getElementById('todayScans');
            
            let html5QrCode;
            let isScanning = false;
            let scanHistory = [];
            let stats = {
                totalScans: 0,
                successfulScans: 0,
                todayScans: 0
            };
            
            // Statistikani yuklash
            loadStats();
            
            // Skanerlashni boshlash
            startButton.addEventListener('click', function() {
                startScanner();
            });
            
            // Skanerlashni to'xtatish
            stopButton.addEventListener('click', function() {
                stopScanner();
            });
            
            // Natijani nusxalash
            copyButton.addEventListener('click', function() {
                const textToCopy = resultDiv.textContent;
                if (textToCopy && textToCopy !== "Hali hech narsa skanerlanmadi...") {
                    navigator.clipboard.writeText(textToCopy)
                        .then(() => {
                            showStatus("Natija nusxalandi!", "success");
                        })
                        .catch(err => {
                            showStatus("Nusxalashda xatolik: " + err, "error");
                        });
                }
            });
            
            // Natijani saqlash
            saveButton.addEventListener('click', function() {
                const textToSave = resultDiv.textContent;
                if (textToSave && textToSave !== "Hali hech narsa skanerlanmadi...") {
                    // Tarixga qo'shish
                    addToHistory(textToSave);
                    showStatus("Natija saqlandi!", "success");
                }
            });
            
            // Tarixni tozalash
            clearHistoryButton.addEventListener('click', function() {
                scanHistory = [];
                updateHistoryList();
                showStatus("Tarix tozalandi!", "success");
            });
            
            // Fayl yuklash
            fileInput.addEventListener('change', function(e) {
                const file = e.target.files[0];
                if (file) {
                    scanImageFile(file);
                }
            });
            
            function startScanner() {
                if (isScanning) return;
                
                html5QrCode = new Html5Qrcode("reader");
                
                Html5Qrcode.getCameras().then(cameras => {
                    if (cameras && cameras.length > 0) {
                        const cameraId = cameras[0].id;
                        
                        // DataMatrix va boshqa 2D kodlarni o'qish uchun konfiguratsiya
                        const config = {
                            fps: 10,
                            qrbox: { width: 250, height: 250 },
                            supportedScanTypes: [
                                Html5QrcodeScanType.SCAN_TYPE_QR_CODE,
                                Html5QrcodeScanType.SCAN_TYPE_DATAMATRIX
                            ]
                        };
                        
                        html5QrCode.start(
                            cameraId,
                            config,
                            onScanSuccess,
                            onScanFailure
                        ).then(() => {
                            isScanning = true;
                            scannerPlaceholder.style.display = 'none';
                            startButton.disabled = true;
                            stopButton.disabled = false;
                            showStatus("DataMatrix skanerlash boshlandi. Kodni kameraga qarating...", "info");
                        }).catch(err => {
                            showStatus("Kamerani ishga tushirishda xatolik: " + err, "error");
                        });
                    } else {
                        showStatus("Kamera topilmadi. Iltimos, kamerangiz mavjudligini tekshiring.", "error");
                    }
                }).catch(err => {
                    showStatus("Kamerani sozlashda xatolik: " + err, "error");
                });
            }
            
            function stopScanner() {
                if (!isScanning || !html5QrCode) return;
                
                html5QrCode.stop().then(() => {
                    isScanning = false;
                    scannerPlaceholder.style.display = 'block';
                    startButton.disabled = false;
                    stopButton.disabled = true;
                    showStatus("Skanerlash to'xtatildi.", "info");
                }).catch(err => {
                    showStatus("Skanerlashni to'xtatishda xatolik: " + err, "error");
                });
            }
            
            function onScanSuccess(decodedText, decodedResult) {
                resultDiv.textContent = decodedText;
                copyButton.disabled = false;
                saveButton.disabled = false;
                showStatus("DataMatrix kod muvaffaqiyatli skanerlandi!", "success");
                
                // Statistikani yangilash
                updateStats(true);
                
                // Avtomatik tarixga qo'shish
                addToHistory(decodedText);
            }
            
            function onScanFailure(error) {
                // Xatolarni faqat konsolga chiqaramiz, foydalanuvchiga ko'rsatmaymiz
                console.error("Skanerlash xatosi:", error);
            }
            
            function scanImageFile(file) {
                const reader = new FileReader();
                
                reader.onload = function(e) {
                    const img = new Image();
                    img.onload = function() {
                        // Rasmni skanerlash uchun canvas yaratish
                        const canvas = document.createElement('canvas');
                        const ctx = canvas.getContext('2d');
                        canvas.width = img.width;
                        canvas.height = img.height;
                        ctx.drawImage(img, 0, 0);
                        
                        // jsQR yordamida DataMatrix ni skanerlash
                        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
                        const code = jsQR(imageData.data, imageData.width, imageData.height);
                        
                        if (code) {
                            resultDiv.textContent = code.data;
                            copyButton.disabled = false;
                            saveButton.disabled = false;
                            showStatus("Rasmdan DataMatrix kod muvaffaqiyatli o'qildi!", "success");
                            
                            // Statistikani yangilash
                            updateStats(true);
                            
                            // Tarixga qo'shish
                            addToHistory(code.data);
                        } else {
                            showStatus("Rasmda DataMatrix kod topilmadi!", "error");
                        }
                    };
                    img.src = e.target.result;
                };
                
                reader.readAsDataURL(file);
            }
            
            function addToHistory(text) {
                const timestamp = new Date().toLocaleString();
                const id = Date.now();
                scanHistory.unshift({ id, text, timestamp });
                
                // Tarixni yangilash
                updateHistoryList();
                
                // Tarixni 20 tadan ko'p bo'lmasligi uchun cheklash
                if (scanHistory.length > 20) {
                    scanHistory = scanHistory.slice(0, 20);
                }
                
                // LocalStorage ga saqlash
                localStorage.setItem('scanHistory', JSON.stringify(scanHistory));
            }
            
            function updateHistoryList() {
                historyList.innerHTML = '';
                
                if (scanHistory.length === 0) {
                    historyList.innerHTML = '<li style="text-align: center; color: #777;">Hali skanerlash tarixi mavjud emas</li>';
                    return;
                }
                
                scanHistory.forEach(item => {
                    const li = document.createElement('li');
                    li.innerHTML = `
                        <div style="flex: 1; overflow: hidden;">
                            <div style="font-weight: 500; margin-bottom: 5px;">${item.text}</div>
                            <div class="history-time">${item.timestamp}</div>
                        </div>
                        <div class="history-actions">
                            <button onclick="copyHistoryItem('${item.id}')" style="background: #2196F3; padding: 5px 10px; font-size: 0.8rem;">
                                <svg width="14" height="14" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M16 12.9V17.1C16 20.6 14.6 22 11.1 22H6.9C3.4 22 2 20.6 2 17.1V12.9C2 9.4 3.4 8 6.9 8H11.1C14.6 8 16 9.4 16 12.9Z" fill="currentColor"/>
                                    <path d="M17.1 2H12.9C9.45001 2 8.05001 3.37 8.01001 6.75H11.1C15.3 6.75 17.25 8.7 17.25 12.9V15.99C20.63 15.95 22 14.55 22 11.1V6.9C22 3.4 20.6 2 17.1 2Z" fill="currentColor"/>
                                </svg>
                            </button>
                            <button onclick="deleteHistoryItem('${item.id}')" style="background: #f44336; padding: 5px 10px; font-size: 0.8rem;">
                                <svg width="14" height="14" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                                    <path d="M6 19C6 20.1 6.9 21 8 21H16C17.1 21 18 20.1 18 19V7H6V19ZM19 4H15.5L14.5 3H9.5L8.5 4H5V6H19V4Z" fill="currentColor"/>
                                </svg>
                            </button>
                        </div>
                    `;
                    historyList.appendChild(li);
                });
            }
            
            function updateStats(success = false) {
                if (success) {
                    stats.successfulScans++;
                }
                stats.totalScans++;
                
                // Bugungi sana
                const today = new Date().toDateString();
                const lastScanDate = localStorage.getItem('lastScanDate');
                
                if (lastScanDate !== today) {
                    stats.todayScans = 0;
                    localStorage.setItem('lastScanDate', today);
                }
                
                if (success) {
                    stats.todayScans++;
                }
                
                // UI ni yangilash
                totalScansElement.textContent = stats.totalScans;
                successfulScansElement.textContent = stats.successfulScans;
                todayScansElement.textContent = stats.todayScans;
                
                // LocalStorage ga saqlash
                localStorage.setItem('scanStats', JSON.stringify(stats));
            }
            
            function loadStats() {
                // Statistikani yuklash
                const savedStats = localStorage.getItem('scanStats');
                if (savedStats) {
                    stats = JSON.parse(savedStats);
                }
                
                // Bugungi sana
                const today = new Date().toDateString();
                const lastScanDate = localStorage.getItem('lastScanDate');
                
                if (lastScanDate !== today) {
                    stats.todayScans = 0;
                }
                
                // Tarixni yuklash
                const savedHistory = localStorage.getItem('scanHistory');
                if (savedHistory) {
                    scanHistory = JSON.parse(savedHistory);
                }
                
                // UI ni yangilash
                totalScansElement.textContent = stats.totalScans;
                successfulScansElement.textContent = stats.successfulScans;
                todayScansElement.textContent = stats.todayScans;
                updateHistoryList();
            }
            
            function showStatus(message, type) {
                statusMessage.textContent = message;
                statusMessage.className = "status " + type;
            }
            
            // Global funktsiyalar tarix elementlari bilan ishlash uchun
            window.copyHistoryItem = function(id) {
                const item = scanHistory.find(item => item.id == id);
                if (item) {
                    navigator.clipboard.writeText(item.text)
                        .then(() => {
                            showStatus("Tarix elementi nusxalandi!", "success");
                        })
                        .catch(err => {
                            showStatus("Nusxalashda xatolik: " + err, "error");
                        });
                }
            };
            
            window.deleteHistoryItem = function(id) {
                scanHistory = scanHistory.filter(item => item.id != id);
                updateHistoryList();
                localStorage.setItem('scanHistory', JSON.stringify(scanHistory));
                showStatus("Tarix elementi o'chirildi!", "success");
            };
        });
    </script>
</body>
</html>
