<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🏦 MyBank System</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(135deg, #007bff, #00bfff);
      color: #fff;
      text-align: center;
      padding: 20px;
    }
    .card {
      background: rgba(255, 255, 255, 0.1);
      border-radius: 15px;
      padding: 20px;
      margin: 20px auto;
      width: 350px;
      box-shadow: 0 0 15px rgba(0,0,0,0.2);
      backdrop-filter: blur(8px);
    }
    input, button {
      padding: 10px;
      margin: 5px;
      border-radius: 5px;
      border: none;
      width: 90%;
    }
    input {
      background: rgba(255, 255, 255, 0.8);
    }
    button {
      background: #007bff;
      color: white;
      cursor: pointer;
    }
    button:hover {
      background: #0056b3;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      color: #fff;
      font-size: 14px;
    }
    th, td {
      border-bottom: 1px solid rgba(255,255,255,0.3);
      padding: 8px;
    }
  </style>
</head>
<body>

  <!-- Login Page -->
  <div id="loginPage" class="card">
    <h2>🏦 MyBank System</h2>
    <button onclick="showAdminLogin()">👑 Admin Login</button>
    <button onclick="showCustomerLogin()">👤 Customer Login</button>
  </div>

  <!-- Admin Login -->
  <div id="adminLogin" class="card" style="display:none;">
    <h2>👑 অ্যাডমিন লগইন</h2>
    <input id="adminEmail" type="text" placeholder="অ্যাডমিন ইমেইল">
    <input id="adminPass" type="password" placeholder="পাসওয়ার্ড">
    <button onclick="adminLogin()">Login</button>
    <p><a onclick="backToMain()">⬅️ ফিরে যান</a></p>
  </div>

  <!-- Customer Login -->
  <div id="customerLogin" class="card" style="display:none;">
    <h2>👤 কাস্টমার লগইন</h2>
    <input id="email" type="text" placeholder="ইমেইল দিন">
    <input id="password" type="password" placeholder="পাসওয়ার্ড দিন">
    <button onclick="login()">Login</button>
    <p>অ্যাকাউন্ট নেই? <a onclick="showRegister()">রেজিস্টার করুন</a></p>
    <p><a onclick="backToMain()">⬅️ ফিরে যান</a></p>
  </div>

  <!-- Register -->
  <div id="registerPage" class="card" style="display:none;">
    <h2>📝 কাস্টমার রেজিস্ট্রেশন</h2>
    <input id="regName" placeholder="নাম">
    <input id="regEmail" placeholder="ইমেইল">
    <input id="regPhone" placeholder="ফোন">
    <input id="regNid" placeholder="NID">
    <input id="regPass" type="password" placeholder="পাসওয়ার্ড">
    <button onclick="register()">রেজিস্টার</button>
    <p><a onclick="backToMain()">⬅️ ফিরে যান</a></p>
  </div>

  <!-- Admin Panel -->
  <div id="adminPanel" class="card" style="display:none;">
    <h2>👑 অ্যাডমিন প্যানেল</h2>
    <table id="customerTable">
      <thead>
        <tr>
          <th>SL</th>
          <th>Account No</th>
          <th>Name</th>
          <th>Balance</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody></tbody>
