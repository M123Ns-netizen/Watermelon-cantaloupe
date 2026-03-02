<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>سجل الألقاب</title>
<style>
body {font-family:'Segoe UI',Tahoma,sans-serif;margin:0;padding:0;direction:rtl;background:#f0f4f8;color:#111827;}
.container{max-width:900px;margin:30px auto;padding:25px;border-radius:16px;background:#fff;box-shadow:0 6px 20px rgba(0,0,0,0.08);}
h1{text-align:center;cursor:pointer;user-select:none;margin-bottom:15px;font-size:28px;color:#1e40af;}
#adminMessage{text-align:center;color:#2563eb;font-size:14px;display:none;margin-bottom:15px;}
.search{display:flex;gap:10px;margin-bottom:20px;flex-wrap:wrap;}
input[type="text"]{padding:10px 15px;border-radius:12px;border:1px solid #ccc;flex:1;font-size:16px;}
button{padding:8px 16px;border-radius:12px;border:none;cursor:pointer;font-size:14px;font-weight:bold;}
.btn-add{background:#10b981;color:#fff;}
.btn-delete{background:#ef4444;color:#fff;}
#themeToggle{background:#3b82f6;color:#fff;margin-bottom:20px;padding:8px 16px;border-radius:12px;cursor:pointer;display:inline-block;}
.section-letter{margin-top:20px;}
.letter-title{font-size:20px;font-weight:bold;margin-bottom:8px;border-bottom:2px solid #e5e7eb;padding-bottom:3px;}
.nickname{display:flex;justify-content:space-between;align-items:center;padding:8px 12px;border-radius:12px;margin-bottom:6px;border:1px solid #e5e7eb;background:#f9fafb;}
.status{margin-left:10px;color:#b91c1c;font-weight:bold;}
body.dark-mode{background:#0f172a;color:#f8fafc;}
body.dark-mode .container{background:#1e293b;color:#f8fafc;}
body.dark-mode input[type='text']{background:#334155;color:#f8fafc;border-color:#475569;}
body.dark-mode .nickname{background:#334155;border-color:#475569;color:#f8fafc;}
body.dark-mode .nickname:hover{background:#475569;}
</style>
</head>
<body>
<div class="container">
<h1 id="title">📜 سجل الألقاب</h1>
<div id="adminMessage">🔒 تم تفعيل وضع المشرف</div>
<div id="themeToggle">🌙/☀️ تبديل الوضع</div>
<div class="search">
<input type="text" id="searchInput" placeholder="ابحث عن لقب" oninput="renderList()">
</div>
<div id="addSection" style="display: none;">
<input type="text" id="newNickname" placeholder="أدخل لقب جديد">
<button class="btn-add" onclick="addNickname()">إضافة</button>
</div>
<div id="nicknameList"></div>
</div>

<script>
const listEl=document.getElementById('nicknameList');
const addSection=document.getElementById('addSection');
const adminMessage=document.getElementById('adminMessage');
const titleEl=document.getElementById('title');
let moderatorActive=false,clickCount=0;

// تحميل الألقاب من التخزين أو استخدام الأصلية
let nicknames = JSON.parse(localStorage.getItem("nicknames")) || [
  'ارثر 🍸','اسكانور','اشبيرون 🌙','ال','اميرة العلكه','انتاريس','انيا','انيوشا','اوليفر 🍀','ايانوكوجي','ايتادوري','ايتاشي','ايتشيغو','ايسديث 🌝','ايشيشا','ايرين','ايميليا',
  'باتشيرا','باجي 👑','باين','بروك','بيم','بيكاتشو',
  'تاتسومي','تانجيرو','توجي ⚔️','توشيرو ☁️','توكيتو 📜',
  'جارب','جاي هوان','جوست','جين موري',
  'دابي','داكيو','دانيال','دلتا','دوما ❄️',
  'روبي','ريو','رينغوكو','رينهارد','ريون 🐺',
  'زورو 🥂','ساسكي 👁️','ساشا','ساكاموتو','سايتما','سكارليت','سوكونا','سونغ','سيلفر','سينشي 🕵️',
  'شادو','شانكس','شوتو','شيزوكو','شيكامارو','شينوبو',
  'غابريلا','غوجيتا','غوكو','غون','غيو','غيوتارو',
  'فانتياس','فيتان ☠️',
  'كايزر 🏹','كاكاشي','كايل','كوشينا','كيزارو','كيلوا ⚡',
  'لاو','ليفاي','ليوريو',
  'ماو ماو','موزان 🩸','ميدوريا','ميغومي🧧','ميكاسا','ميمي 🎀','ميهوك 🦅',
  'ناغي 🎯','نيجي','نيزكو 🌸',
  'هانكوك','هوشينو','هيستوريا',
  'ويليام','يامادا 🎭','يامي','ياشيرو','يواشيرو','يور'
];

// حفظ التغييرات
function saveNicknames(){
  localStorage.setItem("nicknames", JSON.stringify(nicknames));
}

const emojis=['✨','🔥','🌟','💫','🌈','🎯','🪐','🍀','🌸','🎵','⚡','🦋','🥂','🍒','🌙','🌞','🦅','🎀','🧧','⚔️','👑','☁️','🎭','❄️'];
function randomEmoji(){return emojis[Math.floor(Math.random()*emojis.length)];}
function normalize(s){return s.trim().toLowerCase();}

// تفعيل المشرف
function activateModerator(){
  moderatorActive=true;
  addSection.style.display='block';
  adminMessage.style.display='block';
  renderList();
}

titleEl.addEventListener('click',()=>{
  clickCount++;
  if(clickCount>=5&&!moderatorActive){ activateModerator(); clickCount=0; }
  setTimeout(()=>clickCount=0,2000);
});

// إضافة لقب جديد
function addNickname(){
  if(!moderatorActive)return alert('هذه الخاصية متاحة فقط للمشرف');
  const input=document.getElementById('newNickname');
  const name=input.value.trim();
  if(!name)return alert('الرجاء إدخال لقب');
  if(nicknames.includes(name))return alert('اللقب موجود مسبقًا');
  nicknames.push(name);
  saveNicknames(); // حفظ
  input.value='';
  renderList();
}

// حذف لقب
function deleteNickname(name){
  if(!moderatorActive)return alert('هذه الخاصية متاحة فقط للمشرف');
  if(!confirm('هل تريد حذف هذا اللقب؟'))return;
  nicknames=nicknames.filter(n=>n!==name);
  saveNicknames(); // حفظ
  renderList();
}

// عرض الألقاب
function renderList(){
  const search=normalize(document.getElementById('searchInput').value);
  const grouped={};
  nicknames.forEach(n=>{
    if(search&&!normalize(n).includes(search))return;
    const letter=n[0];
    if(!grouped[letter])grouped[letter]=[];
    grouped[letter].push(n);
  });
  const sorted=Object.keys(grouped).sort();
  listEl.innerHTML='';
  sorted.forEach(l=>{
    const sec=document.createElement('div');
    sec.className='section-letter';
    sec.innerHTML=`<div class='letter-title'>${l}</div>`;
    grouped[l].sort((a,b)=>a.localeCompare(b)).forEach(n=>{
      const div=document.createElement('div');
      div.className='nickname';
      div.innerHTML=`<span>${n} ${randomEmoji()}</span> <span class='status'>مأخوذ</span> ${moderatorActive?`<button class='btn-delete' onclick='deleteNickname("${n}")'>حذف</button>`:''}`;
      sec.appendChild(div);
    });
    listEl.appendChild(sec);
  });
}

// تبديل الوضع الليلي
document.getElementById('themeToggle').addEventListener('click',()=>{ document.body.classList.toggle('dark-mode'); });

renderList();
</script>
</body>
</html>
