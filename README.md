 <!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>POS - منظومة المبيعات المتكاملة</title>

<!-- Libraries -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://unpkg.com/html5-qrcode"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

<style>
/* ====== THEME (Light Modern) ====== */
:root{
  --bg:#f6f9fc; --panel:#ffffff; --accent:#0b76ff; --muted:#6b7280;
  --danger:#ef4444; --success:#16a34a; --glass: rgba(255,255,255,0.6);
  --card-shadow: 0 6px 20px rgba(15,25,50,0.06);
  --radius:12px;
}
*{box-sizing:border-box}
html,body{height:100%;margin:0;font-family:Inter, Tahoma, Arial, sans-serif;background:var(--bg);color:#0b1a2b}
.app{display:flex;height:100vh;gap:16px;padding:16px}
.sidebar{width:300px;background:linear-gradient(180deg,var(--panel),#fff);border-radius:var(--radius);padding:16px;box-shadow:var(--card-shadow);flex-shrink:0;overflow:auto}
.brand{display:flex;align-items:center;gap:12px;margin-bottom:14px}
.logo{width:56px;height:56px;border-radius:10px;background:var(--accent);display:flex;align-items:center;justify-content:center;color:white;font-weight:900;font-size:18px}
.menu{display:flex;flex-direction:column;gap:8px;margin-top:10px}
.tab{padding:10px;border-radius:10px;cursor:pointer;color:#123;display:flex;align-items:center;justify-content:space-between;gap:10px;border:1px solid transparent}
.tab:hover{background:#f1f7ff}
.tab.active{background:var(--accent);color:white;box-shadow:0 10px 30px rgba(11,118,255,0.12);border:1px solid rgba(255,255,255,0.06)}
.small{font-size:13px;color:var(--muted)}
.sidebar-stats{margin-top:14px;background:linear-gradient(180deg,#fff,#fbfdff);padding:10px;border-radius:10px}
.main{flex:1;display:flex;flex-direction:column;gap:12px;overflow:auto}
.header{display:flex;justify-content:space-between;align-items:center}
.title{font-weight:800;font-size:20px}
.header-actions{display:flex;gap:8px;align-items:center}
.btn{background:var(--accent);color:white;border:none;padding:9px 14px;border-radius:10px;cursor:pointer}
.btn.ghost{background:transparent;border:1px solid rgba(11,118,255,0.12);color:var(--accent)}
.card{background:var(--panel);padding:14px;border-radius:12px;box-shadow:var(--card-shadow)}
.flex{display:flex;gap:12px;align-items:center}
.input{padding:10px;border-radius:10px;border:1px solid #e6eef8;width:100%;font-size:14px}
.table{width:100%;border-collapse:collapse;margin-top:10px;font-size:14px}
.table th,.table td{padding:10px;border-bottom:1px solid #eef3fb;text-align:center}
.table th{background:#f3f9ff;color:var(--accent)}
.icon-btn{border:none;padding:6px 8px;border-radius:6px;cursor:pointer}
.icon-edit{background:#06b6d4;color:white}
.icon-del{background:var(--danger);color:white}
.notice{background:#fff8e1;padding:10px;border-radius:8px;border-left:4px solid #f59e0b}
.footer{padding:10px;text-align:center;color:var(--muted);font-size:13px}

/* Responsive */
@media(max-width:1000px){.app{flex-direction:column}.sidebar{width:100%;display:flex;flex-direction:row;overflow:auto}.menu{display:flex;flex-direction:row}.tab{flex:1;white-space:nowrap}}

/* Print for thermal: narrow, no margins */
@media print {
  @page { size: auto; margin: 6mm; }
  body * { visibility: hidden; }
  #printArea, #printArea * { visibility: visible; }
  #printArea { position: fixed; left: 0; top: 0; width: 100%; }
}
</style>
</head>
<body>

<div class="app">

  <!-- SIDEBAR -->
  <aside class="sidebar card">
    <div class="brand">
      <div class="logo">POS</div>
      <div>
        <div style="font-weight:800">منظومة المبيعات</div>
        <div class="small">عصري • متعدد اللغات • يعمل بدون خادم</div>
      </div>
    </div>

    <div style="display:flex;gap:8px;margin-bottom:12px">
      <select id="langSel" class="input" style="width:65%">
        <option value="ar" selected>العربية</option>
        <option value="en">English</option>
      </select>
      <button id="loginBtn" class="btn ghost" style="width:35%">Login</button>
    </div>

    <div class="menu" id="menu">
      <div class="tab active" data-target="dashboard"><span>🏠 الرئيسية</span><small class="small">Dashboard</small></div>
      <div class="tab" data-target="products"><span>📦 المنتجات</span><small class="small">Products</small></div>
      <div class="tab" data-target="sales"><span>🧾 نقطة البيع</span><small class="small">POS</small></div>
      <div class="tab" data-target="inventory"><span>📋 الجرد</span><small class="small">Inventory</small></div>
      <div class="tab" data-target="customers"><span>👥 العملاء</span><small class="small">Customers</small></div>
      <div class="tab" data-target="debts"><span>💳 الديون</span><small class="small">Debts</small></div>
      <div class="tab" data-target="expenses"><span>📉 المصروفات</span><small class="small">Expenses</small></div>
      <div class="tab" data-target="reports"><span>📊 التقارير</span><small class="small">Reports</small></div>
      <div class="tab" data-target="users"><span>🔐 المستخدمون</span><small class="small">Users</small></div>
      <div class="tab" data-target="settings"><span>⚙ الإعدادات</span><small class="small">Settings</small></div>
    </div>

    <div class="sidebar-stats">
      <div class="small">الحالة السريعة / Quick stats</div>
      <div style="display:flex;gap:10px;margin-top:8px">
        <div style="flex:1;text-align:center"><div class="small">Products</div><div id="statProducts" style="font-weight:700">0</div></div>
        <div style="flex:1;text-align:center"><div class="small">Customers</div><div id="statCustomers" style="font-weight:700">0</div></div>
        <div style="flex:1;text-align:center"><div class="small">Invoices</div><div id="statInvoices" style="font-weight:700">0</div></div>
      </div>
    </div>

    <div style="margin-top:12px;text-align:center">
      <button class="btn" id="openQrBtn">🔍 مسح QR (كاميرا)</button>
    </div>

    <div style="margin-top:12px;text-align:center">
      <button class="btn ghost" id="exportAll">تصدير الكل (CSV)</button>
    </div>

  </aside>

  <!-- MAIN -->
  <main class="main">

    <div class="header">
      <div>
        <div class="title" id="mainTitle">لوحة التحكم</div>
        <div class="small" id="subTitle">POS System • Local</div>
      </div>
      <div class="header-actions">
        <button class="btn ghost" id="btnPdfAll">تصدير فواتير PDF</button>
        <button class="btn ghost" id="btnClear">مسح البيانات</button>
        <div style="width:8px"></div>
      </div>
    </div>

    <!-- DASHBOARD -->
    <section id="dashboard" class="card">
      <div style="display:flex;gap:12px;flex-wrap:wrap">
        <div style="flex:1;min-width:200px" class="card"><div class="small">إجمالي المنتجات</div><div style="font-size:22px;font-weight:800" id="totalProducts">0</div></div>
        <div style="flex:1;min-width:200px" class="card"><div class="small">إجمالي العملاء</div><div style="font-size:22px;font-weight:800" id="totalCustomers">0</div></div>
        <div style="flex:1;min-width:200px" class="card"><div class="small">إجمالي الفواتير</div><div style="font-size:22px;font-weight:800" id="totalInvoices">0</div></div>
      </div>

      <div style="display:flex;gap:12px;flex-wrap:wrap;margin-top:12px">
        <div style="flex:1;min-width:320px" class="card">
          <div class="small">Sales - آخر 7 أيام</div>
          <canvas id="chartSales" height="120"></canvas>
        </div>
        <div style="width:360px" class="card">
          <div class="small">Latest Invoices</div>
          <div id="recentInvoices"></div>
        </div>
      </div>
    </section>

    <!-- PRODUCTS -->
    <section id="products" class="card" style="display:none">
      <div style="display:flex;gap:10px;flex-wrap:wrap;align-items:center">
        <input id="prodName" class="input" placeholder="اسم المنتج / Product name">
        <input id="prodBarcode" class="input" placeholder="باركود / Barcode (اختياري)">
        <input id="prodPrice" class="input" placeholder="السعر / Price" type="number" min="0" step="0.01">
        <input id="prodQty" class="input" placeholder="الكمية / Quantity" type="number" min="0" step="1">
        <input id="prodImage" class="input" placeholder="رابط صورة (اختياري) / Image URL">
        <button class="btn" id="addProdBtn">إضافة منتج / Add product</button>
      </div>

      <div style="display:flex;gap:10px;align-items:center;margin-top:12px">
        <input id="prodSearch" class="input" placeholder="بحث باسم أو باركود / Search">
        <button class="btn ghost" id="prodSearchBtn">بحث / Search</button>
        <button class="btn ghost" id="prodResetBtn">عرض الكل / Reset</button>
      </div>

      <table class="table" id="tableProducts">
        <thead><tr><th>الصورة</th><th>الاسم (Name)</th><th>الباركود</th><th>السعر</th><th>الكمية</th><th>تعديل</th><th>حذف</th></tr></thead>
        <tbody></tbody>
      </table>
    </section>

    <!-- SALES / POS -->
    <section id="sales" class="card" style="display:none">
      <div style="display:flex;gap:12px;flex-wrap:wrap">
        <div style="flex:1;min-width:300px">
          <div class="small">Barcode scanner / اكتب الكود ثم Enter</div>
          <input id="scanInput" class="input" placeholder="Scan barcode or enter code">
          <div style="margin-top:10px" class="card">
            <div class="small">بحث بالاسم / Search by name</div>
            <div style="display:flex;gap:8px;margin-top:8px">
              <input id="searchName" class="input" placeholder="ابحث بالاسم / Product name">
              <button class="btn ghost" id="searchNameBtn">بحث / Search</button>
            </div>
            <table class="table" id="searchResults"><thead><tr><th>Name</th><th>Price</th><th>Stock</th><th>Add</th></tr></thead><tbody></tbody></table>
          </div>
        </div>

        <div style="width:480px" class="card">
          <div style="display:flex;gap:8px;margin-bottom:10px;align-items:center">
            <select id="saleCustomer" class="input"><option value="">زبون عام / Walk-in</option></select>
            <button class="btn ghost" id="newCustomerQuick">+ عميل / +Customer</button>
          </div>

          <table class="table" id="cartTable"><thead><tr><th>المنتج / Item</th><th>السعر / Price</th><th>كمية / Qty</th><th>الإجمالي</th><th>إجراء</th></tr></thead><tbody></tbody></table>

          <div style="display:flex;justify-content:space-between;align-items:center;margin-top:12px">
            <div>
              <div class="small">الإجمالي / Total</div>
              <div id="cartTotal" style="font-size:20px;font-weight:800">0.00</div>
            </div>
            <div style="display:flex;flex-direction:column;gap:8px">
              <div style="display:flex;gap:8px">
                <button class="btn" id="payCash">نقداً / Cash</button>
                <button class="btn" id="payCard">بطاقة / Card</button>
                <button class="btn ghost" id="payDebt">سجل على الحساب / Charge</button>
              </div>
              <div style="display:flex;gap:8px">
                <button class="btn ghost" id="printInvoiceBtn">معاينة / Print</button>
                <button class="btn ghost" id="exportInvoicePDF">تنزيل PDF</button>
              </div>
            </div>
          </div>

        </div>
      </div>
    </section>

    <!-- INVENTORY -->
    <section id="inventory" class="card" style="display:none">
      <div class="small">جرد المخزن — تعديل الكميات سريعاً / Inventory</div>
      <table class="table" id="inventoryTable"><thead><tr><th>Name</th><th>Barcode</th><th>Quantity</th><th>Edit</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- CUSTOMERS -->
    <section id="customers" class="card" style="display:none">
      <div style="display:flex;gap:8px">
        <input id="custName" class="input" placeholder="اسم العميل / Customer name">
        <input id="custPhone" class="input" placeholder="هاتف (اختياري) / Phone">
        <button class="btn" id="addCustBtn">إضافة عميل / Add customer</button>
      </div>
      <table class="table" id="customersTable"><thead><tr><th>Name</th><th>Phone</th><th>Debt</th><th>Edit</th><th>Delete</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- DEBTS -->
    <section id="debts" class="card" style="display:none">
      <div class="small">قائمة الديون / Debts</div>
      <table class="table" id="debtsTable"><thead><tr><th>Customer</th><th>Remaining</th><th>Last Update</th><th>Settle</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- EXPENSES -->
    <section id="expenses" class="card" style="display:none">
      <div style="display:flex;gap:8px">
        <input id="expDesc" class="input" placeholder="وصف المصروف / Description">
        <input id="expAmount" class="input" placeholder="المبلغ / Amount" type="number">
        <button class="btn" id="addExpBtn">إضافة مصروف / Add</button>
      </div>
      <table class="table" id="expensesTable"><thead><tr><th>Description</th><th>Amount</th><th>Date</th><th>Delete</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- REPORTS -->
    <section id="reports" class="card" style="display:none">
      <div style="display:flex;gap:8px;flex-wrap:wrap">
        <button class="btn" id="rDaily">مبيعات يومية / Daily</button>
        <button class="btn" id="rMonthly">مبيعات شهرية / Monthly</button>
        <button class="btn" id="rTopProducts">الأكثر مبيعاً / Top Products</button>
        <button class="btn" id="rTopCustomers">أكبر العملاء / Top Customers</button>
      </div>
      <pre id="reportOutput" style="white-space:pre-wrap;margin-top:12px"></pre>
    </section>

    <!-- USERS -->
    <section id="users" class="card" style="display:none">
      <div style="display:flex;gap:8px">
        <input id="uName" class="input" placeholder="Username">
        <input id="uPass" class="input" placeholder="Password">
        <select id="uRole" class="input"><option value="cashier">Cashier</option><option value="admin">Admin</option></select>
        <button class="btn" id="addUserBtn">Add User</button>
      </div>
      <table class="table" id="usersTable"><thead><tr><th>Username</th><th>Role</th><th>Delete</th></tr></thead><tbody></tbody></table>
    </section>

    <!-- SETTINGS -->
    <section id="settings" class="card" style="display:none">
      <div style="display:flex;gap:8px;flex-direction:column">
        <input id="shopTitle" class="input" placeholder="اسم المحل / Shop title">
        <input id="taxRate" class="input" placeholder="Tax rate (%)" type="number">
        <div style="display:flex;gap:8px">
          <button class="btn" id="saveSettingsBtn">حفظ الإعدادات / Save</button>
          <button class="btn ghost" id="loadDemoBtn">تحميل ديمو / Load demo</button>
        </div>
      </div>
    </section>

    <div class="footer">POS System — Local • Camera QR & Barcode support</div>
  </main>
</div>

<!-- Modal invoice -->
<div id="modal" class="modal" style="display:none;position:fixed;inset:0;align-items:center;justify-content:center;background:rgba(0,0,0,0.5);z-index:9999">
  <div class="card" style="width:90%;max-width:760px">
    <div style="display:flex;justify-content:space-between;align-items:center"><h3>Invoice Preview</h3>
      <div><button class="btn ghost" onclick="closeModal()">Close</button> <button class="btn" id="modalPrint">Print</button> <button class="btn" id="modalDownloadPdf">Download PDF</button></div>
    </div>
    <div id="printArea" style="margin-top:12px;white-space:normal"></div>
  </div>
</div>

<!-- QR region created dynamically -->

<script>
/* =========================
   POS Full Single-file JS
   - LocalStorage
   - Camera QR (html5-qrcode) + keyboard barcode
   - Users/auth (simple)
   - Thermal-friendly print via window.print()
   ========================= */

/* -------------------------
   Storage & State
   ------------------------- */
const STORE_KEY = 'pos_store_v1';
const defaults = {
  products: [],
  customers: [],
  invoices: [],
  expenses: [],
  users: [{id:1,username:'admin',pass:'admin',role:'admin'}],
  settings: {shopTitle:'متجري', tax:0}
};

let state = loadStore();
function loadStore(){
  try{
    const v = JSON.parse(localStorage.getItem(STORE_KEY) || '{}');
    return {...defaults, ...v, products: v.products || defaults.products, customers: v.customers || defaults.customers, invoices: v.invoices || defaults.invoices, expenses: v.expenses || defaults.expenses, users: v.users || defaults.users, settings: v.settings || defaults.settings };
  }catch(e){ return {...defaults}; }
}
function saveStore(){ localStorage.setItem(STORE_KEY, JSON.stringify(state)); updateAll(); }

/* -------------------------
   Utilities
   ------------------------- */
const $ = id => document.getElementById(id);
const format = n => Number(n||0).toFixed(2);
function uid(){ return Date.now() + Math.floor(Math.random()*999); }
function showToast(msg){ alert(msg); } // simple: replace with fancier later

/* -------------------------
   Navigation & i18n (basic)
   ------------------------- */
const tabs = document.querySelectorAll('.tab');
tabs.forEach(t => t.addEventListener('click', ()=> {
  tabs.forEach(x=>x.classList.remove('active'));
  t.classList.add('active');
  document.querySelectorAll('main section').forEach(s=>s.style.display='none');
  const target = document.getElementById(t.dataset.target);
  if(target) target.style.display = 'block';
  // specific render
  if(t.dataset.target === 'products') renderProducts();
  if(t.dataset.target === 'sales') { renderSearchResults(); renderCustomerSelect(); renderCart(); }
  if(t.dataset.target === 'inventory') renderInventory();
  if(t.dataset.target === 'customers') renderCustomers();
  if(t.dataset.target === 'debts') renderDebts();
  if(t.dataset.target === 'expenses') renderExpenses();
  if(t.dataset.target === 'reports') updateReports();
  if(t.dataset.target === 'users') renderUsers();
}));
function initUI(){ // set dashboard visible
  document.querySelectorAll('main section').forEach(s=>s.style.display='none');
  $('dashboard').style.display='block';
  document.querySelector('.tab[data-target="dashboard"]').classList.add('active');
  $('mainTitle').innerText = state.settings.shopTitle || 'POS';
  updateAll();
}
initUI();

/* -------------------------
   Stats & Dashboard
   ------------------------- */
function updateAll(){
  $('statProducts').innerText = state.products.length;
  $('statCustomers').innerText = state.customers.length;
  $('statInvoices').innerText = state.invoices.length;
  $('totalProducts').innerText = state.products.length;
  $('totalCustomers').innerText = state.customers.length;
  $('totalInvoices').innerText = state.invoices.length;
  renderRecentInvoices();
  renderSalesChart();
}

/* Sales chart */
let salesChart=null;
function renderSalesChart(){
  const ctx = $('chartSales').getContext('2d');
  const days = 7;
  const labels = [];
  const data = [];
  for(let i=days-1;i>=0;i--){
    const d = new Date(); d.setDate(d.getDate()-i);
    const key = d.toLocaleDateString();
    labels.push(key);
    const total = state.invoices.filter(inv => inv.date && inv.date.includes(key)).reduce((s,inv)=>s+Number(inv.total),0);
    data.push(total);
  }
  if(salesChart) salesChart.destroy();
  salesChart = new Chart(ctx, { type:'line', data:{ labels, datasets:[{ label:'Sales', data, fill:true, backgroundColor:'rgba(11,118,255,0.08)', borderColor:'rgba(11,118,255,0.9)' }]}, options:{ responsive:true }});
}

/* Recent invoices */
function renderRecentInvoices(){
  const div = $('recentInvoices'); div.innerHTML = '';
  const list = state.invoices.slice(-6).reverse();
  if(!list.length){ div.innerText = 'لا توجد فواتير بعد'; return; }
  list.forEach(inv=>{
    const el = document.createElement('div'); el.style.padding='8px 6px'; el.style.borderBottom='1px solid #f1f5fb';
    el.innerHTML = `<div style="display:flex;justify-content:space-between"><div style="font-weight:700">${inv.customer||'زبون عام'}</div><div>${inv.date}</div></div><div style="display:flex;justify-content:space-between;margin-top:6px"><div>${inv.items.length} items</div><div style="font-weight:800">${format(inv.total)}</div></div>`;
    div.appendChild(el);
  });
}

/* -------------------------
   PRODUCTS CRUD + Search
   ------------------------- */
function renderProducts(list = state.products){
  const tbody = document.querySelector('#tableProducts tbody'); tbody.innerHTML = '';
  list.forEach((p,i)=>{
    const img = p.image ? `<img src="${p.image}" style="width:48px;height:32px;object-fit:cover;border-radius:6px">` : '📦';
    tbody.innerHTML += `<tr>
      <td>${img}</td>
      <td style="text-align:right">${p.name}</td>
      <td>${p.barcode||''}</td>
      <td>${format(p.price)}</td>
      <td>${p.qty}</td>
      <td><button class="icon-btn icon-edit" onclick="editProduct(${p.id})">✏</button></td>
      <td><button class="icon-btn icon-del" onclick="deleteProduct(${p.id})">🗑</button></td>
    </tr>`;
  });
}
$('addProdBtn').addEventListener('click', ()=>{
  const name = $('prodName').value.trim();
  const barcode = $('prodBarcode').value.trim();
  const price = Number($('prodPrice').value);
  const qty = Number($('prodQty').value);
  const image = $('prodImage').value.trim();
  if(!name || isNaN(price) || isNaN(qty)){ showToast('اكمل بيانات المنتج'); return; }
  state.products.push({ id: uid(), name, barcode, price, qty, image });
  saveStore();
  $('prodName').value=''; $('prodBarcode').value=''; $('prodPrice').value=''; $('prodQty').value=''; $('prodImage').value='';
  renderProducts();
});

function editProduct(id){
  const p = state.products.find(x=>x.id==id); if(!p) return;
  $('prodName').value = p.name; $('prodBarcode').value = p.barcode; $('prodPrice').value = p.price; $('prodQty').value = p.qty; $('prodImage').value = p.image;
  // remove original; Save will add as new (simple flow)
  state.products = state.products.filter(x=>x.id!=id); saveStore(); renderProducts();
}
function deleteProduct(id){ if(!confirm('تأكيد حذف المنتج؟')) return; state.products = state.products.filter(x=>x.id!=id); saveStore(); renderProducts(); }

$('prodSearchBtn').addEventListener('click', ()=> {
  const q = $('prodSearch').value.trim().toLowerCase();
  const res = state.products.filter(p => p.name.toLowerCase().includes(q) || (p.barcode||'').includes(q));
  renderProducts(res);
});
$('prodResetBtn').addEventListener('click', ()=> { $('prodSearch').value=''; renderProducts(); });

/* -------------------------
   INVENTORY
   ------------------------- */
function renderInventory(){
  const tbody = $('inventoryTable').querySelector('tbody'); tbody.innerHTML='';
  state.products.forEach((p,i)=> tbody.innerHTML += `<tr><td style="text-align:right">${p.name}</td><td>${p.barcode||''}</td><td>${p.qty}</td><td><input type="number" value="${p.qty}" onchange="setQty('${p.id}',this.value)" style="width:100px;padding:6px;border-radius:6px"></td></tr>`);
}
function setQty(id,v){ const q=Number(v); if(isNaN(q)) return; const p = state.products.find(x=>x.id==id); if(p){ p.qty = q; saveStore(); renderInventory(); }}

/* -------------------------
   CUSTOMERS
   ------------------------- */
function renderCustomers(){
  const tbody = $('customersTable').querySelector('tbody'); tbody.innerHTML='';
  state.customers.forEach((c,i)=> tbody.innerHTML += `<tr><td style="text-align:right">${c.name}</td><td>${c.phone||''}</td><td>${format(c.debt||0)}</td><td><button class="icon-btn icon-edit" onclick="editCustomer('${c.id}')">✏</button></td><td><button class="icon-btn icon-del" onclick="deleteCustomer('${c.id}')">🗑</button></td></tr>`);
  renderCustomerSelect();
}
function renderCustomerSelect(){
  const sel = $('saleCustomer'); sel.innerHTML = `<option value="">زبون عام / Walk-in</option>`;
  state.customers.forEach(c => sel.innerHTML += `<option value="${c.id}">${c.name}</option>`);
}
$('addCustBtn').addEventListener('click', ()=>{
  const name = $('custName').value.trim(); const phone = $('custPhone').value.trim();
  if(!name){ showToast('ادخل اسم العميل'); return; }
  state.customers.push({ id: uid(), name, phone, debt:0 });
  saveStore(); $('custName').value=''; $('custPhone').value=''; renderCustomers();
});
function editCustomer(id){
  const c = state.customers.find(x=>x.id==id); if(!c) return;
  const name = prompt('الاسم', c.name); if(!name) return; const phone = prompt('الهاتف', c.phone||''); c.name = name; c.phone = phone; saveStore(); renderCustomers();
}
function deleteCustomer(id){ if(!confirm('حذف العميل؟')) return; state.customers = state.customers.filter(x=>x.id!=id); saveStore(); renderCustomers(); }

/* -------------------------
   EXPENSES
   ------------------------- */
function renderExpenses(){
  const tbody = $('expensesTable').querySelector('tbody'); tbody.innerHTML='';
  state.expenses.forEach((e,i)=> tbody.innerHTML += `<tr><td>${e.desc}</td><td>${format(e.amount)}</td><td>${e.date}</td><td><button class="icon-btn icon-del" onclick="deleteExpense('${e.id}')">🗑</button></td></tr>`);
}
$('addExpBtn').addEventListener('click', ()=>{
  const desc = $('expDesc').value.trim(); const amount = Number($('expAmount').value);
  if(!desc || isNaN(amount) || amount<=0){ showToast('اكمل بيانات المصروف'); return; }
  state.expenses.push({ id: uid(), desc, amount, date: (new Date()).toLocaleString() });
  saveStore(); $('expDesc').value=''; $('expAmount').value=''; renderExpenses();
});
function deleteExpense(id){ if(!confirm('حذف المصروف؟')) return; state.expenses = state.expenses.filter(x=>x.id!=id); saveStore(); renderExpenses(); }

/* -------------------------
   CART & SALES
   ------------------------- */
let CART = [];
function renderSearchResults(list = state.products){
  const tbody = $('searchResults').querySelector('tbody'); tbody.innerHTML='';
  (list||[]).forEach(p => tbody.innerHTML += `<tr><td style="text-align:right">${p.name}</td><td>${format(p.price)}</td><td>${p.qty}</td><td><button class="btn ghost" onclick='addToCart("${p.id}")'>Add</button></td></tr>`);
}
function addToCart(productId){
  const prod = state.products.find(p=>p.id==productId);
  if(!prod){ showToast('المنتج غير موجود'); return; }
  const existing = CART.find(c=>c.productId==prod.id);
  if(existing){ existing.qty +=1; } else { CART.push({ productId: prod.id, name: prod.name, price: prod.price, qty:1 }); }
  renderCart();
}
function renderCart(){
  const tbody = $('cartTable').querySelector('tbody'); tbody.innerHTML=''; let total=0;
  CART.forEach((it,idx)=>{ const sum = it.price*it.qty; total += sum; tbody.innerHTML += `<tr><td style="text-align:right">${it.name}</td><td>${format(it.price)}</td><td><input type="number" min="1" value="${it.qty}" onchange="changeQty(${idx},this.value)" style="width:70px;padding:6px;border-radius:6px"></td><td>${format(sum)}</td><td><button class="icon-btn icon-del" onclick="removeCart(${idx})">🗑</button></td></tr>`; });
  $('cartTotal').innerText = format(total);
}
function changeQty(idx,val){ const v = Number(val); if(isNaN(v)||v<1) return; CART[idx].qty = v; renderCart(); }
function removeCart(idx){ CART.splice(idx,1); renderCart(); }
$('searchNameBtn').addEventListener('click', ()=> { const q = $('searchName').value.trim().toLowerCase(); const res = state.products.filter(p=>p.name.toLowerCase().includes(q) || (p.barcode||'').includes(q)); renderSearchResults(res); });

/* scan keyboard barcode (Enter) */
$('scanInput').addEventListener('keydown', (e)=>{ if(e.key==='Enter'){ const code = $('scanInput').value.trim(); if(!code) return; const p = state.products.find(x=>x.barcode==code || x.id==code); if(!p){ showToast('منتج غير موجود'); $('scanInput').value=''; return; } addToCart(p.id); $('scanInput').value=''; }} );

/* payment flow */
function finalizePayment(method){
  if(CART.length===0){ showToast('السلة فارغة'); return; }
  const total = Number($('cartTotal').innerText);
  const custId = $('saleCustomer').value;
  const customer = state.customers.find(c=>c.id==custId);
  const inv = { id: uid(), items: CART.map(it=>({productId:it.productId,name:it.name,price:it.price,qty:it.qty})), total, method, customer: customer ? customer.name : '', date: (new Date()).toLocaleString() };
  state.invoices.push(inv);
  // reduce stock
  CART.forEach(it=>{
    const p = state.products.find(pp=>pp.id==it.productId);
    if(p){ p.qty -= it.qty; if(p.qty<0) p.qty = 0; }
  });
  // if charge to account
  if(method === 'debt'){
    if(!customer){ showToast('اختر عميل لتسجيل الدين'); return; }
    const c = state.customers.find(c=>c.id==custId); c.debt = (c.debt||0) + total;
  }
  saveStore();
  CART = [];
  renderCart(); renderProducts(); renderInventory(); renderDebts();
  showInvoiceModal(inv);
}

$('payCash').addEventListener('click', ()=> finalizePayment('cash'));
$('payCard').addEventListener('click', ()=> { const card = prompt('Enter card number (simulate) / ادخل رقم البطاقة'); if(card) finalizePayment('card'); });
$('payDebt').addEventListener('click', ()=> finalizePayment('debt'));

/* -------------------------
   Invoice preview / print / pdf
   ------------------------- */
function showInvoiceModal(inv){
  $('modal').style.display = 'flex';
  const area = $('printArea');
  let html = `<div id="inv-${inv.id}"><div style="display:flex;justify-content:space-between"><div><h3>${state.settings.shopTitle||'Shop'}</h3><div class="small">${inv.date}</div></div><div style="text-align:left">Invoice #${inv.id}</div></div><hr>`;
  html += `<table style="width:100%;border-collapse:collapse"><thead><tr><th style="text-align:right">المنتج</th><th>السعر</th><th>كمية</th><th>المجموع</th></tr></thead><tbody>`;
  inv.items.forEach(it => html += `<tr><td style="text-align:right">${it.name}</td><td>${format(it.price)}</td><td>${it.qty}</td><td>${format(it.price*it.qty)}</td></tr>`);
  html += `</tbody></table><hr><div style="display:flex;justify-content:space-between"><div>طريقة الدفع: ${inv.method}</div><div style="font-weight:800">الإجمالي: ${format(inv.total)}</div></div>`;
  area.innerHTML = html;
}
function closeModal(){ $('modal').style.display='none'; }
$('modalPrint').addEventListener('click', ()=> window.print());
$('modalDownloadPdf').addEventListener('click', ()=> {
  const { jsPDF } = window.jspdf;
  const doc = new jsPDF();
  doc.html($('printArea'), { callback: function(pdf){ pdf.save('invoice.pdf'); }});
});

/* print invoice from cart (preview) */
$('printInvoiceBtn').addEventListener('click', ()=> {
  if(CART.length===0){ showToast('السلة فارغة'); return; }
  const inv = { id: 'temp-'+uid(), items: CART.map(it=>({ name: it.name, price: it.price, qty: it.qty })), total: Number($('cartTotal').innerText), method:'—', date:(new Date()).toLocaleString() };
  showInvoiceModal(inv);
});
$('exportInvoicePDF').addEventListener('click', ()=> {
  if(CART.length===0){ showToast('السلة فارغة'); return; }
  const { jsPDF } = window.jspdf; const doc = new jsPDF();
  doc.text('Invoice', 10, 10);
  let y = 20; CART.forEach(it => { doc.text(`${it.name} x${it.qty} = ${(it.price*it.qty).toFixed(2)}`, 10, y); y+=8; });
  doc.text('Total: '+$('cartTotal').innerText, 10, y+8); doc.save('invoice.pdf');
});

/* -------------------------
   DEBTS
   ------------------------- */
function renderDebts(){
  const tbody = $('debtsTable').querySelector('tbody'); tbody.innerHTML='';
  state.customers.filter(c=>c.debt && c.debt>0).forEach(c => {
    tbody.innerHTML += `<tr><td style="text-align:right">${c.name}</td><td>${format(c.debt)}</td><td>${new Date().toLocaleString()}</td><td><button class="btn" onclick="openPayDebt('${c.id}')">Pay</button></td></tr>`;
  });
}
function openPayDebt(id){
  const v = Number(prompt('Amount to pay / المبلغ'));
  if(isNaN(v)||v<=0) return;
  const c = state.customers.find(x=>x.id==id); if(!c) return;
  c.debt -= v; if(c.debt < 0) c.debt = 0; saveStore(); renderDebts();
}

/* -------------------------
   REPORTS
   ------------------------- */
function updateReports(){ $('reportOutput').innerText = 'Choose a report'; }
$('rDaily').addEventListener('click', ()=> {
  const today = new Date().toLocaleDateString();
  const list = state.invoices.filter(inv => inv.date.includes(today));
  const sum = list.reduce((s,i)=>s+Number(i.total),0);
  $('reportOutput').innerText = `Daily Sales (${today}): ${format(sum)}\nInvoices: ${list.length}`;
});
$('rMonthly').addEventListener('click', ()=> {
  const m = new Date().getMonth(); let sum=0;
  state.invoices.forEach(inv=>{ const d = new Date(inv.date); if(!isNaN(d) && d.getMonth() == m) sum += Number(inv.total); });
  $('reportOutput').innerText = `Monthly Sales: ${format(sum)}`;
});
$('rTopProducts').addEventListener('click', ()=> {
  const counts = {}; state.invoices.forEach(inv=> inv.items.forEach(it=> counts[it.name] = (counts[it.name]||0) + it.qty));
  const sorted = Object.entries(counts).sort((a,b)=>b[1]-a[1]).slice(0,20);
  $('reportOutput').innerText = 'Top Products:\\n' + sorted.map(s=>`${s[0]} x ${s[1]}`).join('\\n');
});
$('rTopCustomers').addEventListener('click', ()=> {
  const sums = {}; state.invoices.forEach(inv=> { const c = inv.customer || 'Walk-in'; sums[c] = (sums[c]||0) + Number(inv.total); });
  const sorted = Object.entries(sums).sort((a,b)=>b[1]-a[1]).slice(0,20);
  $('reportOutput').innerText = 'Top Customers:\\n' + sorted.map(s=>`${s[0]} : ${format(s[1])}`).join('\\n');
});

/* -------------------------
   USERS (simple auth)
   ------------------------- */
function renderUsers(){
  const tbody = $('usersTable').querySelector('tbody'); tbody.innerHTML='';
  state.users.forEach((u,i) => tbody.innerHTML += `<tr><td>${u.username}</td><td>${u.role}</td><td><button class="icon-btn icon-del" onclick="deleteUser('${u.id}')">🗑</button></td></tr>`);
}
$('addUserBtn').addEventListener('click', ()=> {
  const name = $('uName').value.trim(); const pass = $('uPass').value; const role = $('uRole').value;
  if(!name||!pass){ showToast('Username & password'); return; }
  state.users.push({ id: uid(), username: name, pass, role });
  saveStore(); $('uName').value=''; $('uPass').value=''; renderUsers();
});
function deleteUser(id){ if(!confirm('Delete user?')) return; state.users = state.users.filter(u=>u.id!=id); saveStore(); renderUsers(); }

/* login modal simple */
$('loginBtn').addEventListener('click', ()=> {
  const username = prompt('Username'); if(!username) return; const pass = prompt('Password'); if(!pass) return;
  const u = state.users.find(x => x.username === username && x.pass === pass);
  if(!u){ alert('Invalid credentials'); return; }
  // store current user in sessionStorage
  sessionStorage.setItem('pos_user', JSON.stringify({ id: u.id, username: u.username, role: u.role }));
  alert('Logged in as ' + u.username + ' (' + u.role + ')');
});

/* -------------------------
   Settings & Demo & Export
   ------------------------- */
$('saveSettingsBtn').addEventListener('click', ()=> {
  state.settings.shopTitle = $('shopTitle').value.trim() || state.settings.shopTitle;
  state.settings.tax = Number($('taxRate').value) || 0;
  saveStore(); alert('Settings saved');
});
$('loadDemoBtn').addEventListener('click', ()=> {
  if(!confirm('Load demo data?')) return;
  loadDemo(); saveStore(); initViews();
});
function loadDemo(){
  state.products = [
    {id: uid(), name:'قلم حبر / Pen', barcode:'P001', price:1.20, qty:120, image:''},
    {id: uid(), name:'دفتر A4 / Notebook', barcode:'P002', price:2.50, qty:50, image:''},
    {id: uid(), name:'ممحاة / Eraser', barcode:'P003', price:0.80, qty:200, image:''}
  ];
  state.customers = [{id: uid(), name:'أحمد / Ahmed', phone:'091...', debt:0}];
  state.expenses = [];
  state.invoices = [];
  state.users = [{id: uid(), username:'admin', pass:'admin', role:'admin'}];
  state.settings = { shopTitle:'متجري', tax:0 };
}

/* export CSV of all */
$('exportAll').addEventListener('click', ()=> {
  const toCSV = (arr, headers) => [headers.join(','), ...arr.map(o => headers.map(h => `"${(o[h]||'').toString().replace(/"/g,'""')}"`).join(','))].join('\n');
  downloadText(toCSV(state.products,['id','name','barcode','price','qty']),'products.csv');
  downloadText(toCSV(state.customers,['id','name','phone','debt']),'customers.csv');
  downloadText(toCSV(state.invoices,['id','date','customer','total','method']),'invoices.csv');
});
function downloadText(text, filename){
  const a=document.createElement('a'); a.href='data:text/csv;charset=utf-8,'+encodeURIComponent(text); a.download=filename; a.click(); a.remove();
}

/* PDF all invoices */
$('btnPdfAll').addEventListener('click', ()=> {
  const { jsPDF } = window.jspdf; const doc = new jsPDF();
  let y = 10;
  state.invoices.forEach(inv=>{
    doc.text(`Invoice #${inv.id} - ${inv.date}`, 10, y); y+=6;
    inv.items.forEach(it=>{ doc.text(`${it.name} x${it.qty} = ${(it.price*it.qty).toFixed(2)}`, 10, y); y+=6; });
    doc.text(`Total: ${format(inv.total)}`, 10, y); y+=10;
    if(y>270){ doc.addPage(); y=10; }
  });
  doc.save('invoices_all.pdf');
});

/* clear data */
$('btnClear').addEventListener('click', ()=> { if(!confirm('Clear all data?')) return; localStorage.removeItem(STORE_KEY); state = loadStore(); initViews(); });

/* -------------------------
   Init & render helpers
   ------------------------- */
function initViews(){ renderProducts(); renderCustomers(); renderCustomerSelect(); renderSearchResults(); renderInventory(); renderCart(); renderDebts(); renderExpenses(); renderUsers(); updateAll(); }
function renderCart(){ // ensure exists in DOM
  const tbody = document.querySelector('#cartTable tbody'); if(!tbody) return;
  tbody.innerHTML=''; let total=0; CART.forEach((it,idx)=>{ const sum = it.price*it.qty; total += sum; tbody.innerHTML += `<tr><td style="text-align:right">${it.name}</td><td>${format(it.price)}</td><td><input type="number" min="1" value="${it.qty}" onchange="changeQty(${idx},this.value)" style="width:70px;padding:6px;border-radius:6px"></td><td>${format(sum)}</td><td><button class="icon-btn icon-del" onclick="removeCart(${idx})">🗑</button></td></tr>`; }); if($('cartTotal')) $('cartTotal').innerText = format(total);
}
initViews();

/* -------------------------
   QR / Camera scanner (html5-qrcode)
   - supports camera on phone
   - open/close with button
   ------------------------- */
let qrScanner = null;
let qrOpen = false;
function startCameraQR(){
  if(qrOpen) return;
  const regionId = 'qr-reader-region';
  let region = document.getElementById(regionId);
  if(!region){
    region = document.createElement('div'); region.id = regionId;
    region.style.maxWidth='360px'; region.style.marginTop='12px';
    // place in products section
    document.getElementById('products').appendChild(region);
  }
  qrScanner = new Html5Qrcode(regionId);
  qrScanner.start({ facingMode: 'environment' }, { fps:10, qrbox:250 },
    decodedText => {
      // find product by barcode or id
      const prod = state.products.find(p => p.barcode==decodedText || String(p.id)==decodedText);
      if(prod){
        addToCart(prod.id);
        showToast('Added: '+prod.name);
      } else {
        showToast('Code not linked to product');
      }
    },
    error => {
      // optional debug
    }).catch(err => { console.warn('QR start fail', err); });
  qrOpen = true;
  $('openQrBtn').innerText = '⏹ إيقاف المسح / Stop QR';
}
function stopCameraQR(){
  if(qrScanner){
    qrScanner.stop().then(()=>{ qrScanner.clear(); qrScanner=null; const el=document.getElementById('qr-reader-region'); if(el) el.remove(); }).catch(()=>{});
  }
  qrOpen = false;
  $('openQrBtn').innerText = '🔍 مسح QR (كاميرا)';
}
$('openQrBtn').addEventListener('click', ()=> { if(!qrOpen) startCameraQR(); else stopCameraQR(); });

/* -------------------------
   Convenience: addToCart by barcode quickly (used by QR/keyboard)
   ------------------------- */
function addToCartByBarcode(code){
  const p = state.products.find(x => x.barcode == code || String(x.id) == code);
  if(!p) { showToast('المنتج غير موجود'); return; }
  addToCart(p.id);
}

/* -------------------------
   Demo, onload tasks
   ------------------------- */
if(!state.products.length && !state.customers.length && !state.invoices.length){
  // keep empty until user loads demo or adds items
}
window.addEventListener('beforeunload', ()=> saveStore());

/* ensure elements exist before finishing */
document.addEventListener('DOMContentLoaded', ()=> {
  /* wire simple UI elements already defined above are set */
});

/* END */
</script>

</body>
</html>
