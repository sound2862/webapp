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
    <input type="file" id="fileInput" accept="image/*" style="display:none;" onchange="handleFileSelect(event)">
    <img id="selectedImage" src="" alt="선택된 이미지" style="max-width:100%; height:auto;">
    <p id="extractedText">추출된 텍스트가 여기에 표시됩니다.</p>

    <script>
        function triggerFileInput() {
            document.getElementById('fileInput').click();
        }

        function handleFileSelect(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    document.getElementById('selectedImage').src = e.target.result;
                    // 여기서 이미지를 서버로 업로드하거나 텍스트 추출 등의 작업을 수행할 수 있습니다.
                };
                reader.readAsDataURL(file);
            }
        }

        function handleImageUri(uri) {
            document.getElementById('selectedImage').src = uri;
            // 여기서 이미지를 서버로 업로드하거나 텍스트 추출 등의 작업을 수행할 수 있습니다.
        }

        function processText(text) {
            document.getElementById('extractedText').innerText = text;
        }
    </script>
</body>
</html>
