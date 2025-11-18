# Daily-schedule-
A schedule to help you write down your daily things you wanna do
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Weekly Schedule</title>
<style>
  body {
    font-family: Arial, sans-serif;
    padding: 10px;
    background: #001100; /* dark mode default */
    color: #fff;
    transition: all 0.3s;
  }

  h1 {
    text-align: center;
    color: #fff; /* dark mode header white */
    margin-bottom: 5px;
    transition: color 0.3s;
  }

  /* Light/Dark toggle pill */
  .mode-toggle {
    position: absolute;
    top: 10px;
    right: 10px;
    display: flex;
    background: #333;
    border-radius: 50px;
    overflow: hidden;
    cursor: pointer;
    border: 1px solid #0f0;
  }
  .mode-toggle div {
    padding: 5px 15px;
    color: #fff;
    font-weight: bold;
    transition: all 0.3s;
  }
  .mode-toggle div.active {
    background: #0f0;
    color: #000;
  }

  /* Weekly Navbar */
  .navbar {
    display: flex;
    overflow-x: auto;
    margin-bottom: 15px;
  }
  .navbar button {
    flex: 1;
    min-width: 80px;
    padding: 10px;
    margin: 0 3px;
    border: none;
    border-radius: 8px;
    background: #003300; /* dark green navbar button */
    color: #fff; /* default for dark mode */
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s;
  }
  .navbar button:hover { background: #005500; }
  .navbar button.active { box-shadow: 0 0 5px #0f0; }
  .navbar button.friday, .navbar button.saturday { color: red; }

  /* Streak */
  .streak { text-align: center; font-size: 16px; margin-bottom: 10px; }

  /* Inputs */
  input {
    padding: 10px;
    margin-bottom: 10px;
    border-radius: 5px;
    border: none;
    font-weight: bold;
    background: #003300;
    color: #0f0;
    transition: all 0.3s;
  }
  #taskInput { width: 50%; }
  #startTime, #endTime { width: 18%; }
  button.addBtn {
    padding: 10px;
    border-radius: 8px;
    border: none;
    background: linear-gradient(45deg, #00ff00, #009900);
    color: #000;
    font-weight: bold;
    margin-left: 5px;
    box-shadow: 0 0 5px #0f0;
    transition: all 0.3s;
  }

  /* Daily To Do List nav bar */
  .daily-nav {
    margin-top: 10px;
    padding: 10px;
    border-radius: 12px;
    text-align: center;
    font-weight: bold;
    font-size: 18px;
    background: linear-gradient(90deg, #00ff00, #009900);
    color: #000;
    box-shadow: 0 0 5px #0f0;
  }

  /* Task List */
  ul { list-style: none; padding: 0; margin-top: 10px; }
  li {
    margin: 10px 0;
    padding: 15px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-radius: 12px;
    font-weight: bold;
    cursor: pointer;
    transition: all 0.3s;
    color: #fff;
  }
  li.completed { text-decoration: line-through; color: #555; }
  .task-info { flex: 1; margin-right: 10px; }

  /* Gradient backgrounds for time blocks */
  .morning { background: linear-gradient(90deg, #003300, #006600); }
  .afternoon { background: linear-gradient(90deg, #006600, #009900); }
  .evening { background: linear-gradient(90deg, #009900, #00ff00); }

  /* Progress bar */
  .progress-container {
    background: #003300;
    border-radius: 10px;
    height: 20px;
    margin-top: 10px;
    overflow: hidden;
  }
  .progress-bar {
    height: 100%;
    width: 0%;
    background: linear-gradient(90deg, #00ff00, #009900);
    border-radius: 10px;
    transition: width 0.3s;
  }

  /* Light mode overrides */
  body.light-mode {
    background: #fff; /* normal white */
    color: #000;
  }
  body.light-mode h1 { color: #000; }
  body.light-mode input, body.light-mode .navbar button { background: #eee; color: #000; }
  body.light-mode li.completed { color: #777; }
  body.light-mode .navbar button.friday, body.light-mode .navbar button.saturday { color: red; }
  body.light-mode .navbar button:not(.friday):not(.saturday) { color: black; }

</style>
</head>
<body>

<h1>Weekly Schedule</h1>

<!-- Mode toggle pill -->
<div class="mode-toggle">
  <div id="lightBtn">Light</div>
  <div id="darkBtn" class="active">Dark</div>
</div>

<div class="streak">🔥 Current Streak: <span id="streakCount">0</span> days</div>

<!-- Weekly Navbar -->
<div class="navbar">
  <button onclick="changeDay('Sunday')">Sun</button>
  <button onclick="changeDay('Monday')">Mon</button>
  <button onclick="changeDay('Tuesday')">Tue</button>
  <button onclick="changeDay('Wednesday')">Wed</button>
  <button onclick="changeDay('Thursday')">Thu</button>
  <button onclick="changeDay('Friday')" class="friday">Fri</button>
  <button onclick="changeDay('Saturday')" class="saturday">Sat</button>
</div>

<!-- Task Inputs -->
<input id="taskInput" type="text" placeholder="Task name">
<input id="startTime" type="time">
<input id="endTime" type="time">
<button class="addBtn" onclick="addTask()">Add</button>

<!-- Daily To Do List nav -->
<div class="daily-nav">Daily To Do List</div>

<ul id="taskList"></ul>

<div class="progress-container">
  <div class="progress-bar" id="progressBar"></div>
</div>

<script>
  // --- Light/Dark Mode ---
  const body = document.body;
  const lightBtn = document.getElementById('lightBtn');
  const darkBtn = document.getElementById('darkBtn');

  lightBtn.addEventListener('click', () => {
    body.classList.add('light-mode');
    lightBtn.classList.add('active');
    darkBtn.classList.remove('active');
  });

  darkBtn.addEventListener('click', () => {
    body.classList.remove('light-mode');
    darkBtn.classList.add('active');
    lightBtn.classList.remove('active');
  });

  // --- Weekly schedule logic ---
  let currentDay = new Date().toLocaleDateString('en-US', { weekday: 'long' });
  let streak = parseInt(localStorage.getItem('streak') || '0');
  document.getElementById('streakCount').innerText = streak;

  let weekTasks = JSON.parse(localStorage.getItem('weekTasks') || '{}');
  if (!weekTasks[currentDay]) weekTasks[currentDay] = [];
  displayTasks();

  function changeDay(day) {
    currentDay = day;
    if (!weekTasks[currentDay]) weekTasks[currentDay] = [];
    displayTasks();
  }

  function addTask() {
    let name = document.getElementById('taskInput').value.trim();
    let start = document.getElementById('startTime').value;
    let end = document.getElementById('endTime').value;
    if (!name || !start || !end) return;

    weekTasks[currentDay].push({ name, start, end, done: false });
    saveAndDisplay();
    document.getElementById('taskInput').value = '';
    document.getElementById('startTime').value = '';
    document.getElementById('endTime').value = '';
  }

  function toggleTask(index) {
    weekTasks[currentDay][index].done = !weekTasks[currentDay][index].done;
    saveAndDisplay();
    if (weekTasks[currentDay].length > 0 && weekTasks[currentDay].every(t => t.done)) {
      streak++;
      localStorage.setItem('streak', streak);
      document.getElementById('streakCount').innerText = streak;
    }
  }

  function deleteTask(index) {
    weekTasks[currentDay].splice(index, 1);
    saveAndDisplay();
  }

  function saveAndDisplay() {
    localStorage.setItem('weekTasks', JSON.stringify(weekTasks));
    displayTasks();
    updateProgressBar();
  }

  function displayTasks() {
    let list = document.getElementById('taskList');
    list.innerHTML = '';
    weekTasks[currentDay].sort((a,b)=>a.start.localeCompare(b.start));

    weekTasks[currentDay].forEach((task,i)=>{
      let li = document.createElement('li');
      li.className = task.done ? 'completed' : '';
      let hour = parseInt(task.start.split(':')[0]);
      if(!task.done){
        if(hour < 12) li.classList.add('morning');
        else if(hour < 18) li.classList.add('afternoon');
        else li.classList.add('evening');
      }
      li.innerHTML = `
        <div class="task-info" onclick="toggleTask(${i})">
          ${task.start} - ${task.end} : ${task.name}
        </div>
        <button onclick="deleteTask(${i})">Done</button>
      `;
      list.appendChild(li);
    });
    updateProgressBar();
  }

  function updateProgressBar() {
    let tasksToday = weekTasks[currentDay];
    let completed = tasksToday.filter(t=>t.done).length;
    let percent = tasksToday.length ? (completed / tasksToday.length) * 100 : 0;
    document.getElementById('progressBar').style.width = percent + '%';
  }
</script>

</body>
</html>
