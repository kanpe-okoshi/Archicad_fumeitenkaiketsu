<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ArchiCAD 質問解決アシスタント</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f0f4f8; /* Light blue-gray background */
            display: flex;
            justify-content: center;
            align-items: flex-start; /* Align to top */
            min-height: 100vh;
            padding: 2rem;
        }
        .container {
            max-width: 900px;
            width: 100%;
            background-color: #ffffff;
            border-radius: 1.5rem; /* More rounded corners */
            box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1); /* Stronger shadow */
            padding: 2.5rem;
            display: flex;
            flex-direction: column;
            gap: 2rem;
        }
        .section-title {
            font-size: 1.75rem; /* Larger title */
            font-weight: 700;
            color: #2c3e50; /* Darker text */
            margin-bottom: 1rem;
            text-align: center;
        }
        .input-area textarea {
            min-height: 120px;
            resize: vertical;
            border: 2px solid #cbd5e0; /* Lighter border */
            border-radius: 0.75rem;
            padding: 1rem;
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }
        .input-area textarea:focus {
            outline: none;
            border-color: #3b82f6; /* Blue focus */
            box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2); /* Soft blue glow */
        }
        .btn {
            padding: 0.75rem 1.5rem;
            border-radius: 0.75rem;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.5rem;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .btn-primary {
            background-color: #3b82f6; /* Blue */
            color: white;
        }
        .btn-primary:hover {
            background-color: #2563eb; /* Darker blue */
            transform: translateY(-2px);
        }
        .btn-secondary {
            background-color: #60a5fa; /* Lighter blue */
            color: white;
        }
        .btn-secondary:hover {
            background-color: #3b82f6; /* Blue */
            transform: translateY(-2px);
        }
        .loading {
            display: none;
            text-align: center;
            margin-top: 1rem;
            color: #6b7280;
        }
        .answer-box, .links-box {
            background-color: #f8fafc; /* Very light blue-gray */
            border: 1px solid #e2e8f0; /* Lighter border */
            border-radius: 0.75rem;
            padding: 1.5rem;
            min-height: 100px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #4a5568;
            font-size: 1.05rem;
            line-height: 1.6;
            text-align: center;
        }
        .answer-box.has-content, .links-box.has-content {
            align-items: flex-start;
            justify-content: flex-start;
            text-align: left;
        }
        .links-box ul {
            list-style: none;
            padding: 0;
            width: 100%;
        }
        .links-box li {
            margin-bottom: 0.75rem;
            word-break: break-all; /* Ensure long URLs wrap */
        }
        .links-box a {
            color: #3b82f6;
            text-decoration: none;
            transition: color 0.3s ease;
        }
        .links-box a:hover {
            color: #2563eb;
            text-decoration: underline;
        }
        .error-message {
            color: #ef4444; /* Red for errors */
            font-weight: 500;
            margin-top: 1rem;
            text-align: center;
        }
        .button-group {
            display: flex;
            gap: 1rem;
            justify-content: center;
            flex-wrap: wrap; /* Allow buttons to wrap on smaller screens */
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="section-title">ArchiCAD 質問解決アシスタント</h1>

        <div class="input-area">
            <label for="question" class="block text-gray-700 text-lg font-medium mb-2">ArchiCADに関する質問を入力してください:</label>
            <textarea id="question" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-blue-500" placeholder="例: 壁の厚さを変更する方法は？" rows="5"></textarea>
            <div class="button-group mt-4">
                <button id="generateAnswerBtn" class="btn btn-primary">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M16.364 16.364l.707.707M12 21v-1m-4.673-1h4.673M4.636 16.364l-.707.707M3 12H4m1.636-6.364l.707-.707M6 18a6 6 0 100-12 6 6 0 000 12z"></path></svg>
                    回答を生成
                </button>
                <button id="suggestLinksBtn" class="btn btn-secondary">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.828 10.172a4 4 0 00-5.656 0l-4 4a4 4 0 105.656 5.656l1.102-1.101m-.758-4.899a4 4 0 005.656 0l4-4a4 4 0 00-5.656-5.656l-1.102 1.101m-4.899.758l-.707.707M12 18h.01"></path></svg>
                    関連サイトを検索
                </button>
            </div>
            <div id="loading" class="loading">
                <p>処理中...</p>
            </div>
            <div id="errorMessage" class="error-message hidden"></div>
        </div>

        <div class="answer-section">
            <h2 class="section-title">Geminiによる回答</h2>
            <div id="answerBox" class="answer-box">
                ここにGeminiからの回答が表示されます。
            </div>
        </div>

        <div class="links-section">
            <h2 class="section-title">参考になりそうなサイト（Google検索クエリ）</h2>
            <div id="linksBox" class="links-box">
                質問に関連するGoogle検索クエリがここに表示されます。これらのクエリをGoogleで検索して、さらに詳しい情報を見つけることができます。
            </div>
        </div>
    </div>

    <script type="module">
        const questionInput = document.getElementById('question');
        const generateAnswerBtn = document.getElementById('generateAnswerBtn');
        const suggestLinksBtn = document.getElementById('suggestLinksBtn');
        const answerBox = document.getElementById('answerBox');
        const linksBox = document.getElementById('linksBox');
        const loadingIndicator = document.getElementById('loading');
        const errorMessage = document.getElementById('errorMessage');

        // Function to display messages (instead of alert)
        function showMessage(element, message, isError = false) {
            element.textContent = message;
            if (isError) {
                element.classList.remove('hidden');
            } else {
                element.classList.add('hidden');
            }
        }

        // Function to call Gemini API for answer
        async function generateAnswer() {
            const prompt = questionInput.value.trim();
            if (!prompt) {
                showMessage(errorMessage, '質問を入力してください。', true);
                return;
            }

            showMessage(errorMessage, '', false);
            answerBox.classList.remove('has-content');
            answerBox.textContent = 'Geminiが回答を生成中...';
            loadingIndicator.style.display = 'block';

            try {
                let chatHistory = [];
                chatHistory.push({ role: "user", parts: [{ text: `ArchiCADに関する以下の質問に簡潔に答えてください。専門用語は避け、分かりやすく説明してください。質問: ${prompt}` }] });

                const payload = { contents: chatHistory };
                const apiKey = ""; // Canvas will provide this at runtime
                const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.0-flash:generateContent?key=${apiKey}`;

                const response = await fetch(apiUrl, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const result = await response.json();

                if (result.candidates && result.candidates.length > 0 &&
                    result.candidates[0].content && result.candidates[0].content.parts &&
                    result.candidates[0].content.parts.length > 0) {
                    const text = result.candidates[0].content.parts[0].text;
                    answerBox.textContent = text;
                    answerBox.classList.add('has-content');
                } else {
                    console.error('Gemini API response structure unexpected:', result);
                    answerBox.textContent = '回答の生成に失敗しました。もう一度お試しください。';
                }
            } catch (error) {
                console.error('Error calling Gemini API:', error);
                answerBox.textContent = '回答の生成中にエラーが発生しました。インターネット接続を確認してください。';
                showMessage(errorMessage, '回答の生成中にエラーが発生しました。', true);
            } finally {
                loadingIndicator.style.display = 'none';
            }
        }

        // Function to suggest Google search queries
        function suggestSearchQueries() {
            const prompt = questionInput.value.trim();
            if (!prompt) {
                showMessage(errorMessage, '質問を入力してください。', true);
                return;
            }

            showMessage(errorMessage, '', false);
            linksBox.classList.remove('has-content');
            linksBox.innerHTML = ''; // Clear previous suggestions

            const queries = [
                `ArchiCAD ${prompt} 使い方`,
                `ArchiCAD ${prompt} トラブルシューティング`,
                `ArchiCAD ${prompt} FAQ`,
                `ArchiCAD ${prompt} Tips`,
                `ArchiCAD ${prompt} ヘルプ`,
                `ArchiCAD ${prompt} 公式`
            ];

            const ul = document.createElement('ul');
            queries.forEach(query => {
                const li = document.createElement('li');
                const a = document.createElement('a');
                a.href = `https://www.google.com/search?q=${encodeURIComponent(query)}`;
                a.target = '_blank'; // Open in new tab
                a.rel = 'noopener noreferrer'; // Security best practice
                a.textContent = query;
                li.appendChild(a);
                ul.appendChild(li);
            });

            linksBox.appendChild(ul);
            linksBox.classList.add('has-content');
            linksBox.innerHTML += `<p class="mt-4 text-sm text-gray-500">※このアプリケーションは直接ウェブ検索を行うことはできません。上記のクエリをコピーしてGoogleで検索してください。</p>`;
        }

        // Event Listeners
        generateAnswerBtn.addEventListener('click', generateAnswer);
        suggestLinksBtn.addEventListener('click', suggestSearchQueries);

        // Allow pressing Enter in textarea to generate answer (optional)
        questionInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) { // Shift+Enter for new line
                e.preventDefault();
                generateAnswer();
            }
        });
    </script>
</body>
</html>
