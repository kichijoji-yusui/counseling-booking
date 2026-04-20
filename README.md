<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カウンセリング予約フォーム</title>
    <style>
        body { font-family: sans-serif; background: #f0f2f5; padding: 20px; color: #333; line-height: 1.6; }
        .card { max-width: 500px; margin: auto; background: white; padding: 25px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h2 { color: #185FA5; text-align: center; margin-bottom: 20px; }
        label { display: block; margin-top: 15px; font-weight: bold; font-size: 0.9em; }
        input, select, textarea { width: 100%; padding: 12px; margin-top: 5px; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 16px; }
        .checkbox-group { display: grid; grid-template-columns: 1fr; gap: 5px; margin-top: 10px; }
        .checkbox-item { display: flex; align-items: center; font-size: 0.9em; cursor: pointer; padding: 5px; border-bottom: 1px solid #f9f9f9; }
        .checkbox-item input { width: auto; margin-right: 10px; }
        .hidden { display: none; }
        .screen { display: none; }
        .active { display: block; }
        button { width: 100%; padding: 15px; background: #185FA5; color: white; border: none; border-radius: 8px; font-size: 17px; font-weight: bold; margin-top: 25px; cursor: pointer; }
        .btn-sub { background: #eee; color: #666; margin-top: 10px; font-size: 0.8em; padding: 10px; }
        .admin-item { border-bottom: 1px solid #eee; padding: 15px 0; display: flex; justify-content: space-between; align-items: center; }
        .btn-del { background: #ffebee; color: #c62828; padding: 5px 10px; font-size: 0.7em; width: auto; margin: 0; border: 1px solid #ffcdd2; }
    </style>
</head>
<body>

<div class="container">
    <div id="view-form" class="screen active">
        <div class="card">
            <h2>カウンセリング予約</h2>
            <label>申込者区分</label>
            <select id="type" onchange="toggleFields()">
                <option value="生徒本人">生徒本人</option>
                <option value="保護者">保護者</option>
            </select>

            <label>お名前</label>
            <input type="text" id="name" placeholder="氏名を入力">

            <div id="student-fields">
                <label>学年・クラス・出席番号</label>
                <input type="text" id="s-info" placeholder="例：2年A組 15番">
            </div>

            <div id="parent-fields" class="hidden">
                <label>お子様の名前・学年・クラス</label>
                <input type="text" id="p-child" placeholder="例：1年B組 山田太郎">
                <label>続柄</label>
                <select id="p-rel">
                    <option value="父">父</option><option value="母">母</option><option value="祖父母">祖父母</option><option value="その他">その他</option>
                </select>
                <label>電話番号</label>
                <input type="tel" id="p-tel" placeholder="090-xxxx-xxxx">
                <label>連絡希望時間帯</label>
                <input type="text" id="p-besttime" placeholder="例：平日の16時以降">
            </div>

            <label>相談内容 (複数選択可)</label>
            <div class="checkbox-group">
                <label class="checkbox-item"><input type="checkbox" name="content" value="対人関係：家族"> ①対人関係：家族</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="対人関係：友人"> ②対人関係：友人</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="対人関係：先生"> ③対人関係：先生</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="対人関係：恋愛"> ④対人関係：恋愛</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="学習・進路"> ⑤学習・進路</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="生活習慣"> ⑥生活習慣</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="自分の性格・特性"> ⑦自分の性格・特性</label>
                <label class="checkbox-item"><input type="checkbox" name="content" value="心の健康"> ⑧心の健康</label>
            </div>

            <label>その他（自由記述）</label>
            <textarea id="memo" rows="2" placeholder="詳細や要望があれば入力してください"></textarea>

            <label>予約日</label>
            <select id="date" onchange="updateTimeSlots()"><option value="">日付を選択してください</option></select>
            <label>予約時間</label>
            <select id="time" disabled><option value="">先に日付を選んでください</option></select>

            <button id="submit-btn" onclick="send()">予約を確定する</button>
            <button class="btn-sub" onclick="loginAdmin()">管理者ログイン</button>
        </div>
    </div>

    <div id="view-admin" class="screen">
        <div class="card">
            <h2>予約一覧（管理者）</h2>
            <div id="admin-list">取得中...</div>
            <button class="btn-sub" onclick="showView('view-form')">ログアウト</button>
        </div>
    </div>
</div>

<script>
    const GAS_URL = "https://script.google.com/macros/s/AKfycbw4OMhFZgDq-xxB3sJmGXk-GCf-0Ie39VyncA3r3Mo3IxRnkpz396I0Vlkd-F2p-sAIJQ/exec"; 
    const ADMIN_PASS = "Fujimura2026"; // 管理用パスワード

    let bookedData = [];
    const schedule = {};
    const start = new Date("2026-04-01"), end = new Date("2026-06-30");

    for (let d = new Date(start); d <= end; d.setDate(d.getDate() + 1)) {
        if (d.getDay() === 6) { 
            const dateStr = d.toISOString().split('T')[0];
            schedule[dateStr] = ["12:00", "13:00", "14:00"];
        }
    }
    schedule["2026-05-02"] = ["12:00"]; schedule["2026-05-09"] = ["12:00"]; schedule["2026-05-23"] = ["12:00"];
    schedule["2026-05-30"] = ["11:00"]; schedule["2026-06-06"] = ["11:00"];
    delete schedule["2026-05-16"];
    schedule["2026-05-14"] = ["14:00", "16:00"];

    window.onload = async () => {
        const dateSelect = document.getElementById('date');
        Object.keys(schedule).sort().forEach(date => {
            const opt = document.createElement('option');
            opt.value = date; opt.innerText = date;
            dateSelect.appendChild(opt);
        });
        try { bookedData = await (await fetch(GAS_URL)).json(); } catch(e) {}
    };

    function showView(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    function toggleFields() {
        const type = document.getElementById('type').value;
        document.getElementById('student-fields').classList.toggle('hidden', type !== '生徒本人');
        document.getElementById('parent-fields').classList.toggle('hidden', type !== '保護者');
    }

    function updateTimeSlots() {
        const dateVal = document.getElementById('date').value;
        const timeSelect = document.getElementById('time');
        timeSelect.innerHTML = "";
        if (!dateVal) { timeSelect.disabled = true; return; }
        schedule[dateVal].forEach(t => {
            const opt = document.createElement('option');
            const isBooked = bookedData.includes(dateVal + " " + t);
            opt.value = t; opt.innerText = isBooked ? t + " (予約済み)" : t + " 〜 " + (parseInt(t)+1) + ":00";
            opt.disabled = isBooked;
            timeSelect.appendChild(opt);
        });
        timeSelect.disabled = false;
    }

    async function send() {
        const name = document.getElementById('name').value;
        if (!name || !document.getElementById('date').value) return alert("必須項目を入力してください。");
        const type = document.getElementById('type').value;
        const payload = {
            type: type, name: name,
            subInfo: (type === '生徒本人') ? document.getElementById('s-info').value : `子:${document.getElementById('p-child').value}(${document.getElementById('p-rel').value})`,
            contact: (type === '生徒本人') ? "生徒本人" : `TEL:${document.getElementById('p-tel').value} / ${document.getElementById('p-besttime').value}`,
            content: Array.from(document.querySelectorAll('input[name="content"]:checked')).map(c => c.value).join(", "),
            memo: document.getElementById('memo').value,
            date: document.getElementById('date').value, time: document.getElementById('time').value
        };
        await fetch(GAS_URL, { method: "POST", body: JSON.stringify(payload) });
        alert("予約を送信しました"); location.reload();
    }

    // 管理画面機能
    function loginAdmin() {
        const pw = prompt("パスワードを入力");
        if(pw === ADMIN_PASS) { showView('view-admin'); refreshAdminList(); }
    }

    async function refreshAdminList() {
        const listDiv = document.getElementById('admin-list');
        listDiv.innerText = "取得中...";
        const res = await fetch(GAS_URL + "?action=getAll");
        const data = await res.json();
        listDiv.innerHTML = data.length ? "" : "予約なし";
        data.forEach(item => {
            const row = document.createElement('div');
            row.className = 'admin-item';
            row.innerHTML = `<div><small>${item.date} ${item.time}</small><br><strong>${item.name}</strong><br><small>${item.content}</small></div>
                             <button class="btn-del" onclick="deleteItem(${item.rowNum})">削除</button>`;
            listDiv.appendChild(row);
        });
    }

    async function deleteItem(num) {
        if(confirm("削除しますか？")) {
            await fetch(GAS_URL, { method: "POST", body: JSON.stringify({action: 'delete', rowNum: num}) });
            refreshAdminList();
        }
    }
</script>
</body>
</html>
