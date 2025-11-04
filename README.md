<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <title>Менеджер об’єктів</title>
  <style>
    body {
      background: #1e1e1e;
      color: #f1f1f1;
      font-family: "Segoe UI", sans-serif;
      margin: 0;
      padding: 20px;
    }
    h2, h3 { color: #00bcd4; }
    .hidden { display: none; }

    /* Авторизація */
    .login-box {
      background: #2a2a2a;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px #00000055;
      width: 300px;
      margin: 80px auto;
      text-align: center;
    }
    .login-box input {
      width: 100%;
      padding: 8px;
      margin: 8px 0;
      border: none;
      border-radius: 6px;
      background: #3a3a3a;
      color: #fff;
    }
    .login-box button {
      width: 100%;
      padding: 10px;
      background: #00bcd4;
      border: none;
      color: white;
      border-radius: 6px;
      font-weight: bold;
      cursor: pointer;
    }
    .login-box button:hover { background: #0097a7; }

    /* Верхня панель */
    .top-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
    }
    .logout-btn {
      background: #ff7043;
      padding: 8px 16px;
      border-radius: 6px;
      border: none;
      color: #fff;
      cursor: pointer;
    }
    .logout-btn:hover { background: #f4511e; }

    .container {
      display: flex;
      gap: 20px;
      align-items: flex-start;
    }
    .main-container { flex: 2; }
    .calendar-container {
      flex: 1;
      background: #2a2a2a;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px #00000055;
      text-align: center;
    }

    form, table {
      background: #2a2a2a;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 0 10px #00000055;
      margin-bottom: 20px;
    }

    label { display: block; margin-top: 10px; font-size: 14px; }
    input, select {
      width: 100%;
      padding: 8px;
      margin-top: 4px;
      border-radius: 6px;
      border: none;
      background: #3a3a3a;
      color: #fff;
    }

    button {
      margin-top: 15px;
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      background: #00bcd4;
      color: #fff;
      font-weight: bold;
      cursor: pointer;
    }
    button:hover { background: #0097a7; }

    /* Таблиця */
    table {
      width: 100%;
      border-collapse: collapse;
    }
    th, td {
      border-bottom: 1px solid #444;
      padding: 8px;
      text-align: left;
    }
    th {
      background: #333;
      color: #00bcd4;
    }
    .delete-btn {
      background: #e53935;
      border: none;
      padding: 6px 10px;
      color: white;
      border-radius: 5px;
      cursor: pointer;
    }
    .delete-btn:hover { background: #c62828; }

    .filter, .search-box {
      margin-bottom: 10px;
    }

    /* Календар */
    .calendar {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      gap: 5px;
      text-align: center;
      margin-top: 10px;
    }
    .calendar .day {
      padding: 8px;
      border-radius: 6px;
      background: #3a3a3a;
    }
    .calendar .day.today {
      background: #00bcd4;
      color: #000;
      font-weight: bold;
    }
    .calendar .header {
      grid-column: span 7;
      text-align: center;
      margin-bottom: 8px;
    }

    #usersPage {
      background: #2a2a2a;
      padding: 20px;
      border-radius: 12px;
    }
  </style>
</head>
<body>

  <!-- Авторизація -->
  <div id="loginPage" class="login-box">
    <h2>Менеджер об’єктів</h2>
    <input type="text" id="loginUser" placeholder="Логін">
    <input type="password" id="loginPass" placeholder="Пароль">
    <button onclick="login()">Увійти</button>
    <p id="loginError" style="color:#ff5252;"></p>
  </div>

  <!-- Головна сторінка -->
  <div id="appPage" class="hidden">
    <div class="top-bar">
      <h2>Менеджер об’єктів</h2>
      <div>
        <button id="usersBtn" class="hidden" onclick="showUsers()">👥 Користувачі</button>
        <button class="logout-btn" onclick="logout()">Вийти</button>
      </div>
    </div>

    <div class="container">
      <div class="main-container">
        <form id="objectForm">
          <label>Назва об’єкта:</label>
          <input type="text" id="name" required>
          <label>Адреса:</label>
          <input type="text" id="address" required>
          <label>Контакт замовника:</label>
          <input type="text" id="contact">
          <label>Тип робіт:</label>
          <input type="text" id="workType">
          <label>Бригада:</label>
          <input type="text" id="team">
          <label>Статус:</label>
          <select id="status">
            <option>Заплановано</option>
            <option>В процесі</option>
            <option>Завершено</option>
          </select>
          <label>Початок:</label>
          <input type="datetime-local" id="start">
          <label>Завершення:</label>
          <input type="datetime-local" id="end">
          <button type="submit">Додати</button>
        </form>

        <div class="search-box">
          <label>Пошук:</label>
          <input type="text" id="searchInput" placeholder="Введіть назву, адресу або контакт..." oninput="filterTable()">
        </div>

        <div class="filter">
          <label>Фільтр за статусом:</label>
          <select id="filterStatus" onchange="filterTable()">
            <option value="">Всі</option>
            <option>Заплановано</option>
            <option>В процесі</option>
            <option>Завершено</option>
          </select>
        </div>

        <table id="objectsTable">
          <thead>
            <tr>
              <th>Назва</th>
              <th>Адреса</th>
              <th>Контакт</th>
              <th>Тип робіт</th>
              <th>Бригада</th>
              <th>Статус</th>
              <th>Початок</th>
              <th>Завершення</th>
              <th>Автор</th>
              <th>Дії</th>
            </tr>
          </thead>
          <tbody></tbody>
        </table>
      </div>

      <div class="calendar-container">
        <h3>Календар</h3>
        <div id="calendar" class="calendar"></div>
        <div id="clock" style="font-size:20px; margin-top:15px;"></div>
      </div>
    </div>
  </div>

  <!-- Сторінка користувачів -->
  <div id="usersPage" class="hidden">
    <h3>Користувачі</h3>
    <button onclick="backToApp()">⬅ Назад</button>
    <table id="usersTable">
      <thead>
        <tr><th>Логін</th><th>Роль</th><th>Дії</th></tr>
      </thead>
      <tbody></tbody>
    </table>
    <h4>Додати користувача</h4>
    <input id="newUser" placeholder="Логін">
    <input id="newPass" placeholder="Пароль">
    <select id="newRole">
      <option value="user">Користувач</option>
      <option value="admin">Адміністратор</option>
    </select>
    <button onclick="addUser()">Додати</button>
  </div>

  <script>
    // --- Ініціалізація адміністратора ---
    function initAdminUser() {
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      if (!users.find(u => u.username === "Olya")) {
        users.push({ username: "Olya", password: "Admin5231225", role: "admin" });
        localStorage.setItem("users", JSON.stringify(users));
      }
    }
    initAdminUser();

    let currentUser = null;

    // --- Авторизація ---
    function login() {
      const user = document.getElementById("loginUser").value.trim();
      const pass = document.getElementById("loginPass").value.trim();
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      const found = users.find(u => u.username === user && u.password === pass);
      if (!found) {
        document.getElementById("loginError").innerText = "Невірний логін або пароль.";
        return;
      }
      currentUser = found;
      localStorage.setItem("currentUser", JSON.stringify(currentUser));
      document.getElementById("loginPage").classList.add("hidden");
      document.getElementById("appPage").classList.remove("hidden");
      if (currentUser.role === "admin") document.getElementById("usersBtn").classList.remove("hidden");
      loadData();
      startClock();
      renderCalendar();
    }

    function logout() {
      localStorage.removeItem("currentUser");
      location.reload();
    }

    function backToApp() {
      document.getElementById("usersPage").classList.add("hidden");
      document.getElementById("appPage").classList.remove("hidden");
    }

    function showUsers() {
      document.getElementById("appPage").classList.add("hidden");
      document.getElementById("usersPage").classList.remove("hidden");
      renderUsers();
    }

    // --- Користувачі ---
    function renderUsers() {
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      const tbody = document.querySelector("#usersTable tbody");
      tbody.innerHTML = "";
      users.forEach((u, i) => {
        const tr = document.createElement("tr");
        tr.innerHTML = `
          <td>${u.username}</td>
          <td>${u.role}</td>
          <td><button onclick="deleteUser(${i})">Видалити</button></td>
        `;
        tbody.appendChild(tr);
      });
    }

    function deleteUser(i) {
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      users.splice(i, 1);
      localStorage.setItem("users", JSON.stringify(users));
      renderUsers();
    }

    function addUser() {
      const username = document.getElementById("newUser").value.trim();
      const password = document.getElementById("newPass").value.trim();
      const role = document.getElementById("newRole").value;
      if (!username || !password) return alert("Введіть логін і пароль");
      const users = JSON.parse(localStorage.getItem("users") || "[]");
      if (users.find(u => u.username === username)) return alert("Такий користувач вже існує!");
      users.push({ username, password, role });
      localStorage.setItem("users", JSON.stringify(users));
      renderUsers();
    }

    // --- Дані об’єктів ---
    const form = document.getElementById("objectForm");
    const tableBody = document.querySelector("#objectsTable tbody");

    function loadData() {
      const data = JSON.parse(localStorage.getItem("objectsData") || "[]");
      tableBody.innerHTML = "";
      data.forEach(addRow);
    }

    function saveData() {
      const rows = [];
      document.querySelectorAll("#objectsTable tbody tr").forEach(tr => {
        const cells = [...tr.querySelectorAll("td")].slice(0,9).map(td => td.textContent || td.querySelector("select")?.value);
        rows.push(cells);
      });
      localStorage.setItem("objectsData", JSON.stringify(rows));
    }

    function addRow(cells) {
      const row = document.createElement("tr");
      cells.forEach((text, idx) => {
        const td = document.createElement("td");
        if (idx === 5) {
          const select = document.createElement("select");
          ["Заплановано","В процесі","Завершено"].forEach(opt => {
            const o = document.createElement("option");
            o.text = opt;
            if (opt === text) o.selected = true;
            select.add(o);
          });
          select.onchange = () => { saveData(); filterTable(); };
          td.appendChild(select);
        } else {
          td.textContent = text;
          td.contentEditable = idx !== 8;
          td.addEventListener("input", saveData);
        }
        row.appendChild(td);
      });
      const actions = document.createElement("td");
      const delBtn = document.createElement("button");
      delBtn.textContent = "Видалити";
      delBtn.className = "delete-btn";
      delBtn.onclick = () => { row.remove(); saveData(); };
      actions.appendChild(delBtn);
      row.appendChild(actions);
      tableBody.appendChild(row);
    }

    form.addEventListener("submit", e => {
      e.preventDefault();
      const cells = [
        name.value, address.value, contact.value, workType.value, team.value,
        status.value, start.value.replace("T"," "), end.value.replace("T"," "),
        currentUser.username
      ];
      addRow(cells);
      saveData();
      form.reset();
    });

    // --- Фільтр і пошук ---
    function filterTable() {
      const statusFilter = document.getElementById("filterStatus").value.toLowerCase();
      const searchValue = document.getElementById("searchInput").value.toLowerCase();

      document.querySelectorAll("#objectsTable tbody tr").forEach(tr => {
        const cells = [...tr.children].map(td => td.textContent.toLowerCase());
        const matchesSearch = cells.some(c => c.includes(searchValue));
        const statusCell = tr.children[5];
        const statusValue = statusCell.querySelector("select")?.value.toLowerCase() || statusCell.textContent.toLowerCase();
        const matchesStatus = !statusFilter || statusValue === statusFilter;
        tr.style.display = matchesSearch && matchesStatus ? "" : "none";
      });
    }

    // --- Календар ---
    function renderCalendar() {
      const calendar = document.getElementById("calendar");
      const today = new Date();
      const year = today.getFullYear();
      const month = today.getMonth();
      const firstDay = new Date(year, month, 1);
      const lastDay = new Date(year, month + 1, 0);
      const monthName = today.toLocaleString("uk-UA", { month: "long" });

      let html = `<div class="header"><h4>${monthName} ${year}</h4></div>`;
      const weekDays = ["Пн","Вт","Ср","Чт","Пт","Сб","Нд"];
      weekDays.forEach(d => html += `<div style='font-weight:bold;'>${d}</div>`);

      for (let i = 1; i < (firstDay.getDay() || 7); i++) html += `<div></div>`;
      for (let day = 1; day <= lastDay.getDate(); day++) {
        const isToday = day === today.getDate();
        html += `<div class="day ${isToday ? 'today' : ''}">${day}</div>`;
      }
      calendar.innerHTML = html;
    }

    // --- Годинник ---
    function startClock() {
      const clock = document.getElementById("clock");
      setInterval(() => {
        const d = new Date();
        clock.textContent = d.toLocaleTimeString("uk-UA");
      }, 1000);
    }

    // --- Автологін ---
    const savedUser = localStorage.getItem("currentUser");
    if (savedUser) {
      currentUser = JSON.parse(savedUser);
      document.getElementById("loginPage").classList.add("hidden");
      document.getElementById("appPage").classList.remove("hidden");
      if (currentUser.role === "admin") document.getElementById("usersBtn").classList.remove("hidden");
      loadData();
      startClock();
      renderCalendar();
    }
  </script>

</body>
</html>
