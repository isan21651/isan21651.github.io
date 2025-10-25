<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🏦 MyBank Admin Demo</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #eaf6ff;
      text-align: center;
      padding: 20px;
    }
    .card {
      background: white;
      max-width: 400px;
      margin: auto;
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
    }
    input, textarea {
      width: 90%;
      padding: 10px;
      margin: 5px 0;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    button {
      margin: 5px;
      padding: 10px 20px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      background-color: #007bff;
      color: white;
      transition: 0.3s;
    }
    button:hover { background-color: #0056b3; }
    #dashboard, #adminPanel { display: none; }
    #history { text-align: left; margin-top: 10px; font-size: 14px; }
    .admin-table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 10px;
    }
    .admin-table th, .admin-table td {
      border: 1px solid #ddd;
      padding: 8px;
    }
    .admin-table th {
      background-color: #007bff;
      color: white;
    }
  </style>
</head>
<body>
  <div class="card" id="auth">
    <h2>🏦 MyBank Demo</h2>
    <h3>Login</h3>
    <input type="email" id="loginEmail" placeholder="Gmail">
    <input type="password" id="loginPass" placeholder="Password">
    <button onclick="login()">Login</button>
    <p>অ্যাকাউন্ট নেই? <a href="#" onclick="showSignup()">Signup</a></p>
    <p>🔑 <a href="#" onclick="showAdminLogin()">Admin Login</a></p>
  </div>

  <div class="card" id="signup" style="display:none;">
    <h3>Create Account</h3>
    <input type="text" id="name" placeholder="Name">
    <input type="email" id="email" placeholder="Gmail">
    <input type="password" id="password" placeholder="Password">
    <input type="text" id="phone" placeholder="Phone">
    <input type="text" id="address" placeholder="Address">
    <button onclick="signup()">Signup</button>
    <p>Already have an account? <a href="#" onclick="showLogin()">Login</a></p>
  </div>

  <div class="card" id="dashboard">
    <h2 id="userName"></h2>
    <p id="userEmail"></p>
    <p id="balance"></p>
    <button onclick="deposit()">Deposit</button>
    <button onclick="withdraw()">Withdraw</button>
    <button onclick="logout()">Logout</button>
    <h3>Transaction History:</h3>
    <div id="history"></div>
  </div>

  <div class="card" id="adminLogin" style="display:none;">
    <h3>Admin Login</h3>
    <input type="password" id="adminPass" placeholder="Enter Admin Password">
    <button onclick="adminLogin()">Enter</button>
    <p><a href="#" onclick="showLogin()">🔙 Back</a></p>
  </div>

  <div class="card" id="adminPanel">
    <h2>👑 Admin Panel</h2>
    <button onclick="logoutAdmin()">Logout</button>
    <table class="admin-table" id="userTable">
      <tr><th>Name</th><th>Email</th><th>Phone</th><th>Balance</th><th>History</th></tr>
    </table>
  </div>

  <script>
    let users = JSON.parse(localStorage.getItem("users")) || {};
    let currentUser = JSON.parse(localStorage.getItem("currentUser")) || null;

    const ADMIN_PASSWORD = "12345"; // 🔐 এখানেই তোমার নিজের Admin Password বসাও

    function showSignup() {
      hideAll();
      document.getElementById("signup").style.display = "block";
    }
    function showLogin() {
      hideAll();
      document.getElementById("auth").style.display = "block";
    }
    function showAdminLogin() {
      hideAll();
      document.getElementById("adminLogin").style.display = "block";
    }
    function hideAll() {
      document.querySelectorAll('.card').forEach(div => div.style.display = "none");
    }

    function signup() {
      const name = nameField.value, email = emailField.value, pass = passField.value, phone = phoneField.value, address = addressField.value;
    }

    function signup() {
      const name = document.getElementById("name").value;
      const email = document.getElementById("email").value;
      const pass = document.getElementById("password").value;
      const phone = document.getElementById("phone").value;
      const address = docum

