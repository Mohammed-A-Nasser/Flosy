<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>فلوسي برو | Flosy Pro - النظام المالي المتكامل</title>
    
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap" rel="stylesheet">
    
    <!-- ===== Firebase SDK ===== -->
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
    
    <style>
        /* ===== المتغيرات ===== */
        :root {
            --primary: #1f2937;
            --secondary: #2563eb;
            --success: #22c55e;
            --danger: #ef4444;
            --warning: #f59e0b;
            --info: #3b82f6;
            --purple: #8b5cf6;
            --pink: #ec4899;
            --bg: #f1f5f9;
            --card-bg: #ffffff;
            --text: #1e293b;
            --text-light: #64748b;
            --border: #e2e8f0;
            --shadow: 0 4px 6px -1px rgba(0,0,0,0.1);
            --radius: 16px;
            --transition: all 0.3s ease;
        }

        .dark-mode {
            --bg: #0f172a;
            --card-bg: #1e293b;
            --text: #f1f5f9;
            --text-light: #94a3b8;
            --border: #334155;
            --shadow: 0 4px 6px -1px rgba(0,0,0,0.5);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Tajawal', sans-serif;
            background: var(--bg);
            color: var(--text);
            transition: var(--transition);
            direction: rtl;
        }

        /* ===== شاشات الدخول والقفل ===== */
        #loginScreen {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: var(--primary);
            z-index: 9999;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .login-container {
            background: var(--card-bg);
            padding: 40px;
            border-radius: var(--radius);
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            width: 420px;
            max-width: 95%;
        }
        .login-header { text-align: center; margin-bottom: 30px; }
        .login-header h1 { font-size: 32px; color: var(--secondary); }
        .login-header h1 i { color: var(--secondary); }
        .login-header .subtitle { color: var(--text-light); font-size: 16px; }
        .login-tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .login-tab {
            flex: 1; padding: 10px; border: none; border-radius: 10px;
            background: var(--bg); color: var(--text); cursor: pointer;
            transition: var(--transition); font-family: inherit;
        }
        .login-tab.active { background: var(--secondary); color: white; }
        .login-panel { display: none; }
        .login-panel.active { display: block; }
        .login-error { color: var(--danger); font-size: 14px; min-height: 24px; text-align: center; }

        #quickLockScreen {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.85);
            z-index: 9998;
            display: none;
            align-items: center;
            justify-content: center;
            backdrop-filter: blur(10px);
        }
        .quick-lock-container {
            background: var(--card-bg);
            padding: 40px;
            border-radius: var(--radius);
            box-shadow: 0 20px 60px rgba(0,0,0,0.5);
            width: 360px;
            max-width: 95%;
            text-align: center;
        }
        .quick-lock-container h2 { color: var(--secondary); margin-bottom: 10px; }
        .quick-lock-container p { color: var(--text-light); margin-bottom: 20px; }

        .lock-pin-display {
            display: flex; gap: 12px; justify-content: center; margin: 15px 0;
        }
        .lock-pin-display span {
            width: 16px; height: 16px; border-radius: 50%;
            border: 2px solid var(--border); display: inline-block;
            transition: var(--transition);
        }
        .lock-pin-display span.filled { background: var(--secondary); border-color: var(--secondary); }

        .lock-pin-grid {
            display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px;
            max-width: 250px; margin: 0 auto 15px;
        }
        .lock-pin-grid button {
            padding: 14px; border: 2px solid var(--border); border-radius: 12px;
            background: var(--bg); color: var(--text); font-size: 20px;
            cursor: pointer; transition: var(--transition); font-family: inherit;
        }
        .lock-pin-grid button:hover { background: var(--secondary); color: white; border-color: var(--secondary); }
        .lock-pin-grid .pin-clear {
            background: rgba(239,68,68,0.1);
            border-color: var(--danger); color: var(--danger);
        }
        .lock-pin-grid .pin-clear:hover { background: var(--danger); color: white; }

        /* ===== الشريط الجانبي - مع التمرير ===== */
        .sidebar {
            position: fixed;
            right: 0;
            top: 0;
            width: 280px;
            height: 100%;
            background: var(--primary);
            padding: 20px;
            color: white;
            overflow-y: auto;
            z-index: 1000;
            transition: var(--transition);
            box-shadow: 2px 0 10px rgba(0,0,0,0.1);
            display: flex;
            flex-direction: column;
        }

        .sidebar-header {
            text-align: center;
            padding-bottom: 20px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            flex-shrink: 0;
        }
        .sidebar-header h2 { font-size: 28px; margin-bottom: 5px; }
        .sidebar-header h2 i { color: var(--secondary); }
        .subtitle { font-size: 14px; opacity: 0.7; }

        .sidebar-menu {
            flex: 1;
            overflow-y: auto;
            margin: 20px 0;
            padding-bottom: 10px;
        }
        .sidebar-menu a {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px 16px;
            color: rgba(255,255,255,0.7);
            text-decoration: none;
            border-radius: 12px;
            transition: var(--transition);
            margin: 4px 0;
            font-size: 16px;
            cursor: pointer;
        }
        .sidebar-menu a:hover, .sidebar-menu a.active {
            background: rgba(255,255,255,0.1);
            color: white;
        }
        .sidebar-menu a.active { background: var(--secondary); color: white; }
        .sidebar-menu a i { width: 24px; text-align: center; }

        .sidebar-footer {
            flex-shrink: 0;
            padding-top: 20px;
            border-top: 1px solid rgba(255,255,255,0.1);
            margin-top: auto;
        }
        .lock-btn {
            background: rgba(239,68,68,0.2);
            color: var(--danger);
            border: 1px solid var(--danger);
            padding: 8px 16px;
            border-radius: 10px;
            cursor: pointer;
            width: 100%;
            font-size: 14px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
            transition: var(--transition);
            font-family: inherit;
            margin-top: 5px;
        }
        .lock-btn:hover { background: var(--danger); color: white; }

        .dark-mode-toggle {
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 12px;
            margin-top: 12px;
        }

        /* ===== مفتاح التبديل ===== */
        .switch {
            position: relative;
            display: inline-block;
            width: 50px;
            height: 26px;
        }
        .switch input { opacity: 0; width: 0; height: 0; }
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background: #ccc;
            transition: .4s;
            border-radius: 34px;
        }
        .slider:before {
            position: absolute;
            content: "";
            height: 20px;
            width: 20px;
            right: 3px;
            bottom: 3px;
            background: white;
            transition: .4s;
            border-radius: 50%;
        }
        input:checked + .slider { background: var(--secondary); }
        input:checked + .slider:before { transform: translateX(-24px); }

        .menu-toggle {
            display: none;
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1100;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 12px;
            padding: 10px 14px;
            font-size: 20px;
            cursor: pointer;
            box-shadow: var(--shadow);
        }

        .main-content {
            margin-right: 280px;
            padding: 30px;
            min-height: 100vh;
            transition: var(--transition);
        }

        /* ===== الصفحات ===== */
        .page { display: none; animation: fadeIn 0.4s ease; }
        .page.active { display: block; }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .page-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            flex-wrap: wrap;
            gap: 10px;
        }
        .page-header h1 { font-size: 28px; font-weight: 700; }
        .page-header h1 i { color: var(--secondary); }
        .date-display { color: var(--text-light); font-size: 16px; }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        .stat-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            display: flex;
            align-items: center;
            gap: 15px;
            transition: var(--transition);
            border-right: 4px solid var(--secondary);
        }
        .stat-card:hover { transform: translateY(-4px); box-shadow: 0 10px 20px rgba(0,0,0,0.1); }
        .stat-icon {
            width: 50px; height: 50px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            color: white;
        }
        .income-card .stat-icon { background: var(--success); }
        .expense-card .stat-icon { background: var(--danger); }
        .balance-card .stat-icon { background: var(--secondary); }
        .transactions-card .stat-icon { background: var(--warning); }
        .stat-info h3 { font-size: 14px; color: var(--text-light); font-weight: 400; }
        .stat-value { font-size: 24px; font-weight: 700; margin-top: 4px; }

        .charts-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        .chart-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
        }
        .chart-card h3 { margin-bottom: 15px; font-size: 18px; }
        .chart-card h3 i { color: var(--secondary); }
        .chart-card canvas { max-height: 300px; max-width: 100%; }

        .alerts-section {
            background: var(--card-bg);
            padding: 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            margin-bottom: 30px;
        }
        .alerts-section h3 { margin-bottom: 15px; }
        .alerts-section h3 i { color: var(--warning); }

        .alert-item {
            padding: 12px 16px;
            border-radius: 10px;
            margin: 8px 0;
            display: flex;
            align-items: center;
            gap: 10px;
            background: #fef3c7;
            color: #92400e;
            border-right: 4px solid var(--warning);
        }
        .alert-item.success { background: #d1fae5; color: #065f46; border-color: var(--success); }
        .alert-item.danger { background: #fee2e2; color: #991b1b; border-color: var(--danger); }
        .alert-item.info { background: #dbeafe; color: #1e40af; border-color: var(--info); }
        .alert-item i { font-size: 18px; }

        .form-card {
            background: var(--card-bg);
            padding: 24px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            margin-bottom: 30px;
        }
        .form-card h3 { margin-bottom: 20px; }
        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
        }
        .form-group { display: flex; flex-direction: column; gap: 6px; }
        .form-group label { font-weight: 500; font-size: 14px; color: var(--text-light); }
        .form-group label i { margin-left: 6px; color: var(--secondary); }
        .form-group input, .form-group select, .form-group textarea {
            padding: 10px 12px;
            border: 2px solid var(--border);
            border-radius: 10px;
            background: var(--bg);
            color: var(--text);
            font-family: inherit;
            font-size: 14px;
            transition: var(--transition);
        }
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
            outline: none;
            border-color: var(--secondary);
            box-shadow: 0 0 0 3px rgba(37,99,235,0.1);
        }
        .form-group.full-width { grid-column: 1 / -1; }

        .btn-primary {
            background: var(--secondary);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 10px;
            font-size: 16px;
            font-weight: 500;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-family: inherit;
        }
        .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(37,99,235,0.3); }

        .btn-secondary {
            background: var(--bg);
            color: var(--text);
            border: 2px solid var(--border);
            padding: 10px 20px;
            border-radius: 10px;
            font-size: 14px;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-family: inherit;
        }
        .btn-secondary:hover { background: var(--border); }

        .btn-success {
            background: var(--success);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 10px;
            font-size: 14px;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-family: inherit;
        }
        .btn-success:hover { opacity: 0.8; }

        .btn-danger {
            background: var(--danger);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 10px;
            font-size: 14px;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-family: inherit;
        }
        .btn-danger:hover { opacity: 0.8; }

        .btn-warning {
            background: var(--warning);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 10px;
            font-size: 14px;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-family: inherit;
        }
        .btn-warning:hover { opacity: 0.8; }

        .table-responsive {
            overflow-x: auto;
            background: var(--card-bg);
            border-radius: var(--radius);
            box-shadow: var(--shadow);
        }
        table { width: 100%; border-collapse: collapse; font-size: 14px; }
        thead { background: var(--bg); }
        th { padding: 12px 16px; text-align: right; font-weight: 600; color: var(--text-light); border-bottom: 2px solid var(--border); }
        td { padding: 12px 16px; border-bottom: 1px solid var(--border); }
        tr:hover { background: var(--bg); }

        .table-controls {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-bottom: 15px;
        }
        .table-controls input, .table-controls select {
            padding: 8px 12px;
            border: 2px solid var(--border);
            border-radius: 8px;
            background: var(--bg);
            color: var(--text);
            font-family: inherit;
        }

        .progress-bar {
            width: 100%;
            height: 8px;
            background: var(--bg);
            border-radius: 4px;
            overflow: hidden;
            margin: 8px 0;
        }
        .progress-bar .progress-fill {
            height: 100%;
            border-radius: 4px;
            transition: width 0.6s ease;
        }
        .progress-fill.success { background: var(--success); }
        .progress-fill.warning { background: var(--warning); }
        .progress-fill.danger { background: var(--danger); }

        .goal-card, .subscription-card, .budget-card, .obligation-card, .family-card, .debt-card {
            background: var(--card-bg);
            padding: 16px 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            margin: 10px 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 10px;
            border-right: 4px solid var(--secondary);
        }
        .goal-card .info, .subscription-card .info, .budget-card .info,
        .obligation-card .info, .family-card .info, .debt-card .info {
            flex: 1;
            min-width: 150px;
        }
        .goal-card .info h4, .subscription-card .info h4, .budget-card .info h4,
        .obligation-card .info h4, .family-card .info h4, .debt-card .info h4 {
            font-size: 16px;
            margin-bottom: 4px;
        }
        .goal-card .info p, .subscription-card .info p, .budget-card .info p,
        .obligation-card .info p, .family-card .info p, .debt-card .info p {
            font-size: 14px;
            color: var(--text-light);
        }

        .report-controls {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
            align-items: end;
            background: var(--card-bg);
            padding: 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            margin-bottom: 30px;
        }
        .report-summary {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 15px;
            margin: 20px 0;
        }
        .report-stat {
            background: var(--bg);
            padding: 15px;
            border-radius: var(--radius);
            text-align: center;
        }
        .report-stat h4 { font-size: 14px; color: var(--text-light); margin-bottom: 8px; }
        .report-stat p { font-size: 24px; font-weight: 700; }
        .report-actions { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 20px; }

        .settings-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }
        .settings-card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
        }
        .settings-card h3 { margin-bottom: 15px; font-size: 18px; }
        .settings-card h3 i { color: var(--secondary); }
        .settings-card button { margin: 6px 0; width: 100%; justify-content: center; }
        .setting-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 0;
            border-bottom: 1px solid var(--border);
        }

        .add-category, .add-account, .add-family {
            display: flex;
            gap: 10px;
            margin-top: 10px;
            flex-wrap: wrap;
        }
        .add-category input, .add-account input, .add-family input {
            flex: 1;
            padding: 8px 12px;
            border: 2px solid var(--border);
            border-radius: 8px;
            background: var(--bg);
            color: var(--text);
            min-width: 120px;
        }

        .amount-income { color: var(--success); font-weight: 600; }
        .amount-expense { color: var(--danger); font-weight: 600; }

        .transaction-actions button {
            padding: 4px 8px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            margin: 0 2px;
        }
        .btn-edit { background: var(--warning); color: white; }
        .btn-delete { background: var(--danger); color: white; }

        .filter-badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
        }
        .filter-badge.income { background: #d1fae5; color: #065f46; }
        .filter-badge.expense { background: #fee2e2; color: #991b1b; }

        .badge {
            display: inline-block;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 500;
        }
        .badge-success { background: #d1fae5; color: #065f46; }
        .badge-warning { background: #fef3c7; color: #92400e; }
        .badge-danger { background: #fee2e2; color: #991b1b; }
        .badge-secondary { background: #e2e8f0; color: #475569; }

        .empty-state {
            padding: 40px;
            text-align: center;
            color: var(--text-light);
        }
        .empty-state i {
            font-size: 48px;
            display: block;
            margin-bottom: 15px;
            opacity: 0.5;
        }

        @media (max-width: 768px) {
            .sidebar { right: -280px; }
            .sidebar.open { right: 0; }
            .menu-toggle { display: block; }
            .main-content { margin-right: 0; padding: 15px; }
            .stats-grid { grid-template-columns: repeat(2, 1fr); }
            .charts-grid { grid-template-columns: 1fr; }
            .form-grid { grid-template-columns: 1fr; }
            .table-controls { flex-direction: column; }
            .report-controls { flex-direction: column; }
            .page-header { flex-direction: column; align-items: start; }
            .settings-grid { grid-template-columns: 1fr; }
            .login-container { padding: 20px; }
        }
    </style>
</head>
<body>

    <!-- ===== شاشة تسجيل الدخول ===== -->
    <div id="loginScreen">
        <div class="login-container">
            <div class="login-header">
                <h1><i class="fas fa-wallet"></i> فلوسي برو</h1>
                <p class="subtitle">نظام مالي متكامل مع سحابة</p>
            </div>
            <div class="login-tabs">
                <button class="login-tab active" data-tab="login" onclick="switchLoginTab('login')">تسجيل الدخول</button>
                <button class="login-tab" data-tab="register" onclick="switchLoginTab('register')">إنشاء حساب</button>
            </div>
            <div id="loginForm" class="login-panel active">
                <div class="form-group">
                    <label><i class="fas fa-user"></i> اسم المستخدم</label>
                    <input type="text" id="loginUsername" placeholder="اسم المستخدم" onkeydown="if(event.key==='Enter')loginUser()">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-lock"></i> PIN</label>
                    <input type="password" id="loginPin" placeholder="أدخل PIN" maxlength="4" inputmode="numeric" onkeydown="if(event.key==='Enter')loginUser()">
                </div>
                <div id="loginError" class="login-error"></div>
                <button onclick="loginUser()" class="btn-primary" style="width:100%;justify-content:center;">
                    <i class="fas fa-sign-in-alt"></i> دخول
                </button>
            </div>
            <div id="registerForm" class="login-panel">
                <div class="form-group">
                    <label><i class="fas fa-user"></i> اسم المستخدم</label>
                    <input type="text" id="regUsername" placeholder="اختر اسم مستخدم">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-lock"></i> PIN (4 أرقام)</label>
                    <input type="password" id="regPin" placeholder="أدخل PIN" maxlength="4" inputmode="numeric">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-user-tag"></i> نوع الحساب</label>
                    <select id="regRole">
                        <option value="admin">👑 مدير (صلاحية كاملة)</option>
                        <option value="user">👤 مستخدم عادي</option>
                        <option value="viewer">👁️ مشاهد فقط</option>
                    </select>
                </div>
                <div id="registerError" class="login-error"></div>
                <button onclick="registerUser()" class="btn-success" style="width:100%;justify-content:center;">
                    <i class="fas fa-user-plus"></i> إنشاء حساب
                </button>
                <p style="text-align:center;margin-top:15px;font-size:12px;color:var(--text-light);">
                    🔑 المدير الافتراضي: <strong>admin / PIN: 1234</strong>
                </p>
            </div>
        </div>
    </div>

    <!-- ===== شاشة القفل السريع ===== -->
    <div id="quickLockScreen">
        <div class="quick-lock-container">
            <h2><i class="fas fa-lock"></i> قفل سريع</h2>
            <p>أدخل PIN لفتح النظام</p>
            <div class="lock-pin-display" id="quickPinDisplay">
                <span></span><span></span><span></span><span></span>
            </div>
            <div class="lock-pin-grid">
                <button onclick="quickPinInput('1')">1</button>
                <button onclick="quickPinInput('2')">2</button>
                <button onclick="quickPinInput('3')">3</button>
                <button onclick="quickPinInput('4')">4</button>
                <button onclick="quickPinInput('5')">5</button>
                <button onclick="quickPinInput('6')">6</button>
                <button onclick="quickPinInput('7')">7</button>
                <button onclick="quickPinInput('8')">8</button>
                <button onclick="quickPinInput('9')">9</button>
                <button class="pin-clear" onclick="quickPinClear()"><i class="fas fa-undo"></i></button>
                <button onclick="quickPinInput('0')">0</button>
                <button class="pin-clear" onclick="quickPinBack()"><i class="fas fa-backspace"></i></button>
            </div>
            <div id="quickLockError" class="login-error"></div>
            <button onclick="quickUnlock()" class="btn-primary" style="width:100%;justify-content:center;">
                <i class="fas fa-unlock"></i> فتح
            </button>
            <button onclick="logoutUser()" class="btn-danger" style="width:100%;justify-content:center;margin-top:10px;">
                <i class="fas fa-sign-out-alt"></i> تسجيل خروج
            </button>
        </div>
    </div>

    <!-- ===== زر القائمة للجوال ===== -->
    <button class="menu-toggle" id="menuToggle"><i class="fas fa-bars"></i></button>

    <!-- ===== الشريط الجانبي ===== -->
    <nav class="sidebar" id="sidebar">
        <div class="sidebar-header">
            <h2><i class="fas fa-wallet"></i> فلوسي</h2>
            <p class="subtitle" id="userDisplay">مدير النظام</p>
        </div>
        <div class="sidebar-menu">
            <a class="active" data-page="dashboard"><i class="fas fa-home"></i> لوحة التحكم</a>
            <a data-page="transactions"><i class="fas fa-exchange-alt"></i> العمليات</a>
            <a data-page="portals"><i class="fas fa-door-open"></i> البوابات</a>
            <a data-page="budgets"><i class="fas fa-bullseye"></i> الميزانيات</a>
            <a data-page="obligations"><i class="fas fa-calendar-alt"></i> الالتزامات</a>
            <a data-page="family"><i class="fas fa-users"></i> الأسرة</a>
            <a data-page="debts"><i class="fas fa-handshake"></i> الديون</a>
            <a data-page="goals"><i class="fas fa-trophy"></i> الأهداف</a>
            <a data-page="subscriptions"><i class="fas fa-clock"></i> الاشتراكات</a>
            <a data-page="stats"><i class="fas fa-chart-bar"></i> الإحصائيات</a>
            <a data-page="reports"><i class="fas fa-file-alt"></i> التقارير</a>
            <a data-page="users" id="usersMenu"><i class="fas fa-users-cog"></i> المستخدمين</a>
            <a data-page="settings"><i class="fas fa-cog"></i> الإعدادات</a>
        </div>
        <div class="sidebar-footer">
            <button class="lock-btn" onclick="quickLock()"><i class="fas fa-lock"></i> قفل سريع</button>
            <button class="lock-btn" onclick="logoutUser()" style="background:rgba(239,68,68,0.3);margin-top:5px;">
                <i class="fas fa-sign-out-alt"></i> خروج
            </button>
            <div class="dark-mode-toggle">
                <i class="fas fa-moon"></i>
                <label class="switch"><input type="checkbox" id="darkModeToggle"><span class="slider"></span></label>
                <i class="fas fa-sun"></i>
            </div>
        </div>
    </nav>

    <!-- ===== المحتوى الرئيسي ===== -->
    <main class="main-content" id="mainContent">

        <!-- ===== Dashboard ===== -->
        <section id="dashboard" class="page active">
            <div class="page-header">
                <h1><i class="fas fa-chart-pie"></i> لوحة التحكم</h1>
                <div class="date-display" id="currentDate"></div>
            </div>
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
            <div class="obligations-summary" style="margin-top:20px;">
                <h3><i class="fas fa-calendar-check"></i> الالتزامات القادمة</h3>
                <div id="obligationsSummary" class="stats-grid" style="grid-template-columns:repeat(auto-fit,minmax(150px,1fr));"></div>
            </div>
        </section>

        <!-- ===== العمليات ===== -->
        <section id="transactions" class="page">
            <div class="page-header"><h1><i class="fas fa-exchange-alt"></i> إدارة العمليات</h1></div>
            <div class="form-card">
                <h3>تسجيل عملية جديدة</h3>
                <form id="transactionForm">
                    <div class="form-grid">
                        <div class="form-group">
                            <label><i class="fas fa-tag"></i> النوع</label>
                            <select id="txType" required>
                                <option value="income">دخل</option>
                                <option value="expense">مصروف</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-money-bill"></i> المبلغ</label>
                            <input type="number" id="txAmount" placeholder="المبلغ" required min="0.01" step="0.01">
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-folder"></i> التصنيف</label>
                            <select id="txCategory" required></select>
                        </div>
                        <div class="form-group" id="incomeSourceGroup">
                            <label><i class="fas fa-briefcase"></i> مصدر الدخل</label>
                            <select id="txIncomeSource">
                                <option value="راتب حكومي">راتب حكومي</option>
                                <option value="راتب خاص">راتب قطاع خاص</option>
                                <option value="تسويق">تسويق / عمولات</option>
                                <option value="عمل حر">عمل حر (Freelance)</option>
                                <option value="استثمار">استثمار / أرباح</option>
                                <option value="هدايا">هدايا / مكافآت</option>
                                <option value="أخرى">أخرى</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-door-open"></i> البوابة (للدخل)</label>
                            <select id="txPortal">
                                <option value="">بدون بوابة</option>
                                <option value="حكومة">🏛️ حكومة</option>
                                <option value="خاص">🏢 قطاع خاص</option>
                                <option value="عمل حر">💻 عمل حر</option>
                                <option value="استثمار">📈 استثمارات</option>
                                <option value="تسويق">📢 تسويق</option>
                                <option value="أخرى">📦 أخرى</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-calendar"></i> التاريخ</label>
                            <input type="date" id="txDate" required>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-wallet"></i> الحساب</label>
                            <select id="txAccount"></select>
                        </div>
                        <div class="form-group full-width">
                            <label><i class="fas fa-sticky-note"></i> ملاحظات</label>
                            <input type="text" id="txNotes" placeholder="ملاحظات اختيارية">
                        </div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-save"></i> حفظ العملية</button>
                </form>
            </div>
            <div class="transactions-list">
                <div class="table-header">
                    <h3>جميع العمليات</h3>
                    <div class="table-controls">
                        <input type="text" id="searchTransactions" placeholder="🔍 بحث...">
                        <select id="filterCategory"><option value="all">كل التصنيفات</option></select>
                        <select id="filterType"><option value="all">الكل</option><option value="income">دخل</option><option value="expense">مصروف</option></select>
                    </div>
                </div>
                <div class="table-responsive">
                    <table>
                        <thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>مصدر الدخل</th><th>البوابة</th><th>الحساب</th><th>الملاحظات</th><th>الإجراءات</th></tr></thead>
                        <tbody id="transactionsTableBody"></tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ===== البوابات ===== -->
        <section id="portals" class="page">
            <div class="page-header"><h1><i class="fas fa-door-open"></i> بوابات الدخل</h1></div>
            <div class="stats-grid" id="portalStats">
                <div class="stat-card" style="border-color:var(--secondary);">
                    <div class="stat-icon" style="background:var(--secondary);"><i class="fas fa-landmark"></i></div>
                    <div class="stat-info"><h3>حكومة</h3><p class="stat-value" id="portalGov">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--success);">
                    <div class="stat-icon" style="background:var(--success);"><i class="fas fa-building"></i></div>
                    <div class="stat-info"><h3>قطاع خاص</h3><p class="stat-value" id="portalPrivate">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--warning);">
                    <div class="stat-icon" style="background:var(--warning);"><i class="fas fa-laptop"></i></div>
                    <div class="stat-info"><h3>عمل حر</h3><p class="stat-value" id="portalFreelance">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--purple);">
                    <div class="stat-icon" style="background:var(--purple);"><i class="fas fa-chart-line"></i></div>
                    <div class="stat-info"><h3>استثمارات</h3><p class="stat-value" id="portalInvestment">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--pink);">
                    <div class="stat-icon" style="background:var(--pink);"><i class="fas fa-bullhorn"></i></div>
                    <div class="stat-info"><h3>تسويق</h3><p class="stat-value" id="portalMarketing">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--info);">
                    <div class="stat-icon" style="background:var(--info);"><i class="fas fa-gift"></i></div>
                    <div class="stat-info"><h3>أخرى</h3><p class="stat-value" id="portalOther">0</p></div>
                </div>
            </div>
            <div class="form-card">
                <h3>إضافة دخل عبر بوابة</h3>
                <form id="portalForm">
                    <div class="form-grid">
                        <div class="form-group">
                            <label><i class="fas fa-door-open"></i> البوابة</label>
                            <select id="portalSelect" required>
                                <option value="حكومة">🏛️ حكومة</option>
                                <option value="خاص">🏢 قطاع خاص</option>
                                <option value="عمل حر">💻 عمل حر</option>
                                <option value="استثمار">📈 استثمارات</option>
                                <option value="تسويق">📢 تسويق</option>
                                <option value="أخرى">📦 أخرى</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-tag"></i> التصنيف الفرعي</label>
                            <input type="text" id="portalSubCategory" placeholder="مثال: راتب, عمولة">
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-money-bill"></i> المبلغ</label>
                            <input type="number" id="portalAmount" placeholder="المبلغ" required min="0.01" step="0.01">
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-calendar"></i> التاريخ</label>
                            <input type="date" id="portalDate" required>
                        </div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة عبر البوابة</button>
                </form>
            </div>
            <div class="chart-card" style="margin-bottom:20px;">
                <h3><i class="fas fa-chart-bar"></i> توزيع الدخل حسب البوابات</h3>
                <canvas id="portalChart"></canvas>
            </div>
            <div id="portalTransactions"></div>
        </section>

        <!-- ===== الميزانيات ===== -->
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

        <!-- ===== الالتزامات ===== -->
        <section id="obligations" class="page">
            <div class="page-header"><h1><i class="fas fa-calendar-alt"></i> الالتزامات المستقبلية</h1></div>
            <div class="form-card">
                <h3>إضافة التزام جديد</h3>
                <form id="obligationForm">
                    <div class="form-grid">
                        <div class="form-group"><label>الاسم</label><input type="text" id="oblName" placeholder="مثال: مصروفات المدارس" required></div>
                        <div class="form-group"><label>التصنيف</label>
                            <select id="oblCategory">
                                <option value="تعليم">تعليم</option>
                                <option value="هدايا">هدايا / عيد</option>
                                <option value="أجهزة">أجهزة</option>
                                <option value="سفر">سفر</option>
                                <option value="صحة">صحة / علاج</option>
                                <option value="منزل">منزل</option>
                                <option value="أخرى">أخرى</option>
                            </select>
                        </div>
                        <div class="form-group"><label>المبلغ</label><input type="number" id="oblAmount" placeholder="المبلغ" required min="1" step="0.01"></div>
                        <div class="form-group"><label>التاريخ المتوقع</label><input type="date" id="oblDate" required></div>
                        <div class="form-group"><label>متكرر؟</label>
                            <select id="oblRecurring">
                                <option value="لا">مرة واحدة</option>
                                <option value="شهري">شهري</option>
                                <option value="سنوي">سنوي</option>
                            </select>
                        </div>
                        <div class="form-group full-width"><label>ملاحظات</label><input type="text" id="oblNotes" placeholder="ملاحظات اختيارية"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة التزام</button>
                </form>
            </div>
            <div id="obligationsList"></div>
        </section>

        <!-- ===== الأسرة ===== -->
        <section id="family" class="page">
            <div class="page-header"><h1><i class="fas fa-users"></i> إدارة الأسرة</h1></div>
            <div class="form-card">
                <h3>إضافة فرد جديد</h3>
                <form id="familyForm">
                    <div class="form-grid">
                        <div class="form-group"><label>الاسم</label><input type="text" id="familyName" placeholder="الاسم" required></div>
                        <div class="form-group"><label>الدور</label>
                            <select id="familyRole">
                                <option value="رب أسرة">رب أسرة</option>
                                <option value="زوجة">زوجة</option>
                                <option value="ابن">ابن</option>
                                <option value="ابنة">ابنة</option>
                            </select>
                        </div>
                        <div class="form-group"><label>الميزانية الشهرية</label><input type="number" id="familyBudget" placeholder="الميزانية" min="0" step="0.01"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة فرد</button>
                </form>
            </div>
            <div id="familyList"></div>
        </section>

        <!-- ===== الديون ===== -->
        <section id="debts" class="page">
            <div class="page-header"><h1><i class="fas fa-handshake"></i> إدارة الديون والسلف</h1></div>
            <div class="form-card">
                <h3>إضافة دين أو سلفة</h3>
                <form id="debtForm">
                    <div class="form-grid">
                        <div class="form-group"><label>الاسم</label><input type="text" id="debtName" placeholder="مثال: قرض السيارة" required></div>
                        <div class="form-group"><label>النوع</label>
                            <select id="debtType">
                                <option value="debt">دين عليك (تدفع)</option>
                                <option value="loan">سلفة لك (تستلم)</option>
                            </select>
                        </div>
                        <div class="form-group"><label>المبلغ الإجمالي</label><input type="number" id="debtTotal" placeholder="المبلغ" required min="1" step="0.01"></div>
                        <div class="form-group"><label>المتبقي</label><input type="number" id="debtRemaining" placeholder="المتبقي" required min="0" step="0.01"></div>
                        <div class="form-group"><label>القسط الشهري</label><input type="number" id="debtInstallment" placeholder="القسط الشهري" min="0" step="0.01"></div>
                        <div class="form-group"><label>تاريخ الاستحقاق</label><input type="date" id="debtDueDate"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة</button>
                </form>
            </div>
            <div id="debtsList"></div>
        </section>

        <!-- ===== الأهداف ===== -->
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

        <!-- ===== الاشتراكات ===== -->
        <section id="subscriptions" class="page">
            <div class="page-header"><h1><i class="fas fa-clock"></i> الاشتراكات والفواتير</h1></div>
            <div class="form-card">
                <h3>إضافة اشتراك جديد</h3>
                <form id="subscriptionForm">
                    <div class="form-grid">
                        <div class="form-group"><label>الاسم</label><input type="text" id="subName" placeholder="مثال: ChatGPT" required></div>
                        <div class="form-group"><label>المبلغ الشهري</label><input type="number" id="subAmount" placeholder="المبلغ" required min="1" step="0.01"></div>
                        <div class="form-group"><label>تاريخ الاستحقاق</label><input type="date" id="subDueDate" required></div>
                        <div class="form-group"><label>الحالة</label>
                            <select id="subStatus">
                                <option value="مدفوع">مدفوع</option>
                                <option value="غير مدفوع">غير مدفوع</option>
                                <option value="متأخر">متأخر</option>
                            </select>
                        </div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة اشتراك</button>
                </form>
            </div>
            <div id="subscriptionsList"></div>
        </section>

        <!-- ===== الإحصائيات ===== -->
        <section id="stats" class="page">
            <div class="page-header"><h1><i class="fas fa-chart-bar"></i> الإحصائيات المتقدمة</h1></div>
            <div class="report-controls">
                <div class="form-group"><label>السنة</label><select id="statsYear"></select></div>
                <div class="form-group"><label>الشهر</label>
                    <select id="statsMonth">
                        <option value="all">كل الشهور</option>
                        <option value="1">يناير</option><option value="2">فبراير</option>
                        <option value="3">مارس</option><option value="4">أبريل</option>
                        <option value="5">مايو</option><option value="6">يونيو</option>
                        <option value="7">يوليو</option><option value="8">أغسطس</option>
                        <option value="9">سبتمبر</option><option value="10">أكتوبر</option>
                        <option value="11">نوفمبر</option><option value="12">ديسمبر</option>
                    </select>
                </div>
                <div class="form-group"><label>البوابة</label>
                    <select id="statsPortal">
                        <option value="all">كل البوابات</option>
                        <option value="حكومة">🏛️ حكومة</option>
                        <option value="خاص">🏢 قطاع خاص</option>
                        <option value="عمل حر">💻 عمل حر</option>
                        <option value="استثمار">📈 استثمارات</option>
                        <option value="تسويق">📢 تسويق</option>
                        <option value="أخرى">📦 أخرى</option>
                    </select>
                </div>
                <button onclick="generateAdvancedStats()" class="btn-primary"><i class="fas fa-chart-bar"></i> عرض الإحصائيات</button>
            </div>
            <div id="statsContent">
                <div class="empty-state"><i class="fas fa-chart-bar"></i>اختر الفترة واضغط "عرض الإحصائيات"</div>
            </div>
            <div class="report-actions" style="margin-top:20px;">
                <button onclick="exportStatsPDF()" class="btn-secondary"><i class="fas fa-file-pdf"></i> تصدير PDF</button>
                <button onclick="exportStatsExcel()" class="btn-secondary"><i class="fas fa-file-excel"></i> تصدير Excel</button>
                <button onclick="exportStatsPPT()" class="btn-secondary"><i class="fas fa-file-powerpoint"></i> تصدير PowerPoint</button>
            </div>
        </section>

        <!-- ===== التقارير ===== -->
        <section id="reports" class="page">
            <div class="page-header"><h1><i class="fas fa-file-alt"></i> التقارير</h1></div>
            <div class="report-controls">
                <div class="form-group"><label>السنة</label><select id="reportYear"></select></div>
                <div class="form-group"><label>الشهر</label>
                    <select id="reportMonth">
                        <option value="all">كل الشهور</option>
                        <option value="1">يناير</option><option value="2">فبراير</option>
                        <option value="3">مارس</option><option value="4">أبريل</option>
                        <option value="5">مايو</option><option value="6">يونيو</option>
                        <option value="7">يوليو</option><option value="8">أغسطس</option>
                        <option value="9">سبتمبر</option><option value="10">أكتوبر</option>
                        <option value="11">نوفمبر</option><option value="12">ديسمبر</option>
                    </select>
                </div>
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

        <!-- ===== المستخدمين ===== -->
        <section id="users" class="page">
            <div class="page-header">
                <h1><i class="fas fa-users-cog"></i> إدارة المستخدمين</h1>
                <span class="badge badge-warning" id="userCount">0 مستخدم</span>
            </div>
            <div class="form-card">
                <h3>إضافة مستخدم جديد</h3>
                <form id="userForm">
                    <div class="form-grid">
                        <div class="form-group"><label>اسم المستخدم</label><input type="text" id="newUsername" placeholder="اسم المستخدم" required></div>
                        <div class="form-group"><label>PIN</label><input type="password" id="newUserPin" placeholder="4 أرقام" maxlength="4" required></div>
                        <div class="form-group"><label>الصلاحية</label>
                            <select id="newUserRole" required>
                                <option value="admin">👑 مدير</option>
                                <option value="user">👤 مستخدم</option>
                                <option value="viewer">👁️ مشاهد</option>
                            </select>
                        </div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-user-plus"></i> إضافة مستخدم</button>
                </form>
            </div>
            <div id="usersList"></div>
        </section>

        <!-- ===== الإعدادات ===== -->
        <section id="settings" class="page">
            <div class="page-header"><h1><i class="fas fa-cog"></i> الإعدادات</h1></div>
            <div class="settings-grid" id="settingsGrid">
                <div class="settings-card">
                    <h3><i class="fas fa-shield-alt"></i> الأمان</h3>
                    <div class="setting-item">
                        <label>تغيير PIN الخاص بك</label>
                        <button onclick="changeMyPin()" class="btn-secondary" style="width:auto;padding:6px 16px;">تغيير</button>
                    </div>
                    <div class="setting-item">
                        <label>مدة الجلسة (دقائق)</label>
                        <select id="sessionTimeout" onchange="updateSessionTimeout()">
                            <option value="1">1 دقيقة</option>
                            <option value="5" selected>5 دقائق</option>
                            <option value="15">15 دقيقة</option>
                            <option value="30">30 دقيقة</option>
                            <option value="60">60 دقيقة</option>
                        </select>
                    </div>
                </div>
                <div class="settings-card">
                    <h3><i class="fas fa-palette"></i> المظهر</h3>
                    <div class="setting-item">
                        <label>الوضع الليلي</label>
                        <div class="dark-mode-toggle" style="margin:0;">
                            <i class="fas fa-moon"></i>
                            <label class="switch"><input type="checkbox" id="settingsDarkMode"><span class="slider"></span></label>
                            <i class="fas fa-sun"></i>
                        </div>
                    </div>
                </div>
                <div class="settings-card">
                    <h3><i class="fas fa-database"></i> البيانات المحلية</h3>
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
                    <h3><i class="fas fa-wallet"></i> إدارة الحسابات</h3>
                    <div id="accountsList"></div>
                    <div class="add-account"><input type="text" id="newAccount" placeholder="حساب جديد"><button onclick="addAccount()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button></div>
                </div>
                <!-- ===== قسم المزامنة السحابية (يضاف بالجافاسكريبت) ===== -->
            </div>
        </section>

    </main>

    <script>
        // ============================================================
        // 🔥 إعدادات Firebase (استبدل ببياناتك)
        // ============================================================
/// Import the functions you need from the SDKs you need
import { initializeApp } from "firebase/app";
import { getAnalytics } from "firebase/analytics";
// TODO: Add SDKs for Firebase products that you want to use
// https://firebase.google.com/docs/web/setup#available-libraries

// Your web app's Firebase configuration
// For Firebase JS SDK v7.20.0 and later, measurementId is optional
const firebaseConfig = {
  apiKey: "AIzaSyDH4gHp8nA8T6t1YjX10zWVU6LIYCIxW2c",
  authDomain: "flosy-4cc03.firebaseapp.com",
  projectId: "flosy-4cc03",
  storageBucket: "flosy-4cc03.firebasestorage.app",
  messagingSenderId: "519291221807",
  appId: "1:519291221807:web:eaf0ec21c4ecd68815d3da",
  measurementId: "G-4F9L2KSBY3"
};

// Initialize Firebase
const app = initializeApp(firebaseConfig);
const analytics = getAnalytics(app);

        // تهيئة Firebase
        try {
            firebase.initializeApp(firebaseConfig);
            const db = firebase.firestore();
            console.log('🔥 Firebase متصل بنجاح!');
        } catch (error) {
            console.warn('⚠️ Firebase غير متصل:', error.message);
        }

        // ============================================================
        // 1. نظام المستخدمين والصلاحيات
        // ============================================================
        const AUTH = {
            users: [],
            currentUser: null,
            isLocked: false,
            quickPin: '',
            lastActivity: Date.now(),

            init() {
                const saved = localStorage.getItem('flosy_users');
                if (saved) {
                    this.users = JSON.parse(saved);
                } else {
                    this.users = [{
                        id: 1,
                        username: 'admin',
                        pin: '1234',
                        role: 'admin',
                        createdAt: new Date().toISOString()
                    }];
                    this.saveUsers();
                }
                const session = localStorage.getItem('flosy_session');
                if (session) {
                    const data = JSON.parse(session);
                    const user = this.users.find(u => u.id === data.userId);
                    if (user) {
                        this.currentUser = user;
                        this.isLocked = false;
                        return true;
                    }
                }
                this.currentUser = null;
                this.isLocked = true;
                return false;
            },

            saveUsers() {
                localStorage.setItem('flosy_users', JSON.stringify(this.users));
            },

            saveSession() {
                if (this.currentUser) {
                    localStorage.setItem('flosy_session', JSON.stringify({
                        userId: this.currentUser.id,
                        loginTime: new Date().toISOString()
                    }));
                }
            },

            clearSession() {
                localStorage.removeItem('flosy_session');
                this.currentUser = null;
                this.isLocked = true;
            },

            login(username, pin) {
                const user = this.users.find(u =>
                    u.username.toLowerCase() === username.toLowerCase() &&
                    u.pin === pin
                );
                if (user) {
                    this.currentUser = user;
                    this.isLocked = false;
                    this.saveSession();
                    return { success: true, user };
                }
                return { success: false, message: '❌ اسم المستخدم أو PIN غير صحيح' };
            },

            register(username, pin, role) {
                if (this.users.find(u => u.username.toLowerCase() === username.toLowerCase())) {
                    return { success: false, message: '❌ اسم المستخدم موجود بالفعل' };
                }
                if (!/^\d{4}$/.test(pin)) {
                    return { success: false, message: '❌ PIN يجب أن يكون 4 أرقام' };
                }
                const user = {
                    id: Date.now(),
                    username,
                    pin,
                    role: role || 'user',
                    createdAt: new Date().toISOString()
                };
                this.users.push(user);
                this.saveUsers();
                return { success: true, user };
            },

            deleteUser(id) {
                if (this.currentUser && this.currentUser.id === id) {
                    return { success: false, message: '❌ لا يمكن حذف حسابك الحالي' };
                }
                const adminCount = this.users.filter(u => u.role === 'admin').length;
                const user = this.users.find(u => u.id === id);
                if (user && user.role === 'admin' && adminCount <= 1) {
                    return { success: false, message: '❌ يجب أن يبقى مدير واحد على الأقل' };
                }
                this.users = this.users.filter(u => u.id !== id);
                this.saveUsers();
                return { success: true };
            },

            updateUser(id, updates) {
                const user = this.users.find(u => u.id === id);
                if (user) {
                    Object.assign(user, updates);
                    this.saveUsers();
                    if (this.currentUser && this.currentUser.id === id) {
                        this.currentUser = user;
                    }
                    return { success: true };
                }
                return { success: false };
            },

            hasPermission(permission) {
                if (!this.currentUser) return false;
                if (this.currentUser.role === 'admin') return true;
                if (this.currentUser.role === 'viewer') return false;
                const permissions = {
                    'add_transaction': true,
                    'edit_transaction': true,
                    'delete_transaction': true,
                    'view_reports': true,
                    'manage_budgets': true,
                    'manage_goals': true,
                    'manage_subscriptions': true,
                    'manage_family': true,
                    'manage_obligations': true,
                    'manage_debts': true,
                    'view_stats': true,
                    'manage_users': false,
                    'manage_settings': false
                };
                return permissions[permission] || false;
            }
        };

        // ============================================================
        // 2. إدارة البيانات
        // ============================================================
        const DB = {
            keys: {
                transactions: 'flosy_transactions',
                budgets: 'flosy_budgets',
                goals: 'flosy_goals',
                subscriptions: 'flosy_subscriptions',
                obligations: 'flosy_obligations',
                family: 'flosy_family',
                debts: 'flosy_debts',
                categories: 'flosy_categories',
                accounts: 'flosy_accounts',
                settings: 'flosy_settings'
            },
            init() {
                if (!localStorage.getItem(this.keys.transactions)) localStorage.setItem(this.keys.transactions, JSON
                .stringify([]));
                if (!localStorage.getItem(this.keys.budgets)) localStorage.setItem(this.keys.budgets, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.goals)) localStorage.setItem(this.keys.goals, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.subscriptions)) localStorage.setItem(this.keys.subscriptions, JSON
                    .stringify([]));
                if (!localStorage.getItem(this.keys.obligations)) localStorage.setItem(this.keys.obligations, JSON
                    .stringify([]));
                if (!localStorage.getItem(this.keys.family)) localStorage.setItem(this.keys.family, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.debts)) localStorage.setItem(this.keys.debts, JSON.stringify([]));
                if (!localStorage.getItem(this.keys.categories)) {
                    localStorage.setItem(this.keys.categories, JSON.stringify([
                        'طعام', 'مواصلات', 'فواتير', 'إيجار', 'ترفيه',
                        'صحة', 'تعليم', 'ملابس', 'هدايا', 'أخرى'
                    ]));
                }
                if (!localStorage.getItem(this.keys.accounts)) {
                    localStorage.setItem(this.keys.accounts, JSON.stringify(['نقدي', 'بنك', 'فودافون كاش', 'بطاقة ائتمان']));
                }
                if (!localStorage.getItem(this.keys.settings)) {
                    localStorage.setItem(this.keys.settings, JSON.stringify({ darkMode: false }));
                }
            },
            get(key) { return JSON.parse(localStorage.getItem(this.keys[key]) || '[]'); },
            set(key, data) { localStorage.setItem(this.keys[key], JSON.stringify(data)); },

            addTransaction(t) { const d = this.get('transactions');
                t.id = Date.now();
                d.push(t);
                this.set('transactions', d);
                return t; },
            deleteTransaction(id) { let d = this.get('transactions');
                d = d.filter(t => t.id !== id);
                this.set('transactions', d); },
            updateTransaction(id, u) { let d = this.get('transactions');
                const i = d.findIndex(t => t.id === id); if (i !== -1) { d[i] = { ...d[i], ...u };
                    this.set('transactions', d); return d[i]; } return null; },

            addBudget(b) { const d = this.get('budgets');
                b.id = Date.now();
                d.push(b);
                this.set('budgets', d);
                return b; },
            deleteBudget(id) { let d = this.get('budgets');
                d = d.filter(b => b.id !== id);
                this.set('budgets', d); },

            addGoal(g) { const d = this.get('goals');
                g.id = Date.now();
                g.progress = (g.current / g.target) * 100;
                d.push(g);
                this.set('goals', d);
                return g; },
            deleteGoal(id) { let d = this.get('goals');
                d = d.filter(g => g.id !== id);
                this.set('goals', d); },
            updateGoal(id, c) { let d = this.get('goals');
                const g = d.find(x => x.id === id); if (g) { g.current = c;
                    g.progress = (g.current / g.target) * 100;
                    this.set('goals', d); return g; } return null; },

            addSubscription(s) { const d = this.get('subscriptions');
                s.id = Date.now();
                d.push(s);
                this.set('subscriptions', d);
                return s; },
            deleteSubscription(id) { let d = this.get('subscriptions');
                d = d.filter(s => s.id !== id);
                this.set('subscriptions', d); },

            addObligation(o) { const d = this.get('obligations');
                o.id = Date.now();
                d.push(o);
                this.set('obligations', d);
                return o; },
            deleteObligation(id) { let d = this.get('obligations');
                d = d.filter(o => o.id !== id);
                this.set('obligations', d); },

            addFamilyMember(m) { const d = this.get('family');
                m.id = Date.now();
                d.push(m);
                this.set('family', d);
                return m; },
            deleteFamilyMember(id) { let d = this.get('family');
                d = d.filter(m => m.id !== id);
                this.set('family', d); },

            addDebt(d) { const debts = this.get('debts');
                d.id = Date.now();
                debts.push(d);
                this.set('debts', debts);
                return d; },
            deleteDebt(id) { let d = this.get('debts');
                d = d.filter(x => x.id !== id);
                this.set('debts', d); },

            getCategories() { return this.get('categories'); },
            addCategory(c) { const d = this.get('categories'); if (!d.includes(c)) { d.push(c);
                    this.set('categories', d); return true; } return false; },
            deleteCategory(c) { let d = this.get('categories');
                d = d.filter(x => x !== c);
                this.set('categories', d); },
            getAccounts() { return this.get('accounts'); },
            addAccount(a) { const d = this.get('accounts'); if (!d.includes(a)) { d.push(a);
                    this.set('accounts', d); return true; } return false; },
            deleteAccount(a) { let d = this.get('accounts');
                d = d.filter(x => x !== a);
                this.set('accounts', d); },
            getSettings() { return JSON.parse(localStorage.getItem(this.keys.settings) || '{}'); },
            saveSettings(s) { localStorage.setItem(this.keys.settings, JSON.stringify(s)); },

            backup() {
                return {
                    transactions: this.get('transactions'),
                    budgets: this.get('budgets'),
                    goals: this.get('goals'),
                    subscriptions: this.get('subscriptions'),
                    obligations: this.get('obligations'),
                    family: this.get('family'),
                    debts: this.get('debts'),
                    categories: this.get('categories'),
                    accounts: this.get('accounts'),
                    settings: this.getSettings()
                };
            },
            restore(data) {
                Object.keys(this.keys).forEach(k => { if (data[k]) this.set(k, data[k]); });
            },
            clearAll() {
                Object.values(this.keys).forEach(k => localStorage.removeItem(k));
                this.init();
            }
        };
        DB.init();

        // ============================================================
        // 3. دوال مساعدة
        // ============================================================
        function formatCurrency(a) { return a.toFixed(2) + ' جنيه'; }

        function getDateString(d) { if (!d) return ''; return new Date(d).toLocaleDateString('ar-EG'); }

        function getTypeClass(t) { return t === 'income' ? 'amount-income' : 'amount-expense'; }

        function getTypeLabel(t) { return t === 'income' ? 'دخل' : 'مصروف'; }

        function getStatusBadge(s) {
            const c = { 'مدفوع': 'success', 'غير مدفوع': 'warning', 'متأخر': 'danger' };
            return `<span class="badge badge-${c[s]||''}">${s}</span>`;
        }

        function getObligationStatus(days) {
            if (days < 0) return { label: 'متأخر', class: 'danger' };
            if (days <= 30) return { label: 'قريب', class: 'warning' };
            return { label: 'بعيد', class: 'success' };
        }

        function getBalance() {
            return DB.get('transactions').reduce((s, t) => s + (t.type === 'income' ? t.amount : -t.amount), 0);
        }

        function getTotalIncome() {
            return DB.get('transactions').filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
        }

        function getTotalExpense() {
            return DB.get('transactions').filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
        }

        // ============================================================
        // 4. دوال تسجيل الدخول والخروج
        // ============================================================
        function switchLoginTab(tab) {
            document.querySelectorAll('.login-tab').forEach(t => t.classList.remove('active'));
            document.querySelector(`[data-tab="${tab}"]`).classList.add('active');
            document.querySelectorAll('.login-panel').forEach(p => p.classList.remove('active'));
            document.getElementById(tab === 'login' ? 'loginForm' : 'registerForm').classList.add('active');
        }

        // دوال تسجيل الدخول مع Firebase
        async function loginUser() {
            const username = document.getElementById('loginUsername').value.trim();
            const pin = document.getElementById('loginPin').value;
            const result = AUTH.login(username, pin);
            if (result.success) {
                document.getElementById('loginScreen').style.display = 'none';
                document.getElementById('quickLockScreen').style.display = 'none';
                updateUIForUser();
                
                // تحميل من Firebase
                await syncLoadFromFirebase();
                loadAllData();
                
                document.getElementById('loginError').textContent = '';
            } else {
                document.getElementById('loginError').textContent = result.message;
            }
        }

        async function registerUser() {
            const username = document.getElementById('regUsername').value.trim();
            const pin = document.getElementById('regPin').value;
            const role = document.getElementById('regRole').value;
            const result = AUTH.register(username, pin, role);
            if (result.success) {
                document.getElementById('registerError').textContent = '';
                document.getElementById('regUsername').value = '';
                document.getElementById('regPin').value = '';
                
                AUTH.currentUser = result.user;
                AUTH.isLocked = false;
                AUTH.saveSession();
                
                // حفظ في Firebase
                await syncSaveToFirebase();
                
                document.getElementById('loginScreen').style.display = 'none';
                document.getElementById('quickLockScreen').style.display = 'none';
                updateUIForUser();
                loadAllData();
                
                alert('✅ تم إنشاء الحساب وتسجيل الدخول بنجاح!');
            } else {
                document.getElementById('registerError').textContent = result.message;
            }
        }

        function logoutUser() {
            if (confirm('هل أنت متأكد من تسجيل الخروج؟')) {
                AUTH.clearSession();
                document.getElementById('loginScreen').style.display = 'flex';
                document.getElementById('quickLockScreen').style.display = 'none';
                document.getElementById('loginUsername').value = '';
                document.getElementById('loginPin').value = '';
            }
        }

        // ============================================================
        // 5. القفل السريع
        // ============================================================
        function quickLock() {
            AUTH.isLocked = true;
            document.getElementById('quickLockScreen').style.display = 'flex';
            document.getElementById('quickPinDisplay').querySelectorAll('span').forEach(s => s.className = '');
            AUTH.quickPin = '';
            document.getElementById('quickLockError').textContent = '';
        }

        function quickPinInput(val) {
            if (AUTH.quickPin.length < 4) {
                AUTH.quickPin += val;
                const spans = document.getElementById('quickPinDisplay').querySelectorAll('span');
                spans.forEach((s, i) => { s.className = i < AUTH.quickPin.length ? 'filled' : ''; });
                document.getElementById('quickLockError').textContent = '';
            }
        }

        function quickPinClear() {
            AUTH.quickPin = '';
            document.getElementById('quickPinDisplay').querySelectorAll('span').forEach(s => s.className = '');
            document.getElementById('quickLockError').textContent = '';
        }

        function quickPinBack() {
            AUTH.quickPin = AUTH.quickPin.slice(0, -1);
            const spans = document.getElementById('quickPinDisplay').querySelectorAll('span');
            spans.forEach((s, i) => { s.className = i < AUTH.quickPin.length ? 'filled' : ''; });
            document.getElementById('quickLockError').textContent = '';
        }

        function quickUnlock() {
            if (AUTH.quickPin === AUTH.currentUser.pin) {
                AUTH.isLocked = false;
                document.getElementById('quickLockScreen').style.display = 'none';
                AUTH.quickPin = '';
                document.getElementById('quickLockError').textContent = '';
                AUTH.lastActivity = Date.now();
            } else {
                document.getElementById('quickLockError').textContent = '❌ PIN غير صحيح!';
                AUTH.quickPin = '';
                document.getElementById('quickPinDisplay').querySelectorAll('span').forEach(s => s.className = '');
            }
        }

        // ============================================================
        // 6. تحديث الواجهة حسب المستخدم
        // ============================================================
        function updateUIForUser() {
            const user = AUTH.currentUser;
            document.getElementById('userDisplay').textContent = user.username + ' (' + user.role + ')';
            const usersMenu = document.getElementById('usersMenu');
            if (user.role === 'admin') {
                usersMenu.style.display = 'flex';
            } else {
                usersMenu.style.display = 'none';
            }
            document.getElementById('userCount').textContent = AUTH.users.length + ' مستخدم';
        }

        // ============================================================
        // 7. إدارة المستخدمين
        // ============================================================
        function loadUsersList() {
            const container = document.getElementById('usersList');
            if (!container) return;
            if (AUTH.currentUser.role !== 'admin') {
                container.innerHTML =
                    '<div class="empty-state"><i class="fas fa-lock"></i>غير مصرح لك بمشاهدة هذه الصفحة</div>';
                return;
            }
            const users = AUTH.users;
            if (users.length === 0) {
                container.innerHTML = '<div class="empty-state"><i class="fas fa-users"></i>لا يوجد مستخدمين</div>';
                return;
            }
            const roleLabels = { admin: '👑 مدير', user: '👤 مستخدم', viewer: '👁️ مشاهد' };
            const roleColors = { admin: 'badge-danger', user: 'badge-success', viewer: 'badge-warning' };
            container.innerHTML = users.map(u => `
                <div class="budget-card" style="border-color:${u.role === 'admin' ? 'var(--danger)' : 'var(--secondary)'};">
                    <div class="info">
                        <h4>${u.username}</h4>
                        <p>
                            <span class="badge ${roleColors[u.role] || 'badge-secondary'}">${roleLabels[u.role] || u.role}</span>
                            ${u.id === AUTH.currentUser.id ? ' <span class="badge badge-warning">(أنت)</span>' : ''}
                            <br>
                            <small>PIN: ${u.pin} | منذ: ${new Date(u.createdAt).toLocaleDateString('ar-EG')}</small>
                        </p>
                    </div>
                    <div style="display:flex;gap:8px;">
                        ${u.id !== AUTH.currentUser.id ? `
                            <button class="btn-secondary" onclick="editUserRole(${u.id})" style="padding:6px 12px;">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button class="btn-danger" onclick="deleteUser(${u.id})" style="padding:6px 12px;">
                                <i class="fas fa-trash"></i>
                            </button>
                        ` : `
                            <button class="btn-secondary" onclick="changeMyPin()" style="padding:6px 12px;">
                                <i class="fas fa-key"></i> تغيير PIN
                            </button>
                        `}
                    </div>
                </div>
            `).join('');
        }

        function deleteUser(id) {
            if (!confirm('هل أنت متأكد من حذف هذا المستخدم؟')) return;
            const result = AUTH.deleteUser(id);
            if (result.success) {
                loadUsersList();
                updateUIForUser();
                alert('✅ تم حذف المستخدم بنجاح');
            } else {
                alert(result.message);
            }
        }

        function editUserRole(id) {
            const user = AUTH.users.find(u => u.id === id);
            if (!user) return;
            const roles = ['admin', 'user', 'viewer'];
            const currentIndex = roles.indexOf(user.role);
            const nextRole = roles[(currentIndex + 1) % roles.length];
            if (confirm(`تغيير صلاحية "${user.username}" من "${user.role}" إلى "${nextRole}"؟`)) {
                const result = AUTH.updateUser(id, { role: nextRole });
                if (result.success) {
                    loadUsersList();
                    updateUIForUser();
                    alert('✅ تم تغيير الصلاحية بنجاح');
                }
            }
        }

        function changeMyPin() {
            const newPin = prompt('🔑 أدخل PIN الجديد (4 أرقام):');
            if (!newPin) return;
            if (!/^\d{4}$/.test(newPin)) {
                alert('❌ PIN يجب أن يكون 4 أرقام فقط!');
                return;
            }
            const confirmPin = prompt('🔑 تأكيد PIN الجديد:');
            if (newPin !== confirmPin) {
                alert('❌ PIN غير متطابق!');
                return;
            }
            const result = AUTH.updateUser(AUTH.currentUser.id, { pin: newPin });
            if (result.success) {
                AUTH.currentUser.pin = newPin;
                alert('✅ تم تغيير PIN بنجاح!');
                loadUsersList();
            }
        }

        function updateSessionTimeout() {
            alert('✅ تم تحديث مدة الجلسة');
        }

        // ============================================================
        // 8. بوابات الدخل
        // ============================================================
        const PORTALS = {
            categories: ['حكومة', 'خاص', 'عمل حر', 'استثمار', 'تسويق', 'أخرى'],
            colors: {
                'حكومة': '#3b82f6',
                'خاص': '#22c55e',
                'عمل حر': '#f59e0b',
                'استثمار': '#8b5cf6',
                'تسويق': '#ec4899',
                'أخرى': '#06b6d4'
            },
            getPortalIncome(portal) {
                return DB.get('transactions')
                    .filter(t => t.type === 'income' && t.portal === portal)
                    .reduce((s, t) => s + t.amount, 0);
            },
            getAllPortalStats() {
                const stats = {};
                this.categories.forEach(p => { stats[p] = this.getPortalIncome(p); });
                return stats;
            }
        };

        // ============================================================
        // 9. تحديث البوابات
        // ============================================================
        function updatePortalStats() {
            const stats = PORTALS.getAllPortalStats();
            document.getElementById('portalGov').textContent = formatCurrency(stats['حكومة'] || 0);
            document.getElementById('portalPrivate').textContent = formatCurrency(stats['خاص'] || 0);
            document.getElementById('portalFreelance').textContent = formatCurrency(stats['عمل حر'] || 0);
            document.getElementById('portalInvestment').textContent = formatCurrency(stats['استثمار'] || 0);
            document.getElementById('portalMarketing').textContent = formatCurrency(stats['تسويق'] || 0);
            document.getElementById('portalOther').textContent = formatCurrency(stats['أخرى'] || 0);
            updatePortalChart(stats);
            loadPortalTransactions();
        }

        function updatePortalChart(stats) {
            const ctx = document.getElementById('portalChart');
            if (!ctx) return;
            const data = Object.entries(stats).filter(([k, v]) => v > 0);
            if (window.portalChartInstance) window.portalChartInstance.destroy();
            if (data.length === 0) {
                window.portalChartInstance = new Chart(ctx, {
                    type: 'bar',
                    data: { labels: ['لا توجد بيانات'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] },
                    options: { responsive: true, plugins: { legend: { display: false } } }
                });
                return;
            }
            window.portalChartInstance = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: data.map(([k]) => k),
                    datasets: [{
                        label: 'إجمالي الدخل',
                        data: data.map(([_, v]) => v),
                        backgroundColor: data.map(([k]) => PORTALS.colors[k] || '#3b82f6'),
                        borderRadius: 8
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: { display: false },
                        tooltip: { callbacks: { label: function(context) { return formatCurrency(context.raw); } } }
                    },
                    scales: { y: { beginAtZero: true, ticks: { callback: function(value) { return formatCurrency(
                                    value); } } } }
                }
            });
        }

        function loadPortalTransactions() {
            const container = document.getElementById('portalTransactions');
            if (!container) return;
            const portal = document.getElementById('portalSelect')?.value || 'all';
            let transactions = DB.get('transactions').filter(t => t.type === 'income');
            if (portal !== 'all') {
                transactions = transactions.filter(t => t.portal === portal);
            }
            if (transactions.length === 0) {
                container.innerHTML =
                    `<div class="empty-state"><i class="fas fa-inbox"></i>لا توجد عمليات دخل عبر البوابات</div>`;
                return;
            }
            container.innerHTML = `
                <div class="table-responsive">
                    <table>
                        <thead><tr><th>التاريخ</th><th>البوابة</th><th>التصنيف الفرعي</th><th>المبلغ</th><th>الملاحظات</th><th>الإجراءات</th></tr></thead>
                        <tbody>
                            ${transactions.sort((a,b) => b.id - a.id).slice(0, 50).map(t => `
                                <tr>
                                    <td>${getDateString(t.date)}</td>
                                    <td><span class="badge" style="background:${PORTALS.colors[t.portal] || '#3b82f6'};color:white;">${t.portal || 'غير محدد'}</span></td>
                                    <td>${t.subCategory || '-'}</td>
                                    <td class="amount-income">${formatCurrency(t.amount)}</td>
                                    <td>${t.notes || ''}</td>
                                    <td><button class="btn-delete" onclick="deletePortalTransaction(${t.id})" style="padding:4px 8px;"><i class="fas fa-trash"></i></button></td>
                                </tr>
                            `).join('')}
                        </tbody>
                    </table>
                </div>
            `;
        }

        function deletePortalTransaction(id) {
            if (confirm('هل أنت متأكد من حذف هذه العملية؟')) {
                DB.deleteTransaction(id);
                updatePortalStats();
                loadPortalTransactions();
                updateDashboard();
            }
        }

        // ============================================================
        // 10. الإحصائيات المتقدمة
        // ============================================================
        function generateAdvancedStats() {
            const year = parseInt(document.getElementById('statsYear').value);
            const month = document.getElementById('statsMonth').value;
            const portal = document.getElementById('statsPortal').value;

            let transactions = DB.get('transactions');
            let filtered = transactions.filter(t => {
                const d = new Date(t.date);
                if (d.getFullYear() !== year) return false;
                if (month !== 'all' && d.getMonth() + 1 !== parseInt(month)) return false;
                if (portal !== 'all' && t.portal !== portal) return false;
                return true;
            });

            const income = filtered.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
            const expense = filtered.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
            const profit = income - expense;

            const portalStats = {};
            PORTALS.categories.forEach(p => {
                portalStats[p] = filtered.filter(t => t.type === 'income' && t.portal === p)
                    .reduce((s, t) => s + t.amount, 0);
            });

            const categoryStats = {};
            const categories = DB.getCategories();
            categories.forEach(c => {
                categoryStats[c] = filtered.filter(t => t.type === 'expense' && t.category === c)
                    .reduce((s, t) => s + t.amount, 0);
            });

            let html = `
                <div class="report-summary">
                    <div class="report-stat"><h4>إجمالي الدخل</h4><p style="color:#22c55e;">${formatCurrency(income)}</p></div>
                    <div class="report-stat"><h4>إجمالي المصروفات</h4><p style="color:#ef4444;">${formatCurrency(expense)}</p></div>
                    <div class="report-stat"><h4>صافي الربح</h4><p style="color:${profit >= 0 ? '#22c55e' : '#ef4444'};">${formatCurrency(profit)}</p></div>
                    <div class="report-stat"><h4>عدد العمليات</h4><p>${filtered.length}</p></div>
                </div>
                <div style="margin-top:20px;">
                    <h4><i class="fas fa-door-open"></i> توزيع الدخل حسب البوابات</h4>
                    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:10px;margin-top:10px;">
                        ${Object.entries(portalStats).map(([p, amt]) => `
                            <div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center;border-right:3px solid ${PORTALS.colors[p] || '#3b82f6'};">
                                <strong>${p}</strong>
                                <p style="color:var(--text-light);font-size:14px;">${formatCurrency(amt)}</p>
                            </div>
                        `).join('')}
                    </div>
                </div>
                <div style="margin-top:20px;">
                    <h4><i class="fas fa-tags"></i> توزيع المصروفات حسب التصنيف</h4>
                    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:10px;margin-top:10px;">
                        ${Object.entries(categoryStats).filter(([_, amt]) => amt > 0).map(([c, amt]) => `
                            <div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center;">
                                <strong>${c}</strong>
                                <p style="color:var(--text-light);font-size:14px;">${formatCurrency(amt)}</p>
                            </div>
                        `).join('') || '<p style="color:var(--text-light);">لا توجد بيانات</p>'}
                    </div>
                </div>
            `;

            if (filtered.length > 0) {
                html += `
                    <div style="margin-top:20px;">
                        <h4>العمليات</h4>
                        <div class="table-responsive">
                            <table>
                                <thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>البوابة</th><th>الحساب</th></tr></thead>
                                <tbody>
                                    ${filtered.sort((a,b) => new Date(b.date) - new Date(a.date)).slice(0, 100).map(t => `
                                        <tr>
                                            <td>${getDateString(t.date)}</td>
                                            <td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td>
                                            <td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td>
                                            <td>${t.category}</td>
                                            <td>${t.portal || '-'}</td>
                                            <td>${t.account || 'نقدي'}</td>
                                        </tr>
                                    `).join('')}
                                </tbody>
                            </table>
                        </div>
                    </div>
                `;
            }

            document.getElementById('statsContent').innerHTML = html;
            window._statsData = { income, expense, profit, portalStats, categoryStats, transactions: filtered };
        }

        // ============================================================
        // 11. تصدير الإحصائيات
        // ============================================================
        function exportStatsPDF() {
            const el = document.getElementById('statsContent');
            if (!el || el.innerHTML.trim() === '' || el.querySelector('.empty-state')) {
                alert('الرجاء إنشاء إحصائيات أولاً');
                return;
            }
            const title = document.createElement('h2');
            title.textContent = '📊 تقرير الإحصائيات - فلوسي';
            title.style.textAlign = 'center';
            title.style.marginBottom = '20px';
            title.style.color = '#2563eb';
            const container = document.createElement('div');
            container.appendChild(title);
            container.appendChild(el.cloneNode(true));
            html2pdf().set({
                margin: 10,
                filename: `flosy_stats_${new Date().toISOString().split('T')[0]}.pdf`,
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2 },
                jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
            }).from(container).save();
        }

        function exportStatsExcel() {
            if (!window._statsData) { alert('الرجاء إنشاء إحصائيات أولاً'); return; }
            const data = window._statsData.transactions.map(t => ({
                'التاريخ': getDateString(t.date),
                'النوع': getTypeLabel(t.type),
                'المبلغ': t.amount,
                'التصنيف': t.category,
                'البوابة': t.portal || '-',
                'الحساب': t.account || 'نقدي',
                'الملاحظات': t.notes || ''
            }));
            if (data.length === 0) { alert('لا توجد بيانات لتصديرها'); return; }
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(data), 'الإحصائيات');
            XLSX.writeFile(wb, `flosy_stats_${new Date().toISOString().split('T')[0]}.xlsx`);
        }

        function exportStatsPPT() {
            if (!window._statsData) { alert('الرجاء إنشاء إحصائيات أولاً'); return; }
            const data = window._statsData;
            let html = `
                <html><head><meta charset="UTF-8"><title>تقرير فلوسي</title>
                <style>
                    body { font-family: 'Tajawal', Arial, sans-serif; direction: rtl; padding: 20px; }
                    h1 { color: #2563eb; text-align: center; }
                    table { width: 100%; border-collapse: collapse; margin: 20px 0; }
                    th, td { border: 1px solid #ddd; padding: 8px; text-align: right; }
                    th { background: #f1f5f9; }
                    .summary { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin: 20px 0; }
                    .summary-item { background: #f1f5f9; padding: 15px; border-radius: 8px; text-align: center; }
                </style>
                </head>
                <body>
                <h1>💰 تقرير فلوسي المالي</h1>
                <p style="text-align:center;">تاريخ التقرير: ${new Date().toLocaleDateString('ar-EG')}</p>
                <div class="summary">
                    <div class="summary-item"><h3>الدخل</h3><p style="color:#22c55e;font-size:24px;">${formatCurrency(data.income)}</p></div>
                    <div class="summary-item"><h3>المصروفات</h3><p style="color:#ef4444;font-size:24px;">${formatCurrency(data.expense)}</p></div>
                    <div class="summary-item"><h3>صافي الربح</h3><p style="color:${data.profit >= 0 ? '#22c55e' : '#ef4444'};font-size:24px;">${formatCurrency(data.profit)}</p></div>
                    <div class="summary-item"><h3>عدد العمليات</h3><p style="font-size:24px;">${data.transactions.length}</p></div>
                </div>
                <h3>📊 توزيع الدخل حسب البوابات</h3>
                <table><thead><tr><th>البوابة</th><th>المبلغ</th></tr></thead><tbody>
                ${Object.entries(data.portalStats || {}).filter(([_, v]) => v > 0).map(([p, amt]) => `
                    <tr><td>${p}</td><td>${formatCurrency(amt)}</td></tr>
                `).join('')}
                </tbody></table>
                <h3>📊 توزيع المصروفات حسب التصنيف</h3>
                <table><thead><tr><th>التصنيف</th><th>المبلغ</th></tr></thead><tbody>
                ${Object.entries(data.categoryStats || {}).filter(([_, v]) => v > 0).map(([c, amt]) => `
                    <tr><td>${c}</td><td>${formatCurrency(amt)}</td></tr>
                `).join('')}
                </tbody></table>
                <h3>📋 تفاصيل العمليات</h3>
                <table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th></tr></thead><tbody>
                ${data.transactions.slice(0, 50).map(t => `
                    <tr><td>${getDateString(t.date)}</td><td>${getTypeLabel(t.type)}</td><td>${formatCurrency(t.amount)}</td><td>${t.category}</td></tr>
                `).join('')}
                </tbody></table>
                <p style="text-align:center;margin-top:20px;color:#94a3b8;font-size:12px;">تم إنشاء التقرير بواسطة فلوسي</p>
                </body></html>
            `;
            const blob = new Blob([html], { type: 'application/vnd.ms-powerpoint' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `flosy_stats_${new Date().toISOString().split('T')[0]}.ppt`;
            a.click();
            URL.revokeObjectURL(url);
        }

        // ============================================================
        // 12. دوال التحديث الرئيسية (Dashboard, Transactions, Budgets, etc.)
        // ============================================================
        function updateDashboard() {
            const transactions = DB.get('transactions');
            document.getElementById('totalIncome').textContent = formatCurrency(getTotalIncome());
            document.getElementById('totalExpense').textContent = formatCurrency(getTotalExpense());
            document.getElementById('currentBalance').textContent = formatCurrency(getBalance());
            document.getElementById('transactionCount').textContent = transactions.length;

            // التنبيهات
            const alerts = [];
            const budgets = DB.get('budgets');
            const now = new Date();
            const month = now.getMonth(),
                year = now.getFullYear();
            const monthlyExpenses = transactions.filter(t => {
                const d = new Date(t.date);
                return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year;
            });
            budgets.forEach(b => {
                const spent = monthlyExpenses.filter(t => t.category === b.category).reduce((s, t) => s + t.amount, 0);
                const p = (spent / b.limit) * 100;
                if (p >= 90) alerts.push({ type: 'danger', icon: 'fa-exclamation-triangle',
                    message: `⚠️ تجاوزت ميزانية "${b.category}" ${p.toFixed(0)}%` });
                else if (p >= 70) alerts.push({ type: 'warning', icon: 'fa-exclamation-circle',
                    message: `⚡ ميزانية "${b.category}" قاربت على الانتهاء (${p.toFixed(0)}%)` });
            });
            const subscriptions = DB.get('subscriptions');
            subscriptions.forEach(s => {
                const due = new Date(s.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                if (diff < 0 && s.status !== 'مدفوع') alerts.push({ type: 'danger', icon: 'fa-times-circle',
                    message: `🔴 اشتراك "${s.name}" متأخر` });
                else if (diff <= 3 && diff >= 0 && s.status !== 'مدفوع') alerts.push({ type: 'warning',
                    icon: 'fa-clock', message: `⏰ اشتراك "${s.name}" مستحق خلال ${diff} يوم` });
            });
            const obligations = DB.get('obligations');
            obligations.forEach(o => {
                const due = new Date(o.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                if (diff >= 0 && diff <= 30) alerts.push({ type: 'warning', icon: 'fa-calendar-check',
                    message: `📅 التزام "${o.name}" خلال ${diff} يوم (${formatCurrency(o.amount)})` });
            });
            const bal = getBalance();
            if (bal < 0) alerts.push({ type: 'danger', icon: 'fa-exclamation-triangle',
                message: `❗ رصيدك سالب (${formatCurrency(bal)})` });

            const c = document.getElementById('alertsContainer');
            if (alerts.length === 0) {
                c.innerHTML = `<div class="alert-item success"><i class="fas fa-check-circle"></i> ✅ الوضع المالي مستقر</div>`;
            } else {
                c.innerHTML = alerts.map(a => `<div class="alert-item ${a.type}"><i class="fas ${a.icon}"></i> ${a.message}</div>`)
                    .join('');
            }

            // الرسوم البيانية
            const expenses = transactions.filter(t => t.type === 'expense');
            const cats = DB.getCategories();
            const expenseData = cats.map(c => ({ category: c,
                total: expenses.filter(t => t.category === c).reduce((s, t) => s + t.amount, 0) })).filter(d => d
                .total > 0);
            const ctx1 = document.getElementById('expenseChart').getContext('2d');
            if (window.expenseChartInstance) window.expenseChartInstance.destroy();
            if (expenseData.length === 0) {
                window.expenseChartInstance = new Chart(ctx1, {
                    type: 'doughnut',
                    data: { labels: ['لا توجد مصروفات'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] },
                    options: { responsive: true, plugins: { legend: { position: 'bottom' } } }
                });
            } else {
                const colors = ['#22c55e', '#3b82f6', '#f59e0b', '#ef4444', '#8b5cf6', '#ec4899', '#14b8a6', '#f97316'];
                window.expenseChartInstance = new Chart(ctx1, {
                    type: 'doughnut',
                    data: {
                        labels: expenseData.map(d => d.category),
                        datasets: [{ data: expenseData.map(d => d.total), backgroundColor: colors.slice(0, expenseData
                                .length), borderWidth: 2 }]
                    },
                    options: { responsive: true, plugins: { legend: { position: 'bottom', labels: { font: { family: 'Tajawal' },
                                padding: 10 } } } }
                });
            }

            // التدفق النقدي
            const monthly = {};
            transactions.forEach(t => {
                const d = new Date(t.date);
                const key = d.getFullYear() + '-' + String(d.getMonth() + 1).padStart(2, '0');
                if (!monthly[key]) monthly[key] = { income: 0, expense: 0 };
                if (t.type === 'income') monthly[key].income += t.amount;
                else monthly[key].expense += t.amount;
            });
            const keys = Object.keys(monthly).sort();
            const ctx2 = document.getElementById('cashFlowChart').getContext('2d');
            if (window.cashFlowChartInstance) window.cashFlowChartInstance.destroy();
            if (keys.length === 0) {
                window.cashFlowChartInstance = new Chart(ctx2, {
                    type: 'line',
                    data: {
                        labels: ['لا توجد بيانات'],
                        datasets: [
                            { label: 'الدخل', data: [0], borderColor: '#22c55e' },
                            { label: 'المصروفات', data: [0], borderColor: '#ef4444' }
                        ]
                    },
                    options: { responsive: true, plugins: { legend: { position: 'bottom' } } }
                });
            } else {
                window.cashFlowChartInstance = new Chart(ctx2, {
                    type: 'line',
                    data: {
                        labels: keys.map(k => k.split('-')[1] + '/' + k.split('-')[0]),
                        datasets: [
                            { label: 'الدخل', data: keys.map(k => monthly[k].income), borderColor: '#22c55e',
                                backgroundColor: 'rgba(34,197,94,0.1)', fill: true, tension: 0.4 },
                            { label: 'المصروفات', data: keys.map(k => monthly[k].expense), borderColor: '#ef4444',
                                backgroundColor: 'rgba(239,68,68,0.1)', fill: true, tension: 0.4 }
                        ]
                    },
                    options: {
                        responsive: true,
                        plugins: { legend: { position: 'bottom', labels: { font: { family: 'Tajawal' } } } },
                        scales: { y: { beginAtZero: true } }
                    }
                });
            }

            // آخر العمليات
            const recent = transactions.slice(-5).reverse();
            const rc = document.getElementById('recentTransactions');
            if (recent.length === 0) {
                rc.innerHTML = '<div class="empty-state"><i class="fas fa-inbox"></i>لا توجد عمليات مسجلة</div>';
            } else {
                rc.innerHTML =
                    `<div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th></tr></thead><tbody>${recent.map(t => `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td></tr>`).join('')}</tbody></table></div>`;
            }

            // ملخص الالتزامات
            const obl = DB.get('obligations');
            const now2 = new Date();
            const soon30 = obl.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now2) / (1000 * 60 * 60 * 24)); return diff >=
                    0 && diff <= 30; });
            const soon90 = obl.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now2) / (1000 * 60 * 60 * 24)); return diff >=
                    0 && diff <= 90; });
            const soonYear = obl.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now2) / (1000 * 60 * 60 * 24)); return diff >=
                    0 && diff <= 365; });
            const summary = document.getElementById('obligationsSummary');
            if (summary) {
                summary.innerHTML = `
                    <div class="stat-card" style="border-color:var(--warning);">
                        <div class="stat-icon" style="background:var(--warning);"><i class="fas fa-calendar-day"></i></div>
                        <div class="stat-info"><h3>خلال 30 يوم</h3><p class="stat-value">${formatCurrency(soon30.reduce((s,o) => s + o.amount, 0))}</p><small>${soon30.length} التزام</small></div>
                    </div>
                    <div class="stat-card" style="border-color:var(--info);">
                        <div class="stat-icon" style="background:var(--info);"><i class="fas fa-calendar-week"></i></div>
                        <div class="stat-info"><h3>خلال 90 يوم</h3><p class="stat-value">${formatCurrency(soon90.reduce((s,o) => s + o.amount, 0))}</p><small>${soon90.length} التزام</small></div>
                    </div>
                    <div class="stat-card" style="border-color:var(--secondary);">
                        <div class="stat-icon" style="background:var(--secondary);"><i class="fas fa-calendar-alt"></i></div>
                        <div class="stat-info"><h3>خلال سنة</h3><p class="stat-value">${formatCurrency(soonYear.reduce((s,o) => s + o.amount, 0))}</p><small>${soonYear.length} التزام</small></div>
                    </div>
                `;
            }
        }

        // ============================================================
        // 13. تحديث التصنيفات والحسابات
        // ============================================================
        function updateCategorySelects() {
            const cats = DB.getCategories();
            ['txCategory', 'filterCategory', 'budgetCategory'].forEach(id => {
                const el = document.getElementById(id);
                if (!el) return;
                const val = el.value;
                el.innerHTML = '';
                cats.forEach(c => { const o = document.createElement('option');
                    o.value = c;
                    o.textContent = c;
                    el.appendChild(o); });
                if (id === 'filterCategory') { const o = document.createElement('option');
                    o.value = 'all';
                    o.textContent = 'كل التصنيفات';
                    el.prepend(o); }
                if (val && cats.includes(val)) el.value = val;
            });
        }

        function updateAccountSelects() {
            const accs = DB.getAccounts();
            const el = document.getElementById('txAccount');
            if (!el) return;
            const val = el.value;
            el.innerHTML = '';
            accs.forEach(a => { const o = document.createElement('option');
                o.value = a;
                o.textContent = a;
                el.appendChild(o); });
            if (val && accs.includes(val)) el.value = val;
        }

        // ============================================================
        // 14. العمليات
        // ============================================================
        function loadTransactions() {
            const transactions = DB.get('transactions');
            const search = document.getElementById('searchTransactions').value.toLowerCase();
            const catFilter = document.getElementById('filterCategory').value;
            const typeFilter = document.getElementById('filterType').value;
            let filtered = transactions.filter(t => {
                if (search && !t.category.includes(search) && !(t.notes || '').includes(search) && !(t.account || '')
                    .includes(search)) return false;
                if (catFilter !== 'all' && t.category !== catFilter) return false;
                if (typeFilter !== 'all' && t.type !== typeFilter) return false;
                return true;
            }).sort((a, b) => b.id - a.id);
            const tbody = document.getElementById('transactionsTableBody');
            if (filtered.length === 0) {
                tbody.innerHTML =
                    `<tr><td colspan="9" style="text-align:center;padding:30px;color:var(--text-light)"><i class="fas fa-inbox" style="font-size:24px;display:block;margin-bottom:10px"></i>لا توجد عمليات</td></tr>`;
                return;
            }
            tbody.innerHTML = filtered.map(t => `
                <tr>
                    <td>${getDateString(t.date)}</td>
                    <td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td>
                    <td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td>
                    <td>${t.category}</td>
                    <td>${t.incomeSource || '-'}</td>
                    <td>${t.portal || '-'}</td>
                    <td>${t.account || 'نقدي'}</td>
                    <td>${t.notes || ''}</td>
                    <td class="transaction-actions">
                        <button class="btn-edit" onclick="editTransaction(${t.id})"><i class="fas fa-edit"></i></button>
                        <button class="btn-delete" onclick="deleteTransaction(${t.id})"><i class="fas fa-trash"></i></button>
                    </td>
                </tr>
            `).join('');
        }

        function deleteTransaction(id) {
            if (confirm('هل أنت متأكد من حذف هذه العملية؟')) {
                DB.deleteTransaction(id);
                loadTransactions();
                updateDashboard();
                // حفظ في Firebase
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        function editTransaction(id) {
            const t = DB.get('transactions').find(x => x.id === id);
            if (!t) return;
            const amt = prompt('المبلغ:', t.amount);
            if (amt === null) return;
            const cat = prompt('التصنيف:', t.category);
            if (cat === null) return;
            const note = prompt('الملاحظات:', t.notes || '');
            if (note === null) return;
            DB.updateTransaction(id, { amount: parseFloat(amt), category: cat, notes: note });
            loadTransactions();
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
        }

        // ============================================================
        // 15. الميزانيات
        // ============================================================
        function loadBudgets() {
            const budgets = DB.get('budgets');
            const transactions = DB.get('transactions');
            const now = new Date();
            const month = now.getMonth(),
                year = now.getFullYear();
            const monthlyExpenses = transactions.filter(t => {
                const d = new Date(t.date);
                return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year;
            });
            const c = document.getElementById('budgetsList');
            if (budgets.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-bullseye"></i>لا توجد ميزانيات مسجلة<br><small>أضف ميزانية لبدء التتبع</small></div>`;
                return;
            }
            c.innerHTML = budgets.map(b => {
                const spent = monthlyExpenses.filter(t => t.category === b.category).reduce((s, t) => s + t.amount, 0);
                const p = Math.min((spent / b.limit) * 100, 100);
                const cls = p >= 90 ? 'danger' : p >= 70 ? 'warning' : 'success';
                return `<div class="budget-card">
                    <div class="info">
                        <h4>${b.category}</h4>
                        <p>${formatCurrency(spent)} من ${formatCurrency(b.limit)} (${p.toFixed(0)}%) - المتبقي: ${formatCurrency(Math.max(b.limit - spent, 0))}</p>
                        <div class="progress-bar"><div class="progress-fill ${cls}" style="width:${p}%"></div></div>
                    </div>
                    <button class="btn-danger" onclick="deleteBudget(${b.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteBudget(id) {
            if (confirm('هل أنت متأكد من حذف هذه الميزانية؟')) {
                DB.deleteBudget(id);
                loadBudgets();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 16. الالتزامات
        // ============================================================
        function loadObligations() {
            const obligations = DB.get('obligations');
            const now = new Date();
            const c = document.getElementById('obligationsList');
            obligations.sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate));
            if (obligations.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-calendar-alt"></i>لا توجد التزامات مستقبلية<br><small>سجل التزاماتك المالية القادمة</small></div>`;
                return;
            }
            c.innerHTML = obligations.map(o => {
                const due = new Date(o.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                const status = getObligationStatus(diff);
                const recurring = o.recurring === 'لا' ? 'مرة واحدة' : o.recurring;
                return `<div class="obligation-card" style="border-color:var(--${status.class});">
                    <div class="info">
                        <h4>${o.name} <span class="badge badge-${status.class}">${status.label}</span></h4>
                        <p>${formatCurrency(o.amount)} - ${getDateString(o.dueDate)} ${diff >= 0 ? `(باقي ${diff} يوم)` : `(متأخر ${Math.abs(diff)} يوم)`} - ${recurring}</p>
                        <p style="font-size:12px;color:var(--text-light);">${o.category} ${o.notes ? '| ' + o.notes : ''}</p>
                    </div>
                    <button class="btn-danger" onclick="deleteObligation(${o.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteObligation(id) {
            if (confirm('هل أنت متأكد من حذف هذا الالتزام؟')) {
                DB.deleteObligation(id);
                loadObligations();
                updateDashboard();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 17. الأسرة
        // ============================================================
        function loadFamily() {
            const members = DB.get('family');
            const c = document.getElementById('familyList');
            if (members.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-users"></i>لا توجد أفراد مسجلين<br><small>أضف أفراد أسرتك لتتبع ميزانياتهم</small></div>`;
                return;
            }
            c.innerHTML = members.map(m => {
                const spent = Math.floor(Math.random() * m.budget * 0.7);
                const p = (spent / m.budget) * 100;
                const cls = p >= 90 ? 'danger' : p >= 70 ? 'warning' : 'success';
                return `<div class="family-card">
                    <div class="info">
                        <h4>${m.name} <span class="badge badge-secondary">${m.role}</span></h4>
                        <p>الميزانية: ${formatCurrency(m.budget)} | المصروف: ${formatCurrency(spent)}</p>
                        <div class="progress-bar"><div class="progress-fill ${cls}" style="width:${Math.min(p,100)}%"></div></div>
                    </div>
                    <button class="btn-danger" onclick="deleteFamilyMember(${m.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteFamilyMember(id) {
            if (confirm('هل أنت متأكد من حذف هذا الفرد؟')) {
                DB.deleteFamilyMember(id);
                loadFamily();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 18. الديون
        // ============================================================
        function loadDebts() {
            const debts = DB.get('debts');
            const c = document.getElementById('debtsList');
            if (debts.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-handshake"></i>لا توجد ديون أو سلف<br><small>سجل ديونك وسلفك لتتبعها</small></div>`;
                return;
            }
            c.innerHTML = debts.map(d => {
                const paid = d.total - d.remaining;
                const p = (paid / d.total) * 100;
                const typeLabel = d.type === 'debt' ? 'دين عليك' : 'سلفة لك';
                const typeClass = d.type === 'debt' ? 'danger' : 'success';
                return `<div class="debt-card" style="border-color:var(--${typeClass});">
                    <div class="info">
                        <h4>${d.name} <span class="badge badge-${typeClass}">${typeLabel}</span></h4>
                        <p>${formatCurrency(d.remaining)} متبقي من ${formatCurrency(d.total)} (تم سداد ${p.toFixed(0)}%) ${d.installment > 0 ? `| القسط: ${formatCurrency(d.installment)}` : ''} ${d.dueDate ? `| تاريخ الاستحقاق: ${getDateString(d.dueDate)}` : ''}</p>
                        <div class="progress-bar"><div class="progress-fill ${p >= 90 ? 'success' : p >= 50 ? 'warning' : 'danger'}" style="width:${p}%"></div></div>
                    </div>
                    <button class="btn-danger" onclick="deleteDebt(${d.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteDebt(id) {
            if (confirm('هل أنت متأكد من حذف هذا الدين/السلفة؟')) {
                DB.deleteDebt(id);
                loadDebts();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 19. الأهداف
        // ============================================================
        function loadGoals() {
            const goals = DB.get('goals');
            const c = document.getElementById('goalsList');
            if (goals.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-trophy"></i>لا توجد أهداف مسجلة<br><small>حدد هدفاً مالياً لبدء التوفير</small></div>`;
                return;
            }
            c.innerHTML = goals.map(g => {
                const p = Math.min((g.current / g.target) * 100, 100);
                const cls = p >= 90 ? 'success' : p >= 50 ? 'warning' : 'danger';
                return `<div class="goal-card">
                    <div class="info">
                        <h4>🏆 ${g.name}</h4>
                        <p>${formatCurrency(g.current)} من ${formatCurrency(g.target)} (${p.toFixed(0)}%) - المتبقي: ${formatCurrency(g.target - g.current)}</p>
                        <div class="progress-bar"><div class="progress-fill ${cls}" style="width:${p}%"></div></div>
                    </div>
                    <div style="display:flex;gap:8px">
                        <button class="btn-secondary" onclick="updateGoal(${g.id})" style="padding:6px 12px"><i class="fas fa-plus"></i> إضافة</button>
                        <button class="btn-danger" onclick="deleteGoal(${g.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                    </div>
                </div>`;
            }).join('');
        }

        function updateGoal(id) {
            const g = DB.get('goals').find(x => x.id === id);
            if (!g) return;
            const amt = prompt(`المبلغ المدخر حالياً للهدف "${g.name}":`, g.current);
            if (amt === null) return;
            const val = parseFloat(amt);
            if (isNaN(val) || val < 0 || val > g.target) {
                alert('الرجاء إدخال مبلغ صحيح بين 0 والمبلغ المستهدف');
                return;
            }
            DB.updateGoal(id, val);
            loadGoals();
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
        }

        function deleteGoal(id) {
            if (confirm('هل أنت متأكد من حذف هذا الهدف؟')) {
                DB.deleteGoal(id);
                loadGoals();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 20. الاشتراكات
        // ============================================================
        function loadSubscriptions() {
            const subs = DB.get('subscriptions').sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate));
            const c = document.getElementById('subscriptionsList');
            if (subs.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-clock"></i>لا توجد اشتراكات مسجلة<br><small>أضف اشتراكاتك لتتبع الفواتير</small></div>`;
                return;
            }
            const now = new Date();
            c.innerHTML = subs.map(s => {
                const due = new Date(s.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                const color = s.status === 'متأخر' || diff < 0 ? 'danger' : s.status === 'غير مدفوع' || diff <= 3 ?
                    'warning' : 'success';
                return `<div class="subscription-card" style="border-color:var(--${color})">
                    <div class="info">
                        <h4>${s.name}</h4>
                        <p>${formatCurrency(s.amount)} - ${getDateString(s.dueDate)} ${diff >= 0 ? `(باقي ${diff} يوم)` : `(متأخر ${Math.abs(diff)} يوم)`}</p>
                        <p>${getStatusBadge(s.status)}</p>
                    </div>
                    <div style="display:flex;gap:8px">
                        <button class="btn-secondary" onclick="updateSubscriptionStatus(${s.id})" style="padding:6px 12px"><i class="fas fa-check"></i> تغيير</button>
                        <button class="btn-danger" onclick="deleteSubscription(${s.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                    </div>
                </div>`;
            }).join('');
        }

        function updateSubscriptionStatus(id) {
            const subs = DB.get('subscriptions');
            const s = subs.find(x => x.id === id);
            if (!s) return;
            const statuses = ['مدفوع', 'غير مدفوع', 'متأخر'];
            s.status = statuses[(statuses.indexOf(s.status) + 1) % statuses.length];
            DB.set('subscriptions', subs);
            loadSubscriptions();
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
        }

        function deleteSubscription(id) {
            if (confirm('هل أنت متأكد من حذف هذا الاشتراك؟')) {
                DB.deleteSubscription(id);
                loadSubscriptions();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        // ============================================================
        // 21. التقارير
        // ============================================================
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

            const summary = filtered.filter(t => t.type === 'expense').reduce((acc, t) => {
                acc[t.category] = (acc[t.category] || 0) + t.amount;
                return acc;
            }, {});
            let html = `<div class="report-summary">
                <div class="report-stat"><h4>إجمالي الدخل</h4><p style="color:#22c55e">${formatCurrency(income)}</p></div>
                <div class="report-stat"><h4>إجمالي المصروفات</h4><p style="color:#ef4444">${formatCurrency(expense)}</p></div>
                <div class="report-stat"><h4>صافي الربح</h4><p style="color:${profit>=0?'#22c55e':'#ef4444'}">${formatCurrency(profit)}</p></div>
                <div class="report-stat"><h4>عدد العمليات</h4><p>${filtered.length}</p></div>
            </div>`;
            html +=
                `<div style="margin-top:20px"><h4>تفاصيل المصروفات حسب التصنيف</h4><div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:10px;margin-top:10px">`;
            if (Object.keys(summary).length === 0) html += '<p style="color:var(--text-light)">لا توجد بيانات</p>';
            else html += Object.entries(summary).map(([cat, amt]) =>
                    `<div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center"><strong>${cat}</strong><p style="color:var(--text-light)">${formatCurrency(amt)}</p></div>`
                    ).join('');
            html += `</div></div>`;
            if (filtered.length > 0) {
                html +=
                    `<div style="margin-top:20px"><h4>العمليات</h4><div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>الحساب</th></tr></thead><tbody>`;
                html += filtered.sort((a, b) => new Date(b.date) - new Date(a.date)).map(t =>
                        `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td><td>${t.account||'نقدي'}</td></tr>`
                    ).join('');
                html += `</tbody></table></div></div>`;
            }
            document.getElementById('reportContent').innerHTML = html;
        }

        function exportExcel() {
            const data = DB.get('transactions').map(t => ({
                'التاريخ': getDateString(t.date),
                'النوع': getTypeLabel(t.type),
                'المبلغ': t.amount,
                'التصنيف': t.category,
                'مصدر الدخل': t.incomeSource || '-',
                'البوابة': t.portal || '-',
                'الحساب': t.account || 'نقدي',
                'الملاحظات': t.notes || ''
            }));
            if (data.length === 0) { alert('لا توجد بيانات لتصديرها'); return; }
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(data), 'العمليات');
            XLSX.writeFile(wb, `flosy_${new Date().toISOString().split('T')[0]}.xlsx`);
        }

        function exportPDF() {
            const el = document.getElementById('reportContent');
            if (!el || el.innerHTML.trim() === '') { alert('الرجاء إنشاء تقرير أولاً'); return; }
            html2pdf().set({
                margin: 10,
                filename: `flosy_report_${new Date().toISOString().split('T')[0]}.pdf`,
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2 },
                jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
            }).from(el).save();
        }

        // ============================================================
        // 22. الإعدادات
        // ============================================================
        function loadSettings() {
            loadCategories();
            loadAccounts();
        }

        function loadCategories() {
            const cats = DB.getCategories();
            document.getElementById('categoriesList').innerHTML = cats.map(c =>
                    `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${c}</span><button class="btn-danger" onclick="deleteCategory('${c}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`
                    ).join('');
        }

        function loadAccounts() {
            const accs = DB.getAccounts();
            document.getElementById('accountsList').innerHTML = accs.map(a =>
                    `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${a}</span><button class="btn-danger" onclick="deleteAccount('${a}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`
                    ).join('');
        }

        function addCategory() {
            const input = document.getElementById('newCategory');
            const val = input.value.trim();
            if (!val) { alert('الرجاء إدخال اسم التصنيف'); return; }
            if (DB.addCategory(val)) { input.value = '';
                loadCategories();
                updateCategorySelects();
                alert('✅ تم إضافة التصنيف بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500); } else alert('❌ هذا التصنيف موجود بالفعل');
        }

        function deleteCategory(cat) {
            if (confirm(`هل أنت متأكد من حذف التصنيف "${cat}"؟`)) {
                DB.deleteCategory(cat);
                loadCategories();
                updateCategorySelects();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        function addAccount() {
            const input = document.getElementById('newAccount');
            const val = input.value.trim();
            if (!val) { alert('الرجاء إدخال اسم الحساب'); return; }
            if (DB.addAccount(val)) { input.value = '';
                loadAccounts();
                updateAccountSelects();
                alert('✅ تم إضافة الحساب بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500); } else alert('❌ هذا الحساب موجود بالفعل');
        }

        function deleteAccount(acc) {
            if (confirm(`هل أنت متأكد من حذف الحساب "${acc}"؟`)) {
                DB.deleteAccount(acc);
                loadAccounts();
                updateAccountSelects();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

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
                    if (confirm('سيتم استبدال كل البيانات الحالية. هل أنت متأكد؟')) {
                        DB.restore(data);
                        alert('✅ تم استعادة البيانات بنجاح');
                        location.reload();
                    }
                } catch (err) { alert('❌ خطأ في قراءة الملف'); }
            };
            reader.readAsText(file);
            event.target.value = '';
        }

        function clearAllData() {
            if (confirm('⚠️ تحذير: سيتم حذف كل البيانات نهائياً. هل أنت متأكد؟')) {
                if (confirm('تأكيد نهائي: هل تريد حذف كل شيء؟')) {
                    DB.clearAll();
                    alert('✅ تم حذف كل البيانات');
                    location.reload();
                }
            }
        }

        // ============================================================
        // 23. المزامنة مع Firebase
        // ============================================================
        const SYNC = {
            isSyncing: false,
            lastSync: null,

            async saveToFirebase() {
                try {
                    this.isSyncing = true;
                    if (!AUTH.currentUser) return { success: false, error: 'لا يوجد مستخدم' };
                    const data = {
                        transactions: DB.get('transactions'),
                        budgets: DB.get('budgets'),
                        goals: DB.get('goals'),
                        subscriptions: DB.get('subscriptions'),
                        obligations: DB.get('obligations'),
                        family: DB.get('family'),
                        debts: DB.get('debts'),
                        categories: DB.get('categories'),
                        accounts: DB.get('accounts'),
                        settings: DB.getSettings(),
                        users: AUTH.users,
                        lastUpdated: new Date().toISOString()
                    };
                    await firebase.firestore().collection('flosy_users').doc(AUTH.currentUser.username).set(data);
                    this.lastSync = new Date();
                    console.log('✅ تم حفظ البيانات في Firebase');
                    return { success: true };
                } catch (error) {
                    console.error('❌ خطأ في حفظ البيانات:', error);
                    return { success: false, error: error.message };
                } finally {
                    this.isSyncing = false;
                }
            },

            async loadFromFirebase() {
                try {
                    if (!AUTH.currentUser) return { success: false, error: 'لا يوجد مستخدم' };
                    const doc = await firebase.firestore().collection('flosy_users').doc(AUTH.currentUser.username).get();
                    if (doc.exists) {
                        const data = doc.data();
                        if (data.transactions) DB.set('transactions', data.transactions);
                        if (data.budgets) DB.set('budgets', data.budgets);
                        if (data.goals) DB.set('goals', data.goals);
                        if (data.subscriptions) DB.set('subscriptions', data.subscriptions);
                        if (data.obligations) DB.set('obligations', data.obligations);
                        if (data.family) DB.set('family', data.family);
                        if (data.debts) DB.set('debts', data.debts);
                        if (data.categories) DB.set('categories', data.categories);
                        if (data.accounts) DB.set('accounts', data.accounts);
                        if (data.settings) DB.saveSettings(data.settings);
                        if (data.users) {
                            AUTH.users = data.users;
                            AUTH.saveUsers();
                        }
                        this.lastSync = new Date(data.lastUpdated);
                        console.log('✅ تم تحميل البيانات من Firebase');
                        return { success: true, data };
                    } else {
                        console.log('📭 لا توجد بيانات سابقة في Firebase');
                        return { success: true, data: null };
                    }
                } catch (error) {
                    console.error('❌ خطأ في تحميل البيانات:', error);
                    return { success: false, error: error.message };
                }
            },

            async autoSync() {
                if (this.isSyncing) return;
                if (!AUTH.currentUser) return;
                await this.loadFromFirebase();
                await this.saveToFirebase();
                loadAllData();
            }
        };

        // دوال المزامنة العامة
        async function syncSaveToFirebase() {
            if (!AUTH.currentUser) return;
            try {
                await SYNC.saveToFirebase();
            } catch (e) { console.warn('⚠️ فشل الحفظ في Firebase:', e); }
        }

        async function syncLoadFromFirebase() {
            if (!AUTH.currentUser) return;
            try {
                await SYNC.loadFromFirebase();
            } catch (e) { console.warn('⚠️ فشل التحميل من Firebase:', e); }
        }

        async function syncNow() {
            if (!AUTH.currentUser) {
                alert('❌ الرجاء تسجيل الدخول أولاً');
                return;
            }
            const btn = event?.target;
            if (btn) {
                btn.disabled = true;
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> جاري المزامنة...';
            }
            try {
                await SYNC.autoSync();
                const statusEl = document.getElementById('syncStatus');
                if (statusEl) statusEl.textContent = `✅ تمت المزامنة بنجاح - ${new Date().toLocaleString('ar-EG')}`;
                alert('✅ تمت المزامنة مع السحابة بنجاح!');
            } catch (error) {
                alert('❌ حدث خطأ أثناء المزامنة: ' + error.message);
            } finally {
                if (btn) {
                    btn.disabled = false;
                    btn.innerHTML = '<i class="fas fa-sync"></i> مزامنة الآن';
                }
            }
        }

        async function saveToCloud() {
            if (!AUTH.currentUser) {
                alert('❌ الرجاء تسجيل الدخول أولاً');
                return;
            }
            const result = await SYNC.saveToFirebase();
            if (result.success) {
                const statusEl = document.getElementById('syncStatus');
                if (statusEl) statusEl.textContent = `✅ تم الحفظ في السحابة - ${new Date().toLocaleString('ar-EG')}`;
                alert('✅ تم حفظ البيانات في السحابة بنجاح!');
            } else {
                alert('❌ حدث خطأ: ' + result.error);
            }
        }

        async function loadFromCloud() {
            if (!AUTH.currentUser) {
                alert('❌ الرجاء تسجيل الدخول أولاً');
                return;
            }
            if (!confirm('سيتم استبدال كل البيانات المحلية ببيانات السحابة. هل أنت متأكد؟')) return;
            const result = await SYNC.loadFromFirebase();
            if (result.success && result.data) {
                loadAllData();
                const statusEl = document.getElementById('syncStatus');
                if (statusEl) statusEl.textContent = `✅ تم التحميل من السحابة - ${new Date().toLocaleString('ar-EG')}`;
                alert('✅ تم تحميل البيانات من السحابة بنجاح!');
            } else if (result.success && !result.data) {
                alert('📭 لا توجد بيانات سابقة في السحابة');
            } else {
                alert('❌ حدث خطأ: ' + result.error);
            }
        }

        // ============================================================
        // 24. إضافة قسم المزامنة في الإعدادات
        // ============================================================
        function addSyncSection() {
            const settingsGrid = document.getElementById('settingsGrid');
            if (!settingsGrid) return;
            // تحقق إذا كان موجود بالفعل
            if (document.getElementById('syncCard')) return;

            const syncCard = document.createElement('div');
            syncCard.id = 'syncCard';
            syncCard.className = 'settings-card';
            syncCard.innerHTML = `
                <h3><i class="fas fa-cloud"></i> المزامنة السحابية</h3>
                <button onclick="syncNow()" class="btn-primary" style="width:100%;justify-content:center;margin:5px 0;">
                    <i class="fas fa-sync"></i> مزامنة الآن
                </button>
                <button onclick="saveToCloud()" class="btn-secondary" style="width:100%;justify-content:center;margin:5px 0;">
                    <i class="fas fa-upload"></i> حفظ في السحابة
                </button>
                <button onclick="loadFromCloud()" class="btn-secondary" style="width:100%;justify-content:center;margin:5px 0;">
                    <i class="fas fa-download"></i> تحميل من السحابة
                </button>
                <div id="syncStatus" style="text-align:center;margin-top:10px;font-size:12px;color:var(--text-light);">
                    ${SYNC.lastSync ? `✅ آخر مزامنة: ${SYNC.lastSync.toLocaleString('ar-EG')}` : '⏳ لم تتم المزامنة بعد'}
                </div>
            `;
            settingsGrid.appendChild(syncCard);
        }

        // ============================================================
        // 25. تحميل جميع البيانات
        // ============================================================
        function loadAllData() {
            updateDashboard();
            loadTransactions();
            loadBudgets();
            loadObligations();
            loadFamily();
            loadDebts();
            loadGoals();
            loadSubscriptions();
            loadSettings();
            updatePortalStats();
            loadUsersList();
            addSyncSection();

            const cy = new Date().getFullYear();
            ['statsYear', 'reportYear'].forEach(id => {
                const el = document.getElementById(id);
                if (el) {
                    el.innerHTML = '';
                    for (let y = cy; y >= cy - 5; y--) {
                        const o = document.createElement('option');
                        o.value = y;
                        o.textContent = y;
                        el.appendChild(o);
                    }
                    el.value = cy;
                }
            });
        }

        // ============================================================
        // 26. تهيئة التطبيق
        // ============================================================
        document.addEventListener('DOMContentLoaded', function() {
            const hasSession = AUTH.init();

            if (!hasSession) {
                document.getElementById('loginScreen').style.display = 'flex';
                document.getElementById('quickLockScreen').style.display = 'none';
            } else {
                document.getElementById('loginScreen').style.display = 'none';
                document.getElementById('quickLockScreen').style.display = 'none';
                updateUIForUser();
                // تحميل من Firebase
                setTimeout(async () => {
                    await syncLoadFromFirebase();
                    loadAllData();
                }, 500);
            }

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
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            document.getElementById('settingsDarkMode').addEventListener('change', function() {
                document.body.classList.toggle('dark-mode', this.checked);
                document.getElementById('darkModeToggle').checked = this.checked;
                const s = DB.getSettings();
                s.darkMode = this.checked;
                DB.saveSettings(s);
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Menu Toggle
            document.getElementById('menuToggle').addEventListener('click', function() {
                document.getElementById('sidebar').classList.toggle('open');
            });

            // Navigation
            document.querySelectorAll('.sidebar-menu a').forEach(link => {
                link.addEventListener('click', function(e) {
                    e.preventDefault();
                    if (AUTH.isLocked) { quickLock(); return; }
                    AUTH.lastActivity = Date.now();
                    const pageId = this.dataset.page;
                    document.querySelectorAll('.sidebar-menu a').forEach(a => a.classList.remove('active'));
                    this.classList.add('active');
                    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
                    document.getElementById(pageId).classList.add('active');
                    document.getElementById('sidebar').classList.remove('open');

                    if (pageId === 'dashboard') updateDashboard();
                    else if (pageId === 'transactions') loadTransactions();
                    else if (pageId === 'portals') updatePortalStats();
                    else if (pageId === 'budgets') loadBudgets();
                    else if (pageId === 'obligations') loadObligations();
                    else if (pageId === 'family') loadFamily();
                    else if (pageId === 'debts') loadDebts();
                    else if (pageId === 'goals') loadGoals();
                    else if (pageId === 'subscriptions') loadSubscriptions();
                    else if (pageId === 'stats') generateAdvancedStats();
                    else if (pageId === 'users') loadUsersList();
                    else if (pageId === 'settings') loadSettings();
                });
            });

            // ===== النماذج =====
            // Transaction Form
            document.getElementById('txDate').value = new Date().toISOString().split('T')[0];
            updateCategorySelects();
            updateAccountSelects();

            document.getElementById('txType').addEventListener('change', function() {
                document.getElementById('incomeSourceGroup').style.display = this.value === 'income' ? 'block' :
                    'none';
            });

            document.getElementById('transactionForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const type = document.getElementById('txType').value;
                const amount = parseFloat(document.getElementById('txAmount').value);
                const category = document.getElementById('txCategory').value;
                const date = document.getElementById('txDate').value;
                const account = document.getElementById('txAccount').value;
                const notes = document.getElementById('txNotes').value;
                const incomeSource = document.getElementById('txIncomeSource').value;
                const portal = document.getElementById('txPortal').value;

                if (!amount || amount <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (!date) { alert('الرجاء تحديد التاريخ'); return; }

                const transaction = {
                    type,
                    amount,
                    category,
                    date,
                    account,
                    incomeSource: type === 'income' ? incomeSource : '',
                    portal: type === 'income' ? portal : '',
                    notes: notes || ''
                };

                DB.addTransaction(transaction);
                loadTransactions();
                updateDashboard();
                document.getElementById('txAmount').value = '';
                document.getElementById('txNotes').value = '';
                document.getElementById('txDate').value = new Date().toISOString().split('T')[0];
                alert('✅ تم إضافة العملية بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Budget Form
            document.getElementById('budgetForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const category = document.getElementById('budgetCategory').value;
                const limit = parseFloat(document.getElementById('budgetLimit').value);
                if (!limit || limit <= 0) { alert('الرجاء إدخال حد صحيح'); return; }
                if (DB.get('budgets').some(b => b.category === category)) {
                    alert('يوجد بالفعل ميزانية لهذا التصنيف');
                    return;
                }
                DB.addBudget({ category, limit });
                loadBudgets();
                document.getElementById('budgetLimit').value = '';
                alert('✅ تم إضافة الميزانية بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Obligation Form
            document.getElementById('obligationForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('oblName').value.trim();
                const category = document.getElementById('oblCategory').value;
                const amount = parseFloat(document.getElementById('oblAmount').value);
                const dueDate = document.getElementById('oblDate').value;
                const recurring = document.getElementById('oblRecurring').value;
                const notes = document.getElementById('oblNotes').value;
                if (!name) { alert('الرجاء إدخال اسم الالتزام'); return; }
                if (!amount || amount <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (!dueDate) { alert('الرجاء تحديد التاريخ'); return; }
                DB.addObligation({ name, category, amount, dueDate, recurring, notes: notes || '' });
                loadObligations();
                updateDashboard();
                document.getElementById('oblName').value = '';
                document.getElementById('oblAmount').value = '';
                document.getElementById('oblDate').value = '';
                document.getElementById('oblNotes').value = '';
                alert('✅ تم إضافة الالتزام بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Family Form
            document.getElementById('familyForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('familyName').value.trim();
                const role = document.getElementById('familyRole').value;
                const budget = parseFloat(document.getElementById('familyBudget').value) || 0;
                if (!name) { alert('الرجاء إدخال اسم الفرد'); return; }
                DB.addFamilyMember({ name, role, budget });
                loadFamily();
                document.getElementById('familyName').value = '';
                document.getElementById('familyBudget').value = '';
                alert('✅ تم إضافة الفرد بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Debt Form
            document.getElementById('debtForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('debtName').value.trim();
                const type = document.getElementById('debtType').value;
                const total = parseFloat(document.getElementById('debtTotal').value);
                const remaining = parseFloat(document.getElementById('debtRemaining').value);
                const installment = parseFloat(document.getElementById('debtInstallment').value) || 0;
                const dueDate = document.getElementById('debtDueDate').value;
                if (!name) { alert('الرجاء إدخال اسم الدين/السلفة'); return; }
                if (!total || total <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (remaining < 0 || remaining > total) { alert('المتبقي يجب أن يكون بين 0 والمبلغ الإجمالي'); return; }
                DB.addDebt({ name, type, total, remaining, installment, dueDate });
                loadDebts();
                document.getElementById('debtName').value = '';
                document.getElementById('debtTotal').value = '';
                document.getElementById('debtRemaining').value = '';
                document.getElementById('debtInstallment').value = '';
                document.getElementById('debtDueDate').value = '';
                alert('✅ تم إضافة الدين/السلفة بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Goal Form
            document.getElementById('goalForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('goalName').value.trim();
                const target = parseFloat(document.getElementById('goalTarget').value);
                const current = parseFloat(document.getElementById('goalCurrent').value);
                if (!name) { alert('الرجاء إدخال اسم الهدف'); return; }
                if (!target || target <= 0) { alert('الرجاء إدخال مبلغ مستهدف صحيح'); return; }
                if (current < 0 || current > target) {
                    alert('المبلغ المدخر يجب أن يكون بين 0 والمبلغ المستهدف');
                    return;
                }
                DB.addGoal({ name, target, current });
                loadGoals();
                document.getElementById('goalName').value = '';
                document.getElementById('goalTarget').value = '';
                document.getElementById('goalCurrent').value = '';
                alert('✅ تم إضافة الهدف بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
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
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Portal Form
            document.getElementById('portalForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const portal = document.getElementById('portalSelect').value;
                const subCategory = document.getElementById('portalSubCategory').value.trim();
                const amount = parseFloat(document.getElementById('portalAmount').value);
                const date = document.getElementById('portalDate').value;
                if (!amount || amount <= 0) { alert('الرجاء إدخال مبلغ صحيح'); return; }
                if (!date) { alert('الرجاء تحديد التاريخ'); return; }
                const transaction = {
                    type: 'income',
                    amount,
                    category: portal,
                    portal: portal,
                    subCategory: subCategory || portal,
                    date: date,
                    account: 'نقدي',
                    notes: `دخل عبر بوابة ${portal}`,
                    incomeSource: portal
                };
                DB.addTransaction(transaction);
                updatePortalStats();
                loadPortalTransactions();
                updateDashboard();
                document.getElementById('portalAmount').value = '';
                document.getElementById('portalSubCategory').value = '';
                document.getElementById('portalDate').value = new Date().toISOString().split('T')[0];
                alert('✅ تم إضافة الدخل عبر البوابة بنجاح');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // User Form
            document.getElementById('userForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const username = document.getElementById('newUsername').value.trim();
                const pin = document.getElementById('newUserPin').value;
                const role = document.getElementById('newUserRole').value;
                const result = AUTH.register(username, pin, role);
                if (result.success) {
                    document.getElementById('newUsername').value = '';
                    document.getElementById('newUserPin').value = '';
                    loadUsersList();
                    updateUIForUser();
                    alert('✅ تم إضافة المستخدم بنجاح');
                    setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                } else {
                    alert(result.message);
                }
            });

            // Search & Filters
            document.getElementById('searchTransactions').addEventListener('input', loadTransactions);
            document.getElementById('filterCategory').addEventListener('change', loadTransactions);
            document.getElementById('filterType').addEventListener('change', loadTransactions);

            // التاريخ
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString('ar-EG', {
                year: 'numeric',
                month: 'long',
                day: 'numeric',
                weekday: 'long'
            });

            // نشاط المستخدم
            document.addEventListener('click', function() { if (!AUTH.isLocked) AUTH.lastActivity = Date.now(); });
            document.addEventListener('keydown', function() { if (!AUTH.isLocked) AUTH.lastActivity = Date.now(); });

            // جلسة العمل
            setInterval(() => {
                if (!AUTH.isLocked && AUTH.currentUser) {
                    const idle = (Date.now() - AUTH.lastActivity) / 60000;
                    if (idle >= 5) quickLock();
                }
            }, 30000);

            // Enter للدخول
            document.addEventListener('keydown', function(e) {
                if (e.key === 'Enter' && document.getElementById('quickLockScreen').style.display !== 'none') {
                    quickUnlock();
                }
                if (e.key === 'Enter' && document.getElementById('loginScreen').style.display !== 'none') {
                    loginUser();
                }
            });

            // ESC للقفل
            document.addEventListener('keydown', function(e) {
                if (e.key === 'Escape' && !AUTH.isLocked && AUTH.currentUser) {
                    quickLock();
                }
            });

            console.log('💰 فلوسي برو - النظام المالي المتكامل مع Firebase');
            console.log('👥 المستخدمين:', AUTH.users.length);
            console.log('👤 المستخدم الحالي:', AUTH.currentUser?.username || 'غير مسجل');
            console.log('🔥 Firebase:',
                typeof firebase !== 'undefined' ? '✅ متصل' : '❌ غير متصل (ضع بيانات Firebase)');
        });
    </script>
</body>
</html>
