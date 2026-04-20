<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カウンセリング予約</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
    <style>
        :root { --main: #185FA5; --bg: #f5f5f5; }
        body { font-family: sans-serif; background: var(--bg); margin: 0; padding: 20px; line-height: 1.6; }
        .container { max-width: 500px; margin: 0 auto; }
        .card { background: #fff; padding: 20px; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1); margin-bottom: 20px; }
        h2 { color: var(--main); font-size: 1.2rem; margin-top: 0; }
        label { display: block; font-size: 0.9rem; font-weight: bold; margin-top: 15px; }
        input, select { width: 100%; padding: 10px; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        .btn { width: 100%; padding: 12px; border: none; border-radius: 6px; font-size: 1rem; cursor: pointer; font-weight: bold; margin-top: 20px; transition: 0.3s; }
        .btn-main { background: var(--main); color: #fff; }
        .btn-sub { background: #eee; color: #333; margin-top: 10px; }
        .btn-danger { background: #ffebee; color: #c62828; font-size: 0.8rem; padding: 5px; width: auto; margin: 0; }
        .screen { display: none; }
        .active { display: block; }
        .admin-item { border-bottom: 1px solid #eee; padding: 10px 0; display: flex; justify-content: space-between; align-items: center; }
        #qr-canvas { display: block; margin: 20px auto; }
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
        <button class="btn btn-sub" onclick="showQR()">QRコードを表示</button>
        <button class="btn btn-sub" onclick="loginAdmin()">管理画面（先生用）</button>
    </div>

    <div id="view-success" class="screen">
        <div class="card" style="text-align: center;">
            <h2>予約完了</h2>
            <p>内容を送信しました。確認をお待ちください。</p>
            <button class="btn btn-main" onclick="location.reload()">戻る</button>
        </div>
    </div>

    <div id="view-qr" class="screen">
        <div class="card" style="text-align: center;">
            <h2>掲示用QRコード</h2>
            <canvas id="qr-canvas"></canvas>
            <button class="btn btn-main" onclick="showView('view-form')">閉じる</button>
        </div>
    </div>

    <div id="view-admin" class="screen">
        <div class="card">
            <h2>予約一覧（管理者）</h2>
            <div id="admin-list">読み込み中...</div>
            <button class="btn btn-sub" onclick="showView('view-form')">戻る</button>
        </div>
    </div>
</div>

<script>
    // ★ここを書き換えてください★
    const GAS_URL = "https://script.google.com/macros/s/AKfycbw8_jLAkqQOijA5LhQQk2H4LTbfv179cfKQoElG_dt8Ez9t_poeEPPGfqm1w6RstsS9gg/exec";
    const ADMIN_PASS = "Fujimura2026";

    function showView(id) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    // 送信処理
    async function sendBooking() {
        const data = {
            type: document.getElementById('field-type').value,
            name: document.getElementById('field-name').value,
            info: document.getElementById('field-info').value,
            date: document.getElementById('field-date').value
        };
        if(!data.name || !data.date) return alert("必須項目を入力してください");

        const btn = document.querySelector('.btn-main');
        btn.disabled = true;
        btn.innerText = "送信中...";

        try {
            await fetch(GAS_URL, { method: "POST", body: JSON.stringify(data) });
            showView('view-success');
        } catch(e) {
            alert("エラーが発生しました");
            btn.disabled = false;
            btn.innerText = "予約を送信する";
        }
    }

    // QR表示
    function showQR() {
        showView('view-qr');
        QRCode.toCanvas(document.getElementById('qr-canvas'), window.location.href, { width: 250 });
    }

    // 管理画面ログイン
    function loginAdmin() {
        const pw = prompt("パスワードを入力");
        if(pw === ADMIN_PASS) {
            showView('view-admin');
            refreshAdminList();
        } else {
            alert("認証失敗");
        }
    }

    // 管理リスト更新
    async function refreshAdminList() {
        const listDiv = document.getElementById('admin-list');
        listDiv.innerText = "取得中...";
        try {
            const res = await fetch(GAS_URL + "?action=getAll");
            const data = await res.json();
            listDiv.innerHTML = "";
            data.forEach(item => {
                const row = document.createElement('div');
                row.className = 'admin-item';
                row.innerHTML = `
                    <div>
                        <small>${item.date}</small><br>
                        <strong>${item.name}</strong> (${item.type})
                    </div>
                    <button class="btn btn-danger" onclick="deleteItem(${item.rowNum})">削除</button>
                `;
                listDiv.appendChild(row);
            });
        } catch(e) {
            listDiv.innerText = "データがありません";
        }
    }

    // 削除処理
    async function deleteItem(num) {
        if(!confirm("削除しますか？")) return;
        await fetch(GAS_URL, { method: "POST", body: JSON.stringify({action: 'delete', rowNum: num}) });
        refreshAdminList();
    }
</script>

</body>
</html>
