עע<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>מבזקי השעה</title>
    <style>
        body { font-family: sans-serif; background: #f0f2f5; text-align: center; padding: 20px; }
        .container { max-width: 500px; margin: auto; background: white; padding: 20px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        textarea { width: 100%; height: 100px; margin-bottom: 10px; padding: 10px; box-sizing: border-box; font-size: 16px; }
        .btn { background: #d32f2f; color: white; border: none; padding: 10px 20px; cursor: pointer; width: 100%; font-size: 18px; font-weight: bold; }
        #list { margin-top: 20px; text-align: right; }
        .msg { background: #fff; padding: 10px; border-bottom: 1px solid #ddd; margin-bottom: 5px; }
    </style>
</head>
<body>
    <div class="container">
        <h1>מבזקי השעה</h1>
        <textarea id="newsInput" placeholder="הקלד מבזק חדש..."></textarea>
        <button class="btn" onclick="addNews()">פרסם מבזק</button>
        <div id="list"></div>
    </div>

    <script>
        function addNews() {
            const text = document.getElementById('newsInput').value;
            if (!text.trim()) return;
            const newsObj = {
                content: text,
                time: new Date().toLocaleTimeString('he-IL', {hour: '2-digit', minute:'2-digit'})
            };
            displayMessage(newsObj);
            document.getElementById('newsInput').value = "";
        }

        function displayMessage(data) {
            const div = document.createElement('div');
            div.className = 'msg';
            div.innerHTML = `<strong>${data.time}:</strong> <span>${data.content}</span>`;
            document.getElementById('list').prepend(div);
        }
    </script>
</body>
</html>
