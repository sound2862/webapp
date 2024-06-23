<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Text Image Scanner</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #d3d3d3; /* 회색 배경색 */
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }
        #uploaded-image {
            max-width: 100%;
            max-height: 100%;
            object-fit: cover;
            display: none;
        }
        #upload-icon, #pdf-upload-icon {
            width: 40px;
            height: 40px;
            cursor: pointer;
        }
        #file-input, #pdf-input {
            display: none;
        }
        #icon-container {
            display: flex;
            gap: 10px;
        }
        /* 추가된 스타일 */
        html, body {
            display: contents;
        }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.7.570/pdf.min.js"></script>
</head>
<body>
    <div id="image-container">
        <img id="uploaded-image" src="" alt="Uploaded Image">
    </div>
    <div id="icon-container">
        <img src="https://raw.githubusercontent.com/siuwww/ChatICT/2251f390afe8f51369cb08904df7b0e5d4f6580d/image/upload-icon.png" id="upload-icon" onclick="document.getElementById('file-input').click();">
        <input type="file" id="file-input" accept="image/*" onchange="handleFileChange(event)">
        <img src="https://github.com/siuwww/ChatICT/blob/main/image/pdf-upload-icon.png?raw=true" id="pdf-upload-icon" onclick="document.getElementById('pdf-input').click();">
        <input type="file" id="pdf-input" accept="application/pdf" onchange="handlePdfChange(event)">
    </div>
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js"></script>
    <script>
        let isProcessing = false;

        function handleImageUri(uri) {
            const img = document.getElementById('uploaded-image');
            img.src = uri;
            img.style.display = 'block';
            recognizeText(uri);
            Android.handleImage(uri); // Android 인터페이스를 통해 이미지 URI를 안드로이드로 전달
        }

        function handleFileChange(event) {
            if (isProcessing) {
                alert('현재 다른 OCR 프로세스가 실행 중입니다. 계속하려면 새로고침을 해주세요.');
                return;
            }
            isProcessing = true;
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    handleImageUri(e.target.result);
                    isProcessing = false;
                };
                reader.readAsDataURL(file);
            }
        }

        async function handlePdfChange(event) {
            if (isProcessing) {
                alert('현재 다른 OCR 프로세스가 실행 중입니다. 계속하려면 새로고침을 해주세요.');
                return;
            }
            isProcessing = true;
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = async function(e) {
                    try {
                        const pdfData = new Uint8Array(e.target.result);
                        const pdf = await pdfjsLib.getDocument({ data: pdfData }).promise;
                        let extractedText = '';
                        for (let i = 1; i <= pdf.numPages; i++) {
                            const page = await pdf.getPage(i);
                            const viewport = page.getViewport({ scale: 1.5 });
                            const canvas = document.createElement('canvas');
                            const context = canvas.getContext('2d');
                            canvas.height = viewport.height;
                            canvas.width = viewport.width;
                            await page.render({ canvasContext: context, viewport: viewport }).promise;
                            const imageData = canvas.toDataURL('image/png');
                            const { data: { text } } = await Tesseract.recognize(imageData, 'kor+eng', {
                                logger: m => console.log(m)
                            });
                            extractedText += text + '\n';
                        }
                        Android.processText(extractedText); // Android 인터페이스로 텍스트 전달
                        isProcessing = false;
                    } catch (error) {
                        console.error('PDF 처리 중 오류:', error);
                        isProcessing = false;
                    }
                };
                reader.readAsArrayBuffer(file);
            }
        }

        function recognizeText(imageSrc) {
            Tesseract.recognize(imageSrc, 'kor+eng', {
                logger: m => console.log(m)
            }).then(({ data: { text } }) => {
                Android.processText(text); // 추출된 텍스트를 Android 인터페이스로 전달
            }).catch(err => {
                console.error(err);
            });
        }
    </script>
</body>
</html>
