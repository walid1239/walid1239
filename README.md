<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LIFA - Ultimate</title>
<style>
:root{--bg:#fff;--accent:#ff4081;--card:#fff;--text:#111;--shadow:0 4px 12px rgba(0,0,0,.1);}
body.dark{--bg:#121212;--card:#1e1e1e;--text:#f5f5f5;--shadow:0 4px 12px rgba(0,0,0,.5);}
*{margin:0;padding:0;box-sizing:border-box;}
body{font-family:system-ui;background:var(--bg);color:var(--text);transition:0.3s;padding-bottom:70px;overflow-x:hidden;}

/* شاشات الدخول */
.auth-screen{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;background:linear-gradient(135deg,#007bff,#3399ff);color:white;z-index:3000;}
.logo-text{font-size:48px;font-weight:900;letter-spacing:2px;margin-bottom:20px;}
.auth-screen input{width:80%;max-width:320px;padding:14px;margin:10px;border-radius:12px;border:none;font-size:16px;text-align:center;color:#111;background:white;}
.auth-screen button{width:80%;max-width:320px;padding:14px;border-radius:12px;border:none;background:#fff;color:#007bff;font-weight:bold;cursor:pointer;}

/* الهيدر والقائمة */
header{position:fixed;top:0;width:100%;height:60px;background:var(--card);display:flex;justify-content:space-between;align-items:center;padding:0 15px;box-shadow:var(--shadow);z-index:1000;}
.header-logo{font-size:24px;font-weight:900;background:linear-gradient(90deg,#ff4081,#f50057);-webkit-background-clip:text;-webkit-text-fill-color:transparent;}
.menu-btn{font-size:24px;cursor:pointer;background:none;border:none;color:var(--text);}
.sidebar{position:fixed;top:0;right:-280px;width:260px;height:100%;background:var(--card);z-index:2000;transition:0.3s ease;padding:20px;box-shadow:var(--shadow);}
.sidebar.open{right:0;}
.overlay{position:fixed;inset:0;background:rgba(0,0,0,0.5);display:none;z-index:1900;}
.sidebar-item{padding:12px;border-radius:10px;margin-bottom:8px;cursor:pointer;transition:0.2s;display:flex;align-items:center;gap:10px;}

/* البار السفلي */
.bottom-nav{position:fixed;bottom:0;width:100%;height:65px;background:var(--card);display:flex;justify-content:space-around;align-items:center;box-shadow:0 -2px 10px rgba(0,0,0,0.1);z-index:1000;}
.nav-item{cursor:pointer;opacity:0.4;transition:0.3s;display:flex;align-items:center;justify-content:center;color:var(--text);}
.nav-item svg{width:26px; height:26px; fill:none; stroke:currentColor; stroke-width:2; stroke-linecap:round; stroke-linejoin:round;}
.nav-item.active{opacity:1;color:var(--accent);transform:scale(1.1);}

/* أيقونة LIVE كنقطة حمراء فقط */
.nav-item.live-icon {
    width:16px;
    height:16px;
    border-radius:50%;
    background:red;
    position:relative;
}
.nav-item.live-icon::after {
    content:'';
    position:absolute;
    top:50%; left:50%;
    transform:translate(-50%,-50%);
    width:100%; height:100%;
    border-radius:50%;
    border:2px solid red;
    animation: pulse-ring 1.5s infinite;
}
@keyframes pulse-ring {
    0% { transform: translate(-50%,-50%) scale(1); opacity:1; }
    50% { transform: translate(-50%,-50%) scale(1.5); opacity:0.5; }
    100% { transform: translate(-50%,-50%) scale(1); opacity:1; }
}

/* الصفحات */
.page{display:none;padding:80px 15px 20px;max-width:600px;margin:auto;}
.page.active{display:block;}
.card{background:var(--card);padding:15px;border-radius:16px;margin-bottom:14px;box-shadow:var(--shadow);display:flex;flex-direction:column;position:relative;}
textarea,input{width:100%;border:none;background:rgba(0,0,0,0.03);padding:10px;border-radius:10px;outline:none;margin:5px 0; color:var(--text);}
.btn-post{background:var(--accent);color:white;border:none;padding:10px;border-radius:10px;font-weight:bold;margin-top:10px;cursor:pointer;width:100%;}
.hidden{display:none !important;}
</style>
<body>

<div id="phoneScreen" class="auth-screen">
    <h1 class="logo-text">LIFA</h1>
    <input type="tel" id="phoneInput" placeholder="أدخل رقم الهاتف">
    <button onclick="sendOtpReq()">إرسال الكود</button>
</div>

<div id="otpScreen" class="auth-screen hidden">
    <h1 class="logo-text">LIFA</h1>
    <p style="margin-bottom:15px;">أرسلنا الكود إلى: <span id="otpPhone"></span></p>
    <input type="number" id="otpInput" placeholder="0 0 0 0">
    <button onclick="validateOtp()">تأكيد</button>
</div>

<div id="app" class="hidden">
    <header>
        <button class="menu-btn" onclick="toggleSidebar()">☰</button>
        <span class="header-logo">LIFA</span>
        <div style="width:24px;"></div>
    </header>

    <div id="overlay" class="overlay" onclick="toggleSidebar()"></div>
    <div id="sidebar" class="sidebar">
        <h2 style="color:var(--accent);margin-bottom:20px;">القائمة</h2>
        <div class="sidebar-item" onclick="switchTab('profilePage')">👤 الملف الشخصي</div>
        <div class="sidebar-item" onclick="toggleDark()">🌙 الوضع الليلي</div>
        <div class="sidebar-item" onclick="logout()" style="color:red;margin-top:20px;font-weight:bold;border-top:1px solid #eee;padding-top:20px;">
            <svg viewBox="0 0 24 24" style="width:20px; stroke:red; fill:none; stroke-width:2;">
                <path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"></path>
                <polyline points="16 17 21 12 16 7"></polyline>
                <line x1="21" y1="12" x2="9" y2="12"></line>
            </svg>
            تسجيل الخروج
        </div>
    </div>

    <div id="homePage" class="page active">
        <div class="card">
            <textarea id="postText" rows="2" placeholder="شارك أفكارك الآن..."></textarea>
            <button class="btn-post" onclick="addPost()">نشر</button>
        </div>
        <div id="feed"></div>
    </div>

    <div id="searchPage" class="page"><div class="card"><h2>البحث</h2></div></div>
    <div id="livePage" class="page"><div class="card" style="text-align:center;"><h2>LIVE 🔴</h2><p>بث مباشر قريباً</p></div></div>
    <div id="videoPage" class="page"><div class="card"><h2>فيديوهات</h2></div></div>
    <div id="messagesPage" class="page"><div class="card"><h2>الرسائل</h2></div></div>

    <div id="profilePage" class="page">
      <div class="card" style="text-align:center;">
        <h2>الملف الشخصي</h2>
        <p><strong>رقم الهاتف:</strong> <span id="profilePhone"></span></p>
        <div id="profileForm" style="margin-top:15px;">
          <input type="text" id="profileName" placeholder="الاسم">
          <input type="text" id="profileCity" placeholder="المدينة">
          <button class="btn-post" onclick="saveProfile()">💾 حفظ</button>
        </div>
        <div id="savedProfile" class="hidden">
            <p id="savedNameResult"></p>
            <button class="btn-post" onclick="editProfile()">✏️ تعديل</button>
        </div>
      </div>
    </div>

    <div id="academyPage" class="page">
        <div class="card" style="text-align:center;">
            <h2>🎓 LIFA Académie</h2>
            <p>مرحبا بك في الأكاديمية! 🌟</p>
            <p>هنا يمكنك وضع الدورات، الفيديوهات التعليمية، أو أي محتوى تعليمي.</p>
        </div>
    </div>

    <!-- شريط التنقل السفلي -->
    <div class="bottom-nav">
        <div class="nav-item active" onclick="switchTab('homePage',this)">
            <svg viewBox="0 0 24 24"><path d="M3 9l9-7 9 7v11a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"></path></svg>
        </div>
        <div class="nav-item" onclick="switchTab('searchPage',this)">
            <svg viewBox="0 0 24 24"><circle cx="11" cy="11" r="8"></circle><line x1="21" y1="21" x2="16.65" y2="16.65"></line></svg>
        </div>
        <div class="nav-item live-icon" onclick="switchTab('livePage',this)"></div>
        <div class="nav-item" onclick="switchTab('videoPage',this)">
            <svg viewBox="0 0 24 24"><rect x="2" y="2" width="20" height="20" rx="2.18" ry="2.18"></rect><line x1="7" y1="2" x2="7" y2="22"></line><line x1="17" y1="2" x2="17" y2="22"></line></svg>
        </div>

        <!-- زر الأكاديمية -->
        <div class="nav-item" onclick="switchTab('academyPage', this)">
            🎓
        </div>

        <div class="nav-item" onclick="switchTab('messagesPage',this)">
            <svg viewBox="0 0 24 24"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path><polyline points="22,6 12,13 2,6"></polyline></svg>
        </div>
    </div>
</div>

<script>
let posts = [];

// إرسال الكود
function sendOtpReq(){
    const phone = document.getElementById('phoneInput').value.trim();
    if(phone.length < 5) return alert("رقم غير صحيح");
    document.getElementById('otpPhone').innerText = phone;
    document.getElementById('phoneScreen').classList.add('hidden');
    document.getElementById('otpScreen').classList.remove('hidden');
}

// تأكيد الكود والدخول
function validateOtp(){
    if(document.getElementById('otpInput').value === "1234"){
        const phone = document.getElementById('phoneInput').value;
        localStorage.setItem('lifa_user', phone);
        initApp();
    } else alert("الرمز خاطئ، يجب إدخال 1234");
}

// تشغيل التطبيق
function initApp(){
    document.getElementById('phoneScreen').classList.add('hidden');
    document.getElementById('otpScreen').classList.add('hidden');
    document.getElementById('app').classList.remove('hidden');
    document.getElementById('profilePhone').innerText = localStorage.getItem('lifa_user');

    if(localStorage.getItem('lifa_dark')==='true') document.body.classList.add('dark');
    
    loadProfile();
    loadPosts();
}

// تسجيل الخروج
function logout(){
    if(confirm("هل أنت متأكد أنك تريد تسجيل الخروج والعودة لشاشة الدخول؟")){
        localStorage.removeItem('lifa_user');
        localStorage.removeItem('lifa_name');
        localStorage.removeItem('lifa_city');
        localStorage.removeItem('lifa_posts');

        document.getElementById('app').classList.add('hidden');
        document.getElementById('phoneScreen').classList.remove('hidden');
        document.getElementById('otpInput').value = "";
        document.getElementById('phoneInput').value = "";
        document.getElementById('feed').innerHTML = "";
        if(document.getElementById('sidebar').classList.contains('open')) toggleSidebar();
        alert("تم تسجيل الخروج بنجاح.");
    }
}

window.onload = function(){
    if(localStorage.getItem('lifa_user')) initApp();
}

function toggleSidebar(){
    const side=document.getElementById('sidebar');
    const over=document.getElementById('overlay');
    side.classList.toggle('open');
    over.style.display=side.classList.contains('open')?'block':'none';
}

function switchTab(id, el=null){
    document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
    document.getElementById(id).classList.add('active');
    if(el){
        document.querySelectorAll('.nav-item').forEach(i=>i.classList.remove('active'));
        el.classList.add('active');
    }
    if(document.getElementById('sidebar').classList.contains('open')) toggleSidebar();
}

function toggleDark(){
    const isDark=document.body.classList.toggle('dark');
    localStorage.setItem('lifa_dark',isDark);
}

function saveProfile(){
    const name = document.getElementById('profileName').value.trim();
    const city = document.getElementById('profileCity').value.trim();
    if(!name || !city) return alert("الرجاء ملء الاسم والمدينة!");
    localStorage.setItem('lifa_name', name);
    localStorage.setItem('lifa_city', city);
    showSavedProfile(name, city);
}

function loadProfile(){
    const name = localStorage.getItem('lifa_name');
    const city = localStorage.getItem('lifa_city');
    if(name && city) showSavedProfile(name, city);
}

function showSavedProfile(name, city){
    document.getElementById('savedNameResult').innerText = `مرحباً: ${name} من ${city}`;
    document.getElementById('profileForm').classList.add('hidden');
    document.getElementById('savedProfile').classList.remove('hidden');
}

function editProfile(){
    document.getElementById('profileForm').classList.remove('hidden');
    document.getElementById('savedProfile').classList.add('hidden');
}

// منشورات
function addPost(){
    const txt = document.getElementById('postText').value.trim();
    if(!txt) return alert("لا يمكنك نشر منشور فارغ!");
    const feed = document.getElementById('feed');
    const card = document.createElement('div');
    card.className = "card";
    card.innerHTML = `<p>${txt}</p>`;
    feed.prepend(card);
    posts.unshift(txt);
    localStorage.setItem('lifa_posts', JSON.stringify(posts));
    document.getElementById('postText').value = "";
}

function loadPosts(){
    const saved = localStorage.getItem('lifa_posts');
    if(saved){
        posts = JSON.parse(saved);
        const feed = document.getElementById('feed');
        posts.forEach(txt => {
            const card = document.createElement('div');
            card.className = "card";
            card.innerHTML = `<p>${txt}</p>`;
            feed.appendChild(card);
        });
    }
}
</script>
</body>
</html>