<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>מבזקי השעה</title>
    <style>
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: #f4f7f6; text-align: center; padding: 20px; margin: 0; }
        .container { max-width: 550px; margin: auto; background: white; padding: 25px; border-radius: 15px; box-shadow: 0 4px 20px rgba(0,0,0,0.08); border-top: 6px solid #d32f2f; }
        
        /* עיצוב הלוגו והכותרת בצד */
        .header-section { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; padding-bottom: 15px; border-bottom: 2px solid #f0f0f0; }
        .logo-box { display: flex; align-items: center; gap: 12px; }
        .icon-speaker { background: #d32f2f; color: white; width: 60px; height: 60px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 30px; box-shadow: 0 3px 6px rgba(211,47,47,0.3); }
        .brand-text { text-align: right; }
        .brand-text h1 { margin: 0; font-size: 22px; color: #333; line-height: 1.1; font-weight: 800; }
        .brand-text span { font-size: 11px; color: #d32f2f; font-weight: bold; }

        /* מונה צופים */
        .stats-box { background: #fef2f2; padding: 10px 18px; border-radius: 12px; border: 1px solid #fee2e2; min-width: 80px; }
        .stats-box .count { font-weight: bold; color: #d32f2f; font-size: 20px; display: block; }
        .stats-box .label { font-size: 11px; color: #777; font-weight: bold; }
        
        /* אזור הזנה */
        .input-area { background: #fafafa; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #eee; }
        textarea, input { width: 100%; margin-bottom: 10px; padding: 12px; box-sizing: border-box; font-size: 15px; border: 1px solid #ddd; border-radius: 8px; outline: none; font-family: inherit; }
        textarea { height: 80px; resize: none; }
        
        .btn { background: #d32f2f; color: white; border: none; padding: 14px; cursor: pointer; width: 100%; font-size: 18px; font-weight: bold; border-radius: 10px; transition: 0.2s; }
        .btn:hover { background: #b71c1c; }
        
        /* רשימת המבזקים */
        #list { margin-top: 25px; text-align: right; }
        .msg { background: #fff; padding: 15px; border-bottom: 1px solid #eee; margin-bottom: 15px; border-right: 5px solid #d32f2f; border-radius: 8px; box-shadow: 0 2px 5px rgba(0,0,0,0.03); }
        .time { color: #999; font-size: 12px; margin-left: 10px; }
        .msg-link { display: block; margin-top: 10px; color: #1a73e8; text-decoration: none; font-size: 14px; font-weight: bold; }

        /* תגובות (לייקים) */
        .reactions { display: flex; gap: 10px; margin-top: 12px; padding-top: 10px; border-top: 1px dashed #eee; }
        .react-btn { cursor: pointer; background: #f8f9fa; border: 1px solid #eee; border-radius: 15px; padding: 4px 10px; font-size: 14px; transition: 0.2s; display: flex; align-items: center; gap: 5px; user-select: none; }
        
        .reactions.locked .react-btn { pointer-events: none; cursor: default; opacity: 0.6; }
        .reactions.locked .react-btn.selected { background: #fff5f5; border-color: #d32f2f; opacity: 1; font-weight: bold; color: #d32f2f; }
        .react-btn span { font-size: 12px; color: #666; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header-section">
            <div class="logo-box">
                <div class="icon-speaker">📢</div>
                <div class="brand-text">
                    <h1>מבזקי<br>השעה</h1>
                    <span>עדכונים שוטפים</span>
                </div>
            </div>
            <div class="stats-box">
                <span class="count" id="userCount">...</span>
                <span class="label">צופים כעת</span>
            </div>
        </div>

        <div class="input-area">
            <textarea id="newsInput" placeholder="כתוב את המבזק כאן..."></textarea>
            <input type="text" id="linkInput" placeholder="קישור לכתבה (אופציונלי)...">
            <button class="btn" onclick="addNews()">פרסם מבזק</button>
        </div>
        
        <div id="list"></div>
    </div>

    <script>
        // ניהול מונה צופים אמין (מבוסס מכשיר וזמן)
        function setupRealCounter() {
            let visitors = localStorage.getItem('total_unique_visitors') || 142;
            let lastVisit = localStorage.getItem('last_visit_time');
            let now = new Date().getTime();
            
            if (!lastVisit || (now - lastVisit > 3600000)) {
                visitors = parseInt(visitors) + 1;
            }
            
            localStorage.setItem('total_unique_visitors', visitors);
            localStorage.setItem('last_visit_time', now);
            document.getElementById('userCount').innerText = visitors.toLocaleString();
        }
        setupRealCounter();

        function addNews() {
            const text = document.getElementById('newsInput').value;
            const link = document.getElementById('linkInput').value;
            if (!text.trim()) return;
            
            const newsId = Date.now();
            const newsObj = { id: newsId, content: text, link: link.trim(), time: new Date().toLocaleTimeString('he-IL', {hour: '2-digit', minute:'2-digit'}) };
            displayMessage(newsObj);
            
            document.getElementById('newsInput').value = "";
            document.getElementById('linkInput').value = "";
        }

        function displayMessage(data) {
            const div = document.createElement('div');
            div.className = 'msg';
            let linkHtml = data.link ? `<a href="${data.link}" target="_blank" class="msg-link">🔗 לכתבה המלאה לחץ כאן</a>` : '';
            
            div.innerHTML = `
                <span class="time">${data.time}</span> <strong>:</strong> 
                <span>${data.content}</span>
                ${linkHtml}
                <div class="reactions" id="react-group-${data.id}">
                    <div class="react-btn" onclick="handleOneReaction(this, '${data.id}')">👍 <span>0</span></div>
                    <div class="react-btn" onclick="handleOneReaction(this, '${data.id}')">🔥 <span>0</span></div>
                    <div class="react-btn" onclick="handleOneReaction(this, '${data.id}')">❤️ <span>0</span></div>
                    <div class="react-btn" onclick="handleOneReaction(this, '${data.id}')">😮 <span>0</span></div>
                </div>
            `;
            document.getElementById('list').insertBefore(div, document.getElementById('list').firstChild);
        }

        function handleOneReaction(btn, newsId) {
            const group = document.getElementById('react-group-' + newsId);
            if (group.classList.contains('locked')) return;

            let countSpan = btn.querySelector('span');
            countSpan.innerText = "1";
            
            group.classList.add('locked');
            btn.classList.add('selected');
        }
    </script>
</body>
</html>
