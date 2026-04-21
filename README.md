<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>カウンセリング予約</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{font-family:-apple-system,BlinkMacSystemFont,'Hiragino Sans','Noto Sans JP',sans-serif;background:#f5f5f3;color:#1a1a1a;font-size:16px;line-height:1.6}
.wrap{max-width:540px;margin:0 auto;padding:1.5rem 1rem 4rem}
.screen{display:none}.screen.active{display:block}
.card{background:#fff;border:1px solid #e8e8e4;border-radius:12px;padding:1.25rem;margin-bottom:1rem}
.page-title{font-size:18px;font-weight:500;margin-bottom:4px}
.page-sub{font-size:13px;color:#888;margin-bottom:1.25rem}
.steps{display:flex;align-items:center;gap:6px;margin-bottom:1.25rem}
.sn{width:26px;height:26px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:12px;font-weight:500;background:#f0f0ec;color:#888;border:1px solid #ddd;flex-shrink:0}
.sn.active{background:#185FA5;color:#fff;border-color:transparent}
.sn.done{background:#e6f4d7;color:#2d6a0a;border-color:transparent}
.sl{flex:1;height:1px;background:#e0e0dc}
.sec{font-size:11px;font-weight:600;color:#aaa;letter-spacing:.08em;text-transform:uppercase;margin-bottom:10px;padding-bottom:8px;border-bottom:1px solid #f0f0ec}
.field{margin-bottom:.875rem}
.field label{display:block;font-size:13px;font-weight:500;color:#555;margin-bottom:5px}
.req{color:#e24b4a}
.field input,.field select,.field textarea{width:100%;padding:9px 11px;border:1px solid #ddd;border-radius:8px;font-size:14px;color:#1a1a1a;background:#fff;-webkit-appearance:none}
.field input:focus,.field select:focus{outline:none;border-color:#185FA5}
.row2{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.chips{display:flex;flex-wrap:wrap;gap:6px;margin-top:6px}
.chip{padding:6px 13px;border:1px solid #ddd;border-radius:20px;font-size:13px;cursor:pointer;color:#555;background:#fff;user-select:none;-webkit-user-select:none}
.chip.on{background:#e6f0fb;border-color:#378ADD;color:#0C447C}
.chip-gl{font-size:11px;color:#aaa;font-weight:600;margin-top:10px;margin-bottom:2px;width:100%}
.btn-row{display:flex;justify-content:space-between;align-items:center;margin-top:1.25rem;gap:8px}
.btn{padding:10px 20px;border-radius:8px;font-size:14px;font-weight:500;cursor:pointer;border:1px solid #ddd;background:#fff;color:#1a1a1a}
.btn-p{background:#185FA5;color:#fff;border-color:transparent}
.btn-p:disabled{opacity:.5;cursor:not-allowed}
.cal-nav{display:flex;align-items:center;justify-content:space-between;margin-bottom:.75rem}
.cal-nav button{background:#fff;border:1px solid #ddd;border-radius:8px;padding:5px 14px;font-size:14px;cursor:pointer;color:#1a1a1a}
.cal-nav button:disabled{opacity:.35;cursor:not-allowed}
.cal-wrap{border:1px solid #e8e8e4;border-radius:12px;overflow:hidden;margin-bottom:1rem}
.cal-head{display:grid;grid-template-columns:repeat(7,1fr);background:#f8f8f5}
.cal-dl{text-align:center;font-size:11px;color:#888;padding:8px 0;font-weight:600}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr)}
.cal-c{text-align:center;padding:9px 0;font-size:13px;border-top:1px solid #f0f0ec;color:#ccc}
.cal-c.avail{color:#185FA5;cursor:pointer;font-weight:600;background:#fff}
.cal-c.avail:hover{background:#e6f0fb}
.cal-c.sel{background:#185FA5;color:#fff;font-weight:600}
.cal-c.full{background:#f8f8f5;color:#bbb;font-size:11px}
.slots{display:flex;gap:8px;flex-wrap:wrap;margin-top:.75rem}
.slot{padding:8px 16px;border:1px solid #ddd;border-radius:8px;font-size:14px;cursor:pointer;color:#1a1a1a;background:#fff}
.slot:hover{background:#e6f0fb;border-color:#378ADD;color:#0C447C}
.slot.sel{background:#185FA5;color:#fff;border-color:transparent}
.slot.full{background:#f8f8f5;color:#bbb;cursor:not-allowed;text-decoration:line-through}
.consent-box{background:#f8f8f5;border-radius:8px;padding:.875rem;font-size:12px;color:#666;line-height:1.7;margin-bottom:.75rem}
.ok-icon{width:52px;height:52px;background:#e6f4d7;border-radius:50%;display:flex;align-items:center;justify-content:center;margin:0 auto 1rem}
.ok-icon svg{width:26px;height:26px;stroke:#2d6a0a;fill:none;stroke-width:2.5;stroke-linecap:round;stroke-linejoin:round}
.conf-row{display:flex;padding:8px 0;border-bottom:1px solid #f0f0ec;font-size:13px}
.conf-l{color:#888;width:100px;flex-shrink:0}
.conf-v{color:#1a1a1a;font-weight:500;word-break:break-all}
.err{font-size:12px;color:#e24b4a;margin-top:5px}
.tabs{display:flex;gap:6px;margin-bottom:1rem;flex-wrap:wrap}
.tab{padding:6px 14px;border-radius:8px;font-size:13px;cursor:pointer;border:1px solid #ddd;background:#fff;color:#888}
.tab.on{background:#185FA5;color:#fff;border-color:transparent;font-weight:500}
.aitem{border:1px solid #e8e8e4;border-radius:10px;margin-bottom:8px;overflow:hidden}
.aitem-head{padding:11px 13px;display:flex;justify-content:space-between;align-items:flex-start;gap:8px;cursor:pointer;background:#fff}
.aitem-head:hover{background:#f8f8f5}
.aitem-body{padding:10px 13px;border-top:1px solid #f0f0ec;background:#f8f8f5;display:none}
.aitem-body.open{display:block}
.aitem-row{display:flex;font-size:12px;padding:3px 0;gap:8px}
.aitem-lbl{color:#888;width:88px;flex-shrink:0}
.aitem-val{color:#1a1a1a}
.badge{font-size:11px;padding:3px 10px;border-radius:20px;font-weight:600;white-space:nowrap;flex-shrink:0}
.badge-new{background:#e6f0fb;color:#0C447C}
.badge-done{background:#e6f4d7;color:#2d6a0a}
.empty-msg{text-align:center;padding:2.5rem;font-size:13px;color:#aaa}
.pw-wrap{display:flex;flex-direction:column;align-items:center;padding:2rem;gap:1rem}
.stat-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:1.25rem}
.stat-card{background:#f8f8f5;border-radius:8px;padding:.875rem;text-align:center}
.stat-num{font-size:26px;font-weight:500;color:#1a1a1a}
.stat-lbl{font-size:11px;color:#888;margin-top:2px}
.loading{text-align:center;padding:1.5rem;font-size:13px;color:#aaa}
</style>
</head>
<body>
<div class="wrap">
 
  <!-- 予約フォーム -->
  <div id="screen-booking" class="screen active">
    <div id="v0" style="display:block">
      <div class="card">
        <p class="page-title">カウンセリング予約</p>
        <p class="page-sub">2026年4月〜6月　毎週土曜日</p>
        <div class="field" style="margin-bottom:1rem">
          <label>申込者を選んでください <span class="req">*</span></label>
          <div class="chips">
            <div class="chip" id="chip-s" onclick="pickType('student')">生徒本人</div>
            <div class="chip" id="chip-p" onclick="pickType('parent')">保護者</div>
          </div>
          <div class="err" id="err-type" style="display:none">選択してください</div>
        </div>
        <p style="font-size:13px;color:#888;line-height:1.7">入力内容は担当カウンセラーのみ確認します。どうぞ気軽にご相談ください。</p>
      </div>
      <div class="btn-row" style="justify-content:flex-end">
        <button class="btn btn-p" onclick="step0next()">次へ</button>
      </div>
    </div>
 
    <div id="v1" style="display:none">
      <div class="steps"><div class="sn done">1</div><div class="sl"></div><div class="sn active">2</div><div class="sl"></div><div class="sn">3</div><div class="sl"></div><div class="sn">4</div></div>
      <div class="card" id="sf">
        <div class="sec">生徒の基本情報</div>
        <div class="row2">
          <div class="field"><label>氏名 <span class="req">*</span></label><input id="s-name" type="text" placeholder="山田 太郎"></div>
          <div class="field"><label>ふりがな <span class="req">*</span></label><input id="s-kana" type="text" placeholder="やまだ たろう"></div>
        </div>
        <div class="row2">
          <div class="field"><label>学年 <span class="req">*</span></label>
            <select id="s-grade"><option value="">選択</option><option>1年</option><option>2年</option><option>3年</option></select>
          </div>
          <div class="field"><label>クラス <span class="req">*</span></label>
            <select id="s-class"><option value="">選択</option><option>1組</option><option>2組</option><option>3組</option><option>4組</option></select>
          </div>
        </div>
        <div class="field"><label>出席番号</label><input id="s-num" type="text" placeholder="例：15"></div>
      </div>
      <div class="card" id="pf" style="display:none">
        <div class="sec">保護者情報</div>
        <div class="row2">
          <div class="field"><label>保護者氏名 <span class="req">*</span></label><input id="p-name" type="text" placeholder="山田 花子"></div>
          <div class="field"><label>ふりがな <span class="req">*</span></label><input id="p-kana" type="text" placeholder="やまだ はなこ"></div>
        </div>
        <div class="row2">
          <div class="field"><label>続柄 <span class="req">*</span></label>
            <select id="p-rel"><option value="">選択</option><option>父</option><option>母</option><option>祖父</option><option>祖母</option><option>その他</option></select>
          </div>
          <div class="field"><label>連絡先（電話） <span class="req">*</span></label><input id="p-tel" type="tel" placeholder="090-0000-0000"></div>
        </div>
        <div class="field"><label>連絡先（メール）</label><input id="p-mail" type="email" placeholder="example@email.com"></div>
        <div class="field"><label>連絡可能な時間帯 <span class="req">*</span></label>
          <div class="chips" id="p-times">
            <div class="chip" onclick="toggleChip(this)">平日午前</div>
            <div class="chip" onclick="toggleChip(this)">平日昼</div>
            <div class="chip" onclick="toggleChip(this)">平日夕方（16時〜）</div>
            <div class="chip" onclick="toggleChip(this)">土曜</div>
          </div>
        </div>
        <div class="sec" style="margin-top:1rem">お子さんの情報</div>
        <div class="row2">
          <div class="field"><label>お子さんのお名前 <span class="req">*</span></label><input id="c-name" type="text" placeholder="山田 太郎"></div>
          <div class="field"><label>学年・クラス <span class="req">*</span></label><input id="c-class" type="text" placeholder="例：3年2組"></div>
        </div>
        <div class="field"><label>お子さんは面談を知っていますか <span class="req">*</span></label>
          <div class="chips" id="c-know">
            <div class="chip" onclick="pickOne(this,'c-know')">知っている</div>
            <div class="chip" onclick="pickOne(this,'c-know')">知らない</div>
            <div class="chip" onclick="pickOne(this,'c-know')">これから話す予定</div>
          </div>
        </div>
      </div>
      <div class="btn-row">
        <button class="btn" onclick="showStep('v0')">戻る</button>
        <button class="btn btn-p" onclick="step1next()">次へ</button>
      </div>
    </div>
 
    <div id="v2" style="display:none">
      <div class="steps"><div class="sn done">1</div><div class="sl"></div><div class="sn done">2</div><div class="sl"></div><div class="sn active">3</div><div class="sl"></div><div class="sn">4</div></div>
      <div class="card">
        <div class="sec">相談内容</div>
        <div class="field">
          <label>悩みの種類 <span class="req">*</span>　<span style="font-size:11px;color:#aaa;font-weight:400">複数選択可</span></label>
          <div class="chips" id="consult-types">
            <div class="chip-gl">対人関係</div>
            <div class="chip" onclick="toggleChip(this)">① 家族</div>
            <div class="chip" onclick="toggleChip(this)">② 友人</div>
            <div class="chip" onclick="toggleChip(this)">③ 先生</div>
            <div class="chip" onclick="toggleChip(this)">④ 恋愛</div>
            <div class="chip-gl">その他</div>
            <div class="chip" onclick="toggleChip(this)">⑤ 学習・進路</div>
            <div class="chip" onclick="toggleChip(this)">⑥ 生活習慣</div>
            <div class="chip" onclick="toggleChip(this)">⑦ 自分の性格・特性</div>
            <div class="chip" onclick="toggleChip(this)">⑧ 心の健康</div>
            <div class="chip" id="chip-other-cat" onclick="toggleChipOther(this)">その他</div>
          </div>
          <div class="err" id="err-consult" style="display:none">1つ以上選択してください</div>
        </div>
        <div class="field" id="other-note-field" style="display:none">
          <label>その他の内容（簡単に）</label>
          <input id="other-note" type="text" placeholder="例：部活のこと、先生との関係など">
        </div>
        <div class="field"><label>面談形式の希望</label>
          <div class="chips" id="format">
            <div class="chip" onclick="pickOne(this,'format')">対面</div>
            <div class="chip" onclick="pickOne(this,'format')">オンライン</div>
            <div class="chip" onclick="pickOne(this,'format')">どちらでも</div>
          </div>
        </div>
      </div>
      <div class="btn-row">
        <button class="btn" onclick="showStep('v1')">戻る</button>
        <button class="btn btn-p" onclick="step2next()">次へ</button>
      </div>
    </div>
 
    <div id="v3" style="display:none">
      <div class="steps"><div class="sn done">1</div><div class="sl"></div><div class="sn done">2</div><div class="sl"></div><div class="sn done">3</div><div class="sl"></div><div class="sn active">4</div></div>
      <div class="card">
        <div class="sec">希望日時を選択</div>
        <p style="font-size:13px;color:#888;margin-bottom:.875rem">予約可能な日は青字の土曜日です（6月末まで）。予約済みの枠は表示されません。</p>
        <div class="cal-nav">
          <button id="btn-prev" onclick="prevMonth()">＜</button>
          <span style="font-size:14px;font-weight:500" id="cal-month-label"></span>
          <button id="btn-next" onclick="nextMonth()">＞</button>
        </div>
        <div class="cal-wrap">
          <div class="cal-head">
            <div class="cal-dl" style="color:#e24b4a">日</div>
            <div class="cal-dl">月</div><div class="cal-dl">火</div><div class="cal-dl">水</div><div class="cal-dl">木</div><div class="cal-dl">金</div>
            <div class="cal-dl" style="color:#185FA5">土</div>
          </div>
          <div class="cal-grid" id="cal"></div>
        </div>
        <div id="slot-area" style="display:none">
          <p style="font-size:13px;font-weight:500;margin-bottom:6px" id="slot-label"></p>
          <div class="slots" id="slots"></div>
        </div>
        <div style="margin-top:1.25rem;border-top:1px solid #f0f0ec;padding-top:1rem">
          <div class="consent-box" id="consent-student" style="display:none">入力いただいた情報は、カウンセリングの実施・記録のみに使用します。</div>
          <div class="consent-box" id="consent-parent" style="display:none">入力いただいた情報は、カウンセリングの実施・記録のみに使用します。お子さんの安全に関わる場合（虐待の疑い等）は、法令に基づき関係機関に情報を提供することがあります。</div>
          <label style="display:flex;align-items:flex-start;gap:8px;font-size:13px;cursor:pointer">
            <input type="checkbox" id="consent" style="margin-top:3px;width:auto"> 個人情報の取り扱いに同意します
          </label>
          <div class="err" id="err-submit" style="display:none;margin-top:6px"></div>
        </div>
      </div>
      <div class="btn-row">
        <button class="btn" onclick="showStep('v2')">戻る</button>
        <button class="btn btn-p" id="submit-btn" onclick="submitBooking()">予約を確定する</button>
      </div>
    </div>
 
    <div id="v4" style="display:none">
      <div class="card" style="text-align:center;padding:2rem">
        <div class="ok-icon"><svg viewBox="0 0 24 24"><polyline points="20 6 9 17 4 12"></polyline></svg></div>
        <p style="font-size:17px;font-weight:500;margin-bottom:6px">予約が完了しました</p>
        <p style="font-size:13px;color:#888;margin-bottom:1.25rem">内容を確認次第、ご連絡いたします。</p>
        <div id="confirm-detail" style="margin-bottom:1.5rem;text-align:left"></div>
        <button class="btn" onclick="resetForm()">新しく予約する</button>
      </div>
    </div>
  </div>
 
  <!-- 管理画面ログイン -->
  <div id="screen-admin-login" class="screen">
    <div class="card">
      <div class="sec">管理画面ログイン</div>
      <div class="pw-wrap">
        <p style="font-size:14px;color:#888">スタッフ専用です。パスワードを入力してください。</p>
        <input type="password" id="pw-input" placeholder="パスワード"
          style="width:200px;padding:9px 12px;border:1px solid #ddd;border-radius:8px;font-size:15px;text-align:center">
        <div class="err" id="err-pw" style="display:none">パスワードが違います</div>
        <button class="btn btn-p" onclick="tryLogin()">ログイン</button>
        <button class="btn" style="font-size:13px" onclick="showScreen('screen-booking')">予約フォームに戻る</button>
      </div>
    </div>
  </div>
 
  <!-- 管理画面 -->
  <div id="screen-admin" class="screen">
    <div class="card">
      <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:1rem;flex-wrap:wrap;gap:8px">
        <p style="font-size:17px;font-weight:500">管理画面</p>
        <div style="display:flex;gap:6px">
          <button class="btn" style="font-size:12px;padding:6px 12px" onclick="renderAdmin()">更新</button>
          <button class="btn" style="font-size:12px;padding:6px 12px" onclick="adminLogout()">ログアウト</button>
        </div>
      </div>
      <div class="stat-grid" id="stat-grid"></div>
      <div class="tabs">
        <button class="tab on" onclick="adminTab('upcoming',this)">対応待ち</button>
        <button class="tab" onclick="adminTab('all',this)">すべて</button>
        <button class="tab" onclick="adminTab('done',this)">完了済み</button>
      </div>
      <div id="admin-list"></div>
    </div>
  </div>
 
  <!-- フッター：管理画面リンク -->
  <div style="text-align:center;margin-top:2rem">
    <button class="btn" style="font-size:12px;padding:5px 14px;color:#bbb;border-color:#e0e0dc"
      onclick="showScreen('screen-admin-login')">スタッフ管理画面</button>
  </div>
 
</div>
<script>
// ============================================================
// ★ GASデプロイ後のURLをここに貼り付けてください ★
var GAS_URL = 'https://script.google.com/macros/s/AKfycbwP39-9zーjStsXnGyt9SEXopud_58xM4rdXMvKc8bwuAbnpNOkudW2OfdhJuo8MAczt/exec';
// 管理画面パスワード（変更推奨）
var ADMIN_PW = 'Fujimura2026';
// ============================================================
 
// 予約可能日程（月は0始まり: 3=4月, 4=5月, 5=6月）
var SCHEDULE = {
  '2026-3-25': ['12:00','13:00','14:00'],
  '2026-4-2':  ['12:00'],
  '2026-4-9':  ['12:00'],
  '2026-4-14': ['14:00','16:00'],
  '2026-4-16': null,
  '2026-4-23': ['12:00'],
  '2026-4-30': ['11:00'],
  '2026-5-6':  ['11:00'],
  '2026-5-13': ['12:00','13:00','14:00'],
  '2026-5-20': ['12:00','13:00','14:00'],
  '2026-5-27': ['12:00','13:00','14:00']
};
 
var APP={type:'',selDate:null,selDateKey:'',selTime:'',adminFilter:'upcoming',calYear:2026,calMonth:3,bookedMap:{}};
var MONTHS=['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月'];
 
function gasGet(params){
  var qs=Object.keys(params).map(function(k){return encodeURIComponent(k)+'='+encodeURIComponent(params[k])}).join('&');
  return fetch(GAS_URL+'?'+qs).then(function(r){return r.json()});
}
 
function showScreen(id){
  document.querySelectorAll('.screen').forEach(function(s){s.classList.remove('active')});
  document.getElementById(id).classList.add('active');
}
 
function showStep(id){
  ['v0','v1','v2','v3','v4'].forEach(function(v){
    document.getElementById(v).style.display=v===id?'block':'none';
  });
  if(id==='v3')buildCal();
}
 
function pickType(t){
  APP.type=t;
  document.getElementById('chip-s').classList.toggle('on',t==='student');
  document.getElementById('chip-p').classList.toggle('on',t==='parent');
  document.getElementById('err-type').style.display='none';
}
function step0next(){
  if(!APP.type){document.getElementById('err-type').style.display='';return;}
  document.getElementById('sf').style.display=APP.type==='student'?'block':'none';
  document.getElementById('pf').style.display=APP.type==='parent'?'block':'none';
  document.getElementById('consent-student').style.display=APP.type==='student'?'block':'none';
  document.getElementById('consent-parent').style.display=APP.type==='parent'?'block':'none';
  showStep('v1');
}
 
function step1next(){
  if(APP.type==='student'){
    if(!document.getElementById('s-name').value.trim()||!document.getElementById('s-kana').value.trim()||
       !document.getElementById('s-grade').value||!document.getElementById('s-class').value){
      alert('必須項目をすべて入力してください');return;
    }
  }
  if(APP.type==='parent'){
    if(!document.getElementById('p-name').value.trim()||!document.getElementById('p-kana').value.trim()||
       !document.getElementById('p-rel').value||!document.getElementById('p-tel').value.trim()){
      alert('必須項目をすべて入力してください');return;
    }
  }
  showStep('v2');
}
 
function step2next(){
  var types=[];
  document.querySelectorAll('#consult-types .chip.on').forEach(function(c){types.push(c.textContent.trim())});
  if(!types.length){document.getElementById('err-consult').style.display='';return;}
  document.getElementById('err-consult').style.display='none';
  showStep('v3');
}
 
function toggleChip(el){el.classList.toggle('on')}
function toggleChipOther(el){
  el.classList.toggle('on');
  document.getElementById('other-note-field').style.display=el.classList.contains('on')?'block':'none';
}
function pickOne(el,grp){
  document.getElementById(grp).querySelectorAll('.chip').forEach(function(c){c.classList.remove('on')});
  el.classList.add('on');
}
 
function dk(y,m,d){return y+'-'+m+'-'+d}
function updateNav(){
  document.getElementById('btn-prev').disabled=(APP.calYear===2026&&APP.calMonth<=3);
  document.getElementById('btn-next').disabled=(APP.calYear===2026&&APP.calMonth>=5);
}
function prevMonth(){
  APP.calMonth--;if(APP.calMonth<0){APP.calMonth=11;APP.calYear--;}
  APP.selDate=null;APP.selTime='';document.getElementById('slot-area').style.display='none';buildCal();
}
function nextMonth(){
  APP.calMonth++;if(APP.calMonth>11){APP.calMonth=0;APP.calYear++;}
  APP.selDate=null;APP.selTime='';document.getElementById('slot-area').style.display='none';buildCal();
}
 
function buildCal(){
  var y=APP.calYear,m=APP.calMonth;
  document.getElementById('cal-month-label').textContent=y+'年'+MONTHS[m];
  updateNav();
  APP.bookedMap={};
  gasGet({action:'getBooked'}).then(function(data){
    data.forEach(function(row){
      if(!APP.bookedMap[row.dateKey])APP.bookedMap[row.dateKey]=[];
      APP.bookedMap[row.dateKey].push(row.time);
    });
    renderCal(y,m);
  }).catch(function(){renderCal(y,m)});
}
 
function renderCal(y,m){
  var grid=document.getElementById('cal');grid.innerHTML='';
  var firstDow=new Date(y,m,1).getDay();
  var lastDay=new Date(y,m+1,0).getDate();
  for(var i=0;i<firstDow;i++){var e=document.createElement('div');e.className='cal-c';grid.appendChild(e);}
  for(var d=1;d<=lastDay;d++){
    var cell=document.createElement('div');
    var key=dk(y,m,d);
    if(key in SCHEDULE){
      var slots=SCHEDULE[key];
      if(slots===null){
        cell.className='cal-c';cell.textContent=d;
      } else {
        var booked=APP.bookedMap[key]||[];
        var availSlots=slots.filter(function(t){return booked.indexOf(t)<0});
        if(availSlots.length===0){
          cell.className='cal-c full';cell.innerHTML='<span style="font-size:10px">満</span><br>'+d;
        } else {
          cell.className='cal-c avail';cell.textContent=d;
          (function(day,k,sl){cell.onclick=function(){selectDate(day,k,sl,cell)}})(d,key,availSlots);
        }
      }
    } else {
      cell.className='cal-c';cell.textContent=d;
    }
    grid.appendChild(cell);
  }
}
 
function selectDate(d,key,slots,cell){
  document.querySelectorAll('.cal-c.sel').forEach(function(c){c.classList.remove('sel');c.classList.add('avail')});
  cell.classList.remove('avail');cell.classList.add('sel');
  APP.selDate=d;APP.selDateKey=key;APP.selTime='';
  var dow=['日','月','火','水','木','金','土'][new Date(APP.calYear,APP.calMonth,d).getDay()];
  document.getElementById('slot-label').textContent=APP.calYear+'年'+(APP.calMonth+1)+'月'+d+'日（'+dow+'）の空き枠';
  document.getElementById('slot-area').style.display='block';
  var booked=APP.bookedMap[key]||[];
  var slotsEl=document.getElementById('slots');slotsEl.innerHTML='';
  slots.forEach(function(t){
    var s=document.createElement('div');
    var label=t+'〜'+(parseInt(t)+1)+':00';
    if(booked.indexOf(t)>=0){return;}
    s.className='slot';s.textContent=label;
    s.onclick=function(){
      document.querySelectorAll('.slot').forEach(function(x){x.classList.remove('sel')});
      s.classList.add('sel');APP.selTime=t;
    };
    slotsEl.appendChild(s);
  });
}
 
function submitBooking(){
  var err=document.getElementById('err-submit');
  if(!APP.selDate){err.textContent='日付を選択してください';err.style.display='';return;}
  if(!APP.selTime){err.textContent='時間を選択してください';err.style.display='';return;}
  if(!document.getElementById('consent').checked){err.textContent='同意のチェックが必要です';err.style.display='';return;}
  err.style.display='none';
  var btn=document.getElementById('submit-btn');
  btn.textContent='送信中...';btn.disabled=true;
 
  var y=APP.calYear,m=APP.calMonth,d=APP.selDate;
  var dow=['日','月','火','水','木','金','土'][new Date(y,m,d).getDay()];
  var dateLabel=y+'年'+(m+1)+'月'+d+'日（'+dow+'）';
  var timeLabel=APP.selTime+'〜'+(parseInt(APP.selTime)+1)+':00';
  var name=APP.type==='student'
    ?(document.getElementById('s-name').value||'未入力')
    :(document.getElementById('p-name').value||'未入力')+'（保護者）';
  var types=[];
  document.querySelectorAll('#consult-types .chip.on').forEach(function(c){types.push(c.textContent.trim())});
  var otherNote=document.getElementById('other-note').value.trim();
  if(otherNote)types=types.map(function(t){return t==='その他'?'その他（'+otherNote+'）':t});
  var fmt=document.querySelector('#format .chip.on');
  var info={};
  if(APP.type==='student'){
    info={grade:document.getElementById('s-grade').value,cls:document.getElementById('s-class').value,num:document.getElementById('s-num').value};
  } else {
    var ksel=document.querySelector('#c-know .chip.on');
    info={kana:document.getElementById('p-kana').value,rel:document.getElementById('p-rel').value,
      tel:document.getElementById('p-tel').value,mail:document.getElementById('p-mail').value,
      child:document.getElementById('c-name').value,childClass:document.getElementById('c-class').value,
      know:ksel?ksel.textContent:''};
  }
 
  gasGet({action:'addBooking',type:APP.type,name:name,dateKey:APP.selDateKey,dateLabel:dateLabel,
    time:APP.selTime,timeLabel:timeLabel,consultTypes:types.join('・'),
    format:fmt?fmt.textContent:'未指定',info:JSON.stringify(info),
    createdAt:new Date().toLocaleDateString('ja-JP')
  }).then(function(){
    btn.textContent='予約を確定する';btn.disabled=false;
    document.getElementById('confirm-detail').innerHTML=
      '<div class="conf-row"><span class="conf-l">日時</span><span class="conf-v">'+dateLabel+' '+timeLabel+'</span></div>'+
      '<div class="conf-row"><span class="conf-l">お名前</span><span class="conf-v">'+name+'</span></div>'+
      '<div class="conf-row"><span class="conf-l">悩みの種類</span><span class="conf-v">'+types.join('・')+'</span></div>'+
      '<div class="conf-row" style="border:none"><span class="conf-l">場所</span><span class="conf-v">三鷹校舎2階保健室</span></div>';
    showStep('v4');
  }).catch(function(){
    btn.textContent='予約を確定する';btn.disabled=false;
    err.textContent='送信に失敗しました。もう一度お試しください。';err.style.display='';
  });
}
 
function resetForm(){
  APP.type='';APP.selDate=null;APP.selTime='';
  document.getElementById('chip-s').classList.remove('on');
  document.getElementById('chip-p').classList.remove('on');
  document.getElementById('consent').checked=false;
  document.querySelectorAll('.chip.on').forEach(function(c){c.classList.remove('on')});
  document.querySelectorAll('input[type=text],input[type=tel],input[type=email]').forEach(function(i){i.value=''});
  document.getElementById('other-note-field').style.display='none';
  showStep('v0');
}
 
function tryLogin(){
  if(document.getElementById('pw-input').value===ADMIN_PW){
    document.getElementById('pw-input').value='';
    document.getElementById('err-pw').style.display='none';
    showScreen('screen-admin');renderAdmin();
  } else {document.getElementById('err-pw').style.display='';}
}
document.addEventListener('keydown',function(e){
  if(e.key==='Enter'&&document.getElementById('screen-admin-login').classList.contains('active'))tryLogin();
});
function adminLogout(){showScreen('screen-booking')}
function adminTab(tab,el){
  APP.adminFilter=tab;
  document.querySelectorAll('.tab').forEach(function(t){t.classList.remove('on')});
  el.classList.add('on');renderAdmin();
}
 
function renderAdmin(){
  document.getElementById('admin-list').innerHTML='<div class="loading">読み込み中...</div>';
  gasGet({action:'getAll'}).then(function(bookings){
    var total=bookings.length;
    var newC=bookings.filter(function(b){return b.status!=='完了'}).length;
    var doneC=bookings.filter(function(b){return b.status==='完了'}).length;
    document.getElementById('stat-grid').innerHTML=
      '<div class="stat-card"><div class="stat-num">'+total+'</div><div class="stat-lbl">合計予約数</div></div>'+
      '<div class="stat-card"><div class="stat-num" style="color:#185FA5">'+newC+'</div><div class="stat-lbl">対応待ち</div></div>'+
      '<div class="stat-card"><div class="stat-num" style="color:#2d6a0a">'+doneC+'</div><div class="stat-lbl">完了済み</div></div>';
 
    var filtered=bookings.filter(function(b){
      if(APP.adminFilter==='upcoming')return b.status!=='完了';
      if(APP.adminFilter==='done')return b.status==='完了';
      return true;
    });
    filtered.sort(function(a,b){return a.dateKey.localeCompare(b.dateKey)||a.time.localeCompare(b.time)});
 
    var list=document.getElementById('admin-list');
    if(!filtered.length){list.innerHTML='<div class="empty-msg">該当する予約はありません</div>';return;}
    list.innerHTML=filtered.map(function(b){
      var badge=b.status==='完了'
        ?'<span class="badge badge-done">完了</span>'
        :'<span class="badge badge-new">新規</span>';
      var doneBtn=b.status!=='完了'
        ?'<button class="btn btn-p" style="font-size:12px;padding:5px 12px;margin-top:8px" onclick="markDone(\''+b.row+'\',event)">完了にする</button>':'';
      var info={};try{info=JSON.parse(b.info||'{}')}catch(e){}
      var subLine=b.type==='student'
        ?(info.grade||'')+(info.cls||'')+(info.num?' '+info.num+'番':'')
        :'お子さん：'+(info.child||'')+(info.childClass?' '+info.childClass:'');
      var extra='';
      if(b.type==='parent'){
        if(info.tel)extra+='<div class="aitem-row"><span class="aitem-lbl">電話</span><span class="aitem-val">'+info.tel+'</span></div>';
        if(info.mail)extra+='<div class="aitem-row"><span class="aitem-lbl">メール</span><span class="aitem-val">'+info.mail+'</span></div>';
        if(info.know)extra+='<div class="aitem-row"><span class="aitem-lbl">面談認知</span><span class="aitem-val">'+info.know+'</span></div>';
      }
      return '<div class="aitem">'+
        '<div class="aitem-head" onclick="toggleDetail(\'d'+b.row+'\')">'+
          '<div><p style="font-size:14px;font-weight:500">'+b.dateLabel+' '+b.timeLabel+'</p>'+
          '<p style="font-size:12px;color:#888;margin-top:2px">'+b.name+(subLine?' ／ '+subLine:'')+'</p></div>'+
          badge+'</div>'+
        '<div class="aitem-body" id="d'+b.row+'">'+
          '<div class="aitem-row"><span class="aitem-lbl">悩みの種類</span><span class="aitem-val">'+b.consultTypes+'</span></div>'+
          '<div class="aitem-row"><span class="aitem-lbl">面談形式</span><span class="aitem-val">'+b.format+'</span></div>'+
          '<div class="aitem-row"><span class="aitem-lbl">申込日</span><span class="aitem-val">'+b.createdAt+'</span></div>'+
          extra+doneBtn+'</div></div>';
    }).join('');
  }).catch(function(){
    document.getElementById('admin-list').innerHTML='<div class="empty-msg">データ取得に失敗しました。GAS URLを確認してください。</div>';
  });
}
 
function toggleDetail(id){var el=document.getElementById(id);if(el)el.classList.toggle('open')}
function markDone(row,e){
  e.stopPropagation();
  gasGet({action:'markDone',row:row}).then(function(){renderAdmin()});
}
</script>
</body>
</html>
