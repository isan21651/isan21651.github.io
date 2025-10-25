<!DOCTYPE html>
<html lang="bn">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🏦 MyBank System</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f3faff;
      text-align: center;
      padding: 20px;
    }
    .card {
      background: white;
      max-width: 500px;
      margin: auto;
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
    }
    input, button {
      width: 80%;
      padding: 10px;
      margin: 8px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 16px;
    }
    button {
      background-color: #007bff;
      color: white;
      border: none;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 8px;
    }
    th {
      background-color: #007bff;
      color: white;
    }
  </style>
</head>
<body>

  <div id="loginPage" class="card">
    <h2>🔐 Login</h2>
    <input id="email" type="text" placeholder="Enter Email"><br>
    <input id="password" type="password" placeholder="Enter Password"><br>
    <button onclick="login()">Login</button>
    <p>Or <a href="#" onclick="showRegister()">Create Account</a></p>
  </div>

  <div id="registerPage" class="card" style="display:none;">
    <h2>📝 Create Account</h2>
    <input id="name" type="text" placeholder="Full Name"><br>
    <input id="phone" type="text" placeholder="Phone"><br>
    <input id="emailReg" type="text" placeholder="Email"><br>
    <input id="passwordReg" type="password" placeholder="Password"><br>
    <button onclick="register()">Register</button>
    <p><a href="#" onclick="showLogin()">Back to Login</a></p>
  </div>

  <div id="adminPanel" class="card" style="display:none;">
    <h2>👑 Admin Panel</h2>
    <table>
      <thead>
        <tr>
          <th>Serial</th>
          <th>Acc No</th>
          <th>Name</th>
          <th>Balance</th>
        </tr>
      </thead>
      <tbody id="userTable"></tbody>
    </table>
    <button onclick="logout()">Logout</button>
  </div>

  <div id="userPanel" class="card" style="display:none;">
    <h2>👤 Welcome <span id="userName"></span></h2>
    <p id="userAccount"></p>
    <p id="userBalance"></p>
    <button onclick="deposit()">Deposit</button>
    <button onclick="withdraw()">Withdraw</button>
    <button onclick="logout()">Logout</button>
  </div>

  <script>
    let users = JSON.parse(localStorage.getItem("users")) || [];
    let currentUser = null;
    let baseAccount = 20032001;
    let admins = [
      { email: "admin@bank.com", password: "1234" },
      { email: "manager@bank.com", password: "admin" }
    ];

    function showRegister() {
      document.getElementById("loginPage").style.display = "none";
      document.getElementById("registerPage").style.display = "block";
    }

    function showLogin() {
      document.getElementById("registerPage").style.display = "none";
      document.getElementById("loginPage").style.display = "block";
    }

    function register() {
      let name = document.getElementById("name").value;
      let phone = document.getElementById("phone").value;
      let email = document.getElementById("emailReg").value;
      let password = document.getElementById("passwordReg").value;

      if (!name || !phone || !email || !password) {
        alert("সব ঘর পূরণ করুন!");
        return;
      }

      let accountNo = baseAccount + users.length;
      users.push({ name, phone, email, password, balance: 0, accountNo });
      localStorage.setItem("users", JSON.stringify(users));
      alert("Account Created Successfully!");
      showLogin();
    }

    function login() {
      let email = document.getElementById("email").value;
      let password = document.getElementById("password").value;

      // admin check
      let admin = admins.find(a => a.email === email && a.password === password);
      if (admin) {
        showAdminPanel();
        return;
      }

      // user check
      let user = users.find(u => u.email === email && u.password === password);
      if (user) {
        currentUser = user;
        showUserPanel();
      } else {
        alert("Invalid login!");
      }
    }

    function showAdminPanel() {
      document.getElementById("loginPage").style.display = "none";
      document.getElementById("adminPanel").style.display = "block";
      let table = document.getElementById("userTable");
      table.innerHTML = "";
      users.forEach((u, i) => {
        table.innerHTML += `<tr>
          <td>${i + 1}</td>
          <td>${u.accountNo}</td>
          <td>${u.name}</td>
          <td>${u.balance}৳</td>
        </tr>`;
      });
    }

    function showUserPanel() {
      document.getElementById("loginPage").style.display = "none";
      document.getElementById("userPanel").style.display = "block";
      document.getElementById("userName").innerText = currentUser.name;
      document.getElementById("userAccount").innerText = "Account No: " + currentUser.accountNo;
      document.getElementById("userBalance").innerText = "Balance: " + currentUser.balance + "৳";
    }

    function deposit() {
      let amount = parseInt(prompt("Enter deposit amount:"));
      if (amount > 0) {
        currentUser.balance += amount;
        saveUsers();
        showUserPanel();
      }
    }

    function withdraw() {
      let amount = parseInt(prompt("Enter withdraw amount:"));
      if (amount > 0 && amount <= currentUser.balance) {
        currentUser.balance -= amount;
        saveUsers();
        showUserPanel();
      } else {
        alert("Invalid or insufficient balance!");
      }
    }

    function saveUsers() {
      localStorage.setItem("users", JSON.stringify(users));
    }

    function logout() {
      currentUser = null;
      document.getElementById("adminPanel").style.display = "none";
      document.getElementById("userPanel").style.display = "none";
      document.getElementById("loginPage").style.display = "block";
    }
  </script>
</body>
</html>
