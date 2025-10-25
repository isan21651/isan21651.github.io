<!doctype html>
<html lang="bn">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>MyBank — Demo (Admin + Customer)</title>
<style>
  body{font-family:Arial,Helvetica,sans-serif;background:#eef6ff;margin:0;padding:20px;color:#123}
  .wrap{max-width:980px;margin:18px auto;display:grid;grid-template-columns:1fr;gap:14px}
  .card{background:#fff;padding:16px;border-radius:10px;box-shadow:0 6px 18px rgba(10,20,40,.06)}
  h1,h2{margin:6px 0}
  input,select,textarea{width:100%;padding:8px;margin:6px 0;border:1px solid #cfe3ff;border-radius:6px;box-sizing:border-box}
  button{padding:8px 12px;border-radius:8px;border:0;background:#0470d8;color:#fff;cursor:pointer}
  button.secondary{background:#5b5b5b}
  .row{display:flex;gap:10px}
  .col{flex:1}
  table{width:100%;border-collapse:collapse}
  th,td{padding:8px;border:1px solid #e6eef9;text-align:left;font-size:14px}
  th{background:#0470d8;color:#fff}
  .small{font-size:13px;color:#456}
  .actions button{margin-right:6px}
  .hidden{display:none}
  pre{white-space:pre-wrap;background:#f7fbff;padding:8px;border-radius:6px}
  .muted{color:#667}
  @media(min-width:900px){.wrap{grid-template-columns:360px 1fr}}
</style>
</head>
<body>
<div class="wrap">

  <!-- LEFT: Auth / Admin Login / Admin Signup -->
  <div class="card" id="leftPanel">
    <h2>🏦 MyBank Demo</h2>
    <div id="customerAuth">
      <h3>Customer — Login</h3>
      <input id="custLoginEmail" placeholder="Gmail (customer)">
      <input id="custLoginPass" type="password" placeholder="Password">
      <div class="row"><button onclick="customerLogin()">Login</button><button class="secondary" onclick="showSignup()">Signup</button></div>
      <p class="small muted">অ্যাকাউন্ট নেই? Sign up করো।</p>
      <hr>
      <p class="small">Admin access: <a href="#" onclick="showAdminLogin()">Admin Login</a></p>
    </div>

    <div id="customerSignup" class="hidden">
      <h3>Customer — Signup</h3>
      <input id="suName" placeholder="Full name">
      <input id="suEmail" placeholder="Gmail">
      <input id="suPass" type="password" placeholder="Password">
      <input id="suPhone" placeholder="Phone number">
      <input id="suAddress" placeholder="Address (optional)">
      <div class="row"><button onclick="customerSignup()">Create Account</button><button class="secondary" onclick="showLogin()">Back to Login</button></div>
      <p class="small muted">Default starting balance: 5000৳ (you can change later)</p>
    </div>

    <div id="adminLogin" class="hidden">
      <h3>Admin — Login</h3>
      <input id="admLoginEmail" placeholder="Admin Gmail">
      <input id="admLoginPass" type="password" placeholder="Password">
      <div class="row"><button onclick="adminLogin()">Enter Admin</button><button class="secondary" onclick="showLogin()">Back</button></div>
      <p class="small muted">Want admin account? <a href="#" onclick="showAdminSignup()">Admin Signup</a></p>
    </div>

    <div id="adminSignup" class="hidden">
      <h3>Admin — Signup</h3>
      <input id="admSUname" placeholder="Admin name">
      <input id="admSUemail" placeholder="Admin Gmail">
      <input id="admSUphone" placeholder="Admin phone">
      <input id="admSUpass" type="password" placeholder="Password">
      <div class="row"><button onclick="adminSignup()">Create Admin</button><button class="secondary" onclick="showAdminLogin()">Back</button></div>
      <p class="small muted">Multiple admins allowed. Keep password safe.</p>
    </div>

    <div id="who" class="small muted" style="margin-top:10px"></div>
  </div>

  <!-- RIGHT: Dashboards -->
  <div class="card" id="rightPanel">
    <!-- Customer Dashboard -->
    <div id="custDashboard" class="hidden">
      <h2 id="cdName">Welcome</h2>
      <p id="cdEmail" class="small muted"></p>
      <p id="cdAccount" class="small muted"></p>
      <h3 id="cdBalance">Balance: 0৳</h3>
      <div class="row">
        <div class="col"><button onclick="custDeposit()">Deposit</button></div>
        <div class="col"><button onclick="custWithdraw()">Withdraw</button></div>
        <div class="col"><button onclick="custSend()">Send Money</button></div>
      </div>
      <h4>Transaction History</h4>
      <div id="cdHistory"></div>
      <div style="margin-top:10px"><button class="secondary" onclick="custLogout()">Logout</button></div>
    </div>

    <!-- Admin Dashboard -->
    <div id="adminDashboard" class="hidden">
      <h2>👑 Admin Panel</h2>
      <div class="row" style="align-items:center">
        <div class="col"><b id="admNameDisplay"></b> <div class="small muted" id="admEmailDisplay"></div></div>
        <div><button class="secondary" onclick="adminLogout()">Logout</button></div>
      </div>

      <h3 style="margin-top:12px">Customers</h3>
      <table id="adminTable" class="admin-table">
        <thead>
          <tr><th>Serial</th><th>Account#</th><th>Name</th><th>Email</th><th>Phone</th><th>Balance</th><th>Actions</th></tr>
        </thead>
        <tbody id="adminTbody"></tbody>
      </table>
      <p class="small muted">You can Edit balance or Delete account. History shown in textarea.</p>
    </div>

    <!-- Landing -->
    <div id="landing">
      <h1>Welcome to MyBank Demo</h1>
      <p class="muted">Signup as customer or create admin. Data stored locally in your browser.</p>
      <p class="muted small">Account numbers start from <b>20032001</b> and increase (+1) for each new customer.</p>
    </div>
  </div>

</div>

<script>
/* ========= Data structures in localStorage =========
 - users: array of user objects:
   { serial: number, account: string, name, email, pass, phone, address, balance:number, history: [strings] }

 - admins: array of admin objects:
   { name, email, phone, pass }

 - lastAccountNumber: number (e.g., 20032001)
 - currentSession: { role: 'customer'|'admin', id: account | email }
===================================================*/

// helpers
function load(key, def){ try{const v=localStorage.getItem(key); return v?JSON.parse(v):def }catch(e){return def} }
function save(key, obj){ localStorage.setItem(key, JSON.stringify(obj)); }

// init if missing
let users = load('mb_users', []);
let admins = load('mb_admins', []);
let lastAcc = load('mb_lastAcc', null);
if(!lastAcc){ lastAcc = 20032000; save('mb_lastAcc', lastAcc); } // we will inc before assign
save('mb_users', users); save('mb_admins', admins);

let current = load('mb_session', null);

// UI helpers
function hideAllLeft(){ document.getElementById('customerAuth').classList.add('hidden'); document.getElementById('customerSignup').classList.add('hidden'); document.getElementById('adminLogin').classList.add('hidden'); document.getElementById('adminSignup').classList.add('hidden'); }
function hideAllRight(){ document.getElementById('landing').classList.add('hidden'); document.getElementById('custDashboard').classList.add('hidden'); document.getElementById('adminDashboard').classList.add('hidden'); }

// show functions
function showLogin(){ hideAllLeft(); document.getElementById('customerAuth').classList.remove('hidden'); }
function showSignup(){ hideAllLeft(); document.getElementById('customerSignup').classList.remove('hidden'); }
function showAdminLogin(){ hideAllLeft(); document.getElementById('adminLogin').classList.remove('hidden'); }
function showAdminSignup(){ hideAllLeft(); document.getElementById('adminSignup').classList.remove('hidden'); }

// account number generator
function nextAccountNumber(){
  lastAcc = load('mb_lastAcc', lastAcc);
  lastAcc = Number(lastAcc) + 1;
  save('mb_lastAcc', lastAcc);
  return String(lastAcc);
}

// find helpers
function findUserByEmail(email){ return users.find(u => u.email === email); }
function findUserByAccount(acc){ return users.find(u => u.account === acc); }

// Customer signup
function customerSignup(){
  const name = document.getElementById('suName').value.trim();
  const email = document.getElementById('suEmail').value.trim().toLowerCase();
  const pass = document.getElementById('suPass').value;
  const phone = document.getElementById('suPhone').value.trim();
  const address = document.getElementById('suAddress').value.trim();

  if(!name || !email || !pass || !phone) return alert('সব ফিল্ড পূরণ করুন (Address optional)');

  if(findUserByEmail(email)) return alert('এই ইমেইল দিয়ে আগে থেকেই অ্যাকাউন্ট আছে');

  const acc = nextAccountNumber();
  const serial = users.length + 1;
  const user = { serial, account: acc, name, email, pass, phone, address, balance:5000, history:[`Account created with 5000৳ (Acc# ${acc})`] };
  users.push(user);
  save('mb_users', users);
  alert(`Account created! Account Number: ${acc}`);
  // clear fields and show login
  document.getElementById('suName').value=''; document.getElementById('suEmail').value=''; document.getElementById('suPass').value=''; document.getElementById('suPhone').value=''; document.getElementById('suAddress').value='';
  showLogin();
}

// Customer login
function customerLogin(){
  const email = document.getElementById('custLoginEmail').value.trim().toLowerCase();
  const pass = document.getElementById('custLoginPass').value;
  const u = findUserByEmail(email);
  if(u && u.pass === pass){
    current = { role:'customer', id: u.account };
    save('mb_session', current);
    showCustomerDashboard();
  } else alert('ভুল Gmail অথবা Password');
}

// Customer dashboard actions
function showCustomerDashboard(){
  hideAllRight();
  document.getElementById('custDashboard').classList.remove('hidden');
  const u = findUserByAccount(current.id);
  if(!u) return alert('User not found (session)'); 
  document.getElementById('cdName').innerText = `👤 ${u.name}`;
  document.getElementById('cdEmail').innerText = `📧 ${u.email}`;
  document.getElementById('cdAccount').innerText = `Account#: ${u.account} — Serial: ${u.serial}`;
  document.getElementById('cdBalance').innerText = `Balance: ${u.balance}৳`;
  document.getElementById('cdHistory').innerHTML = u.history.slice().reverse().map(t=>`<pre>${t}</pre>`).join('');
  document.getElementById('leftPanel').scrollIntoView({behavior:'smooth'});
}

// Customer deposit/withdraw/send
function custDeposit(){
  const u = findUserByAccount(current.id);
  const amt = parseFloat(prompt('Deposit amount (৳):'));
  if(isNaN(amt) || amt<=0) return alert('সঠিক এমাউন্ট দিন');
  u.balance += amt;
  u.history.push(`+${amt}৳ Deposited on ${new Date().toLocaleString()}`);
  save('mb_users', users); showCustomerDashboard();
}
function custWithdraw(){
  const u = findUserByAccount(current.id);
  const amt = parseFloat(prompt('Withdraw amount (৳):'));
  if(isNaN(amt) || amt<=0) return alert('সঠিক এমাউন্ট দিন');
  if(amt>u.balance) return alert('Insufficient balance');
  u.balance -= amt;
  u.history.push(`-${amt}৳ Withdrawn on ${new Date().toLocaleString()}`);
  save('mb_users', users); showCustomerDashboard();
}
function custSend(){
  const u = findUserByAccount(current.id);
  const toAcc = prompt('Send to Account Number (e.g. 20032001):');
  const amt = parseFloat(prompt('Amount to send (৳):'));
  if(!toAcc || isNaN(amt) || amt<=0) return alert('Invalid input');
  const rc = findUserByAccount(toAcc.trim());
  if(!rc) return alert('Recipient not found');
  if(amt>u.balance) return alert('Insufficient balance');
  u.balance -= amt; rc.balance += amt;
  const now = new Date().toLocaleString();
  u.history.push(`-${amt}৳ Sent to ${rc.account} (${rc.name}) on ${now}`);
  rc.history.push(`+${amt}৳ Received from ${u.account} (${u.name}) on ${now}`);
  save('mb_users', users); showCustomerDashboard();
}

// Customer logout
function custLogout(){ localStorage.removeItem('mb_session'); current=null; location.reload(); }

// Admin signup/login
function adminSignup(){
  const name = document.getElementById('admSUname').value.trim();
  const email = document.getElementById('admSUemail').value.trim().toLowerCase();
  const phone = document.getElementById('admSUphone').value.trim();
  const pass = document.getElementById('admSUpass').value;
  if(!name || !email || !phone || !pass) return alert('সব ফিল্ড পূরণ করুন');
  if(admins.find(a=>a.email === email)) return alert('এই ইমেইল দিয়ে admin আগে থেকেই আছে');
  admins.push({ name, email, phone, pass });
  save('mb_admins', admins);
  alert('Admin created — এখন লগইন করুন');
  document.getElementById('admSUname').value=''; document.getElementById('admSUemail').value=''; document.getElementById('admSUphone').value=''; document.getElementById('admSUpass').value='';
  showAdminLogin();
}
function adminLogin(){
  const email = document.getElementById('admLoginEmail').value.trim().toLowerCase();
  const pass = document.getElementById('admLoginPass').value;
  const a = admins.find(ad => ad.email === email && ad.pass === pass);
  if(a){
    current = { role: 'admin', id: a.email };
    save('mb_session', current);
    showAdminDashboard();
  } else alert('Admin credentials wrong');
}
function adminLogout(){ localStorage.removeItem('mb_session'); current=null; location.reload(); }

// Admin dashboard
function showAdminDashboard(){
  hideAllRight();
  document.getElementById('adminDashboard').classList.remove('hidden');
  const adm = admins.find(ad => ad.email === current.id);
  document.getElementById('admNameDisplay').innerText = adm ? adm.name : current.id;
  document.getElementById('admEmailDisplay').innerText = adm ? adm.email : '';
  // build table body
  const tbody = document.getElementById('adminTbody');
  tbody.innerHTML = '';
  users.forEach((u, idx) => {
    const tr = document.createElement('tr');
    tr.innerHTML = `
      <td>${u.serial}</td>
      <td>${u.account}</td>
      <td>${u.name}</td>
      <td>${u.email}</td>
      <td>${u.phone}</td>
      <td>${u.balance}৳</td>
      <td class="actions">
        <button onclick="adminEditBalance('${u.account}')">Edit</button>
        <button onclick="adminViewHistory('${u.account}')">History</button>
        <button onclick="adminDelete('${u.account')" class="secondary">Delete</button>
      </td>
    `;
    // fix: create delete button separately to handle quotes
    const delBtn = tr.querySelector('button.secondary');
    delBtn.onclick = ()=> adminDelete(u.account);
    tbody.appendChild(tr);
  });
}

// admin: edit balance
function adminEditBalance(account){
  const u = findUserByAccount(account);
  if(!u) return alert('User not found');
  const val = prompt(`Set new balance for ${u.name} (current ${u.balance}৳):`, u.balance);
  const x = parseFloat(val);
  if(isNaN(x)) return alert('Invalid number');
  u.history.push(`Balance changed from ${u.balance}৳ to ${x}৳ by admin on ${new Date().toLocaleString()}`);
  u.balance = x;
  save('mb_users', users);
  showAdminDashboard();
}

// admin: view history in modal-ish (alert)
function adminViewHistory(account){
  const u = findUserByAccount(account);
  if(!u) return alert('User not found');
  const text = u.history.length ? u.history.join('\n\n') : '(No history)';
  // show in prompt-like large window: use window.open with document.write for nicer view
  const w = window.open('', '_blank', 'width=600,height=600');
  w.document.write(`<pre style="white-space:pre-wrap;font-family:Arial;padding:12px">${text}</pre>`);
}

// admin: delete
function adminDelete(account){
  if(!confirm('Confirm delete this account? This cannot be undone.')) return;
  const idx = users.findIndex(u=>u.account===account);
  if(idx>=0){
    users.splice(idx,1);
    // reassign serials
    users.forEach((u,i)=> u.serial = i+1);
    save('mb_users', users);
    showAdminDashboard();
  }
}

// On load: if session exists, route to proper dashboard
function boot(){
  users = load('mb_users', []);
  admins = load('mb_admins', []);
  lastAcc = load('mb_lastAcc', lastAcc);
  current = load('mb_session', null);
  document.getElementById('who').innerText = `Users: ${users.length} • Admins: ${admins.length}`;
  if(!current) { showLogin(); document.getElementById('landing').classList.remove('hidden'); return; }
  if(current.role === 'customer'){ showCustomerDashboard(); } 
  else if(current.role === 'admin'){ showAdminDashboard(); }
}
boot();

</script>
</body>
</html>
