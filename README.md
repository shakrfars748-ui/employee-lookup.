<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>استعلام بيانات الموظف</title>
<style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    .container { max-width: 600px; margin: auto; }
    label { font-weight: bold; margin-top: 10px; display: block; }
    input, select { width: 100%; padding: 8px; margin-top: 5px; }
    button { width: 100%; padding: 10px; margin-top: 15px; font-size: 18px; cursor: pointer; }
    table { width: 100%; border-collapse: collapse; margin-top: 20px; }
    table, th, td { border: 1px solid #444; }
    th, td { padding: 8px; text-align: center; }
    .lang-menu { position: fixed; top: 20px; left: 20px; cursor: pointer; font-size: 30px; z-index: 1000; }
    .lang-list { position: fixed; top: 65px; left: 15px; background: white; border: 1px solid #ccc; padding: 10px; display: none; z-index: 999; }
    .lang-list div { padding: 5px 10px; cursor: pointer; font-size: 18px; }
    .lang-list div:hover { background: #eee; }
</style>
</head>
<body>

<div class="lang-menu" onclick="toggleLangList()">🌐</div>

<div class="lang-list" id="langList">
    <div onclick="setLang('ar')">🇸🇦 العربية</div>
    <div onclick="setLang('en')">🇺🇸 English</div>
</div>

<div class="container">
    <h2 id="title">استعلام عن بيانات الموظف</h2>

    <label id="label_id">ادخل رقمك الوظيفي</label>
    <input type="text" id="emp_id">

    <label id="label_birth">ادخل تاريخ ميلادك</label>
    <div style="display:flex; gap:10px;">
        <select id="day"></select>
        <select id="month"></select>
        <select id="year"></select>
    </div>

    <button onclick="searchData()" id="btn_search">بحث</button>
    <div id="result"></div>
</div>

<script>
let employees = []; // البيانات ستأتي من شيت Google
let currentLang = "ar";
let txt = {};

// ملء التاريخ
function loadDateFields() {
    const dayEl = document.getElementById('day');
    const monthEl = document.getElementById('month');
    const yearEl = document.getElementById('year');

    for (let d = 1; d <= 31; d++) dayEl.innerHTML += `<option>${d}</option>`;
    for (let m = 1; m <= 12; m++) monthEl.innerHTML += `<option>${m}</option>`;
    for (let y = 1960; y <= 2025; y++) yearEl.innerHTML += `<option>${y}</option>`;
}
loadDateFields();

// تحميل البيانات من Google Sheets
async function loadEmployees(){
    const res = await fetch("https://script.google.com/macros/s/XXXXX/exec"); // ضع رابط Web App هنا
    employees = await res.json();
}
loadEmployees();

// البحث عن الموظف
function searchData() {
    const id = document.getElementById("emp_id").value.trim();
    const birth = `${year.value}-${String(month.value).padStart(2,"0")}-${String(day.value).padStart(2,"0")}`;
    const emp = employees.find(e => e.ID == id && e.Birth == birth);

    if(!emp){
        result.innerHTML = `<p style='color:red'>${txt.noData}</p>`;
        return;
    }

    result.innerHTML = `
        <table>
            <tr><th>${txt.name}</th><td>${emp.Name}</td></tr>
            <tr><th>${txt.id}</th><td>${emp.ID}</td></tr>
            <tr><th>${txt.job}</th><td>${emp.Job}</td></tr>
            <tr><th>${txt.branch}</th><td>${emp.Branch}</td></tr>
            <tr><th>${txt.shift}</th><td>${emp.Shift}</td></tr>
            <tr><th>${txt.start}</th><td>${emp.Start}</td></tr>
            <tr><th>${txt.end}</th><td>${emp.End}</td></tr>
        </table>
    `;
}

// اللغات
const languages = {
    ar: { dir: "rtl", title: "استعلام عن بيانات الموظف", label_id: "ادخل رقمك الوظيفي", label_birth: "ادخل تاريخ ميلادك", search: "بحث", noData: "لا توجد بيانات مطابقة", name: "الاسم", id: "الرقم الوظيفي", job: "المسمى الوظيفي", branch: "الفرع", shift: "الشفت", start: "بداية الدوام", end: "نهاية الدوام" },
    en: { dir: "ltr", title: "Employee Lookup", label_id: "Enter Employee ID", label_birth: "Enter Birth Date", search: "Search", noData: "No matching records found", name: "Name", id: "Employee ID", job: "Job Title", branch: "Branch", shift: "Shift", start: "Start Time", end: "End Time" }
};

// تغيير اللغة
function toggleLangList() {
    let menu = document.getElementById("langList");
    menu.style.display = (menu.style.display === "block") ? "none" : "block";
}
function setLang(lang){
    currentLang = lang;
    txt = languages[lang];

    document.body.setAttribute("dir", txt.dir);
    document.body.style.textAlign = (txt.dir === "rtl") ? "right" : "left";

    let emojiBtn = document.querySelector('.lang-menu');
    if(txt.dir === "rtl"){ emojiBtn.style.left = "20px"; emojiBtn.style.right = "auto"; }
    else { emojiBtn.style.right = "20px"; emojiBtn.style.left = "auto"; }

    title.innerText = txt.title;
    label_id.innerText = txt.label_id;
    label_birth.innerText = txt.label_birth;
    btn_search.innerText = txt.search;
    result.innerHTML = "";
    langList.style.display = "none";
}
setLang("ar");
</script>

</body>
</html>
