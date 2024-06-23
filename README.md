<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Book Scanner</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f4f8;
            color: #333;
            position: relative;
            min-height: 100vh;
        }
        header {
            background-color: #272B73;
            color: white;
            padding: 15px 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        h1 {
            font-size: 26px;
            margin: 0;
        }
        main {
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        .content {
            width: 100%;
            max-width: 1200px;
            background: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            display: flex;
            flex-direction: column;
            align-items: center;
            transition: box-shadow 0.3s;
        }
        .content:hover {
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        }
        .image-translation-section {
            display: flex;
            width: 100%;
            justify-content: space-between;
            align-items: flex-start;
        }
        #uploaded-image-container, .translation-box {
            width: 45%;
            height: 300px;
            border-radius: 12px;
            padding: 26px;
            transition: border-color 0.3s;
            background-color: white;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            border: 2px dashed #272B73;
            position: relative;
        }
        #uploaded-image-container:hover, .translation-box:hover {
            border-color: #00a398;
        }
        #uploaded-image {
            max-width: 100%;
            max-height: 100%;
            border-radius: 12px;
            display: none;
        }
        .placeholder {
            font-size: 20px;
            color: #6c757d;
        }
        .translation-box {
            font-size: 25px;
            color: #6c757d;
            white-space: pre-wrap;
            word-wrap: break-word;
            overflow-y: auto;
            max-height: 600px;
            flex-direction: column;
            align-items: center;
        }
        .icon-button {
            width: 40px;
            height: 40px;
            margin: 3px;
            cursor: pointer;
        }
        .footer-logo {
            position: absolute;
            bottom: 10px;
            right: 10px;
            width: 250px;
            height: auto;
        }
        .button-container {
            display: flex;
            justify-content: right;
            width: 100%;
            position: absolute;
            bottom: 10px;
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
                <div id="uploaded-image-container" onclick="triggerFileInput()">
                    <span class="placeholder">Upload an image to start</span>
                    <img id="uploaded-image" src="" alt="Uploaded Image">
                    <input type="file" id="file-input" accept="image/*" style="display:none;" onchange="handleFileChange(event)">
                </div>
                <div class="translation-box" id="translation-box">
                    <pre id="translation-text">read image</pre>
                </div>
            </div>
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
            Android.handleImage(uri);
        }

        function handleImageUri(uri) {
            const img = document.getElementById('uploaded-image');
            img.src = uri;
            img.style.display = 'block';
            document.querySelector('#uploaded-image-container .placeholder').style.display = 'none';
            recognizeText(uri);
            Android.handleImage(uri);
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
                Android.processText(text);
            }).catch(err => {
                console.error(err);
            });
        }

        function triggerFileInput() {
            document.getElementById('file-input').click();
        }

        function triggerCameraInput() {
            Android.openCamera();
        }
    </script>
</body>
</html>
