<!DOCTYPE html>
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
            background-color: #ffffff;
            color: #444444;
            position: relative;
            min-height: 100vh;
        }
        header {
            background-color: #ffffff;
            color: #616161;
            padding: 15px 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        .header-container {
            display: flex;
            align-items: center;
        }
        .header-container img {
            width: 40px;
            height: 40px;
            margin-right: 10px;
        }
        h1 {
            font-size: 35px;
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
        .panel-container {
            display: flex;
            width: 100%;
            justify-content: space-between;
            align-items: flex-start;
        }
        .panel {
            width: 45%;
            border-radius: 12px;
            padding: 26px;
            background-color: white;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            border: 2px solid #cccccc;
            position: relative;
            min-height: 400px; /* Fixed height */
        }
        .panel:hover {
            border-color: #aaaaaa;
        }
        #uploaded-image-container {
            width: 100%;
            height: 300px; /* Fixed height */
            display: flex;
            align-items: center;
            justify-content: center;
        }
        #uploaded-image {
            width: 100%;
            height: 100%;
            object-fit: contain;
            border-radius: 12px;
            display: none;
        }
        .placeholder {
            font-size: 20px;
            color: #6c757d;
        }
        .content-panel {
            font-size: 18px;
            color: #444444;
            white-space: pre-wrap;
            word-wrap: break-word;
            overflow-y: auto;
            max-height: 580px;
            width: 100%;
            display: flex;
            align-items: flex-start; /* Align items to the top */
            justify-content: flex-start;
            position: relative;
        }
        #translation-text {
            font-size: 18px;
            color: #444444;
            white-space: pre-wrap;
            word-wrap: break-word;
            overflow-y: auto;
            text-align: center;
            width: 100%; /* Ensure text takes full width */
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
            flex-wrap: nowrap;
            width: 100%;
            margin-top: 10px; /* Added margin-top for spacing */
        }
        .progress-bar-container {
            width: 100%;
            background-color: #f3f3f3;
            border-radius: 12px;
            margin-top: 20px;
            display: none;
        }
        .progress-bar {
            width: 0;
            height: 20px;
            background-color: #4caf50;
            border-radius: 12px;
            text-align: center;
            color: white;
            transition: width 0.3s;
        }
    </style>
    <!-- Include the pdfjs-dist library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.7.570/pdf.min.js"></script>
</head>
<body>
    <header>
        <div class="header-container">
            <img src="https://github.com/siuwww/ChatICT/blob/main/image/ocr-icon.png?raw=true" alt="OCR Icon">
            <h1>Chat ICT</h1>
        </div>
    </header>
    <main>
        <div class="content">
            <div class="panel-container">
                <div class="panel">
                    <div id="uploaded-image-container">
                        <span class="placeholder">Upload an image or PDF</span>
                        <img id="uploaded-image" src="" alt="Uploaded Image">
                    </div>
                    <div class="button-container">
                        <img src="https://raw.githubusercontent.com/siuwww/ChatICT/2251f390afe8f51369cb08904df7b0e5d4f6580d/image/upload-icon.png" id="upload-icon" class="icon-button" onclick="document.getElementById('file-input').click();">
                        <input type="file" id="file-input" accept="image/*" style="display:none;">
                        <img src="https://github.com/siuwww/ChatICT/blob/main/image/pdf-upload-icon.png?raw=true" id="pdf-upload-icon" class="icon-button" onclick="document.getElementById('pdf-input').click();">
                        <input type="file" id="pdf-input" accept="application/pdf" style="display:none;">
                    </div>
                    <div class="progress-bar-container" id="progress-bar-container">
                        <div class="progress-bar" id="progress-bar"></div>
                    </div>
                </div>
                <div class="panel">
                    <div class="content-panel" id="translation-box">
                        <span id="translation-text" class="placeholder"></span>
                    </div>
                    <div class="button-container">
                        <img src="https://github.com/siuwww/ChatICT/blob/main/image/copy-icon.png?raw=true" id="copy-text-btn" class="icon-button">
                        <img src="https://github.com/siuwww/ChatICT/blob/main/image/play-icon.png?raw=true" id="play-audio-btn" class="icon-button">
                        <img src="https://github.com/siuwww/ChatICT/blob/main/image/stop-icon.png?raw=true" id="stop-audio-btn" class="icon-button">
                    </div>
                </div>
            </div>
        </div>
        <img src="https://github.com/siuwww/ChatICT/blob/main/image/anu.gif?raw=true" alt="ANU Logo" class="footer-logo">
    </main>
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js"></script>
    <script>
        let audioQueue = [];
        let isProcessing = false; // Flag to track the OCR process
        let currentUtterance = null; // Track the current utterance

        document.getElementById('file-input').addEventListener('change', function(event) {
            resetUploadSection(); // Reset the upload section
            if (isProcessing) {
                alert('현재 다른 OCR 프로세스가 실행 중입니다. 계속하려면 새로고침을 해주세요.');
                return;
            }
            isProcessing = true;
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const img = document.getElementById('uploaded-image');
                    img.src = e.target.result;
                    img.style.display = 'block';
                    document.querySelector('.placeholder').style.display = 'none';
                    // Process the image with Tesseract
                    Tesseract.recognize(
                        e.target.result,
                        'kor+eng',
                        {
                            logger: m => console.log(m)
                        }
                    ).then(({ data: { text } }) => {
                        const translationText = document.getElementById('translation-text');
                        translationText.textContent = formatExtractedText(text);
                        translationText.classList.remove('placeholder'); // Remove placeholder class
                        isProcessing = false; // Reset the flag
                    }).catch(error => {
                        console.error('Error:', error);
                        isProcessing = false; // Reset the flag
                    });
                };
                reader.readAsDataURL(file);
            }
        });

        document.getElementById('pdf-input').addEventListener('change', async function(event) {
            resetUploadSection(); // Reset the upload section
            if (isProcessing) {
                alert('Another OCR process is currently running. Please wait.');
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
                        const progressBarContainer = document.getElementById('progress-bar-container');
                        const progressBar = document.getElementById('progress-bar');
                        progressBarContainer.style.display = 'block'; // Show progress bar
                        for (let i = 1; i <= pdf.numPages; i++) {
                            const page = await pdf.getPage(i);

                            // Attempt to extract text content from the page
                            const textContent = await page.getTextContent();
                            let pageText = textContent.items.map(item => item.str).join(' ');

                            if (!pageText.trim()) {
                                // If no text content, render the page to an image and use OCR
                                const viewport = page.getViewport({ scale: 1.5 });
                                const canvas = document.createElement('canvas');
                                const context = canvas.getContext('2d');
                                canvas.height = viewport.height;
                                canvas.width = viewport.width;

                                // Render the page on the canvas
                                await page.render({ canvasContext: context, viewport: viewport }).promise;

                                // Extract image data from the canvas
                                const imageData = canvas.toDataURL('image/png');

                                // Perform OCR on the extracted image
                                const { data: { text } } = await Tesseract.recognize(imageData, 'kor+eng', {
                                    logger: m => console.log(m)
                                });
                                pageText = text;
                            }

                            extractedText += pageText + '\n';

                            // Update progress bar
                            const progress = (i / pdf.numPages) * 100;
                            progressBar.style.width = `${progress}%`;
                        }
                        const formattedText = formatExtractedText(extractedText);
                        const translationText = document.getElementById('translation-text');
                        translationText.textContent = formattedText;
                        translationText.classList.remove('placeholder');
                        // Show confirmation text in the left panel
                        const uploadedImageContainer = document.getElementById('uploaded-image-container');
                        uploadedImageContainer.innerHTML = '<span class="placeholder">PDF Upload and Extraction Complete</span>';
                        isProcessing = false; // Reset the flag
                        progressBarContainer.style.display = 'none'; // Hide progress bar
                    } catch (error) {
                        console.error('Error during PDF processing:', error);
                        alert('Failed to process PDF file.');
                        isProcessing = false; // Reset the flag
                        progressBarContainer.style.display = 'none'; // Hide progress bar
                    }
                };
                reader.readAsArrayBuffer(file);
            }
        });

        document.getElementById('copy-text-btn').addEventListener('click', function() {
            const text = document.getElementById('translation-text').textContent;
            if (text) {
                navigator.clipboard.writeText(text).then(() => {
                    alert('Text copied to clipboard.');
                }).catch(err => {
                    console.error('Error copying text: ', err);
                });
            } else {
                alert('No text available to copy.');
            }
        });

        document.getElementById('play-audio-btn').addEventListener('click', function() {
            const text = document.getElementById('translation-text').textContent;
            if (text) {
                if (currentUtterance) {
                    speechSynthesis.cancel();
                    currentUtterance = null;
                }
                const segments = splitTextByLanguage(text);
                audioQueue = segments.map(segment => {
                    const utterance = new SpeechSynthesisUtterance(segment.text);
                    utterance.lang = segment.lang;
                    return utterance;
                });
                playAudioQueue();
            } else {
                alert('No text available.');
            }
        });

        document.getElementById('stop-audio-btn').addEventListener('click', function() {
            if (currentUtterance) {
                speechSynthesis.cancel();
                currentUtterance = null;
                audioQueue = [];
            }
        });

        function formatExtractedText(text) {
            // Split text into lines and join with proper spacing
            return text.split(/[\r\n]+/).map(line => line.trim()).join('\n');
        }

        function splitTextByLanguage(text) {
            const koreanChar = /[\u3131-\uD79D]/ugi;
            const englishChar = /[a-zA-Z]/;
            const segments = [];
            let currentLang = null;
            let currentSegment = '';

            for (const char of text) {
                const charLang = koreanChar.test(char) ? 'ko-KR' : englishChar.test(char) ? 'en-US' : currentLang;
                if (charLang !== currentLang && currentSegment) {
                    segments.push({ text: currentSegment, lang: currentLang });
                    currentSegment = '';
                }
                currentLang = charLang;
                currentSegment += char;
            }
            if (currentSegment) {
                segments.push({ text: currentSegment, lang: currentLang });
            }
            return segments;
        }

        function playAudioQueue() {
            if (audioQueue.length > 0) {
                currentUtterance = audioQueue.shift();
                currentUtterance.onend = () => {
                    currentUtterance = null;
                    playAudioQueue();
                };
                speechSynthesis.speak(currentUtterance);
            }
        }

        function resetUploadSection() {
            const img = document.getElementById('uploaded-image');
            img.style.display = 'none';
            img.src = '';
            const placeholder = document.querySelector('.placeholder');
            placeholder.style.display = 'block';
        }
    </script>
</body>
</html>
