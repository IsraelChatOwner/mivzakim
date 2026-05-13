<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>מבזקי השעה</title>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background-color: #f4f7f9; margin: 0; padding: 20px; }
        .container { max-width: 700px; margin: auto; }
        .blue-bar { background-color: #0056b3; color: white; padding: 12px; text-align: center; font-weight: bold; border-radius: 8px; margin-bottom: 15px; animation: blinkBlue 2s infinite; font-size: 18px; }
        @keyframes blinkBlue { 50% { opacity: 0.6; } }
        .header { background: #002d5a; color: white; padding: 20px; text-align: center; border-radius: 12px; }
        .mivzak { background: white; padding: 15px; border-radius: 10px; margin: 15px 0; border-right: 5px solid #0056b3; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .admin-panel { display: none; background: #fff; border: 2px solid #002d5a; padding: 15px; border-radius: 10px; margin-top: 10px; }
        textarea { width: 100%; height: 80px; border-radius: 5px; border: 1px solid #ccc; padding: 10px; resize: none; box-sizing: border-box; }
        .btn-send { background: #002d5a; color: white; border: none; padding: 10px 20px; cursor: pointer; width: 100%; border-radius: 5px; font-weight: bold; margin-top: 10px; }
        .scroll-down { position: fixed; bottom: 20px; left: 20px; background: #002d5a; color: white; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 24px; box-shadow: 0 4px 8px rgba(0,0,0,0.2); }
        .reaction-btn { cursor: pointer; border: 1px solid #ddd; background: #f9f9f9; padding: 5px 12px; border-radius: 20px; margin-left: 5px; }
        .reaction-btn.disabled { opacity: 0.5; cursor: default; }
    </style>
</head>
<body>
<div class="container">
    <div style="text-align: left;"><span style="color: #ccc; cursor: pointer; font-size: 10px;" onclick="unlockAdmin()">ניהול</span></div>
    <div class="blue-bar">● עדכונים חיים מהשטח ●</div>
    <div class="header"><h1>מבזקי השעה 📢</h1></div>
    <div id="adminArea" class="admin-panel">
        <textarea id="newsContent" placeholder="כתוב מבזק חדש..."></textarea>
        <button class="btn-send" onclick="sendToFirebase()">פרסם מבזק</button>
    </div>
    <div id="newsFeed"></div>
</div>
<div class="scroll-down" onclick="window.scrollTo({top: document.body.scrollHeight, behavior: 'smooth'})">↓</div>

<script>
    const firebaseConfig = {
        apiKey: "AIzaSyCV2ZeLm04DFrDWD1fDgfv0Ft71YrGqpFs",
        authDomain: "mivzakim-f35b2.firebaseapp.com",
        databaseURL: "https://mivzakim-f35b2-default-rtdb.firebaseio.com",
        projectId: "mivzakim-f35b2",
        storageBucket: "mivzakim-f35b2.firebasestorage.app",
        messagingSenderId: "264174571876",
        appId: "1:264174571876:web:16117e4a7e90b7f7c56a71"
    };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    function unlockAdmin() {
        if(prompt("סיסמה:") === "2626") document.getElementById('adminArea').style.display = 'block';
    }

    function sendToFirebase() {
        const txt = document.getElementById('newsContent').value;
        if(!txt) return;
        const time = new Date().toLocaleTimeString('he-IL', {hour:'2-digit', minute:'2-digit'});
        db.ref('news').push({ text: txt, time: time, reactions: 0 });
        document.getElementById('newsContent').value = "";
    }

    db.ref('news').on('child_added', (snap) => {
        const data = snap.val();
        const id = snap.key;
        const div = document.createElement('div');
        div.className = 'mivzak';
        div.innerHTML = `<small>${data.time}</small><p style="font-size:18px;">${data.text}</p>
            <button id="reac-${id}" class="reaction-btn" onclick="addReaction('${id}')">👍 <span id="count-${id}">${data.reactions || 0}</span></button>`;
        document.getElementById('newsFeed').prepend(div);
        if(localStorage.getItem('reacted-'+id)) document.getElementById('reac-'+id).classList.add('disabled');
    });

    function addReaction(id) {
        if(localStorage.getItem('reacted-'+id)) return;
        db.ref('news/'+id+'/reactions').transaction(c => (c || 0) + 1);
        localStorage.setItem('reacted-'+id, 'true');
        document.getElementById('reac-'+id).classList.add('disabled');
    }

    db.ref('news').on('child_changed', (snap) => {
        const id = snap.key;
        const countSpan = document.getElementById('count-'+id);
        if(countSpan) countSpan.innerText = snap.val().reactions || 0;
    });
</script>
</body>
</html>
