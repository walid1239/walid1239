<!DOCTYPE html>
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