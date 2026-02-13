<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>ناطر | Nater</title>
<style>
body { font-family: Arial; direction: rtl; background:#f5f5f5; padding:20px }
h2 { text-align:center }
input, select, button { padding:8px; margin:5px }
.section { background:white; padding:15px; margin:15px 0; border-radius:8px }
.grid { display:grid; grid-template-columns: repeat(6,1fr); gap:6px }
.box { padding:8px; text-align:center; color:white; border-radius:6px; cursor:pointer }
.red { background:red }
.green { background:green }
</style>
</head>
<body>

<h2>ناطر | Nater</h2>

<div class="section">
<h3>👤 تسجيل عضو</h3>
<input id="name" placeholder="اسم المحل">
<select id="type">
<option value="restaurant">مطعم</option>
<option value="salon">صالون</option>
<option value="clinic">عيادة</option>
<option value="cafe">كافيه</option>
</select>
<input id="start" type="number" placeholder="بداية الأرقام">
<input id="end" type="number" placeholder="نهاية الأرقام">
<input id="chairs" type="number" placeholder="عدد الكراسي (للكافيه)">
<button onclick="register()">تسجيل</button>
</div>

<div class="section">
<h3>👑 دخول ماستر</h3>
<input id="masterPass" type="password" placeholder="كلمة المرور">
<button onclick="loginMaster()">دخول</button>
<div id="admin"></div>
</div>

<div class="section">
<h3>🧍‍♂️ الزائر</h3>
<input id="checkPlace" placeholder="اسم المحل">
<input id="checkNumber" placeholder="رقمك">
<button onclick="checkVisitor()">تحقق</button>
<p id="visitorResult"></p>
</div>

<script>
let members = JSON.parse(localStorage.getItem("members")) || [];
let masterPassword = "1234";

function save() {
localStorage.setItem("members", JSON.stringify(members));
}

function register() {
let m = {
name: name.value,
type: type.value,
start: +start.value,
end: +end.value,
chairs: +chairs.value || 0,
status: {}
};
members.push(m);
save();
alert("تم تسجيل العضو");
}

function loginMaster() {
if(masterPass.value !== masterPassword){ alert("خطأ"); return; }
admin.innerHTML="";
members.forEach((m,i)=>{
admin.innerHTML+=`
<div>
<strong>${m.name}</strong> (${m.type})
<button onclick="openMember(${i})">فتح</button>
</div>
`;
});
}

function openMember(i){
let m=members[i];
admin.innerHTML=`<h3>${m.name}</h3>`;

if(m.type==="cafe"){
admin.innerHTML+=`
<h2>🪑 ${m.chairs}</h2>
<button onclick="updateChairs(${i},1)">➕</button>
<button onclick="updateChairs(${i},-1)">➖</button>
`;
return;
}

admin.innerHTML+=`<div class="grid" id="grid"></div>`;
let grid=document.getElementById("grid");

for(let n=m.start;n<=m.end;n++){
if(!m.status[n]) m.status[n]=false;
let div=document.createElement("div");
div.className="box "+(m.status[n]?"green":"red");
div.innerText=n;
div.onclick=()=>{
m.status[n]=!m.status[n];
save();
openMember(i);
};
grid.appendChild(div);
}
}

function updateChairs(i,val){
members[i].chairs+=val;
if(members[i].chairs<0) members[i].chairs=0;
save();
openMember(i);
}

function checkVisitor(){
let place=members.find(m=>m.name===checkPlace.value);
if(!place){ visitorResult.innerText="المحل غير موجود"; return; }

if(place.type==="cafe"){
visitorResult.innerText="🪑 الكراسي المتاحة: "+place.chairs;
return;
}

let num=checkNumber.value;
if(place.status[num]){
visitorResult.innerText="✅ جاهز";
}else{
visitorResult.innerText="⏳ غير جاهز";
}
}
</script>

</body>
</html>
