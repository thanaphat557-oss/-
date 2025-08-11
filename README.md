const SHEET_ID = SpreadsheetApp.getActive().getId();
const SHEET_NAME_REQUESTS = "Requests";

// ผู้อนุมัติตามแผนก (จากโจทย์)
const APPROVERS = {
  "ฝ่าย Account": "hatairat.k@taisin.co.th",
  "ฝ่าย CS": "hatairat.k@taisin.co.th",
  "ฝ่าย วางแผนการผลิต": "jidapa.a@taisin.co.th",
  "ฝ่าย Cosmetic": "jidapa.a@taisin.co.th",
  "ฝ่าย ผลิตภัณฑ์กระติก": "jidapa.a@taisin.co.th",
  "ฝ่าย คลังสินค้า": "jidapa.a@taisin.co.th",
  "ฝ่าย ตรวจสอบคุณภาพ": "jidapa.a@taisin.co.th",
  "ฝ่าย วิจัยและพัฒนาผลิตภัณฑ์": "jidapa.a@taisin.co.th",
  "ฝ่าย QMS/RA": "jidapa.a@taisin.co.th",
  "ฝ่าย ขาย": "jidapa.a@taisin.co.th",
  "ฝ่าย HR.": "thanaphat.n@taisin.co.th",
  "ฝ่าย วิศวกรรม": "ruangchai.c@taisin.co.th"
};

// รายการอุปกรณ์ & แผนก
const ITEM_CATALOG = [
  "ปากกาลูกลื่น","ดินสอ","ยางลบ","สมุดโน้ต","แฟ้มเอกสาร","กระดาษ A4","เทปกาว",
  "ลิขวิด","ไฮไลท์เตอร์","คลิปหนีบกระดาษ","ลวดเสียบกระดาษ","อื่นๆ (ระบุ)"
];
const DEPARTMENTS = [
  "ฝ่ายบริหาร","ฝ่าย HR.","ฝ่าย IT","ฝ่าย CS","ฝ่าย Account","ฝ่าย Cosmetic",
  "ฝ่าย ผลิตภัณฑ์กระติก","ฝ่าย คลังสินค้า","ฝ่าย จัดซื้อ","ฝ่าย วางแผนการผลิต",
  "ฝ่าย วิศวกรรม","ฝ่าย ตรวจสอบคุณภาพ","ฝ่าย วิจัยและพัฒนาผลิตภัณฑ์","ฝ่าย QMS/RA","ฝ่าย ขาย"
];

// ผู้ใช้งานหลังบ้าน (เดโม่)
const ADMIN_USERS = [
  { email: "hatairat.k@taisin.co.th", role: "หัวหน้าแผนก" },
  { email: "jidapa.a@taisin.co.th", role: "ผู้จัดการ" },
  { email: "thanaphat.n@taisin.co.th", role: "ผู้จัดการทั่วไป" }
];
const ADMIN_PASSWORD = "123456";

function ensureSheet(){
  const ss = SpreadsheetApp.openById(SHEET_ID);
  let sh = ss.getSheetByName(SHEET_NAME_REQUESTS);
  if (!sh) sh = ss.insertSheet(SHEET_NAME_REQUESTS);
  const headers = [
    "Timestamp","Request ID","Name","EmpCode","Department","Purpose","Priority","Remark",
    "ItemsJSON","Status","ApproverEmail","ApprovalDecision","ApprovalNote","ApprovalAt","ApprovedBy"
  ];
  if (sh.getLastRow() === 0) sh.appendRow(headers);
  return sh;
}

function doGet(e){
  ensureSheet();
  const page = (e && e.parameter && e.parameter.page) || "index";
  if (e && e.parameter && e.parameter.action && e.parameter.id) {
    return handleApprovalAction_(e.parameter);
  }
  if (page === "admin"){
    const t = HtmlService.createTemplateFromFile("Admin");
    t.departments = DEPARTMENTS;
    t.items = ITEM_CATALOG;
    t.adminUsers = ADMIN_USERS.map(u=>u.email);
    t.allApprovers = uniqueEmails_([].concat(ADMIN_USERS.map(u=>u.email), Object.values(APPROVERS)));
    return t.evaluate().setTitle("Admin • Stationery Requests").setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
  }
  const t = HtmlService.createTemplateFromFile("Index");
  t.departments = DEPARTMENTS;
  t.items = ITEM_CATALOG;
  t.allApprovers = uniqueEmails_([].concat(ADMIN_USERS.map(u=>u.email), Object.values(APPROVERS)));
  return t.evaluate().setTitle("แบบฟอร์มเบิกอุปกรณ์เครื่องเขียน").setXFrameOptionsMode(HtmlService.XFrameOptionsMode.ALLOWALL);
}

function include(name){ return HtmlService.createHtmlOutputFromFile(name).getContent(); }
function getApproverByDept(dept){ return APPROVERS[dept] || ""; }
function uniqueEmails_(arr){ return Array.from(new Set(arr.filter(Boolean))); }
function makeRequestId_(){ return "REQ-" + Utilities.getUuid().slice(0,8).toUpperCase(); }

function submitRequest(payload){
  const sh = ensureSheet();
  const reqId = makeRequestId_();
  const approver = payload.approver || getApproverByDept(payload.department) || "";
  const row = [
    new Date(), reqId, payload.name, payload.empCode, payload.department,
    payload.purpose, payload.priority, payload.remark,
    JSON.stringify(payload.items || []),
    "Pending", approver, "","","",""
  ];
  sh.appendRow(row);

  if (approver){
    const url = ScriptApp.getService().getUrl();
    const approveLink = `${url}?action=approve&id=${encodeURIComponent(reqId)}&by=${encodeURIComponent(approver)}`;
    const rejectLink  = `${url}?action=reject&id=${encodeURIComponent(reqId)}&by=${encodeURIComponent(approver)}`;
    const htmlBody = `
      <div style="font-family:Arial,sans-serif">
        <h3>คำขอเบิกอุปกรณ์: ${reqId}</h3>
        <p><b>ผู้ขอ:</b> ${payload.name} (${payload.empCode})<br>
           <b>แผนก:</b> ${payload.department}<br>
           <b>ความเร่งด่วน:</b> ${payload.priority}<br>
           <b>วัตถุประสงค์:</b> ${safeHtml_(payload.purpose)}<br>
           <b>หมายเหตุ:</b> ${safeHtml_(payload.remark)}</p>
        <p><b>รายการอุปกรณ์</b></p>
        ${renderItemsTable_(payload.items)}
        <p>
          <a href="${approveLink}"><b>✅ อนุมัติ</b></a> &nbsp;|&nbsp;
          <a href="${rejectLink}"><b>❌ ไม่อนุมัติ</b></a>
        </p>
      </div>`;
    MailApp.sendEmail({ to: approver, subject: `คำขอเบิกอุปกรณ์ (${reqId}) — ${payload.name} / ${payload.department}`, htmlBody });
  }
  return { ok:true, id:reqId };
}

function handleApprovalAction_(params){
  const { action, id, by } = params;
  const sh = ensureSheet();
  const data = sh.getDataRange().getValues();
  const headers = data.shift();
  const ridx = headers.indexOf("Request ID");
  const statusIdx = headers.indexOf("Status");
  const apprEmailIdx = headers.indexOf("ApproverEmail");
  const decisionIdx = headers.indexOf("ApprovalDecision");
  const atIdx = headers.indexOf("ApprovalAt");
  const byIdx = headers.indexOf("ApprovedBy");

  let rowIndex = -1;
  for (let i=0;i<data.length;i++) if (data[i][ridx] === id){ rowIndex = i+2; break; }
  if (rowIndex === -1) return HtmlService.createHtmlOutput("ไม่พบคำขอ");

  const row = sh.getRange(rowIndex,1,1,headers.length).getValues()[0];
  if (by && row[apprEmailIdx] && by !== row[apprEmailIdx])
    return HtmlService.createHtmlOutput("อีเมลผู้อนุมัติไม่ตรงกับที่กำหนด");

  const decision = action === "approve" ? "Approved" : action === "reject" ? "Rejected" : "";
  if (!decision) return HtmlService.createHtmlOutput("คำสั่งไม่ถูกต้อง");

  sh.getRange(rowIndex, statusIdx+1).setValue(decision);
  sh.getRange(rowIndex, decisionIdx+1).setValue(decision);
  sh.getRange(rowIndex, atIdx+1).setValue(new Date());
  sh.getRange(rowIndex, byIdx+1).setValue(by||"");

  return HtmlService.createHtmlOutput(`<div style="font-family:Arial,sans-serif;padding:24px"><h2>บันทึกผลเรียบร้อย</h2><p>คำขอ ${id} : <b>${decision}</b></p><a href="${ScriptApp.getService().getUrl()}?page=admin">เปิดหน้าระบบหลังบ้าน</a></div>`);
}

// ========== Admin APIs (เพิ่ม ลบ แก้ไข / ช่วงเวลา / ส่งออก) ===========
function adminLogin(email, password){
  if (!ADMIN_USERS.some(u=>u.email===email) || password !== ADMIN_PASSWORD)
    return { ok:false, message:"อีเมลหรือรหัสผ่านไม่ถูกต้อง" };
  const token = Utilities.getUuid();
  CacheService.getDocumentCache().put(`adm:${token}`, email, 60*60);
  return { ok:true, token, email };
}

function adminGetData(token, fromISO, toISO){
  const email = CacheService.getDocumentCache().get(`adm:${token}`);
  if (!email) return { ok:false, message:"Session หมดอายุ กรุณาเข้าสู่ระบบอีกครั้ง" };
  const sh = ensureSheet();
  const values = sh.getDataRange().getValues();
  const headers = values.shift();
  const rows = values.map(r => Object.fromEntries(headers.map((h,i)=>[h, r[i]])));
  // filter by date range if provided
  let from = fromISO ? new Date(fromISO) : null;
  let to = toISO ? new Date(toISO) : null;
  let filtered = rows;
  if (from || to){
    filtered = rows.filter(r=>{
      const ts = new Date(r.Timestamp);
      return (!from || ts >= from) && (!to || ts <= new Date(to.getTime()+24*60*60*1000-1));
    });
  }
  const stats = buildStats_(filtered);
  return { ok:true, rows: filtered, stats, me: email };
}

function adminDeleteRequest(token, reqId){
  const email = CacheService.getDocumentCache().get(`adm:${token}`);
  if (!email) return { ok:false, message:"Session หมดอายุ" };
  const sh = ensureSheet();
  const data = sh.getDataRange().getValues();
  const headers = data.shift();
  const ridx = headers.indexOf("Request ID");
  for (let i=0;i<data.length;i++){
    if (data[i][ridx] === reqId){ sh.deleteRow(i+2); return { ok:true }; }
  }
  return { ok:false, message:"ไม่พบรหัสคำขอ" };
}

function adminUpdateRequest(token, payload){
  const email = CacheService.getDocumentCache().get(`adm:${token}`);
  if (!email) return { ok:false, message:"Session หมดอายุ" };
  const sh = ensureSheet();
  const data = sh.getDataRange().getValues();
  const headers = data.shift();
  const ridx = headers.indexOf("Request ID");
  const mapIdx = Object.fromEntries(headers.map((h,i)=>[h, i+1])); // 1-based columns
  for (let i=0;i<data.length;i++){
    if (data[i][ridx] === payload.reqId){
      const r = i+2;
      if (payload.name !== undefined) sh.getRange(r, mapIdx["Name"]).setValue(payload.name);
      if (payload.empCode !== undefined) sh.getRange(r, mapIdx["EmpCode"]).setValue(payload.empCode);
      if (payload.department !== undefined) sh.getRange(r, mapIdx["Department"]).setValue(payload.department);
      if (payload.purpose !== undefined) sh.getRange(r, mapIdx["Purpose"]).setValue(payload.purpose);
      if (payload.priority !== undefined) sh.getRange(r, mapIdx["Priority"]).setValue(payload.priority);
      if (payload.remark !== undefined) sh.getRange(r, mapIdx["Remark"]).setValue(payload.remark);
      if (payload.items !== undefined) sh.getRange(r, mapIdx["ItemsJSON"]).setValue(JSON.stringify(payload.items));
      if (payload.approver !== undefined) sh.getRange(r, mapIdx["ApproverEmail"]).setValue(payload.approver);
      return { ok:true };
    }
  }
  return { ok:false, message:"ไม่พบรหัสคำขอ" };
}

function exportExcelUrl(token){
  const email = CacheService.getDocumentCache().get(`adm:${token}`);
  if (!email) return { ok:false, message:"Session หมดอายุ" };
  return { ok:true, url: `https://docs.google.com/spreadsheets/d/${SHEET_ID}/export?format=xlsx&gid=0` };
}

function exportExcelFiltered(token, fromISO, toISO){
  const email = CacheService.getDocumentCache().get(`adm:${token}`);
  if (!email) return { ok:false, message:"Session หมดอายุ" };
  const sh = ensureSheet();
  const values = sh.getDataRange().getValues();
  const headers = values.shift();
  const rows = values.map(r => Object.fromEntries(headers.map((h,i)=>[h, r[i]])));
  let from = fromISO ? new Date(fromISO) : null;
  let to = toISO ? new Date(toISO) : null;
  const filtered = rows.filter(r=>{
    const ts = new Date(r.Timestamp);
    return (!from || ts >= from) && (!to || ts <= new Date(to.getTime()+24*60*60*1000-1));
  });
  // สร้างสเปรดชีตชั่วคราวแล้วคืนลิงก์ export xlsx
  const tmp = SpreadsheetApp.create(`Requests Export ${new Date().toISOString().slice(0,10)}`);
  const tsh = tmp.getSheets()[0];
  tsh.clear();
  tsh.appendRow(headers);
  filtered.forEach(r => tsh.appendRow(headers.map(h=>r[h])));
  return { ok:true, url: `https://docs.google.com/spreadsheets/d/${tmp.getId()}/export?format=xlsx&gid=0` };
}

function buildStats_(rows){
  const byDept = {}, byStatus = {};
  rows.forEach(r=>{ byDept[r.Department]=(byDept[r.Department]||0)+1; byStatus[r.Status]=(byStatus[r.Status]||0)+1; });
  return { byDept, byStatus, total: rows.length };
}

function safeHtml_(s){ return (s||"").toString().replace(/[&<>]/g, c=>({"&":"&amp;","<":"&lt;",">":"&gt;"}[c])); }
function renderItemsTable_(items){
  if (!items || !items.length) return "<i>ไม่มีรายการ</i>";
  const rows = items.map(it=>`<tr><td>${safeHtml_(it.name)}</td><td>${it.qty}</td><td>${safeHtml_(it.unit)}</td></tr>`).join("");
  return `<table border="1" cellpadding="6" cellspacing="0"><tr><th>ชื่ออุปกรณ์</th><th>จำนวน</th><th>หน่วย</th></tr>${rows}</table>`;
}

// =============================
// JSON API (POST /exec) — รองรับเรียกใช้งานจากเว็บภายนอก
// =============================
function doPost(e){
  try{
    const data = e && e.postData ? JSON.parse(e.postData.contents || '{}') : {};
    const op = data.op || data.action;         // ชื่อคำสั่ง
    const p  = data.payload || data;           // ข้อมูลที่ส่งมา

    switch(op){
      case 'submit':              // ส่งคำขอเบิก
        return json_( submitRequest(p) );
      case 'adminLogin':          // ล็อกอินหลังบ้าน
        return json_( adminLogin(p.email, p.password) );
      case 'adminGetData':        // ดึงข้อมูล (ช่วงวัน)
        return json_( adminGetData(p.token, p.fromISO, p.toISO) );
      case 'adminUpdate':         // แก้ไขคำขอ
        return json_( adminUpdateRequest(p.token, p.payload) );
      case 'adminDelete':         // ลบคำขอ
        return json_( adminDeleteRequest(p.token, p.reqId) );
      case 'exportExcelUrl':      // ลิงก์ export ทั้งหมด
        return json_( exportExcelUrl(p.token) );
      case 'exportExcelRange':    // ลิงก์ export ตามช่วง
        return json_( exportExcelFiltered(p.token, p.fromISO, p.toISO) );
      default:
        return json_({ ok:false, message:'Unknown op' });
    }
  } catch(err){
    return json_({ ok:false, message:String(err) });
  }
}

// helper: คืนค่ากลับเป็น JSON
function json_(obj){
  return ContentService
    .createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}


// =============================
// File: Index.html  (USER FORM)
// =============================
/*
<html>
  <head>
    <base target="_top">
    <?!= include('Styles'); ?>
  </head>
  <body>
    <div class="container">
      <div class="brandbar">
        <div class="brand">บริษัท ไทยซิน แมนูแฟคเจอริ่ง จำกัด</div>
      </div>
      <h1>แบบฟอร์มเบิกอุปกรณ์เครื่องเขียน</h1>
      <form id="reqForm" onsubmit="return false;">
        <div class="grid">
          <div>
            <label>ชื่อผู้เบิก</label>
            <input id="name" required>
          </div>
          <div>
            <label>รหัสพนักงาน</label>
            <input id="empCode" required>
          </div>
          <div>
            <label>แผนก</label>
            <select id="department" required onchange="onDeptChange()">
              <option value="">-- เลือกแผนก --</option>
              <? for (var i=0;i<departments.length;i++) { ?>
                <option><?= departments[i] ?></option>
              <? } ?>
            </select>
          </div>
          <div>
            <label>ผู้อนุมัติ (เลือกจากรายการ)</label>
            <select id="approverSelect" required></select>
          </div>
        </div>

        <h3>รายการอุปกรณ์</h3>
        <table class="table" id="itemTable">
          <thead>
            <tr>
              <th style="width:40%">ชื่ออุปกรณ์</th>
              <th style="width:20%">จำนวน</th>
              <th style="width:20%">หน่วย</th>
              <th style="width:20%"></th>
            </tr>
          </thead>
          <tbody></tbody>
        </table>
        <button class="btn ghost" type="button" onclick="addItemRow()">+ เพิ่มรายการ</button>

        <div class="grid">
          <div>
            <label>วัตถุประสงค์</label>
            <textarea id="purpose" rows="2"></textarea>
          </div>
          <div>
            <label>ความเร่งด่วน</label>
            <select id="priority">
              <option>ปกติ</option>
              <option>ด่วน</option>
            </select>
          </div>
        </div>

        <label>หมายเหตุ</label>
        <textarea id="remark" rows="2"></textarea>

        <div class="actions">
          <button class="btn" type="button" onclick="submitForm()">ส่งคำขอ</button>
          <button class="btn outline" type="button" onclick="openMailClient()">ส่งอีเมล (เปิดโปรแกรมอีเมล)</button>
        </div>

        <div id="success" class="toast" style="display:none;">✅ ส่งสำเร็จ กรุณารอการอนุมัติจากผู้มีอำนาจ</div>
      </form>
    </div>

    <script>
      const ITEM_CATALOG = <?!= JSON.stringify(items) ?>;
      const ALL_APPROVERS = <?!= JSON.stringify(allApprovers) ?>;

      function fillApproverOptions(selected){
        const sel = document.getElementById('approverSelect');
        sel.innerHTML = '<option value="">-- เลือกผู้อนุมัติ --</option>' + ALL_APPROVERS.map(e=>`<option ${selected===e?'selected':''}>${e}</option>`).join('');
      }

      function onDeptChange(){
        const dept = document.getElementById('department').value;
        google.script.run.withSuccessHandler(email => {
          fillApproverOptions(email||'');
        }).getApproverByDept(dept);
      }

      // default approver options
      fillApproverOptions('');

      function addItemRow(pref={name:'',qty:1,unit:''}){
        const tb = document.querySelector('#itemTable tbody');
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td>
            <select class="w100 itemName" onchange="toggleOther(this)">
              <option value="">-- เลือกรายการ --</option>
              ${ITEM_CATALOG.map(it=>`<option>${it}</option>`).join('')}
            </select>
            <input class="w100 itemOther" placeholder="โปรดระบุ" style="display:none;margin-top:6px;">
          </td>
          <td><input type="number" class="itemQty" min="1" value="${pref.qty||1}" required></td>
          <td><input class="itemUnit" placeholder="ชิ้น/กล่อง/แพ็ค" value="${pref.unit||''}" required></td>
          <td><button class="btn danger" type="button" onclick="this.closest('tr').remove()">ลบ</button></td>
        `;
        tb.appendChild(tr);
      }
      function toggleOther(sel){
        const other = sel.parentElement.querySelector('.itemOther');
        if (sel.value === 'อื่นๆ (ระบุ)') { other.style.display='block'; other.required = true; }
        else { other.style.display='none'; other.required = false; }
      }
      addItemRow();

      function collectItems(){
        const rows = Array.from(document.querySelectorAll('#itemTable tbody tr'));
        return rows.map(r=>{
          const nameSel = r.querySelector('.itemName').value;
          const other = r.querySelector('.itemOther').value.trim();
          const name = (nameSel === 'อื่นๆ (ระบุ)') ? (other || 'อื่นๆ') : nameSel;
          return { name, qty:Number(r.querySelector('.itemQty').value||0), unit:r.querySelector('.itemUnit').value.trim() };
        }).filter(it=>it.name && it.qty>0 && it.unit);
      }

      function submitForm(){
        const payload = {
          name: document.getElementById('name').value.trim(),
          empCode: document.getElementById('empCode').value.trim(),
          department: document.getElementById('department').value,
          purpose: document.getElementById('purpose').value.trim(),
          priority: document.getElementById('priority').value,
          remark: document.getElementById('remark').value.trim(),
          approver: document.getElementById('approverSelect').value,
          items: collectItems()
        };
        if (!payload.name || !payload.empCode || !payload.department || !payload.approver || payload.items.length===0){
          alert('กรุณากรอกข้อมูลให้ครบถ้วน และเลือกผู้อนุมัติจากรายการ');
          return;
        }
        google.script.run.withSuccessHandler(res=>{
          if (res && res.ok){
            document.getElementById('success').style.display='block';
            document.getElementById('reqForm').reset();
            document.querySelector('#itemTable tbody').innerHTML='';
            fillApproverOptions('');
            addItemRow();
          } else alert('เกิดข้อผิดพลาดในการส่ง');
        }).submitRequest(payload);
      }

      function openMailClient(){
        const name = document.getElementById('name').value.trim();
        const emp = document.getElementById('empCode').value.trim();
        const dept = document.getElementById('department').value;
        const purpose = document.getElementById('purpose').value.trim();
        const priority = document.getElementById('priority').value;
        const remark = document.getElementById('remark').value.trim();
        const approver = document.getElementById('approverSelect').value;
        if (!approver){ alert('กรุณาเลือกผู้อนุมัติ'); return; }
        const items = collectItems().map(i=>`- ${i.name} x ${i.qty} ${i.unit}`).join('\n');
        const subject = encodeURIComponent(`คำขอเบิกอุปกรณ์ — ${name} (${dept})`);
        const body = encodeURIComponent(`ชื่อ: ${name}\nรหัส: ${emp}\nแผนก: ${dept}\nความเร่งด่วน: ${priority}\nวัตถุประสงค์: ${purpose}\nหมายเหตุ: ${remark}\n\นรายการ:\n${items}`);
        const href = `mailto:${approver}?subject=${subject}&body=${body}`;
        window.open(href,'_blank');
      }
    </script>
  </body>
</html>
*/


// =============================
// File: Admin.html  (BACK OFFICE — with Edit/Delete/Date Filter)
// =============================
/*
<html>
  <head>
    <base target="_top">
    <?!= include('Styles'); ?>
  </head>
  <body>
    <div class="container">
      <div class="brandbar">
        <div class="brand">บริษัท ไทยซิน แมนูแฟคเจอริ่ง จำกัด</div>
      </div>
      <h1>ระบบหลังบ้าน • เบิกอุปกรณ์</h1>

      <div id="login">
        <div class="card">
          <h3>เข้าสู่ระบบผู้อนุมัติ</h3>
          <label>อีเมลผู้อนุมัติ</label>
          <select id="adminEmail">
            <? for (var i=0;i<adminUsers.length;i++) { ?>
              <option><?= adminUsers[i] ?></option>
            <? } ?>
          </select>
          <label>รหัสผ่าน</label>
          <input id="adminPass" type="password" placeholder="********">
          <button class="btn" onclick="login()">เข้าสู่ระบบ</button>
          <p class="muted">* demo password: 123456</p>
        </div>
      </div>

      <div id="dash" style="display:none;">
        <div class="filters card">
          <div class="grid">
            <div>
              <label>จากวันที่</label>
              <input type="date" id="fromDate">
            </div>
            <div>
              <label>ถึงวันที่</label>
              <input type="date" id="toDate">
            </div>
          </div>
          <div class="actions" style="margin-top:8px">
            <button class="btn" onclick="refresh()">กรองข้อมูล</button>
            <button class="btn outline" onclick="exportExcelAll()">ดาวน์โหลด Excel (ทั้งหมด)</button>
            <button class="btn outline" onclick="exportExcelRange()">ดาวน์โหลด Excel (ช่วงที่เลือก)</button>
          </div>
        </div>

        <div class="grid">
          <div class="stat">รวมทั้งหมด: <b id="stTotal">0</b></div>
          <div class="stat">ตามช่วงที่เลือก: <b id="stRange">0</b></div>
          <div class="stat">สถานะ: <span id="stStatus"></span></div>
        </div>

        <table class="table" id="tbl">
          <thead>
            <tr>
              <th>เวลา</th><th>รหัสคำขอ</th><th>ชื่อ</th><th>รหัสพนง.</th><th>แผนก</th>
              <th>รายการ</th><th>สถานะ</th><th>ผู้อนุมัติ</th><th>อนุมัติเมื่อ</th><th>แก้ไข</th><th>ลบ</th>
            </tr>
          </thead>
          <tbody></tbody>
        </table>
      </div>

      <!-- Modal แก้ไข -->
      <div id="modal" class="modal" style="display:none">
        <div class="modal-card">
          <h3>แก้ไขคำขอ</h3>
          <input type="hidden" id="m_reqId">
          <div class="grid">
            <div>
              <label>ชื่อ</label><input id="m_name">
            </div>
            <div>
              <label>รหัสพนง.</label><input id="m_emp">
            </div>
            <div>
              <label>แผนก</label>
              <select id="m_dept">
                <? for (var i=0;i<departments.length;i++) { ?>
                  <option><?= departments[i] ?></option>
                <? } ?>
              </select>
            </div>
            <div>
              <label>ผู้อนุมัติ</label>
              <select id="m_approver">
                <? for (var i=0;i<adminUsers.length;i++) { ?>
                  <option><?= adminUsers[i] ?></option>
                <? } ?>
              </select>
            </div>
          </div>
          <div class="grid">
            <div>
              <label>วัตถุประสงค์</label><textarea id="m_purpose" rows="2"></textarea>
            </div>
            <div>
              <label>ความเร่งด่วน</label>
              <select id="m_priority"><option>ปกติ</option><option>ด่วน</option></select>
            </div>
          </div>
          <label>หมายเหตุ</label><textarea id="m_remark" rows="2"></textarea>
          <label>รายการ (แก้ไขเป็นบรรทัดละ 1 รายการ: ชื่อ|จำนวน|หน่วย)</label>
          <textarea id="m_items" rows="5" placeholder="เช่น ปากกา|2|กล่อง\nลวดเสียบกระดาษ|10|กล่อง"></textarea>
          <div class="actions" style="margin-top:12px">
            <button class="btn" onclick="saveEdit()">บันทึก</button>
            <button class="btn outline" onclick="closeModal()">ยกเลิก</button>
          </div>
        </div>
      </div>

    </div>

    <script>
      let TOKEN = null; let CURRENT_ROWS = [];
      function login(){
        const email = document.getElementById('adminEmail').value;
        const pass = document.getElementById('adminPass').value;
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok) return alert(res.message||'เข้าสู่ระบบไม่สำเร็จ');
          TOKEN = res.token;
          document.getElementById('login').style.display='none';
          document.getElementById('dash').style.display='block';
          refresh();
        }).adminLogin(email, pass);
      }

      function refresh(){
        const from = document.getElementById('fromDate').value || null;
        const to   = document.getElementById('toDate').value || null;
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok){ alert(res.message||'หมดเซสชัน'); return; }
          CURRENT_ROWS = res.rows;
          document.getElementById('stTotal').innerText = res.stats.total;
          document.getElementById('stRange').innerText = res.rows.length;
          document.getElementById('stStatus').innerText = Object.entries(res.stats.byStatus).map(([k,v])=>`${k||'—'}: ${v}`).join(', ');
          const tb = document.querySelector('#tbl tbody'); tb.innerHTML = '';
          res.rows.forEach(r=>{
            const items = JSON.parse(r.ItemsJSON||'[]').map(i=>`${i.name} x ${i.qty} ${i.unit}`).join('<br>');
            const tr = document.createElement('tr');
            tr.innerHTML = `
              <td>${new Date(r.Timestamp).toLocaleString()}</td>
              <td>${r['Request ID']}</td>
              <td>${r.Name}</td>
              <td>${r.EmpCode}</td>
              <td>${r.Department}</td>
              <td>${items}</td>
              <td>${r.Status||''}</td>
              <td>${r.ApproverEmail||''}</td>
              <td>${r.ApprovalAt? new Date(r.ApprovalAt).toLocaleString(): ''}</td>
              <td><button class="btn" onclick="openEdit('${r['Request ID']}')">แก้ไข</button></td>
              <td><button class="btn danger" onclick="delReq('${r['Request ID']}')">ลบ</button></td>
            `;
            tb.appendChild(tr);
          });
        }).adminGetData(TOKEN, from, to);
      }

      function exportExcelAll(){
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok) return alert(res.message||'ไม่สามารถดาวน์โหลด');
          window.open(res.url,'_blank');
        }).exportExcelUrl(TOKEN);
      }
      function exportExcelRange(){
        const from = document.getElementById('fromDate').value || null;
        const to   = document.getElementById('toDate').value || null;
        if (!from && !to){ alert('กรุณาเลือกอย่างน้อย 1 ค่า (จากวันที่/ถึงวันที่)'); return; }
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok) return alert(res.message||'ไม่สามารถดาวน์โหลด');
          window.open(res.url,'_blank');
        }).exportExcelFiltered(TOKEN, from, to);
      }

      function delReq(reqId){
        if (!confirm('ยืนยันลบคำขอ '+reqId+' ?')) return;
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok) return alert(res.message||'ลบไม่สำเร็จ');
          refresh();
        }).adminDeleteRequest(TOKEN, reqId);
      }

      function openEdit(reqId){
        const r = CURRENT_ROWS.find(x=>x['Request ID']===reqId); if (!r) return;
        document.getElementById('m_reqId').value = reqId;
        document.getElementById('m_name').value = r.Name||'';
        document.getElementById('m_emp').value = r.EmpCode||'';
        document.getElementById('m_dept').value = r.Department||'';
        document.getElementById('m_approver').value = r.ApproverEmail||'';
        document.getElementById('m_purpose').value = r.Purpose||'';
        document.getElementById('m_priority').value = r.Priority||'ปกติ';
        document.getElementById('m_remark').value = r.Remark||'';
        const lines = (JSON.parse(r.ItemsJSON||'[]')).map(i=>`${i.name}|${i.qty}|${i.unit}`).join('\n');
        document.getElementById('m_items').value = lines;
        document.getElementById('modal').style.display='block';
      }
      function closeModal(){ document.getElementById('modal').style.display='none'; }

      function parseLinesToItems(text){
        return text.split(/\n+/).map(s=>s.trim()).filter(Boolean).map(line=>{
          const [name, qty, unit] = line.split('|').map(x=> (x||'').trim());
          return { name, qty: Number(qty||0), unit };
        }).filter(it=>it.name && it.qty>0 && it.unit);
      }

      function saveEdit(){
        const payload = {
          reqId: document.getElementById('m_reqId').value,
          name: document.getElementById('m_name').value.trim(),
          empCode: document.getElementById('m_emp').value.trim(),
          department: document.getElementById('m_dept').value,
          approver: document.getElementById('m_approver').value,
          purpose: document.getElementById('m_purpose').value.trim(),
          priority: document.getElementById('m_priority').value,
          remark: document.getElementById('m_remark').value.trim(),
          items: parseLinesToItems(document.getElementById('m_items').value)
        };
        google.script.run.withSuccessHandler(res=>{
          if (!res.ok) return alert(res.message||'บันทึกไม่สำเร็จ');
          closeModal();
          refresh();
        }).adminUpdateRequest(TOKEN, payload);
      }
    </script>
  </body>
</html>
*/


// =============================
// File: Styles.html  (SHARED)
// =============================
/*
<style>
  :root{ --brand:#22c55e; --bg:#f5fbf7; --ink:#0f172a; --muted:#64748b; --danger:#dc2626; }
  body{ font-family: "Prompt", "Segoe UI", Tahoma, sans-serif; background:var(--bg); color:var(--ink); margin:0; }
  .container{ max-width: 1080px; margin: 24px auto; padding: 0 16px; }
  .brandbar{ display:flex; align-items:center; justify-content:space-between; margin-bottom:8px; }
  .brand{ font-weight:700; color:#0b5ea8; }
  h1{ font-size: 24px; color:#0b5ea8; margin: 8px 0 16px; }
  label{ font-weight:600; font-size:14px; }
  input, select, textarea{ width:100%; padding:10px; border:1px solid #cbd5e1; border-radius:10px; margin:6px 0 12px; background:white; }
  .grid{ display:grid; grid-template-columns: repeat(2,1fr); gap:16px; }
  @media(max-width:720px){ .grid{ grid-template-columns:1fr; } }
  .table{ width:100%; border-collapse: collapse; background:white; border-radius:12px; overflow:hidden; }
  .table th,.table td{ border:1px solid #e2e8f0; padding:8px; text-align:left; vertical-align: top; }
  .btn{ background:var(--brand); color:white; border:none; padding:10px 14px; border-radius:10px; cursor:pointer; font-weight:600; }
  .btn:hover{ filter: brightness(0.95); }
  .btn.outline{ background:white; color:var(--brand); border:1px solid var(--brand); }
  .btn.ghost{ background:#e8f7ee; color:#065f46; border:1px dashed #a7f3d0; }
  .btn.danger{ background:var(--danger); }
  .actions{ display:flex; gap:12px; margin-top:8px; flex-wrap:wrap; }
  .toast{ background:#dcfce7; color:#14532d; border:1px solid #86efac; padding:10px; border-radius:10px; margin-top:16px; text-align:center; }
  .card{ background:white; border:1px solid #e2e8f0; border-radius:12px; padding:16px; }
  .muted{ color:var(--muted); font-size:12px; }
  .modal{ position:fixed; inset:0; background:rgba(0,0,0,.35); display:flex; align-items:center; justify-content:center; z-index:9999; }
  .modal-card{ background:white; border-radius:12px; padding:16px; width: min(720px, 96vw); border:1px solid #e2e8f0; }
</style>
*/
