<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>فلوسي | Flosy - النظام المالي المنزلي</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        :root{--primary:#1f2937;--secondary:#2563eb;--success:#22c55e;--danger:#ef4444;--warning:#f59e0b;--bg:#f1f5f9;--card-bg:#fff;--text:#1e293b;--text-light:#64748b;--border:#e2e8f0;--shadow:0 4px 6px -1px rgba(0,0,0,0.1);--radius:16px;--transition:all 0.3s ease}
        .dark-mode{--bg:#0f172a;--card-bg:#1e293b;--text:#f1f5f9;--text-light:#94a3b8;--border:#334155;--shadow:0 4px 6px -1px rgba(0,0,0,0.5)}
        *{margin:0;padding:0;box-sizing:border-box}
        body{font-family:'Tajawal',sans-serif;background:var(--bg);color:var(--text);transition:var(--transition);direction:rtl}
        .sidebar{position:fixed;right:0;top:0;width:280px;height:100%;background:var(--primary);padding:20px;color:#fff;overflow-y:auto;z-index:1000;transition:var(--transition);box-shadow:2px 0 10px rgba(0,0,0,0.1)}
        .sidebar-header{text-align:center;padding-bottom:20px;border-bottom:1px solid rgba(255,255,255,0.1)}
        .sidebar-header h2{font-size:28px;margin-bottom:5px}.sidebar-header h2 i{color:var(--secondary)}
        .subtitle{font-size:14px;opacity:0.7}
        .sidebar-menu{margin:20px 0}
        .sidebar-menu a{display:flex;align-items:center;gap:12px;padding:12px 16px;color:rgba(255,255,255,0.7);text-decoration:none;border-radius:12px;transition:var(--transition);margin:4px 0;font-size:16px}
        .sidebar-menu a:hover,.sidebar-menu a.active{background:rgba(255,255,255,0.1);color:#fff}
        .sidebar-menu a.active{background:var(--secondary);color:#fff}
        .sidebar-menu a i{width:24px;text-align:center}
        .sidebar-footer{position:absolute;bottom:20px;right:20px;left:20px;padding-top:20px;border-top:1px solid rgba(255,255,255,0.1)}
        .dark-mode-toggle{display:flex;align-items:center;justify-content:center;gap:12px}
        .switch{position:relative;display:inline-block;width:50px;height:26px}
        .switch input{opacity:0;width:0;height:0}
        .slider{position:absolute;cursor:pointer;top:0;left:0;right:0;bottom:0;background:#ccc;transition:.4s;border-radius:34px}
        .slider:before{position:absolute;content:"";height:20px;width:20px;right:3px;bottom:3px;background:#fff;transition:.4s;border-radius:50%}
        input:checked+.slider{background:var(--secondary)}
        input:checked+.slider:before{transform:translateX(-24px)}
        .menu-toggle{display:none;position:fixed;top:20px;right:20px;z-index:1100;background:var(--primary);color:#fff;border:none;border-radius:12px;padding:10px 14px;font-size:20px;cursor:pointer;box-shadow:var(--shadow)}
        .main-content{margin-right:280px;padding:30px;min-height:100vh;transition:var(--transition)}
        .page{display:none;animation:fadeIn .4s ease}
        .page.active{display:block}
        @keyframes fadeIn{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:translateY(0)}}
        .page-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:30px;flex-wrap:wrap;gap:10px}
        .page-header h1{font-size:28px;font-weight:700}.page-header h1 i{color:var(--secondary)}
        .date-display{color:var(--text-light);font-size:16px}
        .stats-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:20px;margin-bottom:30px}
        .stat-card{background:var(--card-bg);padding:20px;border-radius:var(--radius);box-shadow:var(--shadow);display:flex;align-items:center;gap:15px;transition:var(--transition);border-right:4px solid var(--secondary)}
        .stat-card:hover{transform:translateY(-4px);box-shadow:0 10px 20px rgba(0,0,0,0.1)}
        .stat-icon{width:50px;height:50px;border-radius:12px;display:flex;align-items:center;justify-content:center;font-size:24px;color:#fff}
        .income-card .stat-icon{background:var(--success)}
        .expense-card .stat-icon{background:var(--danger)}
        .balance-card .stat-icon{background:var(--secondary)}
        .transactions-card .stat-icon{background:var(--warning)}
        .stat-info h3{font-size:14px;color:var(--text-light);font-weight:400}
        .stat-value{font-size:24px;font-weight:700;margin-top:4px}
        .charts-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(400px,1fr));gap:20px;margin-bottom:30px}
        .chart-card{background:var(--card-bg);padding:20px;border-radius:var(--radius);box-shadow:var(--shadow)}
        .chart-card h3{margin-bottom:15px;font-size:18px}.chart-card h3 i{color:var(--secondary)}
        .chart-card canvas{max-height:300px;max-width:100%}
        .alerts-section{background:var(--card-bg);padding:20px;border-radius:var(--radius);box-shadow:var(--shadow);margin-bottom:30px}
        .alerts-section h3{margin-bottom:15px}.alerts-section h3 i{color:var(--warning)}
        .alert-item{padding:12px 16px;border-radius:10px;margin:8px 0;display:flex;align-items:center;gap:10px;background:#fef3c7;color:#92400e;border-right:4px solid var(--warning)}
        .alert-item.success{background:#d1fae5;color:#065f46;border-color:var(--success)}
        .alert-item.danger{background:#fee2e2;color:#991b1b;border-color:var(--danger)}
        .alert-item i{font-size:18px}
        .form-card{background:var(--card-bg);padding:24px;border-radius:var(--radius);box-shadow:var(--shadow);margin-bottom:30px}
        .form-card h3{margin-bottom:20px}
        .form-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:15px}
        .form-group{display:flex;flex-direction:column;gap:6px}
        .form-group label{font-weight:500;font-size:14px;color:var(--text-light)}
        .form-group label i{margin-left:6px;color:var(--secondary)}
        .form-group input,.form-group select{padding:10px 12px;border:2px solid var(--border);border-radius:10px;background:var(--bg);color:var(--text);font-family:inherit;font-size:14px;transition:var(--transition)}
        .form-group input:focus,.form-group select:focus{outline:none;border-color:var(--secondary);box-shadow:0 0 0 3px rgba(37,99,235,0.1)}
        .form-group.full-width{grid-column:1/-1}
        button{font-family:inherit;cursor:pointer;transition:var(--transition)}
        .btn-primary{background:var(--secondary);color:#fff;border:none;padding:12px 24px;border-radius:10px;font-size:16px;font-weight:500;display:inline-flex;align-items:center;gap:8px}
        .btn-primary:hover{transform:translateY(-2px);box-shadow:0 4px 12px rgba(37,99,235,0.3)}
        .btn-secondary{background:var(--bg);color:var(--text);border:2px solid var(--border);padding:10px 20px;border-radius:10px;font-size:14px;display:inline-flex;align-items:center;gap:8px}
        .btn-secondary:hover{background:var(--border)}
        .btn-danger{background:var(--danger);color:#fff;border:none;padding:10px 20px;border-radius:10px;font-size:14px;display:inline-flex;align-items:center;gap:8px}
        .btn-danger:hover{opacity:.8}
        .table-responsive{overflow-x:auto;background:var(--card-bg);border-radius:var(--radius);box-shadow:var(--shadow)}
        table{width:100%;border-collapse:collapse;font-size:14px}
        thead{background:var(--bg)}
        th{padding:12px 16px;text-align:right;font-weight:600;color:var(--text-light);border-bottom:2px solid var(--border)}
        td{padding:12px 16px;border-bottom:1px solid var(--border)}
        tr:hover{background:var(--bg)}
        .table-controls{display:flex;gap:10px;flex-wrap:wrap;margin-bottom:15px}
        .table-controls input,.table-controls select{padding:8px 12px;border:2px solid var(--border);border-radius:8px;background:var(--bg);color:var(--text);font-family:inherit}
        .progress-bar{width:100%;height:8px;background:var(--bg);border-radius:4px;overflow:hidden;margin:8px 0}
        .progress-bar .progress-fill{height:100%;border-radius:4px;transition:width .6s ease}
        .progress-fill.success{background:var(--success)}
        .progress-fill.warning{background:var(--warning)}
        .progress-fill.danger{background:var(--danger)}
        .goal-card,.subscription-card,.budget-card{background:var(--card-bg);padding:16px 20px;border-radius:var(--radius);box-shadow:var(--shadow);margin:10px 0;display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:10px;border-right:4px solid var(--secondary)}
        .goal-card .goal-info h4,.subscription-card .sub-info h4,.budget-card .budget-info h4{font-size:16px;margin-bottom:4px}
        .goal-card .goal-info p,.subscription-card .sub-info p,.budget-card .budget-info p{font-size:14px;color:var(--text-light)}
        .report-controls{display:flex;gap:15px;flex-wrap:wrap;align-items:end;background:var(--card-bg);padding:20px;border-radius:var(--radius);box-shadow:var(--shadow);margin-bottom:30px}
        .report-summary{display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:15px;margin:20px 0}
        .report-stat{background:var(--bg);padding:15px;border-radius:var(--radius);text-align:center}
        .report-stat h4{font-size:14px;color:var(--text-light);margin-bottom:8px}
        .report-stat p{font-size:24px;font-weight:700}
        .report-actions{display:flex;gap:10px;flex-wrap:wrap;margin-top:20px}
        .settings-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(300px,1fr));gap:20px}
        .settings-card{background:var(--card-bg);padding:20px;border-radius:var(--radius);box-shadow:var(--shadow)}
        .settings-card h3{margin-bottom:15px;font-size:18px}.settings-card h3 i{color:var(--secondary)}
        .settings-card button{margin:6px 0;width:100%;justify-content:center}
        .setting-item{display:flex;justify-content:space-between;align-items:center;padding:10px 0;border-bottom:1px solid var(--border)}
        .add-category,.add-account{display:flex;gap:10px;margin-top:10px}
        .add-category input,.add-account input{flex:1;padding:8px 12px;border:2px solid var(--border);border-radius:8px;background:var(--bg);color:var(--text)}
        .amount-income{color:var(--success);font-weight:600}
        .amount-expense{color:var(--danger);font-weight:600}
        .transaction-actions button{padding:4px 8px;border:none;border-radius:6px;cursor:pointer;margin:0 2px}
        .btn-edit{background:var(--warning);color:#fff}
        .btn-delete{background:var(--danger);color:#fff}
        .filter-badge{display:inline-block;padding:4px 12px;border-radius:20px;font-size:12px;font-weight:500}
        .filter-badge.income{background:#d1fae5;color:#065f46}
        .filter-badge.expense{background:#fee2e2;color:#991b1b}
        @media(max-width:768px){.sidebar{right:-280px}.sidebar.open{right:0}.menu-toggle{display:block}.main-content{margin-right:0;padding:15px}.stats-grid{grid-template-columns:repeat(2,1fr)}.charts-grid{grid-template-columns:1fr}.form-grid{grid-template-columns:1fr}.table-controls{flex-direction:column}.report-controls{flex-direction:column}.page-header{flex-direction:column;align-items:start}}
    </style>
</head>
<body>
    <button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button>
    <nav class="sidebar" id="sidebar">
        <div class="sidebar-header"><h2><i class="fas fa-wallet"></i> فلوسي</h2><p class="subtitle">نظام مالي منزلي</p></div>
        <div class="sidebar-menu">
            <a href="#" class="active" data-page="dashboard"><i class="fas fa-home"></i> لوحة التحكم</a>
            <a href="#" data-page="transactions"><i class="fas fa-exchange-alt"></i> العمليات</a>
            <a href="#" data-page="budgets"><i class="fas fa-bullseye"></i> الميزانيات</a>
            <a href="#" data-page="goals"><i class="fas fa-trophy"></i> الأهداف</a>
            <a href="#" data-page="subscriptions"><i class="fas fa-clock"></i> الاشتراكات</a>
            <a href="#" data-page="reports"><i class="fas fa-file-alt"></i> التقارير</a>
            <a href="#" data-page="settings"><i class="fas fa-cog"></i> الإعدادات</a>
        </div>
        <div class="sidebar-footer">
            <div class="dark-mode-toggle">
                <i class="fas fa-moon"></i>
                <label class="switch"><input type="checkbox" id="darkModeToggle"><span class="slider"></span></label>
                <i class="fas fa-sun"></i>
            </div>
        </div>
    </nav>

    <main class="main-content">
        <section id="dashboard" class="page active">
            <div class="page-header"><h1><i class="fas fa-chart-pie"></i> لوحة التحكم</h1><div class="date-display" id="currentDate"></div></div>
            <div class="stats-grid">
                <div class="stat-card income-card"><div class="stat-icon"><i class="fas fa-arrow-up"></i></div><div class="stat-info"><h3>إجمالي الدخل</h3><p class="stat-value" id="totalIncome">0</p></div></div>
                <div class="stat-card expense-card"><div class="stat-icon"><i class="fas fa-arrow-down"></i></div><div class="stat-info"><h3>إجمالي المصروفات</h3><p class="stat-value" id="totalExpense">0</p></div></div>
                <div class="stat-card balance-card"><div class="stat-icon"><i class="fas fa-coins"></i></div><div class="stat-info"><h3>الرصيد الحالي</h3><p class="stat-value" id="currentBalance">0</p></div></div>
                <div class="stat-card transactions-card"><div class="stat-icon"><i class="fas fa-list"></i></div><div class="stat-info"><h3>عدد العمليات</h3><p class="stat-value" id="transactionCount">0</p></div></div>
            </div>
            <div class="charts-grid">
                <div class="chart-card"><h3><i class="fas fa-chart-doughnut"></i> توزيع المصروفات</h3><canvas id="expenseChart"></canvas></div>
                <div class="chart-card"><h3><i class="fas fa-chart-line"></i> التدفق النقدي</h3><canvas id="cashFlowChart"></canvas></div>
            </div>
            <div class="alerts-section"><h3><i class="fas fa-bell"></i> التنبيهات الذكية</h3><div id="alertsContainer"></div></div>
            <div class="recent-transactions"><h3><i class="fas fa-clock"></i> آخر العمليات</h3><div id="recentTransactions"></div></div>
        </section>

        <section id="transactions" class="page">
            <div class="page-header"><h1><i class="fas fa-exchange-alt"></i> إدارة العمليات</h1></div>
            <div class="form-card">
                <h3>تسجيل عملية جديدة</h3>
                <form id="transactionForm">
                    <div class="form-grid">
                        <div class="form-group"><label><i class="fas fa-tag"></i> النوع</label><select id="txType" required><option value="income">دخل</option><option value="expense">مصروف</option></select></div>
                        <div class="form-group"><label><i class="fas fa-money-bill"></i> المبلغ</label><input type="number" id="txAmount" placeholder="المبلغ" required min="0.01" step="0.01"></div>
                        <div class="form-group"><label><i class="fas fa-folder"></i> التصنيف</label><select id="txCategory" required></select></div>
                        <div class="form-group"><label><i class="fas fa-calendar"></i> التاريخ</label><input type="date" id="txDate" required></div>
                        <div class="form-group"><label><i class="fas fa-wallet"></i> الحساب</label><select id="txAccount"></select></div>
                        <div class="form-group full-width"><label><i class="fas fa-sticky-note"></i> ملاحظات</label><input type="text" id="txNotes" placeholder="ملاحظات اختيارية"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-save"></i> حفظ العملية</button>
                </form>
            </div>
            <div class="transactions-list">
                <div class="table-header"><h3>جميع العمليات</h3><div class="table-controls"><input type="text" id="searchTransactions" placeholder="🔍 بحث..."><select id="filterCategory"><option value="all">كل التصنيفات</option></select><select id="filterType"><option value="all">الكل</option><option value="income">دخل</option><option value="expense">مصروف</option></select></div></div>
                <div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>الحساب</th><th>الملاحظات</th><th>الإجراءات</th></tr></thead><tbody id="transactionsTableBody"></tbody></table></div>
            </div>
        </section>

        <section id="budgets" class="page">
            <div class="page-header"><h1><i class="fas fa-bullseye"></i> الميزانيات والحدود</h1></div>
            <div class="form-card">
                <h3>إضافة ميزانية جديدة</h3>
                <form id="budgetForm">
                    <div class="form-grid">
                        <div class="form-group"><label>التصنيف</label><select id="budgetCategory" required></select></div>
                        <div class="form-group"><label>الحد الشهري</label><input type="number" id="budgetLimit" placeholder="المبلغ" required min="1" step="0.01"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة ميزانية</button>
                </form>
            </div>
            <div id="budgetsList"></div>
        </section>

        <section id="goals" class="page">
            <div class="page-header"><h1><i class="fas fa-trophy"></i> الأهداف المالية</h1></div>
            <div class="form-card">
                <h3>هدف جديد</h3>
                <form id="goalForm">
                    <div class="form-grid">
                        <div class="form-group"><label>اسم الهدف</label><input type="text" id="goalName" placeholder="مثال: شراء لابتوب" required></div>
                        <div class="form-group"><label>المبلغ المستهدف</label><input type="number" id="goalTarget" placeholder="المبلغ" required min="1" step="0.01"></div>
                        <div class="form-group"><label>المبلغ المدخر حالياً</label><input type="number" id="goalCurrent" placeholder="المبلغ المدخر" required min="0" step="0.01"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة هدف</button>
                </form>
            </div>
            <div id="goalsList"></div>
        </section>

        <section id="subscriptions" class="page">
            <div class="page-header"><h1><i class="fas fa-clock"></i> الاشتراكات والفواتير الدورية</h1></div>
            <div class="form-card">
                <h3>إضافة اشتراك جديد</h3>
                <form id="subscriptionForm">
                    <div class="form-grid">
                        <div class="form-group"><label>الاسم</label><input type="text" id="subName" placeholder="مثال: ChatGPT" required></div>
                        <div class="form-group"><label>المبلغ الشهري</label><input type="number" id="subAmount" placeholder="المبلغ" required min="1" step="0.01"></div>
                        <div class="form-group"><label>تاريخ الاستحقاق</label><input type="date" id="subDueDate" required></div>
                        <div class="form-group"><label>الحالة</label><select id="subStatus"><option value="مدفوع">مدفوع</option><option value="غير مدفوع">غير مدفوع</option><option value="متأخر">متأخر</option></select></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة اشتراك</button>
                </form>
            </div>
            <div id="subscriptionsList"></div>
        </section>

        <section id="reports" class="page">
            <div class="page-header"><h1><i class="fas fa-file-alt"></i> التقارير والتحليل</h1></div>
            <div class="report-controls">
                <div class="form-group"><label>السنة</label><select id="reportYear"></select></div>
                <div class="form-group"><label>الشهر</label><select id="reportMonth"><option value="all">كل الشهور</option><option value="1">يناير</option><option value="2">فبراير</option><option value="3">مارس</option><option value="4">أبريل</option><option value="5">مايو</option><option value="6">يونيو</option><option value="7">يوليو</option><option value="8">أغسطس</option><option value="9">سبتمبر</option><option value="10">أكتوبر</option><option value="11">نوفمبر</option><option value="12">ديسمبر</option></select></div>
                <button onclick="generateReport()" class="btn-primary"><i class="fas fa-chart-bar"></i> عرض التقرير</button>
            </div>
            <div id="reportContent">
                <div class="report-summary">
                    <div class="report-stat"><h4>إجمالي الدخل</h4><p id="reportIncome">0</p></div>
                    <div class="report-stat"><h4>إجمالي المصروفات</h4><p id="reportExpense">0</p></div>
                    <div class="report-stat"><h4>صافي الربح</h4><p id="reportProfit">0</p></div>
                </div>
                <div class="report-actions">
                    <button onclick="exportExcel()" class="btn-secondary"><i class="fas fa-file-excel"></i> تصدير Excel</button>
                    <button onclick="exportPDF()" class="btn-secondary"><i class="fas fa-file-pdf"></i> تصدير PDF</button>
                </div>
            </div>
        </section>

        <section id="settings" class="page">
            <div class="page-header"><h1><i class="fas fa-cog"></i> الإعدادات</h1></div>
            <div class="settings-grid">
                <div class="settings-card">
                    <h3><i class="fas fa-palette"></i> المظهر</h3>
                    <div class="setting-item">
                        <label>الوضع الليلي</label>
                        <div class="dark-mode-toggle">
                            <i class="fas fa-moon"></i>
                            <label class="switch"><input type="checkbox" id="settingsDarkMode"><span class="slider"></span></label>
                            <i class="fas fa-sun"></i>
                        </div>
                    </div>
                </div>
                <div class="settings-card">
                    <h3><i class="fas fa-database"></i> البيانات</h3>
                    <button onclick="backupData()" class="btn-secondary"><i class="fas fa-download"></i> نسخ احتياطي</button>
                    <button onclick="document.getElementById('restoreFile').click()" class="btn-secondary"><i class="fas fa-upload"></i> استعادة بيانات</button>
                    <input type="file" id="restoreFile" accept=".json" style="display:none" onchange="restoreData(event)">
                    <button onclick="clearAllData()" class="btn-danger" style="margin-top:10px"><i class="fas fa-trash"></i> حذف كل البيانات</button>
                </div>
                <div class="settings-card">
                    <h3><i class="fas fa-tags"></i> إدارة التصنيفات</h3>
                    <div id="categoriesList"></div>
                    <div class="add-category"><input type="text" id="newCategory" placeholder="تصنيف جديد"><button onclick="addCategory()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button></div>
                </div>
                <div class="settings-card">
                    <h3><i class="fas fa-account"></i> إدارة الحسابات</h3>
                    <div id="accountsList"></div>
                    <div class="add-account"><input type="text" id="newAccount" placeholder="حساب جديد"><button onclick="addAccount()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button></div>
                </div>
            </div>
        </section>
    </main>

    <script>
        // ===== Data Manager =====
        const DB = {
            keys: {
                transactions: 'flosy_transactions',
                budgets: 'flosy_budgets',
                goals: 'flosy_goals',
                subscriptions: 'flosy_subscriptions',
                categories: 'flosy_categories',
                accounts: 'flosy_accounts',
                settings: 'flosy_settings'
            },
            init() {
                if (!localStorage.getItem(this.keys.transactions)) localStorage.setItem(this.keys.transactions, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.budgets)) localStorage.setItem(this.keys.budgets, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.goals)) localStorage.setItem(this.keys.goals, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.subscriptions)) localStorage.setItem(this.keys.subscriptions, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.categories)) localStorage.setItem(this.keys.categories, JSON.stringify(['طعام','مواصلات','فواتير','إيجار','ترفيه','صحة','تعليم','ملابس','هدايا','أخرى']));
                if (!localStorage.getItem(this.keys.accounts)) localStorage.setItem(this.keys.accounts, JSON.stringify(['نقدي','بنك','فودافون كاش','بطاقة ائتمان']));
                if (!localStorage.getItem(this.keys.settings)) localStorage.setItem(this.keys.settings, JSON.stringify({darkMode:false}));
            },
            get(key) { return JSON.parse(localStorage.getItem(this.keys[key]) || '[]'); },
            set(key, data) { localStorage.setItem(this.keys[key], JSON.stringify(data)); },
            addTransaction(t) { const d = this.get('transactions'); t.id = Date.now(); d.push(t); this.set('transactions', d); return t; },
            deleteTransaction(id) { let d = this.get('transactions'); d = d.filter(t => t.id !== id); this.set('transactions', d); },
            updateTransaction(id, u) { let d = this.get('transactions'); const i = d.findIndex(t => t.id === id); if (i !== -1) { d[i] = { ...d[i], ...u }; this.set('transactions', d); return d[i]; } return null; },
            addBudget(b) { const d = this.get('budgets'); b.id = Date.now(); d.push(b); this.set('budgets', d); return b; },
            deleteBudget(id) { let d = this.get('budgets'); d = d.filter(b => b.id !== id); this.set('budgets', d); },
            addGoal(g) { const d = this.get('goals'); g.id = Date.now(); g.progress = (g.current / g.target) * 100; d.push(g); this.set('goals', d); return g; },
            deleteGoal(id) { let d = this.get('goals'); d = d.filter(g => g.id !== id); this.set('goals', d); },
            updateGoal(id, c) { let d = this.get('goals'); const g = d.find(x => x.id === id); if (g) { g.current = c; g.progress = (g.current / g.target) * 100; this.set('goals', d); return g; } return null; },
            addSubscription(s) { const d = this.get('subscriptions'); s.id = Date.now(); d.push(s); this.set('subscriptions', d); return s; },
            deleteSubscription(id) { let d = this.get('subscriptions'); d = d.filter(s => s.id !== id); this.set('subscriptions', d); },
            getCategories() { return this.get('categories'); },
            addCategory(c) { const d = this.get('categories'); if (!d.includes(c)) { d.push(c); this.set('categories', d); return true; } return false; },
            deleteCategory(c) { let d = this.get('categories'); d = d.filter(x => x !== c); this.set('categories', d); },
            getAccounts() { return this.get('accounts'); },
            addAccount(a) { const d = this.get('accounts'); if (!d.includes(a)) { d.push(a); this.set('accounts', d); return true; } return false; },
            deleteAccount(a) { let d = this.get('accounts'); d = d.filter(x => x !== a); this.set('accounts', d); },
            getSettings() { return JSON.parse(localStorage.getItem(this.keys.settings) || '{}'); },
            saveSettings(s) { localStorage.setItem(this.keys.settings, JSON.stringify(s)); },
            backup() { return { transactions: this.get('transactions'), budgets: this.get('budgets'), goals: this.get('goals'), subscriptions: this.get('subscriptions'), categories: this.get('categories'), accounts: this.get('accounts'), settings: this.getSettings() }; },
            restore(data) { Object.keys(this.keys).forEach(k => { if (data[k]) this.set(k, data[k]); }); },
            clearAll() { Object.values(this.keys).forEach(k => localStorage.removeItem(k)); this.init(); }
        };
        DB.init();

        // ===== Helper Functions =====
        function formatCurrency(a) { return a.toFixed(2) + ' جنيه'; }
        function getDateString(d) { if (!d) return ''; return new Date(d).toLocaleDateString('ar-EG'); }
        function getTypeClass(t) { return t === 'income' ? 'amount-income' : 'amount-expense'; }
        function getTypeLabel(t) { return t === 'income' ? 'دخل' : 'مصروف'; }
        function getStatusBadge(s) { const c = { 'مدفوع':'success','غير مدفوع':'warning','متأخر':'danger' }; return `<span class="filter-badge ${c[s]||''}">${s}</span>`; }
        function getBalance() { return DB.get('transactions').reduce((s, t) => s + (t.type === 'income' ? t.amount : -t.amount), 0); }
        function getTotalIncome() { return DB.get('transactions').filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0); }
        function getTotalExpense() { return DB.get('transactions').filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0); }

        function checkAlerts() {
            const transactions = DB.get('transactions');
            const budgets = DB.get('budgets');
            const subscriptions = DB.get('subscriptions');
            const alerts = [];
            const now = new Date();
            const month = now.getMonth(), year = now.getFullYear();
            const monthlyExpenses = transactions.filter(t => { const d = new Date(t.date); return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year; });
            budgets.forEach(b => {
                const spent = monthlyExpenses.filter(t => t.category === b.category).reduce((s, t) => s + t.amount, 0);
                const p = (spent / b.limit) * 100;
                if (p >= 90) alerts.push({ type: 'danger', icon: 'fa-exclamation-triangle', message: `⚠️ تجاوزت ميزانية "${b.category}" ${p.toFixed(0)}% (${formatCurrency(spent)} من ${formatCurrency(b.limit)})` });
                else if (p >= 70) alerts.push({ type: 'warning', icon: 'fa-exclamation-circle', message: `⚡ ميزانية "${b.category}" قاربت على الانتهاء (${p.toFixed(0)}%)` });
            });
            subscriptions.forEach(s => {
                const due = new Date(s.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                if (diff < 0 && s.status !== 'مدفوع') alerts.push({ type: 'danger', icon: 'fa-times-circle', message: `🔴 اشتراك "${s.name}" متأخر منذ ${Math.abs(diff)} يوم` });
                else if (diff <= 3 && diff >= 0 && s.status !== 'مدفوع') alerts.push({ type: 'warning', icon: 'fa-clock', message: `⏰ اشتراك "${s.name}" مستحق خلال ${diff} يوم (${formatCurrency(s.amount)})` });
            });
            const bal = getBalance();
            if (bal < 0) alerts.push({ type: 'danger', icon: 'fa-exclamation-triangle', message: `❗ رصيدك سالب (${formatCurrency(bal)})` });
            return alerts;
        }

        // ===== Dashboard =====
        function updateDashboard() {
            const transactions = DB.get('transactions');
            document.getElementById('totalIncome').textContent = formatCurrency(getTotalIncome());
            document.getElementById('totalExpense').textContent = formatCurrency(getTotalExpense());
            document.getElementById('currentBalance').textContent = formatCurrency(getBalance());
            document.getElementById('transactionCount').textContent = transactions.length;

            const alerts = checkAlerts();
            const c = document.getElementById('alertsContainer');
            if (alerts.length === 0) c.innerHTML = `<div class="alert-item success"><i class="fas fa-check-circle"></i> ✅ الوضع المالي مستقر، لا توجد تنبيهات</div>`;
            else c.innerHTML = alerts.map(a => `<div class="alert-item ${a.type}"><i class="fas ${a.icon}"></i> ${a.message}</div>`).join('');

            updateExpenseChart(transactions);
            updateCashFlowChart(transactions);
            updateRecentTransactions(transactions);
        }

        function updateExpenseChart(transactions) {
            const expenses = transactions.filter(t => t.type === 'expense');
            const cats = DB.getCategories();
            const data = cats.map(c => ({ category: c, total: expenses.filter(t => t.category === c).reduce((s, t) => s + t.amount, 0) })).filter(d => d.total > 0);
            const ctx = document.getElementById('expenseChart').getContext('2d');
            if (window.expenseChartInstance) window.expenseChartInstance.destroy();
            if (data.length === 0) { window.expenseChartInstance = new Chart(ctx, { type: 'doughnut', data: { labels: ['لا توجد مصروفات'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] }, options: { responsive: true, plugins: { legend: { position: 'bottom' } } } }); return; }
            const colors = ['#22c55e','#3b82f6','#f59e0b','#ef4444','#8b5cf6','#ec4899','#14b8a6','#f97316'];
            window.expenseChartInstance = new Chart(ctx, { type: 'doughnut', data: { labels: data.map(d => d.category), datasets: [{ data: data.map(d => d.total), backgroundColor: colors.slice(0, data.length), borderWidth: 2 }] }, options: { responsive: true, plugins: { legend: { position: 'bottom', labels: { font: { family: 'Tajawal' }, padding: 10 } } } } });
        }

        function updateCashFlowChart(transactions) {
            const monthly = {};
            transactions.forEach(t => { const d = new Date(t.date); const key = d.getFullYear() + '-' + String(d.getMonth()+1).padStart(2,'0'); if (!monthly[key]) monthly[key] = { income: 0, expense: 0 }; if (t.type === 'income') monthly[key].income += t.amount; else monthly[key].expense += t.amount; });
            const keys = Object.keys(monthly).sort();
            const ctx = document.getElementById('cashFlowChart').getContext('2d');
            if (window.cashFlowChartInstance) window.cashFlowChartInstance.destroy();
            if (keys.length === 0) { window.cashFlowChartInstance = new Chart(ctx, { type: 'line', data: { labels: ['لا توجد بيانات'], datasets: [{ label: 'الدخل', data: [0], borderColor: '#22c55e' }, { label: 'المصروفات', data: [0], borderColor: '#ef4444' }] }, options: { responsive: true, plugins: { legend: { position: 'bottom' } } } }); return; }
            window.cashFlowChartInstance = new Chart(ctx, { type: 'line', data: { labels: keys.map(k => k.split('-')[1] + '/' + k.split('-')[0]), datasets: [{ label: 'الدخل', data: keys.map(k => monthly[k].income), borderColor: '#22c55e', backgroundColor: 'rgba(34,197,94,0.1)', fill: true, tension: 0.4 }, { label: 'المصروفات', data: keys.map(k => monthly[k].expense), borderColor: '#ef4444', backgroundColor: 'rgba(239,68,68,0.1)', fill: true, tension: 0.4 }] }, options: { responsive: true, plugins: { legend: { position: 'bottom', labels: { font: { family: 'Tajawal' } } } }, scales: { y: { beginAtZero: true } } } });
        }

        function updateRecentTransactions(transactions) {
            const recent = transactions.slice(-5).reverse();
            const c = document.getElementById('recentTransactions');
            if (recent.length === 0) { c.innerHTML = '<p style="color:var(--text-light)">لا توجد عمليات مسجلة</p>'; return; }
            c.innerHTML = `<div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th></tr></thead><tbody>${recent.map(t => `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td></tr>`).join('')}</tbody></table></div>`;
        }

        // ===== Transactions =====
        function updateCategorySelects() {
            const cats = DB.getCategories();
            ['txCategory', 'filterCategory', 'budgetCategory'].forEach(id => {
                const el = document.getElementById(id);
                if (!el) return;
                const val = el.value;
                el.innerHTML = '';
                cats.forEach(c => { const o = document.createElement('option'); o.value = c; o.textContent = c; el.appendChild(o); });
                if (id === 'filterCategory') { const o = document.createElement('option'); o.value = 'all'; o.textContent = 'كل التصنيفات'; el.prepend(o); }
                if (val && cats.includes(val)) el.value = val;
            });
        }

        function updateAccountSelects() {
            const accs = DB.getAccounts();
            const el = document.getElementById('txAccount');
            if (!el) return;
            const val = el.value;
            el.innerHTML = '';
            accs.forEach(a => { const o = document.createElement('option'); o.value = a; o.textContent = a; el.appendChild(o); });
            if (val && accs.includes(val)) el.value = val;
        }

        function loadTransactions() {
            const transactions = DB.get('transactions');
            const search = document.getElementById('searchTransactions').value.toLowerCase();
            const catFilter = document.getElementById('filterCategory').value;
            const typeFilter = document.getElementById('filterType').value;
            let filtered = transactions.filter(t => {
                if (search && !t.category.includes(search) && !(t.notes||'').includes(search) && !(t.account||'').includes(search)) return false;
                if (catFilter !== 'all' && t.category !== catFilter) return false;
                if (typeFilter !== 'all' && t.type !== typeFilter) return false;
                return true;
            }).sort((a,b) => b.id - a.id);
            const tbody = document.getElementById('transactionsTableBody');
            if (filtered.length === 0) { tbody.innerHTML = `<tr><td colspan="7" style="text-align:center;padding:30px;color:var(--text-light)"><i class="fas fa-inbox" style="font-size:24px;display:block;margin-bottom:10px"></i>لا توجد عمليات</td></tr>`; return; }
            tbody.innerHTML = filtered.map(t => `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td><td>${t.account||'نقدي'}</td><td>${t.notes||''}</td><td class="transaction-actions"><button class="btn-edit" onclick="editTransaction(${t.id})"><i class="fas fa-edit"></i></button><button class="btn-delete" onclick="deleteTransaction(${t.id})"><i class="fas fa-trash"></i></button></td></tr>`).join('');
        }

        function deleteTransaction(id) { if (confirm('هل أنت متأكد من حذف هذه العملية؟')) { DB.deleteTransaction(id); loadTransactions(); updateDashboard(); } }
        function editTransaction(id) {
            const t = DB.get('transactions').find(x => x.id === id);
            if (!t) return;
            const amt = prompt('المبلغ:', t.amount); if (amt === null) return;
            const cat = prompt('التصنيف:', t.category); if (cat === null) return;
            const note = prompt('الملاحظات:', t.notes || ''); if (note === null) return;
            DB.updateTransaction(id, { amount: parseFloat(amt), category: cat, notes: note });
            loadTransactions(); updateDashboard();
        }

        // ===== Budgets =====
        function loadBudgets() {
            const budgets = DB.get('budgets');
            const transactions = DB.get('transactions');
            const now = new Date();
            const month = now.getMonth(), year = now.getFullYear();
            const monthlyExpenses = transactions.filter(t => { const d = new Date(t.date); return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year; });
            const c = document.getElementById('budgetsList');
            if (budgets.length === 0) { c.innerHTML = `<div class="card" style="padding:30px;text-align:center;color:var(--text-light)"><i class="fas fa-bullseye" style="font-size:40px;display:block;margin-bottom:10px"></i>لا توجد ميزانيات مسجلة<br><small>أضف ميزانية لبدء التتبع</small></div>`; return; }
            c.innerHTML = budgets.map(b => {
                const spent = monthlyExpenses.filter(t => t.category === b.category).reduce((s, t) => s + t.amount, 0);
                const p = Math.min((spent / b.limit) * 100, 100);
                const cls = p >= 90 ? 'danger' : p >= 70 ? 'warning' : 'success';
                return `<div class="budget-card"><div class="budget-info"><h4>${b.category}</h4><p>${formatCurrency(spent)} من ${formatCurrency(b.limit)} (${p.toFixed(0)}%) - المتبقي: ${formatCurrency(Math.max(b.limit - spent, 0))}</p><div class="progress-bar"><div class="progress-fill ${cls}" style="width:${p}%"></div></div></div><button class="btn-danger" onclick="deleteBudget(${b.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button></div>`;
            }).join('');
        }

        function deleteBudget(id) { if (confirm('هل أنت متأكد من حذف هذه الميزانية؟')) { DB.deleteBudget(id); loadBudgets(); } }

        // ===== Goals =====
        function loadGoals() {
            const goals = DB.get('goals');
            const c = document.getElementById('goalsList');
            if (goals.length === 0) { c.innerHTML = `<div class="card" style="padding:30px;text-align:center;color:var(--text-light)"><i class="fas fa-trophy" style="font-size:40px;display:block;margin-bottom:10px"></i>لا توجد أهداف مسجلة<br><small>حدد هدفاً مالياً لبدء التوفير</small></div>`; return; }
            c.innerHTML = goals.map(g => {
                const p = Math.min((g.current / g.target) * 100, 100);
                const cls = p >= 90 ? 'success' : p >= 50 ? 'warning' : 'danger';
                return `<div class="goal-card"><div class="goal-info"><h4>🏆 ${g.name}</h4><p>${formatCurrency(g.current)} من ${formatCurrency(g.target)} (${p.toFixed(0)}%) - المتبقي: ${formatCurrency(g.target - g.current)}</p><div class="progress-bar"><div class="progress-fill ${cls}" style="width:${p}%"></div></div></div><div style="display:flex;gap:8px"><button class="btn-secondary" onclick="updateGoal(${g.id})" style="padding:6px 12px"><i class="fas fa-plus"></i> إضافة</button><button class="btn-danger" onclick="deleteGoal(${g.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button></div></div>`;
            }).join('');
        }

        function updateGoal(id) {
            const g = DB.get('goals').find(x => x.id === id);
            if (!g) return;
            const amt = prompt(`المبلغ المدخر حالياً للهدف "${g.name}":`, g.current);
            if (amt === null) return;
            const val = parseFloat(amt);
            if (isNaN(val) || val < 0 || val > g.target) { alert('الرجاء إدخال مبلغ صحيح بين 0 والمبلغ المستهدف'); return; }
            DB.updateGoal(id, val); loadGoals(); updateDashboard();
        }
        function deleteGoal(id) { if (confirm('هل أنت متأكد من حذف هذا الهدف؟')) { DB.deleteGoal(id); loadGoals(); } }

        // ===== Subscriptions =====
        function loadSubscriptions() {
            const subs = DB.get('subscriptions').sort((a,b) => new Date(a.dueDate) - new Date(b.dueDate));
            const c = document.getElementById('subscriptionsList');
            if (subs.length === 0) { c.innerHTML = `<div class="card" style="padding:30px;text-align:center;color:var(--text-light)"><i class="fas fa-clock" style="font-size:40px;display:block;margin-bottom:10px"></i>لا توجد اشتراكات مسجلة<br><small>أضف اشتراكاتك لتتبع الفواتير</small></div>`; return; }
            const now = new Date();
            c.innerHTML = subs.map(s => {
                const due = new Date(s.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                const color = s.status === 'متأخر' || diff < 0 ? 'danger' : s.status === 'غير مدفوع' || diff <= 3 ? 'warning' : 'success';
                return `<div class="subscription-card" style="border-color:var(--${color})"><div class="sub-info"><h4>${s.name}</h4><p>${formatCurrency(s.amount)} - تاريخ الاستحقاق: ${getDateString(s.dueDate)} ${diff >= 0 ? `(باقي ${diff} يوم)` : `(متأخر ${Math.abs(diff)} يوم)`}</p><p>${getStatusBadge(s.status)}</p></div><div style="display:flex;gap:8px"><button class="btn-secondary" onclick="updateSubscriptionStatus(${s.id})" style="padding:6px 12px"><i class="fas fa-check"></i> تغيير الحالة</button><button class="btn-danger" onclick="deleteSubscription(${s.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button></div></div>`;
            }).join('');
        }

        function updateSubscriptionStatus(id) {
            const subs = DB.get('subscriptions');
            const s = subs.find(x => x.id === id);
            if (!s) return;
            const statuses = ['مدفوع', 'غير مدفوع', 'متأخر'];
            s.status = statuses[(statuses.indexOf(s.status) + 1) % statuses.length];
            DB.set('subscriptions', subs);
            loadSubscriptions(); updateDashboard();
        }
        function deleteSubscription(id) { if (confirm('هل أنت متأكد من حذف هذا الاشتراك؟')) { DB.deleteSubscription(id); loadSubscriptions(); } }

        // ===== Reports =====
        function generateReport() {
            const year = parseInt(document.getElementById('reportYear').value);
            const month = document.getElementById('reportMonth').value;
            const transactions = DB.get('transactions');
            let filtered = transactions.filter(t => {
                const d = new Date(t.date);
                if (d.getFullYear() !== year) return false;
                if (month !== 'all' && d.getMonth() + 1 !== parseInt(month)) return false;
                return true;
            });
            const income = filtered.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
            const expense = filtered.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
            const profit = income - expense;
            document.getElementById('reportIncome').textContent = formatCurrency(income);
            document.getElementById('reportExpense').textContent = formatCurrency(expense);
            document.getElementById('reportProfit').textContent = formatCurrency(profit);
            document.getElementById('reportProfit').style.color = profit >= 0 ? '#22c55e' : '#ef4444';
            const summary = filtered.filter(t => t.type === 'expense').reduce((acc, t) => { acc[t.category] = (acc[t.category] || 0) + t.amount; return acc; }, {});
            let html = `<div class="report-summary"><div class="report-stat"><h4>إجمالي الدخل</h4><p style="color:#22c55e">${formatCurrency(income)}</p></div><div class="report-stat"><h4>إجمالي المصروفات</h4><p style="color:#ef4444">${formatCurrency(expense)}</p></div><div class="report-stat"><h4>صافي الربح</h4><p style="color:${profit>=0?'#22c55e':'#ef4444'}">${formatCurrency(profit)}</p></div><div class="report-stat"><h4>عدد العمليات</h4><p>${filtered.length}</p></div></div>`;
            html += `<div style="margin-top:20px"><h4>تفاصيل المصروفات حسب التصنيف</h4><div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:10px;margin-top:10px">`;
            if (Object.keys(summary).length === 0) html += '<p style="color:var(--text-light)">لا توجد بيانات</p>';
            else html += Object.entries(summary).map(([cat, amt]) => `<div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center"><strong>${cat}</strong><p style="color:var(--text-light)">${formatCurrency(amt)}</p></div>`).join('');
            html += `</div></div>`;
            if (filtered.length > 0) {
                html += `<div style="margin-top:20px"><h4>العمليات</h4><div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>الحساب</th></tr></thead><tbody>`;
                html += filtered.sort((a,b) => new Date(b.date) - new Date(a.date)).map(t => `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td><td>${t.account||'نقدي'}</td></tr>`).join('');
                html += `</tbody></table></div></div>`;
            }
            document.getElementById('reportContent').innerHTML = html;
        }

        function exportExcel() {
            const data = DB.get('transactions').map(t => ({ 'التاريخ': getDateString(t.date), 'النوع': getTypeLabel(t.type), 'المبلغ': t.amount, 'التصنيف': t.category, 'الحساب': t.account||'نقدي', 'الملاحظات': t.notes||'' }));
            if (data.length === 0) { alert('لا توجد بيانات لتصديرها'); return; }
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(data), 'العمليات');
            XLSX.writeFile(wb, `flosy_${new Date().toISOString().split('T')[0]}.xlsx`);
        }

        function exportPDF() {
            const el = document.getElementById('reportContent');
            if (!el || el.innerHTML.trim() === '') { alert('الرجاء إنشاء تقرير أولاً'); return; }
            html2pdf().set({ margin: 10, filename: `flosy_report_${new Date().toISOString().split('T')[0]}.pdf`, image: { type: 'jpeg', quality: 0.98 }, html2canvas: { scale: 2 }, jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' } }).from(el).save();
        }

        // ===== Settings =====
        function loadSettings() {
            loadCategories();
            loadAccounts();
        }

        function loadCategories() {
            const cats = DB.getCategories();
            document.getElementById('categoriesList').innerHTML = cats.map(c => `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${c}</span><button class="btn-danger" onclick="deleteCategory('${c}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`).join('');
        }

        function loadAccounts() {
            const accs = DB.getAccounts();
            document.getElementById('accountsList').innerHTML = accs.map(a => `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${a}</span><button class="btn-danger" onclick="deleteAccount('${a}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`).join('');
        }

        function addCategory() {
            const input = document.getElementById('newCategory');
            const val = input.value.trim();
            if (!val) { alert('الرجاء إدخال اسم التصنيف'); return; }
            if (DB.addCategory(val)) { input.value = ''; loadCategories(); updateCategorySelects(); alert('✅ تم إضافة التصنيف بنجاح'); } else alert('❌ هذا التصنيف موجود بالفعل');
        }

        function deleteCategory(cat) { if (confirm(`هل أنت متأكد من حذف التصنيف "${cat}"؟`)) { DB.deleteCategory(cat); loadCategories(); updateCategorySelects(); } }

        function addAccount() {
            const input = document.getElementById('newAccount');
            const val = input.value.trim();
            if (!val) { alert('الرجاء إدخال اسم الحساب'); return; }
            if (DB.addAccount(val)) { input.value = ''; loadAccounts(); updateAccountSelects(); alert('✅ تم إضافة الحساب بنجاح'); } else alert('❌ هذا الحساب موجود بالفعل');
        }

        function deleteAccount(acc) { if (confirm(`هل أنت متأكد من حذف الحساب "${acc}"؟`)) { DB.deleteAccount(acc); loadAccounts(); updateAccountSelects(); } }

        function backupData() {
            const data = DB.backup();
            const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `flosy_backup_${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(url);
            alert('✅ تم إنشاء نسخة احتياطية');
        }

        function restoreData(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const data = JSON.parse(e.target.result);
                    if (!data.transactions) { alert('❌ ملف غير صالح'); return; }
                    if (confirm('سيتم استبدال كل البيانات الحالية. هل أنت متأكد؟')) { DB.restore(data); alert('✅ تم استعادة البيانات بنجاح'); location.reload(); }
                } catch(err) { alert('❌ خطأ في قراءة الملف'); }
            };
            reader.readAsText(file);
            event.target.value = '';
        }

        function clearAllData() {
            if (confirm('⚠️ تحذير: سيتم حذف كل البيانات نهائياً. هل أنت متأكد؟')) {
                if (confirm('تأكيد نهائي: هل تريد حذف كل شيء؟')) { DB.clearAll(); alert('✅ تم حذف كل البيانات'); location.reload(); }
            }
        }

        // ===== App Initialization =====
        document.addEventListener('DOMContentLoaded', function() {
            // Date
            const now = new Date();
            document.getElementById('currentDate').textContent = now.toLocaleDateString('ar-EG', { year: 'numeric', month: 'long', day: 'numeric', weekday: 'long' });

            // Dark Mode
            const settings = DB.getSettings();
            if (settings.darkMode) {
                document.body.classList.add('dark-mode');
                document.getElementById('darkModeToggle').checked = true;
                document.getElementById('settingsDarkMode').checked = true;
            }

            document.getElementById('darkModeToggle').addEventListener('change', function() {
                document.body.classList.toggle('dark-mode', this.checked);
                document.getElementById('settingsDarkMode').checked = this.checked;
                const s = DB.getSettings();
                s.darkMode = this.checked;
                DB.saveSettings(s);
            });

            document.getElementById('settingsDarkMode').addEventListener('change', function() {
                document.body.classList.toggle('dark-mode', this.checked);
                document.getElementById('darkModeToggle').checked = this.checked;
                const s = DB.getSettings();
                s.darkMode = this.checked;
                DB.saveSettings(s);
            });

            // Menu Toggle
            document.getElementById('menuToggle').addEventListener('click', function() {
                document.getElementById('sidebar').classList.toggle('open');
            });

            // Navigation
            document.querySelectorAll('.sidebar-menu a').forEach(link => {
                link.addEventListener('click', function(e) {
                    e.preventDefault();
                    const pageId = this.dataset.page;
                    document.querySelectorAll('.sidebar-menu a').forEach(a => a.classList.remove('active'));
                    this.classList.add('active');
                    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
                    document.getElementById(pageId).classList.add('active');
                    document.getElementById('sidebar').classList.remove('open');
                    if (pageId === 'dashboard') updateDashboard();
                    else if (pageId === 'transactions') loadTransactions();
                    else if (pageId === 'budgets') loadBudgets();
                    else if (pageId === 'goals') loadGoals();
                    else if (pageId === 'subscriptions') loadSubscriptions();
                    else if (pageId === 'settings') loadSettings();
                });
            });

            // Transaction Form
            document.getElementById('txDate').value = new Date().toISOString().split('T')[0];
            updateCategorySelects();
            updateAccountSelects();

            document.getElementById('transactionForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const type = document.getElementById('txType').value;
                const amount = parseFloat(document.getElementById('txAmount').value);
                const category = document.getElementById('txCategory').value;
                const date = document.getElementById('txDate').value;
                const account = document.getElementById('txAccount').value;
                const notes = document.getElementById('txNotes').value;
                if (!amount || amount <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (!date) { alert('الرجاء تحديد التاريخ'); return; }
                DB.addTransaction({ type, amount, category, date, account, notes: notes || '' });
                loadTransactions();
                updateDashboard();
                document.getElementById('txAmount').value = '';
                document.getElementById('txNotes').value = '';
                document.getElementById('txDate').value = new Date().toISOString().split('T')[0];
                alert('✅ تم إضافة العملية بنجاح');
            });

            // Budget Form
            document.getElementById('budgetForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const category = document.getElementById('budgetCategory').value;
                const limit = parseFloat(document.getElementById('budgetLimit').value);
                if (!limit || limit <= 0) { alert('الرجاء إدخال حد صحيح'); return; }
                if (DB.get('budgets').some(b => b.category === category)) { alert('يوجد بالفعل ميزانية لهذا التصنيف'); return; }
                DB.addBudget({ category, limit });
                loadBudgets();
                document.getElementById('budgetLimit').value = '';
                alert('✅ تم إضافة الميزانية بنجاح');
            });

            // Goal Form
            document.getElementById('goalForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('goalName').value.trim();
                const target = parseFloat(document.getElementById('goalTarget').value);
                const current = parseFloat(document.getElementById('goalCurrent').value);
                if (!name) { alert('الرجاء إدخال اسم الهدف'); return; }
                if (!target || target <= 0) { alert('الرجاء إدخال مبلغ مستهدف صحيح'); return; }
                if (current < 0 || current > target) { alert('المبلغ المدخر يجب أن يكون بين 0 والمبلغ المستهدف'); return; }
                DB.addGoal({ name, target, current });
                loadGoals();
                document.getElementById('goalName').value = '';
                document.getElementById('goalTarget').value = '';
                document.getElementById('goalCurrent').value = '';
                alert('✅ تم إضافة الهدف بنجاح');
            });

            // Subscription Form
            document.getElementById('subscriptionForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('subName').value.trim();
                const amount = parseFloat(document.getElementById('subAmount').value);
                const dueDate = document.getElementById('subDueDate').value;
                const status = document.getElementById('subStatus').value;
                if (!name) { alert('الرجاء إدخال اسم الاشتراك'); return; }
                if (!amount || amount <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (!dueDate) { alert('الرجاء تحديد تاريخ الاستحقاق'); return; }
                DB.addSubscription({ name, amount, dueDate, status });
                loadSubscriptions();
                document.getElementById('subName').value = '';
                document.getElementById('subAmount').value = '';
                document.getElementById('subDueDate').value = '';
                alert('✅ تم إضافة الاشتراك بنجاح');
            });

            // Search & Filters
            document.getElementById('searchTransactions').addEventListener('input', loadTransactions);
            document.getElementById('filterCategory').addEventListener('change', loadTransactions);
            document.getElementById('filterType').addEventListener('change', loadTransactions);

            // Report Year
            const yrSelect = document.getElementById('reportYear');
            const cy = new Date().getFullYear();
            for (let y = cy; y >= cy - 5; y--) {
                const o = document.createElement('option');
                o.value = y;
                o.textContent = y;
                yrSelect.appendChild(o);
            }
            yrSelect.value = cy;

            // Load initial data
            updateDashboard();
            loadTransactions();
            loadBudgets();
            loadGoals();
            loadSubscriptions();
            loadSettings();
        });
    </script>
</body>
</html>
