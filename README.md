<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>TaskFlow | مدیریت وظایف</title>
<link href="https://fonts.googleapis.com/css2?family=Vazirmatn:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #111118;
    --surface2: #1a1a24;
    --border: #2a2a3a;
    --accent: #6c63ff;
    --accent2: #ff6584;
    --accent3: #43e97b;
    --accent4: #f7971e;
    --text: #e8e8f0;
    --text2: #888899;
    --radius: 16px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Vazirmatn', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Animated background */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background: 
      radial-gradient(ellipse 600px 400px at 10% 20%, rgba(108,99,255,0.08) 0%, transparent 70%),
      radial-gradient(ellipse 400px 500px at 90% 80%, rgba(255,101,132,0.06) 0%, transparent 70%),
      radial-gradient(ellipse 500px 300px at 50% 50%, rgba(67,233,123,0.04) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 24px 20px;
    position: relative;
    z-index: 1;
  }

  /* Header */
  header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 32px;
    flex-wrap: wrap;
    gap: 16px;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .logo-icon {
    width: 44px; height: 44px;
    background: linear-gradient(135deg, var(--accent), var(--accent2));
    border-radius: 12px;
    display: flex; align-items: center; justify-content: center;
    font-size: 20px;
    box-shadow: 0 8px 24px rgba(108,99,255,0.4);
  }

  .logo h1 { font-size: 24px; font-weight: 800; letter-spacing: -0.5px; }
  .logo h1 span { color: var(--accent); }

  .date-badge {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 10px 18px;
    font-size: 14px;
    color: var(--text2);
    display: flex; align-items: center; gap: 8px;
  }

  .date-badge .dot {
    width: 8px; height: 8px;
    border-radius: 50%;
    background: var(--accent3);
    box-shadow: 0 0 8px var(--accent3);
    animation: pulse 2s infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(0.8); }
  }

  /* Stats row */
  .stats-row {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 28px;
  }

  .stat-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
    position: relative;
    overflow: hidden;
    transition: transform 0.2s, box-shadow 0.2s;
  }

  .stat-card:hover { transform: translateY(-2px); box-shadow: 0 12px 32px rgba(0,0,0,0.3); }

  .stat-card::after {
    content: '';
    position: absolute;
    top: 0; right: 0;
    width: 60px; height: 60px;
    border-radius: 50%;
    opacity: 0.15;
    transform: translate(20px, -20px);
  }

  .stat-card:nth-child(1)::after { background: var(--accent); }
  .stat-card:nth-child(2)::after { background: var(--accent3); }
  .stat-card:nth-child(3)::after { background: var(--accent4); }
  .stat-card:nth-child(4)::after { background: var(--accent2); }

  .stat-label { font-size: 12px; color: var(--text2); margin-bottom: 8px; }
  .stat-value { font-size: 32px; font-weight: 800; }
  .stat-card:nth-child(1) .stat-value { color: var(--accent); }
  .stat-card:nth-child(2) .stat-value { color: var(--accent3); }
  .stat-card:nth-child(3) .stat-value { color: var(--accent4); }
  .stat-card:nth-child(4) .stat-value { color: var(--accent2); }

  /* Main grid */
  .main-grid {
    display: grid;
    grid-template-columns: 1fr 340px;
    gap: 24px;
    align-items: start;
  }

  /* Panel */
  .panel {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
  }

  .panel-header {
    padding: 20px 24px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
  }

  .panel-title {
    font-size: 16px; font-weight: 700;
    display: flex; align-items: center; gap: 10px;
  }

  .panel-title .icon {
    width: 32px; height: 32px;
    border-radius: 8px;
    display: flex; align-items: center; justify-content: center;
    font-size: 16px;
  }

  /* Add task form */
  .add-form {
    padding: 20px 24px;
    border-bottom: 1px solid var(--border);
    background: var(--surface2);
  }

  .form-row {
    display: flex; gap: 10px;
    margin-bottom: 12px;
    flex-wrap: wrap;
  }

  .form-row:last-child { margin-bottom: 0; }

  input, select, textarea {
    font-family: 'Vazirmatn', sans-serif;
    background: var(--bg);
    border: 1px solid var(--border);
    border-radius: 10px;
    color: var(--text);
    padding: 10px 14px;
    font-size: 14px;
    outline: none;
    transition: border-color 0.2s, box-shadow 0.2s;
  }

  input:focus, select:focus, textarea:focus {
    border-color: var(--accent);
    box-shadow: 0 0 0 3px rgba(108,99,255,0.15);
  }

  input::placeholder { color: var(--text2); }
  select option { background: var(--bg); }

  .task-input { flex: 1; min-width: 200px; }

  .time-input { width: 130px; }

  .priority-select { width: 120px; }

  .btn {
    font-family: 'Vazirmatn', sans-serif;
    border: none; cursor: pointer;
    border-radius: 10px;
    padding: 10px 20px;
    font-size: 14px; font-weight: 600;
    transition: all 0.2s;
    display: flex; align-items: center; gap: 6px;
  }

  .btn-primary {
    background: linear-gradient(135deg, var(--accent), #8b83ff);
    color: white;
    box-shadow: 0 4px 16px rgba(108,99,255,0.35);
  }
  .btn-primary:hover { transform: translateY(-1px); box-shadow: 0 8px 24px rgba(108,99,255,0.45); }

  .btn-ghost {
    background: transparent;
    color: var(--text2);
    border: 1px solid var(--border);
  }
  .btn-ghost:hover { border-color: var(--accent); color: var(--accent); }

  /* Filters */
  .filters {
    padding: 16px 24px;
    border-bottom: 1px solid var(--border);
    display: flex; gap: 8px; flex-wrap: wrap;
  }

  .filter-btn {
    padding: 6px 14px;
    border-radius: 20px;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--text2);
    font-family: 'Vazirmatn', sans-serif;
    font-size: 13px; cursor: pointer;
    transition: all 0.2s;
  }

  .filter-btn.active, .filter-btn:hover {
    background: var(--accent);
    border-color: var(--accent);
    color: white;
  }

  /* Task list */
  .task-list { padding: 12px; max-height: 420px; overflow-y: auto; }

  .task-list::-webkit-scrollbar { width: 4px; }
  .task-list::-webkit-scrollbar-track { background: transparent; }
  .task-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }

  .task-item {
    display: flex; align-items: center; gap: 12px;
    padding: 14px 16px;
    border-radius: 12px;
    border: 1px solid transparent;
    margin-bottom: 6px;
    transition: all 0.25s;
    cursor: pointer;
    animation: slideIn 0.3s ease;
    position: relative;
  }

  @keyframes slideIn {
    from { opacity: 0; transform: translateX(20px); }
    to { opacity: 1; transform: translateX(0); }
  }

  .task-item:hover { background: var(--surface2); border-color: var(--border); }

  .task-item.completed { opacity: 0.5; }
  .task-item.completed .task-title { text-decoration: line-through; color: var(--text2); }

  .task-check {
    width: 22px; height: 22px; flex-shrink: 0;
    border-radius: 6px; border: 2px solid var(--border);
    display: flex; align-items: center; justify-content: center;
    transition: all 0.2s;
    cursor: pointer;
  }

  .task-item.completed .task-check {
    background: var(--accent3);
    border-color: var(--accent3);
    box-shadow: 0 0 10px rgba(67,233,123,0.4);
  }

  .task-info { flex: 1; min-width: 0; }
  .task-title { font-size: 15px; font-weight: 500; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .task-meta { display: flex; align-items: center; gap: 8px; margin-top: 4px; flex-wrap: wrap; }
  .task-time { font-size: 12px; color: var(--text2); display: flex; align-items: center; gap: 4px; }

  .priority-badge {
    font-size: 11px; padding: 2px 8px; border-radius: 20px; font-weight: 600;
  }
  .priority-high { background: rgba(255,101,132,0.15); color: var(--accent2); }
  .priority-medium { background: rgba(247,151,30,0.15); color: var(--accent4); }
  .priority-low { background: rgba(67,233,123,0.15); color: var(--accent3); }

  .task-actions { display: flex; gap: 4px; opacity: 0; transition: opacity 0.2s; }
  .task-item:hover .task-actions { opacity: 1; }

  .action-btn {
    width: 28px; height: 28px; border-radius: 6px;
    border: none; cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    font-size: 13px; background: var(--bg);
    color: var(--text2); transition: all 0.15s;
  }
  .action-btn:hover { background: var(--accent2); color: white; }

  /* Progress bar */
  .progress-section { padding: 16px 24px; }
  .progress-label { display: flex; justify-content: space-between; font-size: 13px; margin-bottom: 8px; color: var(--text2); }
  .progress-bar { height: 8px; background: var(--bg); border-radius: 4px; overflow: hidden; }
  .progress-fill {
    height: 100%; border-radius: 4px;
    background: linear-gradient(90deg, var(--accent), var(--accent3));
    transition: width 0.5s cubic-bezier(0.34, 1.56, 0.64, 1);
    box-shadow: 0 0 10px rgba(108,99,255,0.5);
  }

  /* Sidebar panels */
  .sidebar { display: flex; flex-direction: column; gap: 20px; }

  .chart-container { padding: 20px; height: 220px; position: relative; }

  /* Calendar */
  .calendar-grid {
    padding: 16px 20px;
  }

  .cal-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 16px;
  }
  .cal-title { font-size: 15px; font-weight: 700; }
  .cal-nav {
    width: 28px; height: 28px; border-radius: 6px; border: 1px solid var(--border);
    background: transparent; color: var(--text2); cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    transition: all 0.2s;
  }
  .cal-nav:hover { background: var(--accent); border-color: var(--accent); color: white; }

  .weekdays { display: grid; grid-template-columns: repeat(7, 1fr); gap: 2px; margin-bottom: 6px; }
  .weekday { text-align: center; font-size: 11px; color: var(--text2); padding: 4px; }

  .days-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 2px; }

  .day {
    aspect-ratio: 1; display: flex; align-items: center; justify-content: center;
    border-radius: 6px; font-size: 12px; cursor: pointer;
    transition: all 0.15s; position: relative;
  }
  .day:hover { background: var(--surface2); }
  .day.today {
    background: var(--accent);
    color: white;
    font-weight: 700;
    box-shadow: 0 4px 12px rgba(108,99,255,0.4);
  }
  .day.has-task::after {
    content: '';
    position: absolute; bottom: 3px;
    width: 4px; height: 4px; border-radius: 50%;
    background: var(--accent3);
  }
  .day.empty { cursor: default; }

  /* Empty state */
  .empty-state {
    text-align: center; padding: 48px 24px;
    color: var(--text2);
  }
  .empty-state .empty-icon { font-size: 48px; margin-bottom: 12px; opacity: 0.5; }
  .empty-state p { font-size: 14px; }

  @media (max-width: 768px) {
    .stats-row { grid-template-columns: repeat(2, 1fr); }
    .main-grid { grid-template-columns: 1fr; }
    .form-row { flex-direction: column; }
    .time-input, .priority-select { width: 100%; }
  }
</style>
</head>
<body>

<div class="container">
  <!-- Header -->
  <header>
    <div class="logo">
      <div class="logo-icon">✦</div>
      <h1>Task<span>Flow</span></h1>
    </div>
    <div class="date-badge">
      <div class="dot"></div>
      <span id="currentDate"></span>
    </div>
  </header>

  <!-- Stats -->
  <div class="stats-row">
    <div class="stat-card">
      <div class="stat-label">کل وظایف</div>
      <div class="stat-value" id="totalCount">0</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">انجام شده</div>
      <div class="stat-value" id="doneCount">0</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">در انتظار</div>
      <div class="stat-value" id="pendingCount">0</div>
    </div>
    <div class="stat-card">
      <div class="stat-label">درصد موفقیت</div>
      <div class="stat-value" id="successRate">0%</div>
    </div>
  </div>

  <!-- Main Grid -->
  <div class="main-grid">
    <!-- Left: Task Panel -->
    <div>
      <div class="panel">
        <div class="panel-header">
          <div class="panel-title">
            <div class="icon" style="background:rgba(108,99,255,0.15); color:var(--accent);">📋</div>
            وظایف من
          </div>
        </div>

        <!-- Add Form -->
        <div class="add-form">
          <div class="form-row">
            <input class="task-input" id="taskInput" type="text" placeholder="وظیفه جدید را وارد کنید...">
            <button class="btn btn-primary" onclick="addTask()">
              <span>+</span> افزودن
            </button>
          </div>
          <div class="form-row">
            <input class="time-input" id="taskDate" type="date">
            <input class="time-input" id="taskTime" type="time">
            <select class="priority-select" id="taskPriority">
              <option value="low">اولویت کم</option>
              <option value="medium" selected>اولویت متوسط</option>
              <option value="high">اولویت بالا</option>
            </select>
            <input style="flex:1;min-width:120px;" id="taskCategory" type="text" placeholder="دسته‌بندی (اختیاری)">
          </div>
        </div>

        <!-- Filters -->
        <div class="filters">
          <button class="filter-btn active" onclick="setFilter('all', this)">همه</button>
          <button class="filter-btn" onclick="setFilter('pending', this)">در انتظار</button>
          <button class="filter-btn" onclick="setFilter('completed', this)">انجام شده</button>
          <button class="filter-btn" onclick="setFilter('high', this)">اولویت بالا</button>
          <button class="filter-btn" onclick="setFilter('today', this)">امروز</button>
        </div>

        <!-- Progress -->
        <div class="progress-section">
          <div class="progress-label">
            <span>پیشرفت کلی</span>
            <span id="progressText">0 از 0</span>
          </div>
          <div class="progress-bar">
            <div class="progress-fill" id="progressFill" style="width:0%"></div>
          </div>
        </div>

        <!-- Task List -->
        <div class="task-list" id="taskList">
          <div class="empty-state">
            <div class="empty-icon">🎯</div>
            <p>هنوز وظیفه‌ای اضافه نشده</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Right: Sidebar -->
    <div class="sidebar">
      <!-- Performance Chart -->
      <div class="panel">
        <div class="panel-header">
          <div class="panel-title">
            <div class="icon" style="background:rgba(67,233,123,0.15); color:var(--accent3);">📊</div>
            نمودار عملکرد
          </div>
        </div>
        <div class="chart-container">
          <canvas id="perfChart"></canvas>
        </div>
      </div>

      <!-- Priority Distribution -->
      <div class="panel">
        <div class="panel-header">
          <div class="panel-title">
            <div class="icon" style="background:rgba(247,151,30,0.15); color:var(--accent4);">🎯</div>
            توزیع اولویت
          </div>
        </div>
        <div class="chart-container" style="height:180px">
          <canvas id="priorityChart"></canvas>
        </div>
      </div>

      <!-- Calendar -->
      <div class="panel">
        <div class="panel-header">
          <div class="panel-title">
            <div class="icon" style="background:rgba(255,101,132,0.15); color:var(--accent2);">📅</div>
            تقویم
          </div>
        </div>
        <div class="calendar-grid" id="calendarGrid"></div>
      </div>
    </div>
  </div>
</div>

<script>
let tasks = JSON.parse(localStorage.getItem('taskflow_tasks') || '[]');
let currentFilter = 'all';
let calDate = new Date();
let perfChart, priorityChart;

const persianMonths = ['فروردین','اردیبهشت','خرداد','تیر','مرداد','شهریور','مهر','آبان','آذر','دی','بهمن','اسفند'];
const weekDays = ['ش','ی','د','س','چ','پ','ج'];

function toJalali(y, m, d) {
  // Gregorian to Jalali
  const g_d_no = 365 * y + Math.floor((y + 3) / 4) - Math.floor((y + 99) / 100) + Math.floor((y + 399) / 400) + d + Math.floor(367 * m / 12) - 30;
  let j_d_no = g_d_no - 79;
  const j_np = Math.floor(j_d_no / 12053);
  j_d_no %= 12053;
  let jy = 979 + 33 * j_np + 4 * Math.floor(j_d_no / 1461);
  j_d_no %= 1461;
  if (j_d_no >= 366) { jy += Math.floor((j_d_no - 1) / 365); j_d_no = (j_d_no - 1) % 365; }
  let i = 0, jm = 0, jd = 0;
  for (i = 0; i < 11 && j_d_no >= [31,31,31,31,31,31,30,30,30,30,30,29][i]; i++) j_d_no -= [31,31,31,31,31,31,30,30,30,30,30,29][i];
  jm = i + 1; jd = j_d_no + 1;
  return [jy, jm, jd];
}

function setCurrentDate() {
  const now = new Date();
  const [jy, jm, jd] = toJalali(now.getFullYear(), now.getMonth() + 1, now.getDate());
  const weekDayNames = ['یکشنبه','دوشنبه','سه‌شنبه','چهارشنبه','پنجشنبه','جمعه','شنبه'];
  const dayName = weekDayNames[now.getDay()];
  document.getElementById('currentDate').textContent = `${dayName}، ${jd} ${persianMonths[jm-1]} ${jy}`;
}

function addTask() {
  const title = document.getElementById('taskInput').value.trim();
  if (!title) return;
  const task = {
    id: Date.now(),
    title,
    date: document.getElementById('taskDate').value,
    time: document.getElementById('taskTime').value,
    priority: document.getElementById('taskPriority').value,
    category: document.getElementById('taskCategory').value.trim(),
    completed: false,
    createdAt: new Date().toISOString()
  };
  tasks.unshift(task);
  saveTasks();
  document.getElementById('taskInput').value = '';
  document.getElementById('taskCategory').value = '';
  renderAll();
}

function toggleTask(id) {
  const t = tasks.find(t => t.id === id);
  if (t) { t.completed = !t.completed; saveTasks(); renderAll(); }
}

function deleteTask(id) {
  tasks = tasks.filter(t => t.id !== id);
  saveTasks(); renderAll();
}

function setFilter(f, btn) {
  currentFilter = f;
  document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
  renderTasks();
}

function getFilteredTasks() {
  const today = new Date().toISOString().slice(0,10);
  switch(currentFilter) {
    case 'pending': return tasks.filter(t => !t.completed);
    case 'completed': return tasks.filter(t => t.completed);
    case 'high': return tasks.filter(t => t.priority === 'high');
    case 'today': return tasks.filter(t => t.date === today);
    default: return tasks;
  }
}

function renderTasks() {
  const list = document.getElementById('taskList');
  const filtered = getFilteredTasks();
  if (!filtered.length) {
    list.innerHTML = `<div class="empty-state"><div class="empty-icon">🎯</div><p>وظیفه‌ای یافت نشد</p></div>`;
    return;
  }
  const priorityLabel = { high: 'بالا', medium: 'متوسط', low: 'کم' };
  list.innerHTML = filtered.map(t => {
    let meta = '';
    if (t.date || t.time) {
      let d = '';
      if (t.date) {
        const [y,m,day] = t.date.split('-');
        const [jy,jm,jd] = toJalali(+y,+m,+day);
        d = `${jd} ${persianMonths[jm-1]}`;
      }
      meta += `<span class="task-time">📅 ${d} ${t.time || ''}</span>`;
    }
    if (t.category) meta += `<span class="task-time">🏷️ ${t.category}</span>`;
    return `
      <div class="task-item ${t.completed ? 'completed' : ''}" onclick="toggleTask(${t.id})">
        <div class="task-check">${t.completed ? '✓' : ''}</div>
        <div class="task-info">
          <div class="task-title">${t.title}</div>
          <div class="task-meta">
            <span class="priority-badge priority-${t.priority}">${priorityLabel[t.priority]}</span>
            ${meta}
          </div>
        </div>
        <div class="task-actions" onclick="event.stopPropagation()">
          <button class="action-btn" onclick="deleteTask(${t.id})" title="حذف">🗑</button>
        </div>
      </div>`;
  }).join('');
}

function updateStats() {
  const total = tasks.length;
  const done = tasks.filter(t => t.completed).length;
  const pending = total - done;
  const rate = total ? Math.round(done / total * 100) : 0;
  document.getElementById('totalCount').textContent = total;
  document.getElementById('doneCount').textContent = done;
  document.getElementById('pendingCount').textContent = pending;
  document.getElementById('successRate').textContent = rate + '%';
  document.getElementById('progressText').textContent = `${done} از ${total}`;
  document.getElementById('progressFill').style.width = rate + '%';
}

function renderCharts() {
  // Performance: last 7 days
  const labels = [], doneData = [], addedData = [];
  for (let i = 6; i >= 0; i--) {
    const d = new Date(); d.setDate(d.getDate() - i);
    const ds = d.toISOString().slice(0,10);
    const [jy,jm,jd] = toJalali(d.getFullYear(), d.getMonth()+1, d.getDate());
    labels.push(`${jd}/${jm}`);
    doneData.push(tasks.filter(t => t.completed && t.createdAt?.slice(0,10) === ds).length);
    addedData.push(tasks.filter(t => t.createdAt?.slice(0,10) === ds).length);
  }

  const chartDefaults = {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { labels: { color: '#888899', font: { family: 'Vazirmatn', size: 11 }, boxWidth: 12, padding: 10 } } }
  };

  if (perfChart) perfChart.destroy();
  perfChart = new Chart(document.getElementById('perfChart'), {
    type: 'bar',
    data: {
      labels,
      datasets: [
        { label: 'اضافه شده', data: addedData, backgroundColor: 'rgba(108,99,255,0.5)', borderRadius: 4 },
        { label: 'انجام شده', data: doneData, backgroundColor: 'rgba(67,233,123,0.6)', borderRadius: 4 }
      ]
    },
    options: {
      ...chartDefaults,
      scales: {
        x: { ticks: { color: '#888899', font: { family: 'Vazirmatn' } }, grid: { color: '#2a2a3a' } },
        y: { max: 100, ticks: { color: '#888899', font: { family: 'Vazirmatn' }, stepSize: 10 }, grid: { color: '#2a2a3a' } }
      }
    }
  });

  // Priority Pie
  const high = tasks.filter(t => t.priority === 'high').length;
  const med = tasks.filter(t => t.priority === 'medium').length;
  const low = tasks.filter(t => t.priority === 'low').length;
  if (priorityChart) priorityChart.destroy();
  priorityChart = new Chart(document.getElementById('priorityChart'), {
    type: 'doughnut',
    data: {
      labels: ['بالا', 'متوسط', 'کم'],
      datasets: [{ data: [high || 0, med || 0, low || 0], backgroundColor: ['rgba(255,101,132,0.8)','rgba(247,151,30,0.8)','rgba(67,233,123,0.8)'], borderWidth: 0, borderRadius: 3 }]
    },
    options: { ...chartDefaults, cutout: '65%' }
  });
}

function renderCalendar() {
  const now = new Date();
  const [jy, jm] = toJalali(calDate.getFullYear(), calDate.getMonth()+1, calDate.getDate());
  const [, , todayJ] = toJalali(now.getFullYear(), now.getMonth()+1, now.getDate());
  const todayStr = now.toISOString().slice(0,10);
  const taskDates = new Set(tasks.map(t => t.date).filter(Boolean));

  const monthLen = jm <= 6 ? 31 : jm <= 11 ? 30 : 29;
  // first day of month weekday (approx)
  const firstDay = new Date(calDate.getFullYear(), calDate.getMonth(), 1);
  let dow = (firstDay.getDay() + 1) % 7;

  let html = `
    <div class="cal-header">
      <button class="cal-nav" onclick="prevMonth()">›</button>
      <div class="cal-title">${persianMonths[jm-1]} ${jy}</div>
      <button class="cal-nav" onclick="nextMonth()">‹</button>
    </div>
    <div class="weekdays">${weekDays.map(w => `<div class="weekday">${w}</div>`).join('')}</div>
    <div class="days-grid">`;

  for (let i = 0; i < dow; i++) html += `<div class="day empty"></div>`;
  for (let d = 1; d <= monthLen; d++) {
    const isToday = d === todayJ && calDate.getMonth() === now.getMonth();
    // rough date calculation for task matching
    html += `<div class="day ${isToday ? 'today' : ''}">${d}</div>`;
  }
  html += `</div>`;
  document.getElementById('calendarGrid').innerHTML = html;
}

function prevMonth() { calDate.setMonth(calDate.getMonth() - 1); renderCalendar(); }
function nextMonth() { calDate.setMonth(calDate.getMonth() + 1); renderCalendar(); }

function saveTasks() { localStorage.setItem('taskflow_tasks', JSON.stringify(tasks)); }

function renderAll() { renderTasks(); updateStats(); renderCharts(); renderCalendar(); }

// Enter key
document.getElementById('taskInput').addEventListener('keydown', e => { if(e.key === 'Enter') addTask(); });

// Set default date to today
document.getElementById('taskDate').value = new Date().toISOString().slice(0,10);

setCurrentDate();
renderAll();

// Demo tasks if empty
if (!tasks.length) {
  tasks = [
    { id: 1, title: 'مطالعه فصل ۳ کتاب برنامه‌نویسی', date: new Date().toISOString().slice(0,10), time: '10:00', priority: 'high', category: 'تحصیل', completed: false, createdAt: new Date().toISOString() },
    { id: 2, title: 'جلسه تیمی هفتگی', date: new Date().toISOString().slice(0,10), time: '14:00', priority: 'medium', category: 'کار', completed: true, createdAt: new Date().toISOString() },
    { id: 3, title: 'ورزش صبحگاهی', date: new Date().toISOString().slice(0,10), time: '07:00', priority: 'low', category: 'سلامت', completed: true, createdAt: new Date().toISOString() },
    { id: 4, title: 'بررسی ایمیل‌های کاری', date: '', time: '', priority: 'medium', category: 'کار', completed: false, createdAt: new Date().toISOString() },
  ];
  saveTasks(); renderAll();
}
</script>
</body>
</html>
