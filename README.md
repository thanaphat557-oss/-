<html lang="th">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>TSM | Login</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <?!= include('styles'); ?>
  <style>
    body{font-family:'Sarabun',sans-serif}
    .seg{display:flex;border:1px solid #e5e7eb;border-radius:.75rem;overflow:hidden}
    .seg input{display:none}
    .seg label{flex:1;padding:.6rem 1rem;text-align:center;cursor:pointer;font-weight:600}
    .seg input:checked + label{background:#1d4ed8;color:#fff}
  </style>
</head>
<body class="bg-gray-50">
  <header class="app-header">
    <div class="flex justify-between items-center">
      <div class="brand">
        <div class="logo">TSM</div>
        <div class="titles">
          <h1 class="company-name">บริษัท ไทยซิน แมนูแฟคเจอริ่ง จำกัด</h1>
          <h1 class="page-title">เข้าสู่ระบบ</h1>
        </div>
      </div>
      <nav class="nav">
        <a class="btn" href="?page=index">ไปหน้าแบบฟอร์ม</a>
        <a class="btn" href="?page=admin">ไปหน้า Admin</a>
      </nav>
    </div>
  </header>

  <main class="container">
    <section class="card" style="max-width:560px;margin-inline:auto;">
      <div class="form-field">
        <label>โหมดเข้าสู่ระบบ</label>
        <div class="seg">
          <input type="radio" id="mode-user" name="mode" value="user" checked>
          <label for="mode-user">ผู้ใช้งาน</label>
          <input type="radio" id="mode-admin" name="mode" value="admin">
          <label for="mode-admin">Admin</label>
        </div>
      </div>

      <form id="loginForm" class="mt-4">
        <div class="form-field">
          <label id="idLabel">อีเมล (ผู้ใช้งาน)</label>
          <input id="identity" type="text" placeholder="your.name@taisin.co.th" required>
        </div>
        <div class="form-field">
          <label>รหัสผ่าน</label>
          <input id="password" type="password" placeholder="••••••" required>
        </div>
        <div class="actions" style="margin-top:12px;display:flex;gap:8px">
          <button class="btn primary" type="submit">เข้าสู่ระบบ</button>
          <button class="btn" type="reset">ล้างค่า</button>
        </div>
      </form>

      <div class="card-sub" style="margin-top:16px">
        <div class="text-sm text-gray-700 leading-6">
          <div class="font-semibold mb-1">สำหรับ Admin</div>
          <ul class="list-disc pl-6">
            <li>เลือก <strong>“Admin”</strong> ด้านบน</li>
            <li>Username: <code>admin</code> หรืออีเมลในรายชื่อที่กำหนด</li>
            <li>Password: <code>123456</code></li>
          </ul>
        </div>
      </div>
    </section>
  </main>

  <script>
    // ====== Config ======
    const DEFAULT_PASS = '123456';
    const ADMIN_EMAILS = [
      'hatairat.k@taisin.co.th',
      'jidapa.a@taisin.co.th',
      'thanaphat.n@taisin.co.th',
      'ruangchai.c@taisin.co.th'
    ].map(s=>s.toLowerCase());

    // ====== Helpers ======
    const $ = (s, r=document)=>r.querySelector(s);

    function setMode(mode){
      const id = $('#identity');
      const lbl = $('#idLabel');
      if(mode==='admin'){
        lbl.textContent = 'Username หรืออีเมล (Admin)';
        id.placeholder = 'admin หรือ your.name@taisin.co.th';
      } else {
        lbl.textContent = 'อีเมล (ผู้ใช้งาน)';
        id.placeholder = 'your.name@taisin.co.th';
      }
      id.value = '';
      $('#password').value = '';
      id.focus();
    }

    // init mode switch
    document.querySelectorAll('input[name="mode"]').forEach(r=>{
      r.addEventListener('change', e=> setMode(e.target.value));
    });

    // ====== Fake client-side auth (สำหรับเดโม่ / ใช้งานภายใน) ======
    // หมายเหตุ: วิธีนี้ไม่ปลอดภัยสำหรับงาน Production — แนะนำย้าย logic ไปฝั่ง Apps Script
    $('#loginForm').addEventListener('submit', (e)=>{
      e.preventDefault();
      const mode = document.querySelector('input[name="mode"]:checked').value;
      const id = $('#identity').value.trim().toLowerCase();
      const pass = $('#password').value;

      if(pass !== DEFAULT_PASS){
        alert('รหัสผ่านไม่ถูกต้อง');
        return;
      }

      if(mode==='admin'){
        const isAdmin = (id==='admin') || ADMIN_EMAILS.includes(id);
        if(!isAdmin){
          alert('บัญชีนี้ไม่มีสิทธิ์ Admin');
          return;
        }
        try{ localStorage.setItem('tsm_auth', JSON.stringify({role:'admin', id:id})); }catch{}
        // ไปหน้า Admin ของระบบเดิม
        window.location.href='?page=admin';
      }else{
        try{ localStorage.setItem('tsm_auth', JSON.stringify({role:'user', id:id})); }catch{}
        window.location.href='?page=index';
      }
    });

    // ตั้งค่าเริ่มต้น
    setMode('user');
  </script>
</body>
</html>

