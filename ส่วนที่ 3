let knockCount = 0;
let isAdminActive = false;

// 1. ระบบลับ: เคาะ 5 ครั้งปลดล็อกแอดมิน
function adminKnock() {
    knockCount++;
    if (navigator.vibrate) navigator.vibrate(50);
    if (knockCount >= 5) {
        document.getElementById('admin-lock').classList.remove('hidden');
        knockCount = 0;
    }
}

function closeModal() { document.getElementById('admin-lock').classList.add('hidden'); }

function unlockAdmin() {
    if (document.getElementById('admin-pass').value === "2222") {
        isAdminActive = true;
        alert("🔓 แอดมินพัดเข้าระบบสำเร็จ!");
        document.getElementById('admin-editor').classList.remove('hidden');
        closeModal(); render();
    } else { alert("รหัสผ่านไม่ถูกต้อง"); }
}

// 2. ระบบสลับหน้า
function switchTab(tab) {
    document.getElementById('view-news').classList.toggle('hidden', tab !== 'news');
    document.getElementById('view-public').classList.toggle('hidden', tab !== 'public');
    document.getElementById('tab-news').classList.toggle('active', tab === 'news');
    document.getElementById('tab-public').classList.toggle('active', tab === 'public');
}

// 3. ระบบโพสต์ (ข่าวสาร & ถามตอบ)
function postNews() {
    const title = document.getElementById('n-title').value;
    const msg = document.getElementById('n-msg').value;
    if(title && msg) {
        const news = JSON.parse(localStorage.getItem('v53_news') || "[]");
        news.unshift({ id: Date.now(), title, msg, img: document.getElementById('n-img').value, date: new Date().toLocaleString(), pinned: false });
        localStorage.setItem('v53_news', JSON.stringify(news));
        location.reload();
    }
}

function postPublic() {
    const title = document.getElementById('p-title').value;
    const msg = document.getElementById('p-msg').value;
    const name = document.getElementById('p-name').value || "นิรนาม";
    if(title && msg) {
        localStorage.setItem('v53_saved_name', name);
        localStorage.setItem('v53_saved_email', document.getElementById('p-email').value);
        const posts = JSON.parse(localStorage.getItem('v53_public') || "[]");
        posts.unshift({ id: Date.now(), name, title, msg, imgs: [document.getElementById('p-img1').value, document.getElementById('p-img2').value].filter(u=>u), date: new Date().toLocaleString(), pinned: false });
        localStorage.setItem('v53_public', JSON.stringify(posts));
        location.reload();
    }
}

// 4. ระบบจัดการ: ปักหมุด / ลบรายโพสต์
function deleteItem(id, db) {
    if(!isAdminActive) return;
    if(confirm("ลบโพสต์นี้ใช่ไหม?")) {
        let data = JSON.parse(localStorage.getItem(db));
        data = data.filter(i => i.id !== id);
        localStorage.setItem(db, JSON.stringify(data));
        render();
    }
}

function togglePin(id, db) {
    if(!isAdminActive) return;
    let data = JSON.parse(localStorage.getItem(db));
    data = data.map(i => {
        if(i.id === id) i.pinned = !i.pinned;
        return i;
    });
    localStorage.setItem(db, JSON.stringify(data));
    render();
}

// 5. ระบบรีเซ็ต (ล้างหน้าคุยกัน แต่ข่าวสารอยู่ครบ)
function resetPublicBoard() {
    if(!isAdminActive) return;
    if(confirm("⚠️ ยืนยันการล้าง 'หน้าคุยกัน' ทั้งหมด? (ข่าวสารจะไม่ถูกลบ)")) {
        localStorage.removeItem('v53_public');
        alert("🧹 ล้างข้อมูลหน้าพูดคุยเรียบร้อย!");
        render();
    }
}

function render() {
    if(localStorage.getItem('v53_saved_name')) {
        document.getElementById('p-name').value = localStorage.getItem('v53_saved_name');
        document.getElementById('p-email').value = localStorage.getItem('v53_saved_email');
    }

    const draw = (dbName, containerId) => {
        let data = JSON.parse(localStorage.getItem(dbName) || "[]");
        data.sort((a,b) => b.pinned - a.pinned || b.id - a.id);
        document.getElementById(containerId).innerHTML = data.map(i => `
            <div class="card ${i.pinned ? 'pinned-card' : ''}">
                ${isAdminActive ? `
                    <div class="admin-btns">
                        <button class="pin-btn" onclick="togglePin(${i.id}, '${dbName}')">${i.pinned ? '📍 เลิกปัก' : '📌 ปัก'}</button>
                        <button class="del-btn" onclick="deleteItem(${i.id}, '${dbName}')">🗑️ ลบ</button>
                    </div>` : ''}
                <h3 style="margin:0;">${i.pinned ? '📌 ' : ''}${i.title}</h3>
                <p style="white-space:pre-wrap;">${i.msg}</p>
                ${i.img ? `<img src="${i.img}" class="post-img">` : ''}
                ${i.imgs ? i.imgs.map(m=>`<img src="${m}" class="post-img">`).join('') : ''}
                <div style="text-align:right;"><small>${i.name || 'Admin'} | ${i.date}</small></div>
            </div>
        `).join('') || '<p style="text-align:center; color:gray;">ว่างเปล่า...</p>';
    }
    draw('v53_news', 'news-feed');
    draw('v53_public', 'public-feed');
}
window.onload = render;
