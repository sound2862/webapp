<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Scanner</title>
    <style>
        /* 기존 스타일 유지 */
        /* 카메라 버튼 스타일 추가 */
        .camera-button {
            background-color: #272B73;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        .camera-button:hover {
            background-color: #00a398;
        }
    </style>
</head>
<body>
    <header>
        <h1>Text Image Scanner</h1>
    </header>
    <main>
        <div class="content">
            <div class="image-translation-section">
                <div id="uploaded-image-container">
                    <span class="placeholder">Upload an image to start</span>
                    <img id="uploaded-image" src="" alt="Uploaded Image">
                    <input type="file" id="file-input" accept="image/*" style="display:none;" onchange="handleFileChange(event)">
                </div>
                <div class="translation-box" id="translation-box">
                    <pre id="translation-text">read image</pre>
                </div>
            </div>
            <button class="camera-button" onclick="openCamera()">Open Camera</button>
        </div>
        <img src="https://siuwww.github.io/ChatICT/images/anu.gif" alt="ANU Logo" class="footer-logo">
    </main>
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js"></script>
    <script>
        document.getElementById('uploaded-image').style.display = 'none';

        function handleImageUri(uri) {
            const img = document.getElementById('uploaded-image');
            img.src = uri;
            img.style.display = 'block';
            document.querySelector('#uploaded-image-container .placeholder').style.display = 'none';
            recognizeText(uri);
            // Android 인터페이스를 통해 이미지 URI를 안드로이드로 전달
            Android.handleImage(uri);
        }

        function handleFileChange(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    handleImageUri(e.target.result);
                };
                reader.readAsDataURL(file);
            }
        }

        function recognizeText(imageSrc) {
            Tesseract.recognize(
                imageSrc,
                'kor+eng',
                {
                    logger: m => console.log(m)
                }
            ).then(({ data: { text } }) => {
                document.getElementById('translation-text').innerText = text;
                Android.processText(text); // 추출된 텍스트를 Android 인터페이스로 전달
            }).catch(err => {
                console.error(err);
            });
        }

        function triggerFileInput() {
            document.getElementById('file-input').click();
        }

        function openCamera() {
            Android.openCamera(); // 카메라 열기 위해 Android 메서드 호출
        }
    </script>
</body>
</html>
