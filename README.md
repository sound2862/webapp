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
            background-color: #d3d3d3; /* 회색 배경색 */
            color: #333;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }
        #uploaded-image {
            display: none;
            max-width: 100%;
            max-height: 100%;
            object-fit: cover;
        }
        .placeholder {
            display: none;
        }
    </style>
</head>
<body>
    <img id="uploaded-image" src="" alt="Uploaded Image">
    <input type="file" id="file-input" accept="image/*" style="display:none;" onchange="handleFileChange(event)">
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js"></script>
    <script>
        function handleImageUri(uri) {
            const img = document.getElementById('uploaded-image');
            img.src = uri;
            img.style.display = 'block';
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
                Android.processText(text); // 추출된 텍스트를 Android 인터페이스로 전달
            }).catch(err => {
                console.error(err);
            });
        }

        function triggerFileInput() {
            document.getElementById('file-input').click();
        }

        // 이미지 업로드 트리거 (예시)
        document.addEventListener('DOMContentLoaded', () => {
            triggerFileInput();
        });
    </script>
</body>
</html>
