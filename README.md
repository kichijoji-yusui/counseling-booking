<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>カウンセリング予約システム</title>
    <script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
    <style>
        *{box-sizing:border-box;margin:0;padding:0}
        body{font-family:-apple-system,BlinkMacSystemFont,'Hiragino Sans','Noto Sans JP',sans-serif;background:#f5f5f3;color:#1a1a1a;font-size:16px;line-height:1.6}
        .wrap{max-width:540px;margin:0 auto;padding:1.5rem 1rem 4rem}
        .screen{display:none}
        .screen.active{display:block}
        .card{background:#fff;border:1px solid #e8e8e4;border-radius:12px;padding:1.25rem;margin-bottom:1rem}
        .page-title{font-size:18px;font-weight:700;margin-bottom:4px;color:#185FA5}
        .page-sub{font-size:13px;color:#888;margin-bottom:1.25rem}
        .field{margin-bottom:1rem}
        .field label{display:block;font-size:13px;font-weight:600;margin-bottom:5px}
        .req{color:#e24b4a;margin-left:4px}
        input, select, textarea{width:100%;padding:10px;border:1px solid #ddd;border-radius:8px;font-size:16px}
        .btn{padding:12px 24px;border-radius:8px;border:1px solid #ddd;cursor:pointer;font-weight:600;transition:0.2s}
        .btn-p{background:#185FA5;color:#fff;border:none;width:100%}
        .btn-d{background:#fee;color:#c00;border:1px solid #fcc;font-size:12px;padding:4px 8px}
        .chips{display:flex;flex-wrap:wrap;gap:8px}
        .chip{padding:8px 16px;border:1px solid #ddd;border-radius:20px;font-size:14px;cursor:pointer;background:#fff}
        .chip.on{background:#e6f0fb;border-color:#185FA5;color:#185FA5}
        .admin-item{border-bottom:1px solid #eee;padding:10px 0;display:flex;justify-content:space-between;align-items:center}
        #qrcode-canvas{margin:20px auto;display:block}
    </style>
</head>
<body>
<div class="wrap">

    <div id="v0" class="screen active">
        <div class="card">
            <p class="page-title">カウンセリング予約</p>
            <p class="page-sub">2026年度 相談窓口</p>
            <div class="field">
                <label>申込者区分<span class="req">*</span></label>
                <div class="chips">
                    <div class="chip" id="type-s" onclick="setType('student')">生徒本人</div>
                    <div class="chip" id="type-p" onclick="setType('parent')">保護者</div>
                </div>
            </div>
            <button class="btn btn-p" onclick="nextToInfo()">次へ進む</button>
        </div>
        <div style="text-align:center;margin-top:20px">
            <button class="btn" onclick="showQRScreen()">QRコードを表示</button>
            <button class="btn" style="margin-left:10px" onclick="showAdminLogin()">管理画面</button>
        </div>
    </div>

    <div id="v1" class="screen">
        <div class="card">
            <p class="page-title">基本情報の入力</p>
            <div id="form-fields"></div>
            <div class="field">
                <label>希望日時<span class="req">*</span></label>
                <input type="datetime-local" id="reserve-date">
            </div>
            <button class="btn btn-p" onclick="submitBooking()">予約を確定する</button>
            <button class="btn" style="width:100%;margin-top:10px" onclick="showScreen('v0')">戻る</button>
        </div>
    </div>

    <div id="v2" class="screen">
        <div class="card" style="text-align:center">
            <p class="page-title">予約が完了しました</p>
            <p>内容を確認し、後ほどご連絡いたします。</p>
            <button class="btn btn-p" style="margin-top:20px" onclick="location.reload()">トップに戻る</button>
        </div>
    </div>

    <div id="v_qr" class="screen">
        <div class="card" style="text-align:center">
            <p class="page-title">予約フォームQRコード</p>
            <canvas id="qrcode-canvas"></canvas>
            <p style="font-size:12px;color:#888">この画面を印刷・掲示して使用してください</p>
            <button class="btn" style="margin-top:20px" onclick="showScreen('v0')">閉じる</button>
        </div>
    </div>

    <div id="v_admin" class="screen">
        <div class="card">
            <p class="page-title">予約管理パネル</p>
            <div id="admin-list">読み込み中...</div>
            <button class="btn" style="width:100%;margin-top:20px" onclick="showScreen('v0')">ログアウト</button>
        </div>
    </div>

</div>

<script>
// ==========================================
// 設定エリア
// ==========================================
const GAS_URL = "https://script.google.com/macros/s/AKfycbxGJcx681iJB_vA-2cd3R-Rt0LeKmBtAB51xzx5BxUQcYv4va9QPRtOJ8du-2BzMdfujg/exec"; 
const ADMIN_PW = "Fujimura2026"; 
// ==========================================

let selectedType = "";

function showScreen(id) {
    document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
    document.getElementById(id).classList.add('active');
}

function setType(type) {
    selectedType = type;
    document.getElementById('type-s').classList.toggle('on', type === 'student');
    document.getElementById('type-p').classList.toggle('on', type === 'parent');
}

function nextToInfo() {
    if(!selectedType) return alert("区分を選択してください");
    const container = document.getElementById('form-fields');
    if(selectedType === 'student') {
        container.innerHTML = `
            <div class="field"><label>氏名</label><input id="name" placeholder="山田 太郎"></div>
            <div class="field"><label>学年クラス</label><input id="info" placeholder="1年A組"></div>
        `;
    } else {
        container.innerHTML = `
            <div class="field"><label>保護者氏名</label><input id="name" placeholder="山田 花子"></div>
            <div class="field"><label>お子様の名前・クラス</label><input id="info" placeholder="1年A組 山田太郎"></div>
        `;
    }
    showScreen('v1');
}

// 予約送信
async function submitBooking() {
    const name = document.getElementById('name').value;
    const date = document.getElementById('reserve-date').value;
    if(!name || !date) return alert("すべて入力してください");

    const payload = {
        action: "add",
        type: selectedType,
        name: name,
        info: document.getElementById('info').value,
        date: date
    };

    try {
        await fetch(GAS_URL, {
            method: "POST",
            body: JSON.stringify(payload)
        });
        showScreen('v2');
    } catch(e) {
        alert("送信に失敗しました");
    }
}

// QRコード表示
function showQRScreen() {
    showScreen('v_qr');
    QRCode.toCanvas(document.getElementById('qrcode-canvas'), window.location.href, { width: 200 });
}

// 管理画面表示
function showAdminLogin() {
    const pw = prompt("管理者パスワードを入力してください");
    if(pw === ADMIN_PW) {
        loadAdminData();
        showScreen('v_admin');
    } else {
        alert("パスワードが違います");
    }
}

async function loadAdminData() {
    const list = document.getElementById('admin-list');
    list.innerHTML = "読み込み中...";
    try {
        const res = await fetch(`${GAS_URL}?action=getAll`);
        const data = await res.json();
        list.innerHTML = data.length ? "" : "予約はありません";
        data.forEach(item => {
            const div = document.createElement('div');
            div.className = "admin-item";
            div.innerHTML = `
                <div>
                    <div style="font-weight:bold">${item.name} (${item.date})</div>
                    <div style="font-size:12px;color:#666">${item.info}</div>
                </div>
                <button class="btn btn-d" onclick="deleteItem(${item.id})">削除</button>
            `;
            list.appendChild(div);
        });
    } catch(e) {
        list.innerHTML = "データの取得に失敗しました。";
    }
}

async function deleteItem(id) {
    if(!confirm("この予約を削除しますか？")) return;
    await fetch(GAS_URL, {
        method: "POST",
        body: JSON.stringify({ action: "delete", id: id })
    });
    loadAdminData();
}
</script>
</body>
</html>
