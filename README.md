<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>📘 نظام المربي — إدارة الصفوف والمواد</title>
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
  table{width:100%;border-collapse:collapse;min-width:600px}
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

  /* ✅ التصميم المتجاوب */
  @media (max-width: 768px) {
    header {font-size:16px;padding:10px;}
    .container{padding:10px;}
    h2{font-size:18px;}
    table{min-width:100%;font-size:12px;}
    th,td{padding:6px;}
    .btn,.role-btn{font-size:13px;padding:8px 10px;}
    .grid{grid-template-columns:repeat(auto-fit,minmax(160px,1fr));}
    .box{max-width:90%;}
  }
</style>
</head>
<body>
<header>📘 نظام المربي — إدارة الصفوف والمواد</header>

<div class="container" id="app">
  <section id="pageHome" class="page" style="display:block">
    <h2>اختر الصف</h2>
    <div id="gridClasses" class="grid"></div>
  </section>

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

  <section id="pageMaterials" class="page">
    <div class="center">
      <h2 id="matTitle">مواد الصف</h2>
      <button class="btn btn-back" onclick="backToRole()">← رجوع</button>
    </div>
    <div id="roleHint" class="hint"></div>
    <div id="gridSubjects" class="grid"></div>
  </section>

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

<!-- المودالات -->
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
/* نفس كود الجافاسكربت الأصلي تماماً بدون تغيير */
</script>
</body>
</html>
