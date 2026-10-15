<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>متجر قهوة مختصة</title>
<link href="https://fonts.googleapis.com/css2?family=Reem+Kufi:wght@600;700&family=Readex+Pro:wght@300;400;500&display=swap" rel="stylesheet">
<style>
:root{--bg:#E9EEF0;--card:#fff;--ink:#12303A;--muted:#5B7078;--accent:#E8A317;--line:#CBD6DA}
*{box-sizing:border-box;margin:0}
html{scroll-behavior:smooth}
body{font-family:"Readex Pro",Tahoma,sans-serif;background:var(--bg);color:var(--ink);line-height:1.7;padding-bottom:90px}
h1,h2,h3,.logo{font-family:"Reem Kufi","Readex Pro",Tahoma,sans-serif}
button{font:inherit;cursor:pointer;border:0}
:focus-visible{outline:3px solid var(--accent);outline-offset:2px}

header{position:sticky;top:0;z-index:5;background:var(--ink);color:#fff;display:flex;align-items:center;justify-content:space-between;padding:12px 18px}
.logo{font-size:22px;font-weight:700}
.cart-btn{background:var(--accent);color:var(--ink);padding:8px 16px;border-radius:99px;font-weight:500;display:flex;gap:8px;align-items:center}
.cart-btn b{background:var(--ink);color:#fff;min-width:22px;height:22px;border-radius:99px;display:grid;place-items:center;font-size:13px}

.hero{padding:34px 18px 10px;max-width:760px;margin:auto}
.hero h1{font-size:clamp(30px,8vw,48px);line-height:1.25;font-weight:700}
.hero p{color:var(--muted);margin-top:10px;max-width:46ch}
.filters{display:flex;gap:8px;margin-top:22px;overflow-x:auto;padding-bottom:4px}
.filters button{background:transparent;border:1.5px solid var(--ink);color:var(--ink);padding:7px 18px;border-radius:99px;white-space:nowrap}
.filters button[aria-pressed="true"]{background:var(--ink);color:#fff}

main{max-width:760px;margin:auto;padding:18px;display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:14px}
.card{background:var(--card);border-radius:18px;padding:14px;display:flex;flex-direction:column}
.card svg{width:100%;height:150px}
.card h3{font-size:19px;margin-top:8px}
.notes{color:var(--muted);font-size:14px;flex:1}
.row{display:flex;justify-content:space-between;align-items:center;margin-top:12px}
.price{font-weight:500}
.add{background:var(--ink);color:#fff;padding:7px 14px;border-radius:10px;font-size:14px}
.add:active{transform:scale(.96)}

.drawer{position:fixed;inset:auto 0 0 0;max-height:80vh;background:#fff;border-radius:22px 22px 0 0;box-shadow:0 -10px 40px rgba(18,48,58,.25);transform:translateY(105%);transition:transform .25s;z-index:10;display:flex;flex-direction:column}
.drawer.open{transform:none}
.drawer-head{display:flex;justify-content:space-between;align-items:center;padding:16px 20px;border-bottom:1px solid var(--line)}
.drawer-head h2{font-size:22px}
.close{background:var(--bg);width:36px;height:36px;border-radius:50%;font-size:18px}
.items{overflow:auto;padding:6px 20px}
.item{display:flex;justify-content:space-between;align-items:center;padding:12px 0;border-bottom:1px solid var(--line);gap:10px}
.item small{color:var(--muted);display:block}
.qty{display:flex;align-items:center;gap:10px}
.qty button{width:30px;height:30px;border-radius:50%;background:var(--bg);font-size:17px}
.empty{color:var(--muted);padding:24px 0;text-align:center}
.foot{padding:16px 20px;border-top:1px solid var(--line)}
.total{display:flex;justify-content:space-between;font-size:18px;font-weight:500;margin-bottom:12px}
.field{width:100%;padding:11px 12px;border:1.5px solid var(--line);border-radius:10px;font:inherit;margin-bottom:10px}
.send{width:100%;background:#1FA855;color:#fff;padding:13px;border-radius:12px;font-weight:500;font-size:16px}
.send:disabled{background:var(--line);cursor:not-allowed}
.scrim{position:fixed;inset:0;background:rgba(18,48,58,.45);opacity:0;pointer-events:none;transition:opacity .2s;z-index:9}
.scrim.open{opacity:1;pointer-events:auto}
@media (prefers-reduced-motion:reduce){*{transition:none!important}}
</style>
</head>
<body>

<header>
  <span class="logo" id="storeName"></span>
  <button class="cart-btn" id="openCart" aria-label="فتح السلة">السلة <b id="count">0</b></button>
</header>

<section class="hero">
  <h1>قهوة محمصة طازجة، تصلك خلال يومين</h1>
  <p>اختر درجة التحميص التي تناسبك، وأرسل طلبك مباشرة عبر واتساب.</p>
  <div class="filters" id="filters" role="group" aria-label="درجة التحميص"></div>
</section>

<main id="grid"></main>

<div class="scrim" id="scrim"></div>
<aside class="drawer" id="drawer" aria-label="سلة المشتريات">
  <div class="drawer-head"><h2>سلتك</h2><button class="close" id="closeCart" aria-label="إغلاق">✕</button></div>
  <div class="items" id="items"></div>
  <div class="foot">
    <div class="total"><span>المجموع</span><span id="total">0</span></div>
    <input class="field" id="custName" placeholder="اسمك">
    <input class="field" id="custAddr" placeholder="عنوان التوصيل">
    <button class="send" id="send" disabled>إرسال الطلب عبر واتساب</button>
  </div>
</aside>

<script>
/* ===== إعدادات المتجر: عدّلها فقط ===== */
const STORE_NAME = "بن الحصاد";
const WHATSAPP   = "9647700000000";   // رقمك بصيغة دولية بدون + أو أصفار
const CURRENCY   = "د.ع";

/* ===== المنتجات: أضف أو احذف كما تشاء ===== */
const PRODUCTS = [
  {id:1, name:"إثيوبيا يرغاتشيف", notes:"زهور وحمضيات، قوام خفيف", price:18000, roast:"فاتح",  color:"#E8A317"},
  {id:2, name:"كولومبيا هويلا",   notes:"كراميل وفاكهة حمراء",     price:16000, roast:"متوسط", color:"#C8553D"},
  {id:3, name:"البرازيل سانتوس",  notes:"شوكولاتة ومكسرات",        price:14000, roast:"متوسط", color:"#6B8F71"},
  {id:4, name:"هرر اليمن",        notes:"توابل وتمر، قوام ثقيل",   price:22000, roast:"داكن",  color:"#7A4B3A"},
  {id:5, name:"خلطة الصباح",      notes:"متوازنة، تناسب الفلتر",   price:12000, roast:"متوسط", color:"#2F6F8F"},
  {id:6, name:"إسبريسو الحصاد",   notes:"كريمة كثيفة ونهاية حلوة", price:15000, roast:"داكن",  color:"#3B3B4F"}
];

const fmt = n => n.toLocaleString("en-US") + " " + CURRENCY;
const $ = id => document.getElementById(id);
const cart = {};           // id -> الكمية
let roast = "الكل";

$("storeName").textContent = STORE_NAME;
document.title = STORE_NAME;

const bag = c => `<svg viewBox="0 0 120 150" aria-hidden="true">
  <path d="M30 24h60l8 112H22z" fill="${c}"/>
  <path d="M30 24h60l1 14H29z" fill="rgba(0,0,0,.18)"/>
  <rect x="40" y="62" width="40" height="44" rx="6" fill="#fff" opacity=".92"/>
  <circle cx="60" cy="84" r="9" fill="${c}"/>
</svg>`;

/* الفلاتر */
const roasts = ["الكل","فاتح","متوسط","داكن"];
$("filters").innerHTML = roasts.map(r =>
  `<button data-r="${r}" aria-pressed="${r===roast}">${r}</button>`).join("");
$("filters").onclick = e => {
  const b = e.target.closest("button"); if(!b) return;
  roast = b.dataset.r;
  [...$("filters").children].forEach(x => x.setAttribute("aria-pressed", x.dataset.r===roast));
  renderGrid();
};

/* المنتجات */
function renderGrid(){
  const list = PRODUCTS.filter(p => roast==="الكل" || p.roast===roast);
  $("grid").innerHTML = list.map(p => `
    <article class="card">
      ${bag(p.color)}
      <h3>${p.name}</h3>
      <p class="notes">${p.notes}</p>
      <div class="row"><span class="price">${fmt(p.price)}</span>
      <button class="add" data-id="${p.id}">أضف للسلة</button></div>
    </article>`).join("");
}
$("grid").onclick = e => {
  const b = e.target.closest(".add"); if(!b) return;
  const id = +b.dataset.id;
  cart[id] = (cart[id]||0) + 1;
  renderCart();
};

/* السلة */
function renderCart(){
  const ids = Object.keys(cart).filter(id => cart[id] > 0);
  const total = ids.reduce((s,id) => s + PRODUCTS.find(p=>p.id==id).price * cart[id], 0);
  $("count").textContent = ids.reduce((s,id)=>s+cart[id],0);
  $("total").textContent = fmt(total);
  $("send").disabled = !ids.length;
  $("items").innerHTML = ids.length ? ids.map(id => {
    const p = PRODUCTS.find(p=>p.id==id);
    return `<div class="item"><div>${p.name}<small>${fmt(p.price)}</small></div>
      <div class="qty"><button data-a="-" data-id="${id}" aria-label="إنقاص">−</button>
      <span>${cart[id]}</span>
      <button data-a="+" data-id="${id}" aria-label="زيادة">+</button></div></div>`;
  }).join("") : `<p class="empty">السلة فارغة. أضف منتجاً من القائمة.</p>`;
}
$("items").onclick = e => {
  const b = e.target.closest("button"); if(!b) return;
  cart[b.dataset.id] += b.dataset.a==="+" ? 1 : -1;
  renderCart();
};

const toggle = open => {
  $("drawer").classList.toggle("open", open);
  $("scrim").classList.toggle("open", open);
};
$("openCart").onclick = () => toggle(true);
$("closeCart").onclick = $("scrim").onclick = () => toggle(false);
document.addEventListener("keydown", e => e.key==="Escape" && toggle(false));

/* الإرسال عبر واتساب */
$("send").onclick = () => {
  const ids = Object.keys(cart).filter(id => cart[id] > 0);
  const lines = ids.map(id => {
    const p = PRODUCTS.find(p=>p.id==id);
    return `• ${p.name} × ${cart[id]} = ${fmt(p.price*cart[id])}`;
  });
  const msg = [
    `طلب جديد من ${STORE_NAME}`, "", ...lines, "",
    `المجموع: ${$("total").textContent}`,
    `الاسم: ${$("custName").value || "-"}`,
    `العنوان: ${$("custAddr").value || "-"}`
  ].join("\n");
  window.open(`https://wa.me/${WHATSAPP}?text=${encodeURIComponent(msg)}`, "_blank");
};

renderGrid();
renderCart();
</script>
</body>
</html>
