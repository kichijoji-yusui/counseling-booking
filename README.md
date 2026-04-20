<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カウンセリング予約</title>
    <style>
        :root { --main: #185FA5; --bg: #f5f5f5; }
        body { font-family: sans-serif; background: var(--bg); margin: 0; padding: 20px; line-height: 1.6; }
        .container { max-width: 500px; margin: 0 auto; }
        .card { background: #fff; padding: 20px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 20px; }
        h2 { color: var(--main); font-size: 1.2rem; margin-top: 0; }
        label { display: block; font-size: 0.8rem; font-weight: bold; margin-top: 15px; }
        input, select { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        .btn { width: 100%; padding: 12px; border: none; border-radius: 6px; font-size: 1rem; cursor: pointer; font-weight: bold; margin-top: 20px; transition: 0.3s; }
        .btn-main { background: var(--main); color: #fff; }
        .btn-sub { background: #eee; color: #333; margin-top: 10px; }
        .btn-danger { background: #ffebee; color: #c62828; font-size: 0.75rem; padding: 5px 10px; width: auto; margin: 0; border: 1px solid #ef9a9a; }
        .screen { display: none; }
        .active { display: block; }
        .admin-item { border-bottom: 1px solid #eee; padding: 12px 0; display: flex; justify-content: space-between; align-items: center; }
    </style>
</head>
<body>

<div class="container">
    <div id="view-form" class="screen active">
        <div class="card">
            <h2>カウンセリング予約フォーム</h2>
            <label>区分</label>
            <select id="field-type">
                <option value="student">生徒本人</option>
                <option value="parent">保護者</option>
            </select>
            
            <label>お名前</label>
            <input type="text" id="field-name" placeholder="氏名を入力">
            
            <label>学年クラス / 連絡先</label>
            <input type="text" id="field-info" placeholder="例：1年A組 / 090-xxxx-xxxx">
            
            <label>希望日時</label>
            <input type="datetime-local" id="field-date">
            
            <button class="btn btn-main" onclick="sendBooking()">予約を送信する</button>
        </div>
        <button class="btn btn-sub" onclick="loginAdmin()">管理画面（先生用ログイン）</button>
    </div>

    <div id="view-success" class="screen">
        <div class="card" style="text-align: center;">
            <h2>予約完了</h2>
            <p>内容を送信しました。<br>確認後、担当よりご連絡いたします。</p>
            <button class="btn btn-main" onclick="location.reload()">戻る</button>
        </div>
    </div>

    <div id="view-admin" class="screen">
        <div class="card">
            <h2>予約一覧（管理者）</h2>
            <div id="admin-list">読み込み中...</div>
            <button class="btn btn-sub" onclick="showView('view-form')">ログアウト</button>
        </div>
    </div>
</div>

<script>
    // ★設定項目
    const GAS_URL = "https://script.google.com/macros/s/AKfycbw8_jLAkqQOijA5LhQQk2H4LTbfv179cfKQoElG_dt8Ez9t_poeEPPGfqm1w6RstsS9gg/exec";
    const ADMIN_PASS = "Fujimura2026";

    function showView(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    // 予約を送信
    async function sendBooking() {
        const data = {
            type: document.getElementById('field-type').value,
            name: document.getElementById('field-name').value,
            info: document.getElementById('field-info').value,
            date: document.getElementById('field-date').value
        };
        if(!data.name || !data.date) return alert("名前と日時は必須です");

        const btn = document.querySelector('.btn-main');
        btn.disabled = true;
        btn.innerText = "送信中...";

        try {
            await fetch(GAS_URL, { method: "POST", body: JSON.stringify(data) });
            showView('view-success');
        } catch(e) {
            alert("エラーが発生しました。接続を確認してください。");
            btn.disabled = false;
            btn.innerText = "予約を送信する";
        }
    }

    // 管理画面へ
    function loginAdmin() {
        const pw = prompt("パスワードを入力してください");
        if(pw === ADMIN_PASS) {
            showView('view-admin');
            refreshAdminList();
        } else if(pw !== null) {
            alert("パスワードが違います");
        }
    }

    // 予約リストの読み込み・表示
    async function refreshAdminList() {
        const listDiv = document.getElementById('admin-list');
        listDiv.innerText = "データ取得中...";
        try {
            const res = await fetch(GAS_URL + "?action=getAll");
            const data = await res.json();
            listDiv.innerHTML = "";
            if(data.length === 0) {
                listDiv.innerText = "現在の予約はありません";
                return;
            }
            data.forEach(item => {
                const row = document.createElement('div');
                row.className = 'admin-item';
                row.innerHTML = `
                    <div style="font-size: 0.9rem;">
                        <strong>${item.name}</strong> (${item.type})<br>
                        📅 ${item.date.replace('T', ' ')}<br>
                        memo: ${item.info}
                    </div>
                    <button class="btn btn-danger" onclick="deleteItem(${item.rowNum})">削除</button>
                `;
                listDiv.appendChild(row);
            });
        } catch(e) {
            listDiv.innerText = "データの取得に失敗しました";
        }
    }

    // 予約の削除
    async function deleteItem(num) {
        if(!confirm("この予約データを完全に削除してもよろしいですか？")) return;
        try {
            await fetch(GAS_URL, { 
                method: "POST", 
                body: JSON.stringify({action: 'delete', rowNum: num}) 
            });
            refreshAdminList();
        } catch(e) {
            alert("削除に失敗しました");
        }
    }
</script>
</body>
</html>
