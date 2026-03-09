<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="UTF-8">
  <title>سجل الألقاب</title>
  <style>
    body {
      font-family: "Tahoma", sans-serif;
      background: #050816;
      color: #f5f5f5;
      text-align: center;
      margin: 0;
      padding: 0;
    }
    header {
      padding: 20px;
      background: linear-gradient(135deg, #111827, #1f2937);
      border-bottom: 1px solid #374151;
    }
    h1 { margin: 0; font-size: 26px; }
    main { max-width: 800px; margin: 20px auto; padding: 15px; }
    .card {
      background: #111827;
      border-radius: 10px;
      padding: 15px;
      margin-bottom: 20px;
      border: 1px solid #1f2937;
    }
    input[type="text"] {
      width: 80%;
      max-width: 350px;
      padding: 8px 10px;
      border-radius: 6px;
      border: 1px solid #4b5563;
      background: #020617;
      color: #f9fafb;
      outline: none;
      margin-bottom: 10px;
    }
    button {
      padding: 7px 14px;
      border-radius: 6px;
      border: none;
      cursor: pointer;
      background: #38bdf8;
      color: #020617;
      font-size: 14px;
      margin: 5px;
    }
    .hidden { display: none; }
    .status { margin-top: 8px; font-weight: bold; font-size: 14px; }
    .taken { color: #f97316; }
    .free { color: #22c55e; }
    ul { list-style: none; padding: 0; margin: 0; text-align: right; font-size: 14px; }
    li { padding: 2px 0; }
  </style>
</head>
<body>

<header>
  <h1>سِجل الألقاب</h1>
</header>

<main>
  <section class="card">
    <h2>بحث / تعديل</h2>
    <input id="nicknameInput" type="text" placeholder="اكتب اللقب هنا...">
    <br>
    <button onclick="checkNickname()">بحث</button>

    <!-- أزرار التعديل (مخفية) -->
    <button id="addBtn" class="hidden" onclick="addNickname()">إضافة</button>
    <button id="deleteBtn" class="hidden" onclick="deleteNickname()">حذف</button>

    <div id="status" class="status"></div>
  </section>

  <section class="card">
    <h2>الألقاب المحفوظة</h2>
    <ul id="list"></ul>
  </section>
</main>

<footer>
  ~『𝑲.𝑵.𝑯⊰❄️⊱𝑺𝑵𝑶𝑾』~
</footer>

<script>
  const secretCode = "#snow"; // الكود السري الخاص بك

  // تحميل القائمة من التخزين أو إنشاء قائمة جديدة
  let nicknames = JSON.parse(localStorage.getItem("nicknames")) || [
    "ارثر 🍸","اسكانور","اشبيرون 🌙","ال","اميرة العلكه","انتاريس","انيا","انيوشا",
    "اوليفر 🍀","ايانوكوجي","ايتادوري","ايتاشي","ايتشيغو","ايسديث 🌝","ايشيشا","ايرين","ايميليا",
    "باتشيرا","باجي 👑","باين","بروك","بيم","بيكاتشو",
    "تاتسومي","تانجيرو","توجي ⚔️","توشيرو ☁️","توكيتو 📜",
    "جارب","جاي هوان","جوست","جين موري",
    "دابي","داكيو","دانيال","دلتا","دوما ❄️",
    "روبي","ريو","رينغوكو","رينهارد","ريون 🐺",
    "زورو 🥂",
    "ساسكي 👁️","ساشا","ساكاموتو","سايتما","سكارليت","سوكونا","سونغ","سيلفر","سينشي 🕵️",
    "شادو","شانكس","شوتو","شيزوكو","شيكامارو","شينوبو",
    "غابريلا","غوجيتا","غوكو","غون","غيو","غيوتارو",
    "فانتياس","فيتان ☠️",
    "كايزر 🏹","كاكاشي","كايل","كوشينا","كيزارو","كيلوا ⚡","كونان 🔎",
    "لاو","ليفاي","ليوريو",
    "ماو ماو","موزان 🩸","ميدوريا","ميغومي🧧","ميكاسا","ميمي 🎀","ميهوك 🦅","ماكي",
    "ناغي 🎯","نيجي","نيزكو 🌸",
    "هانكوك","هوشينو","هيستوريا",
    "ويليام",
    "يامادا 🎭","يامي","ياشيرو","يواشيرو","يور"
  ];

  function save() {
    localStorage.setItem("nicknames", JSON.stringify(nicknames));
  }

  function renderList() {
    nicknames.sort((a, b) => a.localeCompare(b, "ar"));
    const list = document.getElementById("list");
    list.innerHTML = "";
    nicknames.forEach(n => {
      const li = document.createElement("li");
      li.textContent = "⧝┊" + n;
      list.appendChild(li);
    });
  }

  function checkNickname() {
    const input = document.getElementById("nicknameInput");
    const status = document.getElementById("status");
    const addBtn = document.getElementById("addBtn");
    const deleteBtn = document.getElementById("deleteBtn");
    const name = input.value.trim();

    if (!name) {
      status.textContent = "اكتب لقب أول.";
      status.className = "status";
      return;
    }

    // تفعيل وضع التعديل
    if (name === secretCode) {
      addBtn.classList.remove("hidden");
      deleteBtn.classList.remove("hidden");
      status.textContent = "تم تفعيل وضع التعديل.";
      status.className = "status free";
      input.value = "";
      return;
    }

    const isTaken = nicknames.map(n => n.toLowerCase()).includes(name.toLowerCase());

    if (isTaken) {
      status.textContent = "اللقب «" + name + "» موجود.";
      status.className = "status taken";
    } else {
      status.textContent = "اللقب «" + name + "» غير موجود.";
      status.className = "status free";
    }
  }

  function addNickname() {
    const input = document.getElementById("nicknameInput");
    const name = input.value.trim();
    const status = document.getElementById("status");

    if (!name) {
      status.textContent = "اكتب لقب لإضافته.";
      return;
    }

    nicknames.push(name);
    save();
    renderList();

    status.textContent = "تم إضافة اللقب «" + name + "».";
    status.className = "status free";
    input.value = "";
  }

  function deleteNickname() {
    const input = document.getElementById("nicknameInput");
    const name = input.value.trim();
    const status = document.getElementById("status");

    if (!name) {
      status.textContent = "اكتب لقب لحذفه.";
      return;
    }

    const index = nicknames.findIndex(n => n.toLowerCase() === name.toLowerCase());

    if (index === -1) {
      status.textContent = "اللقب غير موجود.";
      status.className = "status taken";
      return;
    }

    nicknames.splice(index, 1);
    save();
    renderList();

    status.textContent = "تم حذف اللقب «" + name + "».";
    status.className = "status free";
    input.value = "";
  }

  renderList();
</script>

</body>
</html>
