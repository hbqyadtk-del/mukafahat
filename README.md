
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>نظام المربي — إدارة الصفوف والمواد</title>
<style>
  :root{
    --bg:#0a0f1c;
    --card:#111827;
    --border:#1f2937;
    --text:#ffffff;
    --muted:#9ca3af;
    --accent:#38bdf8;
    --ok:#22c55e;
    --warn:#f59e0b;
    --danger:#ef4444;
  }
  
  *{box-sizing:border-box}
  body{margin:0;background:var(--bg);color:var(--text);font-family:Tahoma,Arial,sans-serif}
  header{position:sticky;top:0;z-index:10;background:#0b1220;border-bottom:1px solid var(--border);padding:14px 18px;text-align:center;font-weight:800}
  .container{max-width:1100px;margin:18px auto;padding:0 16px}
  .grid{display:grid;gap:14px;grid-template-columns:repeat(auto-fit,minmax(220px,1fr))}
  .card{background:var(--card);border:1px solid var(--border);border-radius:16px;padding:18px;cursor:pointer;transition:.2s;box-shadow:0 6px 18px rgba(0,0,0,.25)}
  .card:hover{transform:translateY(-3px);border-color:#213145}
  .title{display:flex;align-items:center;justify-content:space-between;font-weight:800;margin-bottom:6px}
  .dot{width:10px;height:10px;border-radius:50%;background:var(--ok);box-shadow:0 0 10px var(--ok);margin-inline-start:6px}
  .pill{background:#0b1a2b;border:1px solid #1f3550;color:var(--accent);padding:6px 10px;border-radius:10px;font-size:12px}
  .sub{color:var(--muted);font-size:13px}
  .btn{padding:10px 14px;border:none;border-radius:10px;cursor:pointer;font-weight:800}
  .btn-back{background:#0b1a2b;color:#bcd7f7;border:1px solid #1f3550}
  .btn-add{background:var(--warn);color:#2a1a04}
  .btn-save{background:var(--ok);color:#062a13}
  .btn-del{background:var(--danger);color:#2a0a0a;padding:6px 10px;border-radius:8px}
  .hint{color:var(--muted);font-size:13px;margin:8px 0 14px}
  .page{display:none}
  h2{margin:0 0 14px;font-size:20px}
  .row{display:flex;gap:10px;flex-wrap:wrap}
  .center{display:flex;justify-content:space-between;align-items:center;gap:8px;margin-bottom:12px}
  /* جدول المادة */
  .table-wrap{background:#0b1220;border:1px solid var(--border);border-radius:14px;overflow-x:auto}
  table{width:100%;border-collapse:collapse;min-width:700px}
  th,td{border:1px solid var(--border);padding:10px;text-align:center;font-size:14px}
  th{background:#0e1a2b;color:#cfe8ff;font-weight:800}
  th.name,td.name{text-align:right}
  .cell{width:100%;min-height:24px;outline:none;border:none;background:transparent;color:var(--text);white-space:pre-wrap;word-break:break-word;text-align:right;padding:2px}
  .cell[contenteditable="false"]{color:#a7b4c2}
  /* مودالات */
  .modal{position:fixed;inset:0;display:none;place-items:center;background:rgba(0,0,0,.55);z-index:20}
  .box{width:95%;max-width:420px;background:#0c1424;border:1px solid var(--border);border-radius:16px;padding:18px}
  .box h3{margin:0 0 10px}
  .input{width:100%;padding:10px 12px;border-radius:10px;border:1px solid #1f2937;background:#071020;color:var(--text);outline:none}
  .role-btn{flex:1 1 180px;padding:12px;text-align:center;border-radius:12px;border:1px solid #1f2937;background:#0b1a2b;color:#cfe8ff;cursor:pointer;font-weight:800}
  .role-btn:hover{border-color:#284062}
  .muted{color:#9ca3af}
</style>
</head>
<body>
<header>📘 نظام المربي — إدارة الصفوف والمواد</header>

<div class="container">
  <!-- الصفحة 1: اختيار الصف -->
  <section id="pageHome" class="page" style="display:block">
    <h2>اختر الصف</h2>
    <div id="gridClasses" class="grid"></div>
  </section>

  <!-- الصفحة 2: اختيار الدور -->
  <section id="pageRole" class="page">
    <div class="center">
      <h2 id="roleTitle">الصف:</h2>
      <button class="btn btn-back" onclick="goHome()">← رجوع</button>
    </div>
    <div class="hint">اختر نوع الدخول لهذا الصف.</div>
    <div class="row">
      <button class="role-btn" onclick="chooseRole('parent')">👨‍👩‍👦 ولي أمر</button>
      <button class="role-btn" onclick="chooseRole('teacher')">👨‍🏫 مربي</button>
    </div>
  </section>

  <!-- الصفحة 3: قائمة المواد -->
  <section id="pageMaterials" class="page">
    <div class="center">
      <h2 id="matTitle">مواد الصف</h2>
      <button class="btn btn-back" onclick="backToRole()">← رجوع</button>
    </div>
    <div id="roleHint" class="hint"></div>
    <div id="gridSubjects" class="grid"></div>
  </section>

  <!-- الصفحة 4: جدول المادة -->
  <section id="pageTable" class="page">
    <div class="center">
      <h2 id="tableTitle">📗 مادة</h2>
      <button class="btn btn-back" onclick="backToMaterials()">← رجوع</button>
    </div>
    <div id="tableHint" class="hint"></div>
    <div class="table-wrap" id="tableWrap"></div>
    <div id="teacherActions" class="row" style="margin-top:12px;display:none">
      <button class="btn btn-add" onclick="addStudent()">➕ إضافة طالب</button>
      <button class="btn btn-save" onclick="manualSave()">💾 حفظ</button>
    </div>
  </section>
</div>

<!-- مودال كلمة سر المربي العامة -->
<div id="modalTeacher" class="modal">
  <div class="box">
    <h3>🔑 كلمة سر المربي</h3>
    <p class="muted">اكتب كلمة السر العامة للمربي لهذا الصف.</p>
    <input id="teacherPassInput" type="password" class="input" placeholder="590" />
    <div class="row" style="margin-top:10px">
      <button class="role-btn" onclick="confirmTeacherPass()">دخول</button>
      <button class="role-btn" onclick="closeTeacherModal()">إلغاء</button>
    </div>
  </div>
</div>

<!-- مودال كلمة سر المادة الخاصة -->
<div id="modalSubject" class="modal">
  <div class="box">
    <h3 id="subPassTitle">🔐 كلمة سر المادة</h3>
    <p id="subPassHint" class="muted"></p>
    <input id="subjectPassInput" type="password" class="input" placeholder="اكتب كلمة سر المادة" />
    <div class="row" style="margin-top:10px">
      <button class="role-btn" onclick="confirmSubjectPass()">متابعة</button>
      <button class="role-btn" onclick="closeSubjectModal()">إلغاء</button>
    </div>
  </div>
</div>

<script>
/* =========================
   الإعدادات والثوابت
========================= */
const TEACHER_MASTER_PASS = "590"; // كلمة السر العامة للمربي
const classes = [
  "صف أول ابتدائي","صف ثاني ابتدائي","صف ثالث ابتدائي",
  "صف رابع ابتدائي","صف خامس ابتدائي","صف سادس ابتدائي",
  "صف أول إعدادي","صف ثاني إعدادي","صف ثالث إعدادي",
  "صف أول ثانوي","صف ثاني ثانوي","صف ثالث ثانوي"
];
const subjects = [
  "قرآن","إسلامية","عربي","رياضيات","اجتماعيات","علوم",
  "كيمياء","أحياء","فيزياء","إنجليزي","حاسوب"
];

/* مفاتيح التخزين */
const kRoster   = cls => `roster__${cls}`;                   // مصفوفة أسماء الطلاب للصف
const kData     = (cls,subj) => `data__${cls}__${subj}`;     // بيانات المادة (صفيف كائنات)
const kSubPass  = (cls,subj) => `subpass__${cls}__${subj}`;  // كلمة سر المادة

/* حالة التطبيق */
let currentClass = null;
let currentRole  = null;  // "parent" | "teacher"
let currentSubject = null;

/* عناصر DOM مختصرة */
const byId = id => document.getElementById(id);
const pageHome = byId('pageHome');
const pageRole = byId('pageRole');
const pageMaterials = byId('pageMaterials');
const pageTable = byId('pageTable');

/* ================
   صفحة الصفوف
================ */
const gridClasses = byId('gridClasses');
classes.forEach(cls=>{
  const card = document.createElement('div');
  card.className = 'card';
  card.onclick = () => openRolePage(cls);
  card.innerHTML = `
    <div class="title">
      <span style="display:flex;align-items:center;gap:6px">
        <span class="dot"></span><span>${cls}</span>
      </span>
      <span class="pill">ادخل</span>
    </div>
    <div class="sub">البيانات محفوظة محليًا على هذا الجهاز.</div>
  `;
  gridClasses.appendChild(card);
});
function openRolePage(cls){
  currentClass = cls;
  pageHome.style.display = 'none';
  pageRole.style.display = 'block';
  byId('roleTitle').textContent = `اختيار الدور — ${cls}`;
}
function goHome(){
  pageRole.style.display = 'none';
  pageMaterials.style.display = 'none';
  pageTable.style.display = 'none';
  pageHome.style.display = 'block';
  currentClass = null; currentRole=null; currentSubject=null;
}

/* ================
   اختيار الدور
================ */
function chooseRole(role){
  currentRole = role;
  if(role === 'teacher'){
    openTeacherModal();
  }else{
    openMaterialsPage();
  }
}
function backToRole(){
  pageMaterials.style.display = 'none';
  pageRole.style.display = 'block';
}

/* ================
   صفحة المواد (كروت)
================ */
const gridSubjects = byId('gridSubjects');
function openMaterialsPage(){
  pageRole.style.display = 'none';
  pageMaterials.style.display = 'block';
  byId('matTitle').textContent = `مواد — ${currentClass}`;
  byId('roleHint').textContent = (currentRole==='teacher')
    ? 'وضع المربي: اختر المادة لإدخال/تعديل الدرجات.'
    : 'وضع ولي الأمر: اختر المادة لعرض درجات الطالب (قراءة فقط).';

  gridSubjects.innerHTML = '';
  subjects.forEach(sub=>{
    const c = document.createElement('div');
    c.className = 'card';
    c.onclick = ()=> openSubject(sub);
    c.innerHTML = `
      <div class="title">
        <span style="display:flex;align-items:center;gap:6px">
          <span class="dot"></span><span>${sub}</span>
        </span>
        <span class="pill">${(currentRole==='teacher'?'إدارة':'عرض')}</span>
      </div>
      <div class="sub">اسم المادة: ${sub}</div>
    `;
    gridSubjects.appendChild(c);
  });

  // تأكد من وجود كشف أسماء مبدئي
  ensureRoster(currentClass);
}

/* ================
   صفحة جدول المادة
================ */
function openSubject(subj){
  currentSubject = subj;
  if(currentRole==='teacher'){
    // تحقق من كلمة سر المادة الخاصة
    const saved = localStorage.getItem(kSubPass(currentClass, currentSubject));
    if(!saved){
      // أول مرة: اطلب تعيين كلمة
      byId('subPassTitle').textContent = `تعيين كلمة سر — ${currentSubject}`;
      byId('subPassHint').textContent = 'أول مرة لهذه المادة. عيّن كلمة سر وسيتم تذكرها.';
      byId('subjectPassInput').value = '';
      byId('modalSubject').style.display = 'grid';
      return;
    }else{
      // اطلب إدخالها
      byId('subPassTitle').textContent = `🔐 كلمة سر المادة — ${currentSubject}`;
      byId('subPassHint').textContent = 'اكتب كلمة السر التي عيّنها مدرس هذه المادة.';
      byId('subjectPassInput').value = '';
      byId('modalSubject').style.display = 'grid';
      return;
    }
  }
  // ولي الأمر → افتح مباشرة
  enterTable(false);
}

function enterTable(canEdit){
  pageMaterials.style.display = 'none';
  pageTable.style.display = 'block';
  byId('tableTitle').textContent = `📗 مادة ${currentSubject}`;
  byId('tableHint').textContent = canEdit
    ? `وضع المربي — يمكنك الإضافة والحذف والتعديل. الصف: ${currentClass}`
    : `وضع ولي الأمر — قراءة فقط. الصف: ${currentClass}`;
  byId('teacherActions').style.display = canEdit ? 'flex' : 'none';

  // تأكد من تزامن أسماء الطلاب بين المادة وكشف الأسماء
  syncSubjectWithRoster(currentClass, currentSubject);

  // ابنِ الجدول
  renderTable(canEdit);
}

/* بناء جدول المادة */
function renderTable(canEdit){
  const data = loadSubjectData(currentClass, currentSubject);
  let html = `<table><thead><tr>
    <th class="name">اسم الطالب</th>
    <th>الحضور</th>
    <th>المشاركة</th>
    <th>اختبار شفوي</th>
    <th>اختبار تحريري</th>
    ${canEdit?'<th>إجراء</th>':''}
  </tr></thead><tbody>`;

  data.forEach((row, i)=>{
    html += `<tr>
      <td class="name"><div class="cell" contenteditable="${canEdit?'true':'false'}" data-f="name" data-i="${i}">${esc(row.name)}</div></td>
      <td><div class="cell" contenteditable="${canEdit?'true':'false'}" data-f="attend" data-i="${i}">${esc(row.attend??'')}</div></td>
      <td><div class="cell" contenteditable="${canEdit?'true':'false'}" data-f="part" data-i="${i}">${esc(row.part??'')}</div></td>
      <td><div class="cell" contenteditable="${canEdit?'true':'false'}" data-f="oral" data-i="${i}">${esc(row.oral??'')}</div></td>
      <td><div class="cell" contenteditable="${canEdit?'true':'false'}" data-f="written" data-i="${i}">${esc(row.written??'')}</div></td>
      ${canEdit?`<td><button class="btn-del" data-del="${i}">حذف</button></td>`:''}
    </tr>`;
  });

  html += `</tbody></table>`;
  byId('tableWrap').innerHTML = html;

  if(!canEdit) return;

  // أحداث التحرير الفوري
  byId('tableWrap').querySelectorAll('.cell[contenteditable="true"]').forEach(el=>{
    el.addEventListener('input', ()=> {
      const i = +el.getAttribute('data-i');
      const f = el.getAttribute('data-f');
      const arr = loadSubjectData(currentClass, currentSubject);
      arr[i][f] = el.innerText.trim();
      saveSubjectData(currentClass, currentSubject, arr);
    });
  });
  // حذف طالب
  byId('tableWrap').querySelectorAll('[data-del]').forEach(btn=>{
    btn.addEventListener('click', ()=>{
      const i = +btn.getAttribute('data-del');
      if(!confirm('تأكيد حذف هذا الطالب من جميع المواد في هذا الصف؟')) return;
      // حذف من كشف الأسماء العام
      const roster = loadRoster(currentClass);
      const name = (loadSubjectData(currentClass,currentSubject)[i]||{}).name;
      const idx = roster.indexOf(name);
      if(idx>-1) roster.splice(idx,1);
      saveRoster(currentClass, roster);
      // مزامنة كل المواد
      subjects.forEach(s=> removeStudentFromSubject(currentClass, s, name));
      // إعادة العرض
      syncSubjectWithRoster(currentClass, currentSubject);
      renderTable(true);
    });
  });
}

/* ================
   إضافة طالب جديد
================ */
function addStudent(){
  const name = prompt('اكتب اسم الطالب:');
  if(!name) return;
  // أضِف إلى كشف الأسماء
  const roster = loadRoster(currentClass);
  if(!roster.includes(name)) {
    roster.push(name);
    saveRoster(currentClass, roster);
  }
  // أضِف إلى جميع المواد (صفوف فارغة)
  subjects.forEach(s=>{
    const arr = loadSubjectData(currentClass, s);
    if(!arr.find(r=>r.name===name)){
      arr.push({name, attend:'', part:'', oral:'', written:''});
      saveSubjectData(currentClass, s, arr);
    }
  });
  // أعِد الرسم للمادة الحالية
  renderTable(true);
}
function manualSave(){ alert('✅ تم حفظ البيانات محليًا في هذا الجهاز.'); }

/* ================
   تنقّل
================ */
function backToMaterials(){
  pageTable.style.display = 'none';
  pageMaterials.style.display = 'block';
}

/* =========================
   مخزن البيانات (localStorage)
========================= */
function ensureRoster(cls){
  let roster = loadRoster(cls);
  if(roster.length===0){
    roster = ["محمد ياسين أحمد الجدري","أحمد صالح محمد"];
    saveRoster(cls, roster);
    // أنشئ سجلات لكل المواد
    subjects.forEach(s=>{
      const arr = roster.map(n=>({name:n, attend:'', part:'', oral:'', written:''}));
      saveSubjectData(cls, s, arr);
    });
  }
}
function loadRoster(cls){
  try{ return JSON.parse(localStorage.getItem(kRoster(cls))||'[]'); }catch{ return []; }
}
function saveRoster(cls, roster){
  localStorage.setItem(kRoster(cls), JSON.stringify(roster));
}
function loadSubjectData(cls, subj){
  try{ return JSON.parse(localStorage.getItem(kData(cls,subj))||'[]'); }catch{ return []; }
}
function saveSubjectData(cls, subj, arr){
  localStorage.setItem(kData(cls,subj), JSON.stringify(arr));
}
function syncSubjectWithRoster(cls, subj){
  const roster = loadRoster(cls);
  let arr = loadSubjectData(cls, subj);
  // أضِف المفقودين
  roster.forEach(name=>{
    if(!arr.find(r=>r.name===name)){
      arr.push({name, attend:'', part:'', oral:'', written:''});
    }
  });
  // احذف الزائدين غير الموجودين في الروستر
  arr = arr.filter(r=>roster.includes(r.name));
  saveSubjectData(cls, subj, arr);
}
function removeStudentFromSubject(cls, subj, name){
  let arr = loadSubjectData(cls, subj);
  arr = arr.filter(r=>r.name!==name);
  saveSubjectData(cls, subj, arr);
}

/* =========================
   حمايات كلمة السر
========================= */
function openTeacherModal(){
  byId('teacherPassInput').value = '';
  byId('modalTeacher').style.display = 'grid';
}
function closeTeacherModal(){ byId('modalTeacher').style.display = 'none'; }
function confirmTeacherPass(){
  const val = (byId('teacherPassInput').value||'').trim();
  if(val === TEACHER_MASTER_PASS){
    closeTeacherModal();
    openMaterialsPage();
  }else{
    alert('❌ كلمة السر غير صحيحة.');
  }
}
function closeSubjectModal(){ byId('modalSubject').style.display = 'none'; }
function confirmSubjectPass(){
  const entered = (byId('subjectPassInput').value||'').trim();
  if(!entered){ alert('اكتب كلمة السر.'); return; }
  const key = kSubPass(currentClass, currentSubject);
  const saved = localStorage.getItem(key);
  if(!saved){
    // تعيين أول مرة
    localStorage.setItem(key, entered);
    closeSubjectModal();
    enterTable(true);
  }else{
    if(saved === entered){
      closeSubjectModal();
      enterTable(true);
    }else{
      alert('❌ كلمة سر المادة غير صحيحة.');
    }
  }
}

/* أدوات */
function esc(s){ return String(s??'').replace(/&/g,'&amp;').replace(/</g,'&lt;') }

/* إغلاق المودالات عند الضغط خارج الصندوق */
byId('modalTeacher').addEventListener('click',e=>{ if(e.target.id==='modalTeacher') closeTeacherModal(); });
byId('modalSubject').addEventListener('click',e=>{ if(e.target.id==='modalSubject') closeSubjectModal(); });
</script>
</body>
</html>
