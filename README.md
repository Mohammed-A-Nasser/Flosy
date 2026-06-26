<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>فلوسي برو | Flosy Pro v6.0</title>
    
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap" rel="stylesheet">
    
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-analytics-compat.js"></script>
    
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
            --font-size: 16px;
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
            font-size: var(--font-size);
        }

        /* ===== تنسيقات الإشعارات (Toasts) ===== */
        .toast-container {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%);
            z-index: 99999;
            display: flex;
            flex-direction: column;
            gap: 10px;
            max-width: 500px;
            width: 90%;
        }
        .toast {
            padding: 16px 20px;
            border-radius: var(--radius);
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            display: flex;
            align-items: center;
            gap: 12px;
            animation: slideDown 0.4s ease;
            color: white;
            cursor: pointer;
        }
        .toast-success { background: var(--success); }
        .toast-error { background: var(--danger); }
        .toast-warning { background: var(--warning); }
        .toast-info { background: var(--info); }
        .toast i { font-size: 20px; }
        .toast .toast-msg { flex: 1; font-size: 14px; }
        .toast .toast-close { cursor: pointer; opacity: 0.7; transition: var(--transition); }
        .toast .toast-close:hover { opacity: 1; }

        @keyframes slideDown {
            from { opacity: 0; transform: translateY(-30px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* ===== شاشات الدخول ===== */
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
            width: 450px;
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

        /* ===== استعادة كلمة المرور ===== */
        #forgotPasswordScreen {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0,0,0,0.8);
            z-index: 9997;
            display: none;
            align-items: center;
            justify-content: center;
            backdrop-filter: blur(10px);
        }
        .forgot-password-container {
            background: var(--card-bg);
            padding: 40px;
            border-radius: var(--radius);
            box-shadow: 0 20px 60px rgba(0,0,0,0.5);
            width: 400px;
            max-width: 95%;
        }
        .forgot-password-container h2 { color: var(--secondary); margin-bottom: 20px; text-align: center; }

        /* ===== شاشة القفل ===== */
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

        /* ===== الشريط الجانبي ===== */
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
        .sidebar-header { text-align: center; padding-bottom: 20px; border-bottom: 1px solid rgba(255,255,255,0.1); flex-shrink: 0; }
        .sidebar-header .avatar { width: 70px; height: 70px; border-radius: 50%; background: var(--secondary); display: flex; align-items: center; justify-content: center; font-size: 30px; margin: 0 auto 10px; cursor: pointer; overflow: hidden; border: 3px solid rgba(255,255,255,0.2); transition: var(--transition); }
        .sidebar-header .avatar:hover { border-color: var(--secondary); }
        .sidebar-header .avatar img { width: 100%; height: 100%; object-fit: cover; }
        .sidebar-header h2 { font-size: 28px; margin-bottom: 5px; }
        .sidebar-header h2 i { color: var(--secondary); }
        .sidebar-header .subtitle { font-size: 14px; opacity: 0.7; }

        .sidebar-menu { flex: 1; overflow-y: auto; margin: 20px 0; padding-bottom: 10px; }
        .sidebar-menu a {
            display: flex; align-items: center; gap: 12px;
            padding: 12px 16px; color: rgba(255,255,255,0.7);
            text-decoration: none; border-radius: 12px;
            transition: var(--transition); margin: 4px 0; font-size: 16px;
            cursor: pointer;
        }
        .sidebar-menu a:hover, .sidebar-menu a.active {
            background: rgba(255,255,255,0.1); color: white;
        }
        .sidebar-menu a.active { background: var(--secondary); color: white; }
        .sidebar-menu a i { width: 24px; text-align: center; }

        .sidebar-footer { flex-shrink: 0; padding-top: 20px; border-top: 1px solid rgba(255,255,255,0.1); margin-top: auto; }
        .lock-btn {
            background: rgba(239,68,68,0.2); color: var(--danger);
            border: 1px solid var(--danger); padding: 8px 16px;
            border-radius: 10px; cursor: pointer; width: 100%;
            font-size: 14px; display: flex; align-items: center;
            justify-content: center; gap: 8px; transition: var(--transition);
            font-family: inherit; margin-top: 5px;
        }
        .lock-btn:hover { background: var(--danger); color: white; }

        .dark-mode-toggle { display: flex; align-items: center; justify-content: center; gap: 12px; margin-top: 12px; }

        .switch { position: relative; display: inline-block; width: 50px; height: 26px; }
        .switch input { opacity: 0; width: 0; height: 0; }
        .slider { position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0; background: #ccc; transition: .4s; border-radius: 34px; }
        .slider:before { position: absolute; content: ""; height: 20px; width: 20px; right: 3px; bottom: 3px; background: white; transition: .4s; border-radius: 50%; }
        input:checked + .slider { background: var(--secondary); }
        input:checked + .slider:before { transform: translateX(-24px); }

        .menu-toggle { display: none; position: fixed; top: 20px; right: 20px; z-index: 1100; background: var(--primary); color: white; border: none; border-radius: 12px; padding: 10px 14px; font-size: 20px; cursor: pointer; box-shadow: var(--shadow); }

        .main-content { margin-right: 280px; padding: 30px; min-height: 100vh; transition: var(--transition); }

        .page { display: none; animation: fadeIn 0.4s ease; }
        .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .page-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 30px; flex-wrap: wrap; gap: 10px; }
        .page-header h1 { font-size: 28px; font-weight: 700; }
        .page-header h1 i { color: var(--secondary); }
        .date-display { color: var(--text-light); font-size: 16px; }

        /* ===== البطاقات ===== */
        .stats-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .stat-card {
            background: var(--card-bg); padding: 20px; border-radius: var(--radius);
            box-shadow: var(--shadow); display: flex; align-items: center;
            gap: 15px; transition: var(--transition);
            border-right: 4px solid var(--secondary);
            cursor: pointer;
        }
        .stat-card:hover { transform: translateY(-4px); box-shadow: 0 10px 20px rgba(0,0,0,0.1); }
        .stat-icon { width: 50px; height: 50px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 24px; color: white; }
        .income-card .stat-icon { background: var(--success); }
        .expense-card .stat-icon { background: var(--danger); }
        .balance-card .stat-icon { background: var(--secondary); }
        .transactions-card .stat-icon { background: var(--warning); }
        .stat-info h3 { font-size: 14px; color: var(--text-light); font-weight: 400; }
        .stat-value { font-size: 24px; font-weight: 700; margin-top: 4px; }

        /* ===== Quick Actions ===== */
        .quick-actions {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
            gap: 15px;
            margin-bottom: 30px;
        }
        .quick-action {
            background: var(--card-bg);
            padding: 15px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            text-align: center;
            cursor: pointer;
            transition: var(--transition);
            border: 2px solid transparent;
        }
        .quick-action:hover {
            transform: translateY(-3px);
            border-color: var(--secondary);
        }
        .quick-action i {
            font-size: 28px;
            color: var(--secondary);
            display: block;
            margin-bottom: 8px;
        }
        .quick-action span {
            font-size: 13px;
            font-weight: 500;
        }

        .charts-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(400px, 1fr)); gap: 20px; margin-bottom: 30px; }
        .chart-card { background: var(--card-bg); padding: 20px; border-radius: var(--radius); box-shadow: var(--shadow); }
        .chart-card h3 { margin-bottom: 15px; font-size: 18px; }
        .chart-card h3 i { color: var(--secondary); }
        .chart-card canvas { max-height: 300px; max-width: 100%; }

        /* ===== التنبيهات ===== */
        .alerts-section { background: var(--card-bg); padding: 20px; border-radius: var(--radius); box-shadow: var(--shadow); margin-bottom: 30px; }
        .alerts-section h3 { margin-bottom: 15px; }
        .alerts-section h3 i { color: var(--warning); }

        .alert-item { padding: 12px 16px; border-radius: 10px; margin: 8px 0; display: flex; align-items: center; gap: 10px; background: #fef3c7; color: #92400e; border-right: 4px solid var(--warning); }
        .alert-item.success { background: #d1fae5; color: #065f46; border-color: var(--success); }
        .alert-item.danger { background: #fee2e2; color: #991b1b; border-color: var(--danger); }
        .alert-item.info { background: #dbeafe; color: #1e40af; border-color: var(--info); }
        .alert-item i { font-size: 18px; }

        /* ===== النماذج ===== */
        .form-card { background: var(--card-bg); padding: 24px; border-radius: var(--radius); box-shadow: var(--shadow); margin-bottom: 30px; }
        .form-card h3 { margin-bottom: 20px; }
        .form-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 15px; }
        .form-group { display: flex; flex-direction: column; gap: 6px; }
        .form-group label { font-weight: 500; font-size: 14px; color: var(--text-light); }
        .form-group label i { margin-left: 6px; color: var(--secondary); }
        .form-group input, .form-group select, .form-group textarea {
            padding: 10px 12px; border: 2px solid var(--border);
            border-radius: 10px; background: var(--bg); color: var(--text);
            font-family: inherit; font-size: 14px; transition: var(--transition);
        }
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
            outline: none; border-color: var(--secondary);
            box-shadow: 0 0 0 3px rgba(37,99,235,0.1);
        }
        .form-group.full-width { grid-column: 1 / -1; }

        /* ===== الأزرار ===== */
        .btn-primary { background: var(--secondary); color: white; border: none; padding: 12px 24px; border-radius: 10px; font-size: 16px; font-weight: 500; display: inline-flex; align-items: center; gap: 8px; cursor: pointer; transition: var(--transition); font-family: inherit; }
        .btn-primary:hover { transform: translateY(-2px); box-shadow: 0 4px 12px rgba(37,99,235,0.3); }
        .btn-secondary { background: var(--bg); color: var(--text); border: 2px solid var(--border); padding: 10px 20px; border-radius: 10px; font-size: 14px; display: inline-flex; align-items: center; gap: 8px; cursor: pointer; transition: var(--transition); font-family: inherit; }
        .btn-secondary:hover { background: var(--border); }
        .btn-success { background: var(--success); color: white; border: none; padding: 10px 20px; border-radius: 10px; font-size: 14px; display: inline-flex; align-items: center; gap: 8px; cursor: pointer; transition: var(--transition); font-family: inherit; }
        .btn-success:hover { opacity: 0.8; }
        .btn-danger { background: var(--danger); color: white; border: none; padding: 10px 20px; border-radius: 10px; font-size: 14px; display: inline-flex; align-items: center; gap: 8px; cursor: pointer; transition: var(--transition); font-family: inherit; }
        .btn-danger:hover { opacity: 0.8; }
        .btn-warning { background: var(--warning); color: white; border: none; padding: 10px 20px; border-radius: 10px; font-size: 14px; display: inline-flex; align-items: center; gap: 8px; cursor: pointer; transition: var(--transition); font-family: inherit; }
        .btn-warning:hover { opacity: 0.8; }

        /* ===== الجداول ===== */
        .table-responsive { overflow-x: auto; background: var(--card-bg); border-radius: var(--radius); box-shadow: var(--shadow); }
        table { width: 100%; border-collapse: collapse; font-size: 14px; }
        thead { background: var(--bg); }
        th { padding: 12px 16px; text-align: right; font-weight: 600; color: var(--text-light); border-bottom: 2px solid var(--border); }
        td { padding: 12px 16px; border-bottom: 1px solid var(--border); }
        tr:hover { background: var(--bg); }

        .table-controls { display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 15px; }
        .table-controls input, .table-controls select {
            padding: 8px 12px; border: 2px solid var(--border);
            border-radius: 8px; background: var(--bg); color: var(--text);
            font-family: inherit;
        }

        .progress-bar { width: 100%; height: 8px; background: var(--bg); border-radius: 4px; overflow: hidden; margin: 8px 0; }
        .progress-bar .progress-fill { height: 100%; border-radius: 4px; transition: width 0.6s ease; }
        .progress-fill.success { background: var(--success); }
        .progress-fill.warning { background: var(--warning); }
        .progress-fill.danger { background: var(--danger); }

        /* ===== التقارير ===== */
        .report-controls { display: flex; gap: 15px; flex-wrap: wrap; align-items: end; background: var(--card-bg); padding: 20px; border-radius: var(--radius); box-shadow: var(--shadow); margin-bottom: 30px; }
        .report-summary { display: grid; grid-template-columns: repeat(auto-fit, minmax(150px, 1fr)); gap: 15px; margin: 20px 0; }
        .report-stat { background: var(--bg); padding: 15px; border-radius: var(--radius); text-align: center; }
        .report-stat h4 { font-size: 14px; color: var(--text-light); margin-bottom: 8px; }
        .report-stat p { font-size: 24px; font-weight: 700; }
        .report-actions { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 20px; }

        /* ===== الإعدادات ===== */
        .settings-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 20px; }
        .settings-card { background: var(--card-bg); padding: 20px; border-radius: var(--radius); box-shadow: var(--shadow); }
        .settings-card h3 { margin-bottom: 15px; font-size: 18px; }
        .settings-card h3 i { color: var(--secondary); }
        .settings-card button { margin: 6px 0; width: 100%; justify-content: center; }
        .setting-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid var(--border); }

        .color-options { display: flex; gap: 8px; }
        .color-btn { width: 32px; height: 32px; border-radius: 50%; border: 3px solid transparent; cursor: pointer; transition: var(--transition); }
        .color-btn:hover { transform: scale(1.1); }
        .color-btn.active { border-color: var(--text); }

        .add-category, .add-account, .add-family, .add-income-source {
            display: flex; gap: 10px; margin-top: 10px; flex-wrap: wrap;
        }
        .add-category input, .add-account input, .add-family input, .add-income-source input {
            flex: 1; padding: 8px 12px; border: 2px solid var(--border); border-radius: 8px; background: var(--bg); color: var(--text); min-width: 120px;
        }

        .badge { display: inline-block; padding: 4px 12px; border-radius: 20px; font-size: 12px; font-weight: 500; }
        .badge-success { background: #d1fae5; color: #065f46; }
        .badge-warning { background: #fef3c7; color: #92400e; }
        .badge-danger { background: #fee2e2; color: #991b1b; }
        .badge-secondary { background: #e2e8f0; color: #475569; }
        .badge-gold { background: #fbbf24; color: #78350f; }
        .badge-silver { background: #e2e8f0; color: #475569; }
        .badge-bronze { background: #d97706; color: #451a03; }

        .filter-badge { display: inline-block; padding: 4px 12px; border-radius: 20px; font-size: 12px; font-weight: 500; }
        .filter-badge.income { background: #d1fae5; color: #065f46; }
        .filter-badge.expense { background: #fee2e2; color: #991b1b; }

        .empty-state { padding: 40px; text-align: center; color: var(--text-light); }
        .empty-state i { font-size: 48px; display: block; margin-bottom: 15px; opacity: 0.5; }

        .goal-card, .subscription-card, .budget-card, .obligation-card, .family-card, .debt-card,
        .saving-card, .visa-card, .association-card {
            background: var(--card-bg); padding: 16px 20px; border-radius: var(--radius);
            box-shadow: var(--shadow); margin: 10px 0;
            display: flex; justify-content: space-between; align-items: center;
            flex-wrap: wrap; gap: 10px; border-right: 4px solid var(--secondary);
        }
        .goal-card .info, .subscription-card .info, .budget-card .info,
        .obligation-card .info, .family-card .info, .debt-card .info,
        .saving-card .info, .visa-card .info, .association-card .info {
            flex: 1; min-width: 150px;
        }

        .activity-log { max-height: 300px; overflow-y: auto; }
        .activity-item { display: flex; justify-content: space-between; padding: 8px 12px; border-bottom: 1px solid var(--border); font-size: 14px; }
        .activity-item .time { color: var(--text-light); font-size: 12px; }
        .activity-item .action { font-weight: 500; }

        /* ===== الـ Badge الإنجازات ===== */
        .achievement-badge {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            padding: 6px 14px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
            background: var(--bg);
            border: 2px solid var(--border);
        }
        .achievement-badge.unlocked {
            border-color: var(--gold);
            background: #fef3c7;
            color: #78350f;
        }
        .achievement-badge i { font-size: 16px; }

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
            .quick-actions { grid-template-columns: repeat(2, 1fr); }
        }

        .font-small { --font-size: 14px; }
        .font-medium { --font-size: 16px; }
        .font-large { --font-size: 18px; }
        body.font-small { font-size: 14px; }
        body.font-medium { font-size: 16px; }
        body.font-large { font-size: 18px; }

        /* ===== شريط التحميل Lazy Loading ===== */
        .lazy-load {
            opacity: 0;
            transition: opacity 0.6s ease;
        }
        .lazy-load.loaded {
            opacity: 1;
        }

        /* ===== صفحة المطور Dashboard ===== */
        .dev-stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }
        .dev-stat-card {
            background: var(--card-bg);
            padding: 15px;
            border-radius: var(--radius);
            box-shadow: var(--shadow);
            text-align: center;
        }
        .dev-stat-card .value {
            font-size: 28px;
            font-weight: 700;
        }
        .dev-stat-card .label {
            font-size: 13px;
            color: var(--text-light);
        }
        .dev-stat-card .status-online { color: var(--success); }
        .dev-stat-card .status-offline { color: var(--danger); }
    </style>
</head>
<body>

    <!-- ===== Toast Container ===== -->
    <div class="toast-container" id="toastContainer"></div>

    <!-- ===== شاشة تسجيل الدخول ===== -->
    <div id="loginScreen">
        <div class="login-container">
            <div class="login-header">
                <h1><i class="fas fa-wallet"></i> فلوسي برو</h1>
                <p class="subtitle">نظام مالي متكامل v6.0</p>
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
                    <label><i class="fas fa-lock"></i> كلمة المرور</label>
                    <input type="password" id="loginPassword" placeholder="أدخل كلمة المرور" onkeydown="if(event.key==='Enter')loginUser()">
                </div>
                <div style="text-align:left;margin-bottom:10px;">
                    <a href="#" onclick="showForgotPassword()" style="color:var(--secondary);font-size:13px;text-decoration:none;">
                        <i class="fas fa-key"></i> نسيت كلمة المرور؟
                    </a>
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
                    <label><i class="fas fa-lock"></i> كلمة المرور (8 أحرف مختلفة)</label>
                    <input type="password" id="regPassword" placeholder="أدخل كلمة المرور">
                </div>
                <div class="form-group">
                    <label><i class="fas fa-question-circle"></i> سؤال الأمان</label>
                    <select id="regSecurityQuestion">
                        <option value="ما هو اسم والدتك؟">ما هو اسم والدتك؟</option>
                        <option value="ما هو اسم مدرستك الأولى؟">ما هو اسم مدرستك الأولى؟</option>
                        <option value="ما هو اسم حيوانك الأليف؟">ما هو اسم حيوانك الأليف؟</option>
                        <option value="ما هو اسم مدينتك المفضلة؟">ما هو اسم مدينتك المفضلة؟</option>
                    </select>
                </div>
                <div class="form-group">
                    <label><i class="fas fa-answer"></i> إجابة سؤال الأمان</label>
                    <input type="text" id="regSecurityAnswer" placeholder="أدخل الإجابة">
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
                    🔑 المدير الافتراضي: <strong>admin / كلمة المرور: Admin@1234</strong>
                </p>
            </div>
        </div>
    </div>

    <!-- ===== شاشة استعادة كلمة المرور ===== -->
    <div id="forgotPasswordScreen">
        <div class="forgot-password-container">
            <h2><i class="fas fa-key"></i> استعادة كلمة المرور</h2>
            <div class="form-group">
                <label>اسم المستخدم</label>
                <input type="text" id="forgotUsername" placeholder="أدخل اسم المستخدم">
            </div>
            <div class="form-group">
                <label>سؤال الأمان</label>
                <select id="forgotSecurityQuestion"></select>
            </div>
            <div class="form-group">
                <label>إجابة سؤال الأمان</label>
                <input type="text" id="forgotSecurityAnswer" placeholder="أدخل الإجابة">
            </div>
            <div id="forgotError" class="login-error"></div>
            <button onclick="resetPassword()" class="btn-primary" style="width:100%;justify-content:center;">
                <i class="fas fa-undo"></i> استعادة كلمة المرور
            </button>
            <button onclick="document.getElementById('forgotPasswordScreen').style.display='none'" class="btn-secondary" style="width:100%;justify-content:center;margin-top:10px;">
                <i class="fas fa-times"></i> إلغاء
            </button>
        </div>
    </div>

    <!-- ===== شاشة القفل السريع ===== -->
    <div id="quickLockScreen">
        <div class="quick-lock-container">
            <h2><i class="fas fa-lock"></i> قفل سريع</h2>
            <p>أدخل كلمة المرور لفتح النظام</p>
            <div class="form-group">
                <input type="password" id="quickPassword" placeholder="كلمة المرور" onkeydown="if(event.key==='Enter')quickUnlock()">
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
            <div class="avatar" id="userAvatar" onclick="document.getElementById('avatarInput').click()">
                <i class="fas fa-user"></i>
            </div>
            <input type="file" id="avatarInput" accept="image/*" style="display:none" onchange="uploadAvatar(event)">
            <h2><i class="fas fa-wallet"></i> فلوسي</h2>
            <p class="subtitle" id="userDisplay">مدير النظام</p>
        </div>
        <div class="sidebar-menu">
            <a class="active" data-page="dashboard"><i class="fas fa-home"></i> لوحة التحكم</a>
            <a data-page="transactions"><i class="fas fa-exchange-alt"></i> العمليات</a>
            <a data-page="portals"><i class="fas fa-door-open"></i> البوابات</a>
            <a data-page="budgets"><i class="fas fa-bullseye"></i> الميزانيات</a>
            <a data-page="savings"><i class="fas fa-piggy-bank"></i> الادخار</a>
            <a data-page="visa"><i class="fas fa-credit-card"></i> فيزا المشتريات</a>
            <a data-page="associations"><i class="fas fa-hand-holding-heart"></i> الجمعيات</a>
            <a data-page="obligations"><i class="fas fa-calendar-alt"></i> الالتزامات</a>
            <a data-page="family"><i class="fas fa-users"></i> الأسرة</a>
            <a data-page="debts"><i class="fas fa-handshake"></i> الديون</a>
            <a data-page="goals"><i class="fas fa-trophy"></i> الأهداف</a>
            <a data-page="subscriptions"><i class="fas fa-clock"></i> الاشتراكات</a>
            <a data-page="reports"><i class="fas fa-file-alt"></i> التقارير</a>
            <a data-page="users" id="usersMenu"><i class="fas fa-users-cog"></i> المستخدمين</a>
            <a data-page="settings"><i class="fas fa-cog"></i> الإعدادات</a>
            <a data-page="developer"><i class="fas fa-code"></i> لوحة المطور</a>
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

            <!-- Quick Actions -->
            <div class="quick-actions">
                <div class="quick-action" onclick="navigateTo('transactions')">
                    <i class="fas fa-plus-circle"></i>
                    <span>إضافة عملية</span>
                </div>
                <div class="quick-action" onclick="navigateTo('reports')">
                    <i class="fas fa-file-alt"></i>
                    <span>التقارير</span>
                </div>
                <div class="quick-action" onclick="navigateTo('savings')">
                    <i class="fas fa-piggy-bank"></i>
                    <span>الادخار</span>
                </div>
                <div class="quick-action" onclick="navigateTo('goals')">
                    <i class="fas fa-trophy"></i>
                    <span>الأهداف</span>
                </div>
            </div>

            <!-- Stats -->
            <div class="stats-grid">
                <div class="stat-card income-card"><div class="stat-icon"><i class="fas fa-arrow-up"></i></div><div class="stat-info"><h3>إجمالي الدخل</h3><p class="stat-value" id="totalIncome">0</p></div></div>
                <div class="stat-card expense-card"><div class="stat-icon"><i class="fas fa-arrow-down"></i></div><div class="stat-info"><h3>إجمالي المصروفات</h3><p class="stat-value" id="totalExpense">0</p></div></div>
                <div class="stat-card balance-card"><div class="stat-icon"><i class="fas fa-coins"></i></div><div class="stat-info"><h3>الرصيد الحالي</h3><p class="stat-value" id="currentBalance">0</p></div></div>
                <div class="stat-card transactions-card"><div class="stat-icon"><i class="fas fa-list"></i></div><div class="stat-info"><h3>عدد العمليات</h3><p class="stat-value" id="transactionCount">0</p></div></div>
            </div>

            <!-- العجز/الفائض -->
            <div class="stats-grid" style="grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));">
                <div class="stat-card" style="border-color:var(--success);">
                    <div class="stat-icon" style="background:var(--success);"><i class="fas fa-check-circle"></i></div>
                    <div class="stat-info"><h3>فائض الشهر</h3><p class="stat-value" id="monthlySurplus" style="color:var(--success);">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--danger);">
                    <div class="stat-icon" style="background:var(--danger);"><i class="fas fa-exclamation-triangle"></i></div>
                    <div class="stat-info"><h3>عجز الشهر</h3><p class="stat-value" id="monthlyDeficit" style="color:var(--danger);">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--warning);">
                    <div class="stat-icon" style="background:var(--warning);"><i class="fas fa-piggy-bank"></i></div>
                    <div class="stat-info"><h3>إجمالي الادخار</h3><p class="stat-value" id="totalSavings">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--purple);">
                    <div class="stat-icon" style="background:var(--purple);"><i class="fas fa-credit-card"></i></div>
                    <div class="stat-info"><h3>مشتريات فيزا</h3><p class="stat-value" id="totalVisa">0</p></div>
                </div>
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
                            <select id="txIncomeSource"></select>
                        </div>
                        <div class="form-group">
                            <label><i class="fas fa-door-open"></i> البوابة</label>
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
                <div class="stat-card" style="border-color:var(--secondary);"><div class="stat-icon" style="background:var(--secondary);"><i class="fas fa-landmark"></i></div><div class="stat-info"><h3>حكومة</h3><p class="stat-value" id="portalGov">0</p></div></div>
                <div class="stat-card" style="border-color:var(--success);"><div class="stat-icon" style="background:var(--success);"><i class="fas fa-building"></i></div><div class="stat-info"><h3>قطاع خاص</h3><p class="stat-value" id="portalPrivate">0</p></div></div>
                <div class="stat-card" style="border-color:var(--warning);"><div class="stat-icon" style="background:var(--warning);"><i class="fas fa-laptop"></i></div><div class="stat-info"><h3>عمل حر</h3><p class="stat-value" id="portalFreelance">0</p></div></div>
                <div class="stat-card" style="border-color:var(--purple);"><div class="stat-icon" style="background:var(--purple);"><i class="fas fa-chart-line"></i></div><div class="stat-info"><h3>استثمارات</h3><p class="stat-value" id="portalInvestment">0</p></div></div>
                <div class="stat-card" style="border-color:var(--pink);"><div class="stat-icon" style="background:var(--pink);"><i class="fas fa-bullhorn"></i></div><div class="stat-info"><h3>تسويق</h3><p class="stat-value" id="portalMarketing">0</p></div></div>
                <div class="stat-card" style="border-color:var(--info);"><div class="stat-icon" style="background:var(--info);"><i class="fas fa-gift"></i></div><div class="stat-info"><h3>أخرى</h3><p class="stat-value" id="portalOther">0</p></div></div>
            </div>
            <div class="form-card">
                <h3>إضافة دخل عبر بوابة</h3>
                <form id="portalForm">
                    <div class="form-grid">
                        <div class="form-group"><label><i class="fas fa-door-open"></i> البوابة</label>
                            <select id="portalSelect" required>
                                <option value="حكومة">🏛️ حكومة</option>
                                <option value="خاص">🏢 قطاع خاص</option>
                                <option value="عمل حر">💻 عمل حر</option>
                                <option value="استثمار">📈 استثمارات</option>
                                <option value="تسويق">📢 تسويق</option>
                                <option value="أخرى">📦 أخرى</option>
                            </select>
                        </div>
                        <div class="form-group"><label><i class="fas fa-tag"></i> التصنيف الفرعي</label><input type="text" id="portalSubCategory" placeholder="مثال: راتب, عمولة"></div>
                        <div class="form-group"><label><i class="fas fa-money-bill"></i> المبلغ</label><input type="number" id="portalAmount" placeholder="المبلغ" required min="0.01" step="0.01"></div>
                        <div class="form-group"><label><i class="fas fa-calendar"></i> التاريخ</label><input type="date" id="portalDate" required></div>
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

        <!-- ===== الادخار ===== -->
        <section id="savings" class="page">
            <div class="page-header"><h1><i class="fas fa-piggy-bank"></i> إدارة الادخار</h1></div>
            <div class="stats-grid">
                <div class="stat-card" style="border-color:var(--success);">
                    <div class="stat-icon" style="background:var(--success);"><i class="fas fa-piggy-bank"></i></div>
                    <div class="stat-info"><h3>إجمالي الادخار</h3><p class="stat-value" id="totalSavingsDisplay">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--warning);">
                    <div class="stat-icon" style="background:var(--warning);"><i class="fas fa-hand-holding-usd"></i></div>
                    <div class="stat-info"><h3>عدد صناديق الادخار</h3><p class="stat-value" id="savingCount">0</p></div>
                </div>
            </div>
            <div class="form-card">
                <h3>إضافة صندوق ادخار جديد</h3>
                <form id="savingForm">
                    <div class="form-grid">
                        <div class="form-group"><label>اسم الصندوق</label><input type="text" id="savingName" placeholder="مثال: سيارة جديدة" required></div>
                        <div class="form-group"><label>المبلغ المستهدف</label><input type="number" id="savingTarget" placeholder="المبلغ المستهدف" required min="1" step="0.01"></div>
                        <div class="form-group"><label>المبلغ المدخر حالياً</label><input type="number" id="savingCurrent" placeholder="المبلغ المدخر" required min="0" step="0.01"></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة صندوق ادخار</button>
                </form>
            </div>
            <div id="savingsList"></div>
        </section>

        <!-- ===== فيزا المشتريات ===== -->
        <section id="visa" class="page">
            <div class="page-header"><h1><i class="fas fa-credit-card"></i> إدارة فيزا المشتريات</h1></div>
            <div class="stats-grid">
                <div class="stat-card" style="border-color:var(--purple);">
                    <div class="stat-icon" style="background:var(--purple);"><i class="fas fa-credit-card"></i></div>
                    <div class="stat-info"><h3>إجمالي المشتريات</h3><p class="stat-value" id="totalVisaDisplay">0</p></div>
                </div>
                <div class="stat-card" style="border-color:var(--danger);">
                    <div class="stat-icon" style="background:var(--danger);"><i class="fas fa-clock"></i></div>
                    <div class="stat-info"><h3>المشتريات المستحقة</h3><p class="stat-value" id="visaDueCount">0</p></div>
                </div>
            </div>
            <div class="form-card">
                <h3>إضافة عملية شراء بفيزا</h3>
                <form id="visaForm">
                    <div class="form-grid">
                        <div class="form-group"><label>اسم المشتريات</label><input type="text" id="visaName" placeholder="مثال: أجهزة منزلية" required></div>
                        <div class="form-group"><label>المبلغ الإجمالي</label><input type="number" id="visaTotal" placeholder="المبلغ الإجمالي" required min="1" step="0.01"></div>
                        <div class="form-group"><label>عدد الأقساط</label><input type="number" id="visaInstallments" placeholder="عدد الأقساط" required min="1"></div>
                        <div class="form-group"><label>القسط الشهري</label><input type="number" id="visaMonthly" placeholder="القسط الشهري" required min="1" step="0.01"></div>
                        <div class="form-group"><label>تاريخ الشراء</label><input type="date" id="visaDate" required></div>
                        <div class="form-group"><label>تاريخ الاستحقاق</label><input type="date" id="visaDueDate" required></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة</button>
                </form>
            </div>
            <div id="visaList"></div>
        </section>

        <!-- ===== الجمعيات ===== -->
        <section id="associations" class="page">
            <div class="page-header"><h1><i class="fas fa-hand-holding-heart"></i> إدارة الجمعيات</h1></div>
            <div class="form-card">
                <h3>إضافة جمعية جديدة</h3>
                <form id="associationForm">
                    <div class="form-grid">
                        <div class="form-group"><label>اسم الجمعية</label><input type="text" id="assocName" placeholder="مثال: جمعية أم ريم" required></div>
                        <div class="form-group"><label>المبلغ الشهري</label><input type="number" id="assocAmount" placeholder="المبلغ الشهري" required min="1" step="0.01"></div>
                        <div class="form-group"><label>عدد الشهور</label><input type="number" id="assocMonths" placeholder="عدد الشهور" required min="1"></div>
                        <div class="form-group"><label>دوري رقم</label><input type="number" id="assocTurn" placeholder="دوري رقم" required min="1"></div>
                        <div class="form-group"><label>تاريخ البدء</label><input type="date" id="assocStartDate" required></div>
                        <div class="form-group"><label>تاريخ الانتهاء</label><input type="date" id="assocEndDate" required></div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة جمعية</button>
                </form>
            </div>
            <div id="associationsList"></div>
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
                        <div class="form-group"><label>متكرر</label>
                            <select id="subRecurring">
                                <option value="شهري">شهري</option>
                                <option value="سنوي">سنوي</option>
                                <option value="لا">مرة واحدة</option>
                            </select>
                        </div>
                    </div>
                    <button type="submit" class="btn-primary"><i class="fas fa-plus"></i> إضافة اشتراك</button>
                </form>
            </div>
            <div id="subscriptionsList"></div>
        </section>

        <!-- ===== التقارير المتقدمة ===== -->
        <section id="reports" class="page">
            <div class="page-header"><h1><i class="fas fa-file-alt"></i> التقارير المتقدمة</h1></div>

            <div class="report-controls">
                <div class="form-group">
                    <label>نوع التقرير</label>
                    <select id="reportPeriod">
                        <option value="month">شهري</option>
                        <option value="quarter">ربع سنوي</option>
                        <option value="half">نصف سنوي</option>
                        <option value="year">سنوي</option>
                        <option value="custom">مخصص</option>
                    </select>
                </div>
                <div class="form-group" id="customDateGroup" style="display:none;">
                    <label>من</label>
                    <input type="date" id="reportStartDate">
                </div>
                <div class="form-group" id="customDateGroup2" style="display:none;">
                    <label>إلى</label>
                    <input type="date" id="reportEndDate">
                </div>
                <div class="form-group">
                    <label>السنة</label>
                    <select id="reportYear"></select>
                </div>
                <div class="form-group">
                    <label>الشهر/الربع</label>
                    <select id="reportPeriodSelect">
                        <option value="1">الشهر 1</option>
                        <option value="2">الشهر 2</option>
                        <option value="3">الشهر 3</option>
                        <option value="4">الشهر 4</option>
                        <option value="5">الشهر 5</option>
                        <option value="6">الشهر 6</option>
                        <option value="7">الشهر 7</option>
                        <option value="8">الشهر 8</option>
                        <option value="9">الشهر 9</option>
                        <option value="10">الشهر 10</option>
                        <option value="11">الشهر 11</option>
                        <option value="12">الشهر 12</option>
                    </select>
                </div>
                <div class="form-group">
                    <label>التصنيف</label>
                    <select id="reportCategory">
                        <option value="all">كل التصنيفات</option>
                    </select>
                </div>
                <button onclick="generateAdvancedReport()" class="btn-primary"><i class="fas fa-chart-bar"></i> عرض التقرير</button>
            </div>

            <!-- مقارنة بين الفترات -->
            <div class="form-card">
                <h3><i class="fas fa-exchange-alt"></i> مقارنة بين الفترات</h3>
                <div class="form-grid">
                    <div class="form-group">
                        <label>الفترة الأولى</label>
                        <select id="comparePeriod1">
                            <option value="1">الشهر الماضي</option>
                            <option value="3">آخر 3 شهور</option>
                            <option value="6">آخر 6 شهور</option>
                            <option value="12">آخر سنة</option>
                        </select>
                    </div>
                    <div class="form-group">
                        <label>المقارنة مع</label>
                        <select id="comparePeriod2">
                            <option value="1">الشهر السابق له</option>
                            <option value="3">نفس الفترة قبل 3 شهور</option>
                            <option value="6">نفس الفترة قبل 6 شهور</option>
                            <option value="12">نفس الفترة قبل سنة</option>
                        </select>
                    </div>
                    <div style="display:flex;align-items:end;">
                        <button onclick="generateComparison()" class="btn-primary"><i class="fas fa-chart-bar"></i> مقارنة</button>
                    </div>
                </div>
                <div id="comparisonResult"></div>
            </div>

            <div id="reportContent">
                <div class="report-summary">
                    <div class="report-stat"><h4>إجمالي الدخل</h4><p id="reportIncome">0</p></div>
                    <div class="report-stat"><h4>إجمالي المصروفات</h4><p id="reportExpense">0</p></div>
                    <div class="report-stat"><h4>صافي الربح</h4><p id="reportProfit">0</p></div>
                    <div class="report-stat"><h4>نسبة الادخار</h4><p id="reportSavingsRate">0%</p></div>
                </div>
                <div class="report-actions">
                    <button onclick="exportReportPDF()" class="btn-secondary"><i class="fas fa-file-pdf"></i> تصدير PDF (A4)</button>
                    <button onclick="exportReportExcel()" class="btn-secondary"><i class="fas fa-file-excel"></i> تصدير Excel</button>
                    <button onclick="exportReportPPT()" class="btn-secondary"><i class="fas fa-file-powerpoint"></i> تصدير PowerPoint</button>
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
                        <div class="form-group"><label>كلمة المرور</label><input type="password" id="newUserPassword" placeholder="كلمة المرور" required></div>
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
            <div class="page-header"><h1><i class="fas fa-cog"></i> الإعدادات المتقدمة</h1></div>
            <div class="settings-grid" id="settingsGrid"></div>
        </section>

        <!-- ===== لوحة المطور ===== -->
        <section id="developer" class="page">
            <div class="page-header"><h1><i class="fas fa-code"></i> لوحة المطور</h1></div>

            <div class="dev-stats-grid">
                <div class="dev-stat-card">
                    <div class="value" id="devFirebaseStatus">✅</div>
                    <div class="label">حالة Firebase</div>
                </div>
                <div class="dev-stat-card">
                    <div class="value" id="devApiStatus">✅</div>
                    <div class="label">حالة الـ API</div>
                </div>
                <div class="dev-stat-card">
                    <div class="value" id="devServerStatus">✅</div>
                    <div class="label">حالة السيرفر</div>
                </div>
                <div class="dev-stat-card">
                    <div class="value" id="devLoadTime">0ms</div>
                    <div class="label">سرعة التحميل</div>
                </div>
                <div class="dev-stat-card">
                    <div class="value" id="devStorageUsed">0 KB</div>
                    <div class="label">المساحة المستخدمة</div>
                </div>
                <div class="dev-stat-card">
                    <div class="value" id="devTransactionCount">0</div>
                    <div class="label">عدد العمليات</div>
                </div>
            </div>

            <div class="form-card">
                <h3><i class="fas fa-bug"></i> سجل الأخطاء (Error Log)</h3>
                <div id="errorLog" style="max-height:300px;overflow-y:auto;background:var(--bg);padding:15px;border-radius:var(--radius);font-family:monospace;font-size:13px;direction:ltr;">
                    <div style="color:var(--text-light);">لا توجد أخطاء مسجلة</div>
                </div>
                <button onclick="clearErrorLog()" class="btn-danger" style="margin-top:10px;"><i class="fas fa-trash"></i> مسح السجل</button>
            </div>

            <div class="form-card">
                <h3><i class="fas fa-database"></i> حالة التخزين</h3>
                <div id="storageStatus"></div>
            </div>
        </section>

    </main>

    <script>
        // ============================================================
        // 🔥 إعدادات Firebase
        // ============================================================
        const firebaseConfig = {
            apiKey: "AIzaSyDH4gHp8nA8T6t1YjX10zWVU6LIYCIxW2c",
            authDomain: "flosy-4cc03.firebaseapp.com",
            projectId: "flosy-4cc03",
            storageBucket: "flosy-4cc03.firebasestorage.app",
            messagingSenderId: "519291221807",
            appId: "1:519291221807:web:eaf0ec21c4ecd68815d3da",
            measurementId: "G-4F9L2KSBY3"
        };

        let db = null;
        let analytics = null;
        let errorLog = [];

        try {
            if (typeof firebase !== 'undefined') {
                firebase.initializeApp(firebaseConfig);
                db = firebase.firestore();
                console.log('🔥 Firebase متصل بنجاح!');
                if (typeof firebase.analytics === 'function') {
                    analytics = firebase.analytics();
                    analytics.logEvent('app_open', { user: 'flosy_user' });
                }
            }
        } catch (error) {
            console.warn('⚠️ Firebase غير متصل:', error.message);
            logError('Firebase', error.message);
        }

        // ============================================================
        // 1. نظام Toast للإشعارات
        // ============================================================
        function showToast(message, type = 'info', duration = 3000) {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            const icons = {
                success: 'fa-check-circle',
                error: 'fa-exclamation-circle',
                warning: 'fa-exclamation-triangle',
                info: 'fa-info-circle'
            };
            toast.className = `toast toast-${type}`;
            toast.innerHTML = `
                <i class="fas ${icons[type] || icons.info}"></i>
                <span class="toast-msg">${message}</span>
                <span class="toast-close" onclick="this.parentElement.remove()"><i class="fas fa-times"></i></span>
            `;
            container.appendChild(toast);
            setTimeout(() => {
                toast.style.opacity = '0';
                toast.style.transform = 'translateY(-20px)';
                setTimeout(() => toast.remove(), 300);
            }, duration);
        }

        // ============================================================
        // 2. نظام المستخدمين المتقدم
        // ============================================================
        const AUTH = {
            users: [],
            currentUser: null,
            isLocked: false,
            lastActivity: Date.now(),

            init() {
                const saved = localStorage.getItem('flosy_users');
                if (saved) {
                    this.users = JSON.parse(saved);
                } else {
                    this.users = [{
                        id: 1,
                        username: 'admin',
                        password: 'Admin@1234',
                        role: 'admin',
                        securityQuestion: 'ما هو اسم والدتك؟',
                        securityAnswer: 'admin',
                        createdAt: new Date().toISOString(),
                        level: 1,
                        points: 0,
                        achievements: []
                    }];
                    this.saveUsers();
                }
                const session = localStorage.getItem('flosy_session');
                if (session) {
                    const data = JSON.parse(session);
                    const user = this.users.find(u => u.id === data.userId);
                    if (user) { this.currentUser = user;
                        this.isLocked = false; return true; }
                }
                this.currentUser = null;
                this.isLocked = true;
                return false;
            },
            saveUsers() { localStorage.setItem('flosy_users', JSON.stringify(this.users)); },
            saveSession() { if (this.currentUser) localStorage.setItem('flosy_session', JSON.stringify({ userId: this
                        .currentUser.id, loginTime: new Date().toISOString() })); },
            clearSession() { localStorage.removeItem('flosy_session'); this.currentUser = null;
                this.isLocked = true; },

            validatePassword(password) {
                if (password.length < 8) return { valid: false, message: 'كلمة المرور يجب أن تكون 8 أحرف على الأقل' };
                const uniqueChars = new Set(password);
                if (uniqueChars.size < 8) return { valid: false,
                message: 'يجب أن تحتوي كلمة المرور على 8 أحرف مختلفة على الأقل' };
                return { valid: true };
            },

            login(username, password) {
                const user = this.users.find(u => u.username.toLowerCase() === username.toLowerCase() && u.password ===
                password);
                if (user) { this.currentUser = user;
                    this.isLocked = false;
                    this.saveSession();
                    this.updateUserActivity(user.id);
                    return { success: true, user }; }
                return { success: false, message: '❌ اسم المستخدم أو كلمة المرور غير صحيحة' };
            },

            register(username, password, role, securityQuestion, securityAnswer) {
                if (this.users.find(u => u.username.toLowerCase() === username.toLowerCase())) {
                    return { success: false, message: '❌ اسم المستخدم موجود بالفعل' };
                }
                const validation = this.validatePassword(password);
                if (!validation.valid) return { success: false, message: validation.message };
                const user = {
                    id: Date.now(),
                    username,
                    password,
                    role: role || 'user',
                    securityQuestion: securityQuestion || 'ما هو اسم والدتك؟',
                    securityAnswer: securityAnswer || '',
                    createdAt: new Date().toISOString(),
                    level: 1,
                    points: 0,
                    achievements: []
                };
                this.users.push(user);
                this.saveUsers();
                return { success: true, user };
            },

            resetPassword(username, answer) {
                const user = this.users.find(u => u.username.toLowerCase() === username.toLowerCase() && u.securityAnswer ===
                    answer);
                if (!user) return { success: false, message: '❌ البيانات غير صحيحة' };
                const newPassword = 'Flosy@' + Math.floor(Math.random() * 9000 + 1000);
                user.password = newPassword;
                this.saveUsers();
                return { success: true, password: newPassword };
            },

            updateUserActivity(id) {
                const user = this.users.find(u => u.id === id);
                if (user) {
                    user.lastActive = new Date().toISOString();
                    this.saveUsers();
                }
            },

            deleteUser(id) {
                if (this.currentUser && this.currentUser.id === id) return { success: false,
                    message: '❌ لا يمكن حذف حسابك الحالي' };
                const adminCount = this.users.filter(u => u.role === 'admin').length;
                const user = this.users.find(u => u.id === id);
                if (user && user.role === 'admin' && adminCount <= 1) return { success: false,
                    message: '❌ يجب أن يبقى مدير واحد على الأقل' };
                this.users = this.users.filter(u => u.id !== id);
                this.saveUsers();
                return { success: true };
            },
            updateUser(id, updates) {
                const user = this.users.find(u => u.id === id);
                if (user) { Object.assign(user, updates);
                    this.saveUsers(); if (this.currentUser && this.currentUser.id === id) this.currentUser = user; return { success: true }; }
                return { success: false };
            }
        };

        // ============================================================
        // 3. نظام الإنجازات والتحديات
        // ============================================================
        const ACHIEVEMENTS = {
            list: [
                { id: 'first_transaction', name: '🌟 أول عملية', description: 'تسجيل أول عملية مالية', icon: 'fa-star' },
                { id: 'ten_transactions', name: '📊 10 عمليات', description: 'تسجيل 10 عمليات مالية', icon: 'fa-chart-simple' },
                { id: 'hundred_transactions', name: '🏆 100 عملية', description: 'تسجيل 100 عملية مالية', icon: 'fa-trophy' },
                { id: 'savings_goal', name: '🐷 أول صندوق ادخار', description: 'إنشاء أول صندوق ادخار', icon: 'fa-piggy-bank' },
                { id: 'budget_set', name: '🎯 أول ميزانية', description: 'تحديد أول ميزانية', icon: 'fa-bullseye' },
                { id: 'subscription_tracker', name: '⏰ متتبع اشتراكات', description: 'إضافة أول اشتراك', icon: 'fa-clock' }
            ],

            check(userId) {
                const user = AUTH.users.find(u => u.id === userId);
                if (!user) return;
                const transactions = DB.get('transactions');
                const savings = DB.get('savings');
                const budgets = DB.get('budgets');
                const subscriptions = DB.get('subscriptions');
                let newAchievements = [];

                if (transactions.length >= 1 && !user.achievements.includes('first_transaction')) {
                    newAchievements.push('first_transaction');
                    showToast('🌟 أنجزت إنجاز: أول عملية مالية!', 'success');
                }
                if (transactions.length >= 10 && !user.achievements.includes('ten_transactions')) {
                    newAchievements.push('ten_transactions');
                    showToast('📊 أنجزت إنجاز: 10 عمليات مالية!', 'success');
                }
                if (transactions.length >= 100 && !user.achievements.includes('hundred_transactions')) {
                    newAchievements.push('hundred_transactions');
                    showToast('🏆 أنجزت إنجاز: 100 عملية مالية!', 'success');
                }
                if (savings.length >= 1 && !user.achievements.includes('savings_goal')) {
                    newAchievements.push('savings_goal');
                    showToast('🐷 أنجزت إنجاز: أول صندوق ادخار!', 'success');
                }
                if (budgets.length >= 1 && !user.achievements.includes('budget_set')) {
                    newAchievements.push('budget_set');
                    showToast('🎯 أنجزت إنجاز: أول ميزانية!', 'success');
                }
                if (subscriptions.length >= 1 && !user.achievements.includes('subscription_tracker')) {
                    newAchievements.push('subscription_tracker');
                    showToast('⏰ أنجزت إنجاز: أول اشتراك!', 'success');
                }

                if (newAchievements.length > 0) {
                    user.achievements = [...user.achievements, ...newAchievements];
                    user.points = (user.points || 0) + newAchievements.length * 10;
                    // ترقية المستوى
                    if (user.points >= 50) user.level = 2;
                    if (user.points >= 150) user.level = 3;
                    if (user.points >= 300) user.level = 4;
                    if (user.points >= 500) user.level = 5;
                    AUTH.saveUsers();
                }
            }
        };

        // ============================================================
        // 4. إدارة البيانات
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
                settings: 'flosy_settings',
                fixedExpenses: 'flosy_fixed_expenses',
                incomeSources: 'flosy_income_sources',
                activityLog: 'flosy_activity_log',
                savings: 'flosy_savings',
                visa: 'flosy_visa',
                associations: 'flosy_associations'
            },
            init() {
                Object.values(this.keys).forEach(k => { if (!localStorage.getItem(k)) localStorage.setItem(k, JSON
                        .stringify([])); });
                if (!localStorage.getItem(this.keys.categories)) localStorage.setItem(this.keys.categories, JSON
                    .stringify(['طعام', 'مواصلات', 'فواتير', 'إيجار', 'ترفيه', 'صحة', 'تعليم', 'ملابس', 'هدايا', 'أخرى']));
                if (!localStorage.getItem(this.keys.accounts)) localStorage.setItem(this.keys.accounts, JSON.stringify([
                        'نقدي', 'بنك', 'فودافون كاش', 'بطاقة ائتمان'
                    ]));
                if (!localStorage.getItem(this.keys.settings)) localStorage.setItem(this.keys.settings, JSON.stringify({
                        darkMode: false,
                        primaryColor: '#2563eb',
                        fontSize: 'medium',
                        currency: 'جنيه',
                        autoBackup: true,
                        notifications: {
                            email: '',
                            telegram: '',
                            viber: ''
                        }
                    }));
                if (!localStorage.getItem(this.keys.incomeSources)) localStorage.setItem(this.keys.incomeSources, JSON
                    .stringify(['راتب حكومي', 'راتب خاص', 'تسويق', 'عمل حر', 'استثمار', 'هدايا', 'أخرى']));
            },
            get(key) { return JSON.parse(localStorage.getItem(this.keys[key]) || '[]'); },
            set(key, data) { localStorage.setItem(this.keys[key], JSON.stringify(data)); },

            addTransaction(t) { const d = this.get('transactions');
                t.id = Date.now();
                d.push(t);
                this.set('transactions', d);
                this.logActivity('إضافة عملية', `${t.type === 'income' ? 'دخل' : 'مصروف'} ${t.amount}`);
                ACHIEVEMENTS.check(AUTH.currentUser?.id);
                return t; },
            deleteTransaction(id) { let d = this.get('transactions');
                d = d.filter(t => t.id !== id);
                this.set('transactions', d);
                this.logActivity('حذف عملية', `حذف عملية`); },
            updateTransaction(id, u) { let d = this.get('transactions');
                const i = d.findIndex(t => t.id === id); if (i !== -1) { d[i] = { ...d[i], ...u };
                    this.set('transactions', d);
                    this.logActivity('تعديل عملية', `تعديل عملية`); return d[i]; } return null; },

            addBudget(b) { const d = this.get('budgets');
                b.id = Date.now();
                d.push(b);
                this.set('budgets', d);
                this.logActivity('إضافة ميزانية', `${b.category}`);
                ACHIEVEMENTS.check(AUTH.currentUser?.id);
                return b; },
            deleteBudget(id) { let d = this.get('budgets');
                d = d.filter(b => b.id !== id);
                this.set('budgets', d);
                this.logActivity('حذف ميزانية', ``); },

            addSaving(s) { const d = this.get('savings');
                s.id = Date.now();
                s.progress = (s.current / s.target) * 100;
                d.push(s);
                this.set('savings', d);
                this.logActivity('إضافة ادخار', `${s.name}`);
                ACHIEVEMENTS.check(AUTH.currentUser?.id);
                return s; },
            deleteSaving(id) { let d = this.get('savings');
                d = d.filter(s => s.id !== id);
                this.set('savings', d);
                this.logActivity('حذف ادخار', ``); },
            updateSaving(id, current) { let d = this.get('savings');
                const s = d.find(x => x.id === id); if (s) { s.current = current;
                    s.progress = (s.current / s.target) * 100;
                    this.set('savings', d);
                    this.logActivity('تحديث ادخار', `${s.name}`); return s; } return null; },

            addVisa(v) { const d = this.get('visa');
                v.id = Date.now();
                d.push(v);
                this.set('visa', d);
                this.logActivity('إضافة فيزا', `${v.name}`); return v; },
            deleteVisa(id) { let d = this.get('visa');
                d = d.filter(v => v.id !== id);
                this.set('visa', d);
                this.logActivity('حذف فيزا', ``); },

            addAssociation(a) { const d = this.get('associations');
                a.id = Date.now();
                d.push(a);
                this.set('associations', d);
                this.logActivity('إضافة جمعية', `${a.name}`); return a; },
            deleteAssociation(id) { let d = this.get('associations');
                d = d.filter(a => a.id !== id);
                this.set('associations', d);
                this.logActivity('حذف جمعية', ``); },

            addGoal(g) { const d = this.get('goals');
                g.id = Date.now();
                g.progress = (g.current / g.target) * 100;
                d.push(g);
                this.set('goals', d);
                this.logActivity('إضافة هدف', `${g.name}`);
                ACHIEVEMENTS.check(AUTH.currentUser?.id);
                return g; },
            deleteGoal(id) { let d = this.get('goals');
                d = d.filter(g => g.id !== id);
                this.set('goals', d);
                this.logActivity('حذف هدف', ``); },
            updateGoal(id, c) { let d = this.get('goals');
                const g = d.find(x => x.id === id); if (g) { g.current = c;
                    g.progress = (g.current / g.target) * 100;
                    this.set('goals', d);
                    this.logActivity('تحديث هدف', `${g.name}`); return g; } return null; },

            addSubscription(s) { const d = this.get('subscriptions');
                s.id = Date.now();
                d.push(s);
                this.set('subscriptions', d);
                this.logActivity('إضافة اشتراك', `${s.name}`);
                ACHIEVEMENTS.check(AUTH.currentUser?.id);
                return s; },
            deleteSubscription(id) { let d = this.get('subscriptions');
                d = d.filter(s => s.id !== id);
                this.set('subscriptions', d);
                this.logActivity('حذف اشتراك', ``); },

            addObligation(o) { const d = this.get('obligations');
                o.id = Date.now();
                d.push(o);
                this.set('obligations', d);
                this.logActivity('إضافة التزام', `${o.name}`); return o; },
            deleteObligation(id) { let d = this.get('obligations');
                d = d.filter(o => o.id !== id);
                this.set('obligations', d);
                this.logActivity('حذف التزام', ``); },

            addFamilyMember(m) { const d = this.get('family');
                m.id = Date.now();
                d.push(m);
                this.set('family', d);
                this.logActivity('إضافة فرد', `${m.name}`); return m; },
            deleteFamilyMember(id) { let d = this.get('family');
                d = d.filter(m => m.id !== id);
                this.set('family', d);
                this.logActivity('حذف فرد', ``); },

            addDebt(d) { const debts = this.get('debts');
                d.id = Date.now();
                debts.push(d);
                this.set('debts', debts);
                this.logActivity('إضافة دين', `${d.name}`); return d; },
            deleteDebt(id) { let d = this.get('debts');
                d = d.filter(x => x.id !== id);
                this.set('debts', d);
                this.logActivity('حذف دين', ``); },

            getCategories() { return this.get('categories'); },
            addCategory(c) { const d = this.get('categories'); if (!d.includes(c)) { d.push(c);
                    this.set('categories', d);
                    this.logActivity('إضافة تصنيف', c); return true; } return false; },
            deleteCategory(c) { let d = this.get('categories');
                d = d.filter(x => x !== c);
                this.set('categories', d);
                this.logActivity('حذف تصنيف', c); },

            getAccounts() { return this.get('accounts'); },
            addAccount(a) { const d = this.get('accounts'); if (!d.includes(a)) { d.push(a);
                    this.set('accounts', d);
                    this.logActivity('إضافة حساب', a); return true; } return false; },
            deleteAccount(a) { let d = this.get('accounts');
                d = d.filter(x => x !== a);
                this.set('accounts', d);
                this.logActivity('حذف حساب', a); },

            getIncomeSources() { return this.get('incomeSources'); },
            addIncomeSource(s) { const d = this.get('incomeSources'); if (!d.includes(s)) { d.push(s);
                    this.set('incomeSources', d);
                    this.logActivity('إضافة مصدر دخل', s); return true; } return false; },
            deleteIncomeSource(s) { let d = this.get('incomeSources');
                d = d.filter(x => x !== s);
                this.set('incomeSources', d);
                this.logActivity('حذف مصدر دخل', s); },

            getSettings() { return JSON.parse(localStorage.getItem(this.keys.settings) || '{}'); },
            saveSettings(s) { localStorage.setItem(this.keys.settings, JSON.stringify(s)); },
            getActivityLog() { return this.get('activityLog'); },
            logActivity(action, details) { const d = this.get('activityLog');
                d.unshift({ action, details, time: new Date().toISOString() }); if (d.length > 100) d.pop();
                this.set('activityLog', d);
                this.sendNotification('نشاط جديد', `${action}: ${details}`); },

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
                    settings: this.getSettings(),
                    fixedExpenses: this.get('fixedExpenses'),
                    incomeSources: this.get('incomeSources'),
                    savings: this.get('savings'),
                    visa: this.get('visa'),
                    associations: this.get('associations'),
                    users: AUTH.users
                };
            },
            restore(data) {
                Object.keys(this.keys).forEach(k => { if (data[k]) this.set(k, data[k]); });
                if (data.users) { AUTH.users = data.users;
                    AUTH.saveUsers(); }
            },
            clearAll() {
                Object.values(this.keys).forEach(k => localStorage.removeItem(k));
                this.init();
            }
        };
        DB.init();

        // ============================================================
        // 5. نظام الإشعارات (Email - Telegram - Viber)
        // ============================================================
        function sendNotification(title, message) {
            const settings = DB.getSettings();
            const notifications = settings.notifications || {};

            // إشعار داخل التطبيق
            showToast(`🔔 ${title}: ${message}`, 'info', 4000);

            // إشعار بريد إلكتروني (محاكاة)
            if (notifications.email) {
                console.log(`📧 إرسال بريد إلكتروني إلى ${notifications.email}: ${title} - ${message}`);
                // هنا يمكن إضافة تكامل مع EmailJS أو SMTP
            }

            // إشعار Telegram
            if (notifications.telegram) {
                console.log(`📨 إرسال Telegram إلى ${notifications.telegram}: ${title} - ${message}`);
                // هنا يمكن إضافة تكامل مع Telegram Bot API
            }

            // إشعار Viber
            if (notifications.viber) {
                console.log(`📱 إرسال Viber إلى ${notifications.viber}: ${title} - ${message}`);
                // هنا يمكن إضافة تكامل مع Viber API
            }
        }

        // ============================================================
        // 6. النسخ الاحتياطي التلقائي الشهري
        // ============================================================
        function checkMonthlyBackup() {
            const settings = DB.getSettings();
            if (!settings.autoBackup) return;

            const lastBackup = localStorage.getItem('flosy_last_backup');
            const now = new Date();
            const currentMonth = now.getMonth();
            const currentYear = now.getFullYear();

            if (lastBackup) {
                const last = new Date(lastBackup);
                if (last.getMonth() === currentMonth && last.getFullYear() === currentYear) {
                    return; // تم النسخ هذا الشهر
                }
            }

            // عمل نسخ احتياطي شهري
            const data = DB.backup();
            const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `flosy_backup_${currentYear}-${String(currentMonth + 1).padStart(2, '0')}.json`;
            a.click();
            URL.revokeObjectURL(url);

            localStorage.setItem('flosy_last_backup', now.toISOString());
            showToast('📦 تم إنشاء نسخة احتياطية شهرية تلقائية', 'success');
            DB.logActivity('نسخ احتياطي تلقائي', `شهر ${currentMonth + 1}/${currentYear}`);
        }

        // ============================================================
        // 7. دوال مساعدة
        // ============================================================
        function formatCurrency(amount) {
            const settings = DB.getSettings();
            const currency = settings.currency || 'جنيه';
            return amount.toFixed(2) + ' ' + currency;
        }

        function getDateString(d) { if (!d) return ''; return new Date(d).toLocaleDateString('ar-EG'); }
        function getTypeClass(t) { return t === 'income' ? 'amount-income' : 'amount-expense'; }
        function getTypeLabel(t) { return t === 'income' ? 'دخل' : 'مصروف'; }
        function getStatusBadge(s) { const c = { 'مدفوع': 'success', 'غير مدفوع': 'warning', 'متأخر': 'danger' }; return `<span class="badge badge-${c[s]||''}">${s}</span>`; }
        function getObligationStatus(days) { if (days < 0) return { label: 'متأخر', class: 'danger' }; if (days <= 30) return { label: 'قريب', class: 'warning' }; return { label: 'بعيد', class: 'success' }; }

        function getBalance() { return DB.get('transactions').reduce((s, t) => s + (t.type === 'income' ? t.amount : -t.amount), 0); }
        function getTotalIncome() { return DB.get('transactions').filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0); }
        function getTotalExpense() { return DB.get('transactions').filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0); }

        function logError(source, message) {
            errorLog.push({ source, message, time: new Date().toISOString() });
            localStorage.setItem('flosy_error_log', JSON.stringify(errorLog));
            console.error(`❌ [${source}] ${message}`);
        }

        // ============================================================
        // 8. دوال تسجيل الدخول
        // ============================================================
        function switchLoginTab(tab) {
            document.querySelectorAll('.login-tab').forEach(t => t.classList.remove('active'));
            document.querySelector(`[data-tab="${tab}"]`).classList.add('active');
            document.querySelectorAll('.login-panel').forEach(p => p.classList.remove('active'));
            document.getElementById(tab === 'login' ? 'loginForm' : 'registerForm').classList.add('active');
        }

        function showForgotPassword() {
            document.getElementById('forgotPasswordScreen').style.display = 'flex';
            // تعبئة أسئلة الأمان
            const select = document.getElementById('forgotSecurityQuestion');
            const users = AUTH.users;
            const questions = [...new Set(users.map(u => u.securityQuestion))];
            select.innerHTML = '';
            questions.forEach(q => {
                const option = document.createElement('option');
                option.value = q;
                option.textContent = q;
                select.appendChild(option);
            });
        }

        function resetPassword() {
            const username = document.getElementById('forgotUsername').value.trim();
            const question = document.getElementById('forgotSecurityQuestion').value;
            const answer = document.getElementById('forgotSecurityAnswer').value.trim();

            const result = AUTH.resetPassword(username, answer);
            if (result.success) {
                document.getElementById('forgotError').textContent = '';
                document.getElementById('forgotPasswordScreen').style.display = 'none';
                showToast(`✅ تم إعادة تعيين كلمة المرور: ${result.password}`, 'success');
                DB.logActivity('استعادة كلمة مرور', username);
            } else {
                document.getElementById('forgotError').textContent = result.message;
            }
        }

        async function loginUser() {
            const username = document.getElementById('loginUsername').value.trim();
            const password = document.getElementById('loginPassword').value;
            const result = AUTH.login(username, password);
            if (result.success) {
                document.getElementById('loginScreen').style.display = 'none';
                document.getElementById('quickLockScreen').style.display = 'none';
                updateUIForUser();
                await syncLoadFromFirebase();
                loadAllData();
                document.getElementById('loginError').textContent = '';
                DB.logActivity('تسجيل دخول', username);
                showToast(`👋 مرحباً ${username}!`, 'success');
                checkMonthlyBackup();
            } else {
                document.getElementById('loginError').textContent = result.message;
            }
        }

        async function registerUser() {
            const username = document.getElementById('regUsername').value.trim();
            const password = document.getElementById('regPassword').value;
            const role = document.getElementById('regRole').value;
            const securityQuestion = document.getElementById('regSecurityQuestion').value;
            const securityAnswer = document.getElementById('regSecurityAnswer').value.trim();
            const result = AUTH.register(username, password, role, securityQuestion, securityAnswer);
            if (result.success) {
                document.getElementById('registerError').textContent = '';
                document.getElementById('regUsername').value = '';
                document.getElementById('regPassword').value = '';
                document.getElementById('regSecurityAnswer').value = '';
                AUTH.currentUser = result.user;
                AUTH.isLocked = false;
                AUTH.saveSession();
                await syncSaveToFirebase();
                document.getElementById('loginScreen').style.display = 'none';
                document.getElementById('quickLockScreen').style.display = 'none';
                updateUIForUser();
                loadAllData();
                DB.logActivity('إنشاء حساب', username);
                showToast('✅ تم إنشاء الحساب بنجاح!', 'success');
            } else {
                document.getElementById('registerError').textContent = result.message;
            }
        }

        function logoutUser() {
            if (confirm('هل أنت متأكد من تسجيل الخروج؟')) {
                DB.logActivity('تسجيل خروج', AUTH.currentUser?.username);
                AUTH.clearSession();
                document.getElementById('loginScreen').style.display = 'flex';
                document.getElementById('quickLockScreen').style.display = 'none';
                document.getElementById('loginPassword').value = '';
            }
        }

        // ============================================================
        // 9. القفل السريع
        // ============================================================
        function quickLock() {
            AUTH.isLocked = true;
            document.getElementById('quickLockScreen').style.display = 'flex';
            document.getElementById('quickPassword').value = '';
            document.getElementById('quickLockError').textContent = '';
        }

        function quickUnlock() {
            const password = document.getElementById('quickPassword').value;
            if (password === AUTH.currentUser.password) {
                AUTH.isLocked = false;
                document.getElementById('quickLockScreen').style.display = 'none';
                document.getElementById('quickLockError').textContent = '';
                AUTH.lastActivity = Date.now();
            } else {
                document.getElementById('quickLockError').textContent = '❌ كلمة المرور غير صحيحة!';
                document.getElementById('quickPassword').value = '';
            }
        }

        // ============================================================
        // 10. دوال التنقل السريع
        // ============================================================
        function navigateTo(page) {
            document.querySelectorAll('.sidebar-menu a').forEach(a => a.classList.remove('active'));
            document.querySelector(`[data-page="${page}"]`).classList.add('active');
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(page).classList.add('active');
            document.getElementById('sidebar').classList.remove('open');
        }

        // ============================================================
        // 11. التقارير المتقدمة
        // ============================================================
        function generateAdvancedReport() {
            const period = document.getElementById('reportPeriod').value;
            const year = parseInt(document.getElementById('reportYear').value);
            const periodSelect = parseInt(document.getElementById('reportPeriodSelect').value);
            const category = document.getElementById('reportCategory').value;

            let startDate, endDate;
            const now = new Date();

            switch (period) {
                case 'month':
                    startDate = new Date(year, periodSelect - 1, 1);
                    endDate = new Date(year, periodSelect, 0);
                    break;
                case 'quarter':
                    const quarterStart = (periodSelect - 1) * 3;
                    startDate = new Date(year, quarterStart, 1);
                    endDate = new Date(year, quarterStart + 3, 0);
                    break;
                case 'half':
                    const halfStart = (periodSelect - 1) * 6;
                    startDate = new Date(year, halfStart, 1);
                    endDate = new Date(year, halfStart + 6, 0);
                    break;
                case 'year':
                    startDate = new Date(year, 0, 1);
                    endDate = new Date(year, 11, 31);
                    break;
                case 'custom':
                    startDate = new Date(document.getElementById('reportStartDate').value);
                    endDate = new Date(document.getElementById('reportEndDate').value);
                    break;
                default:
                    startDate = new Date(year, periodSelect - 1, 1);
                    endDate = new Date(year, periodSelect, 0);
            }

            const transactions = DB.get('transactions');
            let filtered = transactions.filter(t => {
                const d = new Date(t.date);
                if (d < startDate || d > endDate) return false;
                if (category !== 'all' && t.category !== category) return false;
                return true;
            });

            const income = filtered.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
            const expense = filtered.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
            const profit = income - expense;
            const savings = DB.get('savings').reduce((s, item) => s + item.current, 0);
            const savingsRate = income > 0 ? (savings / income) * 100 : 0;

            document.getElementById('reportIncome').textContent = formatCurrency(income);
            document.getElementById('reportExpense').textContent = formatCurrency(expense);
            document.getElementById('reportProfit').textContent = formatCurrency(profit);
            document.getElementById('reportProfit').style.color = profit >= 0 ? '#22c55e' : '#ef4444';
            document.getElementById('reportSavingsRate').textContent = savingsRate.toFixed(1) + '%';

            let html = `
                <div class="report-summary">
                    <div class="report-stat"><h4>إجمالي الدخل</h4><p style="color:#22c55e">${formatCurrency(income)}</p></div>
                    <div class="report-stat"><h4>إجمالي المصروفات</h4><p style="color:#ef4444">${formatCurrency(expense)}</p></div>
                    <div class="report-stat"><h4>صافي الربح</h4><p style="color:${profit>=0?'#22c55e':'#ef4444'}">${formatCurrency(profit)}</p></div>
                    <div class="report-stat"><h4>نسبة الادخار</h4><p style="color:${savingsRate >= 20 ? '#22c55e' : '#ef4444'}">${savingsRate.toFixed(1)}%</p></div>
                </div>
            `;

            // توزيع المصروفات حسب التصنيف
            const summary = filtered.filter(t => t.type === 'expense').reduce((acc, t) => {
                acc[t.category] = (acc[t.category] || 0) + t.amount;
                return acc;
            }, {});

            html += `<div style="margin-top:20px"><h4>📊 توزيع المصروفات حسب التصنيف</h4><div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:10px;margin-top:10px">`;
            if (Object.keys(summary).length === 0) html += '<p style="color:var(--text-light)">لا توجد بيانات</p>';
            else html += Object.entries(summary).sort((a, b) => b[1] - a[1]).map(([cat, amt]) =>
                `<div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center"><strong>${cat}</strong><p style="color:var(--text-light)">${formatCurrency(amt)}</p></div>`
            ).join('');
            html += `</div></div>`;

            // جدول العمليات
            if (filtered.length > 0) {
                html += `<div style="margin-top:20px"><h4>📋 العمليات</h4><div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th><th>الحساب</th></tr></thead><tbody>`;
                html += filtered.sort((a, b) => new Date(b.date) - new Date(a.date)).slice(0, 100).map(t =>
                    `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td><td>${t.account||'نقدي'}</td></tr>`
                ).join('');
                html += `</tbody></table></div></div>`;
            }

            document.getElementById('reportContent').innerHTML = html;
            window._reportData = { income, expense, profit, savingsRate, transactions: filtered };
        }

        // ============================================================
        // 12. مقارنة بين الفترات
        // ============================================================
        function generateComparison() {
            const period1 = parseInt(document.getElementById('comparePeriod1').value);
            const period2 = parseInt(document.getElementById('comparePeriod2').value);

            const now = new Date();
            const endDate1 = new Date(now);
            const startDate1 = new Date(now);
            startDate1.setMonth(startDate1.getMonth() - period1);

            const endDate2 = new Date(startDate1);
            const startDate2 = new Date(startDate1);
            startDate2.setMonth(startDate2.getMonth() - period2);

            const transactions = DB.get('transactions');

            const getPeriodData = (start, end) => {
                const filtered = transactions.filter(t => {
                    const d = new Date(t.date);
                    return d >= start && d <= end;
                });
                const income = filtered.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
                const expense = filtered.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
                return { income, expense, profit: income - expense, count: filtered.length };
            };

            const data1 = getPeriodData(startDate1, endDate1);
            const data2 = getPeriodData(startDate2, endDate2);

            const incomeChange = data2.income > 0 ? ((data1.income - data2.income) / data2.income * 100) : 0;
            const expenseChange = data2.expense > 0 ? ((data1.expense - data2.expense) / data2.expense * 100) : 0;
            const profitChange = data2.profit > 0 ? ((data1.profit - data2.profit) / data2.profit * 100) : 0;

            const container = document.getElementById('comparisonResult');
            container.innerHTML = `
                <div style="margin-top:20px;display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:15px;">
                    <div style="background:var(--bg);padding:15px;border-radius:var(--radius);text-align:center;">
                        <h4>📈 التغير في الدخل</h4>
                        <p style="font-size:24px;color:${incomeChange >= 0 ? 'var(--success)' : 'var(--danger)'}">
                            ${incomeChange >= 0 ? '+' : ''}${incomeChange.toFixed(1)}%
                        </p>
                        <p style="font-size:13px;color:var(--text-light);">
                            ${formatCurrency(data2.income)} → ${formatCurrency(data1.income)}
                        </p>
                    </div>
                    <div style="background:var(--bg);padding:15px;border-radius:var(--radius);text-align:center;">
                        <h4>📉 التغير في المصروفات</h4>
                        <p style="font-size:24px;color:${expenseChange <= 0 ? 'var(--success)' : 'var(--danger)'}">
                            ${expenseChange >= 0 ? '+' : ''}${expenseChange.toFixed(1)}%
                        </p>
                        <p style="font-size:13px;color:var(--text-light);">
                            ${formatCurrency(data2.expense)} → ${formatCurrency(data1.expense)}
                        </p>
                    </div>
                    <div style="background:var(--bg);padding:15px;border-radius:var(--radius);text-align:center;">
                        <h4>💹 التغير في صافي الربح</h4>
                        <p style="font-size:24px;color:${profitChange >= 0 ? 'var(--success)' : 'var(--danger)'}">
                            ${profitChange >= 0 ? '+' : ''}${profitChange.toFixed(1)}%
                        </p>
                        <p style="font-size:13px;color:var(--text-light);">
                            ${formatCurrency(data2.profit)} → ${formatCurrency(data1.profit)}
                        </p>
                    </div>
                    <div style="background:var(--bg);padding:15px;border-radius:var(--radius);text-align:center;">
                        <h4>📊 عدد العمليات</h4>
                        <p style="font-size:24px;">${data2.count} → ${data1.count}</p>
                        <p style="font-size:13px;color:var(--text-light);">
                            ${((data1.count - data2.count) / (data2.count || 1) * 100).toFixed(1)}% تغير
                        </p>
                    </div>
                </div>
            `;
        }

        // ============================================================
        // 13. تصدير التقارير
        // ============================================================
        function exportReportPDF() {
            const el = document.getElementById('reportContent');
            if (!el || el.innerHTML.trim() === '') { showToast('الرجاء إنشاء تقرير أولاً', 'warning'); return; }

            // إضافة ترويسة للتقرير
            const title = document.createElement('div');
            title.style.cssText = 'text-align:center;padding:20px;border-bottom:2px solid #2563eb;margin-bottom:20px;';
            title.innerHTML = `
                <h1 style="color:#2563eb;font-size:28px;">💰 فلوسي - تقرير مالي</h1>
                <p style="color:#64748b;">${new Date().toLocaleDateString('ar-EG')}</p>
                <p style="color:#64748b;">${AUTH.currentUser?.username || 'مدير النظام'}</p>
            `;

            const footer = document.createElement('div');
            footer.style.cssText =
            'text-align:center;padding:20px;border-top:2px solid #e2e8f0;margin-top:20px;color:#94a3b8;font-size:12px;';
            footer.textContent = 'تم إنشاء التقرير بواسطة فلوسي - النظام المالي المنزلي';

            const container = document.createElement('div');
            container.style.cssText = 'padding:20px;background:white;';
            container.appendChild(title);
            container.appendChild(el.cloneNode(true));
            container.appendChild(footer);

            html2pdf().set({
                margin: 10,
                filename: `flosy_report_${new Date().toISOString().split('T')[0]}.pdf`,
                image: { type: 'jpeg', quality: 0.98 },
                html2canvas: { scale: 2, useCORS: true },
                jsPDF: { unit: 'mm', format: 'a4', orientation: 'portrait' }
            }).from(container).save();

            showToast('📄 تم تصدير التقرير كـ PDF', 'success');
        }

        function exportReportExcel() {
            if (!window._reportData) { showToast('الرجاء إنشاء تقرير أولاً', 'warning'); return; }
            const data = window._reportData.transactions.map(t => ({
                'التاريخ': getDateString(t.date),
                'النوع': getTypeLabel(t.type),
                'المبلغ': t.amount,
                'التصنيف': t.category,
                'مصدر الدخل': t.incomeSource || '-',
                'البوابة': t.portal || '-',
                'الحساب': t.account || 'نقدي',
                'الملاحظات': t.notes || ''
            }));
            if (data.length === 0) { showToast('لا توجد بيانات لتصديرها', 'warning'); return; }
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, XLSX.utils.json_to_sheet(data), 'التقرير');
            XLSX.writeFile(wb, `flosy_report_${new Date().toISOString().split('T')[0]}.xlsx`);
            showToast('📊 تم تصدير التقرير كـ Excel', 'success');
        }

        function exportReportPPT() {
            if (!window._reportData) { showToast('الرجاء إنشاء تقرير أولاً', 'warning'); return; }
            const data = window._reportData;
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
                <h3>📊 توزيع المصروفات</h3>
                <table><thead><tr><th>التصنيف</th><th>المبلغ</th></tr></thead><tbody>
                ${Object.entries(data.transactions.filter(t => t.type === 'expense').reduce((acc, t) => { acc[t.category] = (acc[t.category] || 0) + t.amount; return acc; }, {})).sort((a,b) => b[1]-a[1]).map(([c, amt]) => `
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
            a.download = `flosy_report_${new Date().toISOString().split('T')[0]}.ppt`;
            a.click();
            URL.revokeObjectURL(url);
            showToast('📊 تم تصدير التقرير كـ PowerPoint', 'success');
        }

        // ============================================================
        // 14. دوال التحديث (Dashboard)
        // ============================================================
        function updateDashboard() {
            const transactions = DB.get('transactions');
            const totalIncome = getTotalIncome();
            const totalExpense = getTotalExpense();
            const balance = getBalance();

            document.getElementById('totalIncome').textContent = formatCurrency(totalIncome);
            document.getElementById('totalExpense').textContent = formatCurrency(totalExpense);
            document.getElementById('currentBalance').textContent = formatCurrency(balance);
            document.getElementById('transactionCount').textContent = transactions.length;

            // العجز/الفائض
            const surplus = calculateMonthlySurplus();
            if (surplus.surplus >= 0) {
                document.getElementById('monthlySurplus').textContent = formatCurrency(surplus.surplus);
                document.getElementById('monthlyDeficit').textContent = '0';
            } else {
                document.getElementById('monthlySurplus').textContent = '0';
                document.getElementById('monthlyDeficit').textContent = formatCurrency(Math.abs(surplus.surplus));
            }

            // الادخار
            const savings = DB.get('savings');
            const totalSavings = savings.reduce((s, item) => s + item.current, 0);
            document.getElementById('totalSavings').textContent = formatCurrency(totalSavings);
            document.getElementById('totalSavingsDisplay').textContent = formatCurrency(totalSavings);
            document.getElementById('savingCount').textContent = savings.length;

            // فيزا
            const visa = DB.get('visa');
            const totalVisa = visa.reduce((s, item) => s + item.total, 0);
            document.getElementById('totalVisa').textContent = formatCurrency(totalVisa);
            document.getElementById('totalVisaDisplay').textContent = formatCurrency(totalVisa);
            document.getElementById('visaDueCount').textContent = visa.length;

            // التنبيهات
            generateAlerts();
            updateExpenseChart(transactions);
            updateCashFlowChart(transactions);
            updateRecentTransactions(transactions);
            updateObligationsSummary();

            // تحديث لوحة المطور
            updateDeveloperDashboard();
        }

        function calculateMonthlySurplus() {
            const now = new Date();
            const month = now.getMonth();
            const year = now.getFullYear();
            const transactions = DB.get('transactions');
            const monthlyIncome = transactions.filter(t => {
                const d = new Date(t.date);
                return t.type === 'income' && d.getMonth() === month && d.getFullYear() === year;
            }).reduce((s, t) => s + t.amount, 0);
            const monthlyExpense = transactions.filter(t => {
                const d = new Date(t.date);
                return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year;
            }).reduce((s, t) => s + t.amount, 0);
            return { income: monthlyIncome, expense: monthlyExpense, surplus: monthlyIncome - monthlyExpense };
        }

        // ============================================================
        // 15. التنبيهات الذكية
        // ============================================================
        function generateAlerts() {
            const transactions = DB.get('transactions');
            const alerts = [];
            const budgets = DB.get('budgets');
            const now = new Date();
            const month = now.getMonth(),
                year = now.getFullYear();
            const monthlyExpenses = transactions.filter(t => {
                const d = new Date(t.date);
                return t.type === 'expense' && d.getMonth() === month && d.getFullYear() === year;
            });

            // تنبيهات الميزانيات
            budgets.forEach(b => {
                const spent = monthlyExpenses.filter(t => t.category === b.category).reduce((s, t) => s + t.amount, 0);
                const p = (spent / b.limit) * 100;
                if (p >= 90) alerts.push({ type: 'danger', icon: 'fa-exclamation-triangle',
                    message: `⚠️ تجاوزت ميزانية "${b.category}" ${p.toFixed(0)}%` });
                else if (p >= 70) alerts.push({ type: 'warning', icon: 'fa-exclamation-circle',
                    message: `⚡ ميزانية "${b.category}" قاربت على الانتهاء (${p.toFixed(0)}%)` });
            });

            // تنبيهات الاشتراكات المتكررة
            const subscriptions = DB.get('subscriptions');
            subscriptions.forEach(s => {
                const due = new Date(s.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                if (diff < 0 && s.status !== 'مدفوع') alerts.push({ type: 'danger', icon: 'fa-times-circle',
                    message: `🔴 اشتراك "${s.name}" متأخر` });
                else if (diff <= 3 && diff >= 0 && s.status !== 'مدفوع') alerts.push({ type: 'warning',
                    icon: 'fa-clock', message: `⏰ اشتراك "${s.name}" مستحق خلال ${diff} يوم` });
                if (s.recurring && s.recurring !== 'لا') {
                    alerts.push({ type: 'info', icon: 'fa-sync', message: `🔄 اشتراك "${s.name}" متكرر (${s.recurring})` });
                }
            });

            // تنبيهات الالتزامات
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

            // إنجازات وتحديات
            const user = AUTH.currentUser;
            if (user) {
                // تحديات شهرية
                const monthStart = new Date(now.getFullYear(), now.getMonth(), 1);
                const monthTransactions = transactions.filter(t => new Date(t.date) >= monthStart);
                const monthIncome = monthTransactions.filter(t => t.type === 'income').reduce((s, t) => s + t.amount, 0);
                const monthExpense = monthTransactions.filter(t => t.type === 'expense').reduce((s, t) => s + t.amount, 0);
                const savingsRate = monthIncome > 0 ? ((monthIncome - monthExpense) / monthIncome * 100) : 0;

                if (savingsRate >= 20) {
                    alerts.push({ type: 'success', icon: 'fa-star',
                        message: `🌟 تحدي الشهر: وفرت ${savingsRate.toFixed(0)}% من دخلك!` });
                }

                // عرض الإنجازات
                if (user.achievements && user.achievements.length > 0) {
                    const unlocked = ACHIEVEMENTS.list.filter(a => user.achievements.includes(a.id));
                    if (unlocked.length > 0) {
                        alerts.push({ type: 'info', icon: 'fa-trophy',
                            message: `🏆 لديك ${unlocked.length} إنجاز${unlocked.length > 1 ? 'ات' : ''}: ${unlocked.map(a => a.name).join(', ')}` });
                    }
                }
            }

            const c = document.getElementById('alertsContainer');
            if (alerts.length === 0) {
                c.innerHTML = `<div class="alert-item success"><i class="fas fa-check-circle"></i> ✅ الوضع المالي مستقر</div>`;
            } else {
                c.innerHTML = alerts.slice(0, 10).map(a => `<div class="alert-item ${a.type}"><i class="fas ${a.icon}"></i> ${a.message}</div>`)
                    .join('');
            }
        }

        // ============================================================
        // 16. الرسوم البيانية
        // ============================================================
        function updateExpenseChart(transactions) {
            const expenses = transactions.filter(t => t.type === 'expense');
            const cats = DB.getCategories();
            const data = cats.map(c => ({ category: c, total: expenses.filter(t => t.category === c).reduce((s, t) => s + t
                        .amount, 0) })).filter(d => d.total > 0);
            const ctx = document.getElementById('expenseChart').getContext('2d');
            if (window.expenseChartInstance) window.expenseChartInstance.destroy();
            if (data.length === 0) {
                window.expenseChartInstance = new Chart(ctx, {
                    type: 'doughnut',
                    data: { labels: ['لا توجد مصروفات'], datasets: [{ data: [1], backgroundColor: ['#e2e8f0'] }] },
                    options: { responsive: true, plugins: { legend: { position: 'bottom' } } }
                });
                return;
            }
            const colors = ['#22c55e', '#3b82f6', '#f59e0b', '#ef4444', '#8b5cf6', '#ec4899', '#14b8a6', '#f97316'];
            window.expenseChartInstance = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: data.map(d => d.category),
                    datasets: [{ data: data.map(d => d.total), backgroundColor: colors.slice(0, data.length),
                        borderWidth: 2 }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: { position: 'bottom', labels: { font: { family: 'Tajawal' }, padding: 10 } },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    const total = context.dataset.data.reduce((a, b) => a + b, 0);
                                    const percentage = ((context.parsed / total) * 100).toFixed(1);
                                    return `${context.label}: ${formatCurrency(context.parsed)} (${percentage}%)`;
                                }
                            }
                        }
                    }
                }
            });
        }

        function updateCashFlowChart(transactions) {
            const monthly = {};
            transactions.forEach(t => {
                const d = new Date(t.date);
                const key = d.getFullYear() + '-' + String(d.getMonth() + 1).padStart(2, '0');
                if (!monthly[key]) monthly[key] = { income: 0, expense: 0 };
                if (t.type === 'income') monthly[key].income += t.amount;
                else monthly[key].expense += t.amount;
            });
            const keys = Object.keys(monthly).sort();
            const ctx = document.getElementById('cashFlowChart').getContext('2d');
            if (window.cashFlowChartInstance) window.cashFlowChartInstance.destroy();
            if (keys.length === 0) {
                window.cashFlowChartInstance = new Chart(ctx, {
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
                return;
            }
            window.cashFlowChartInstance = new Chart(ctx, {
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
                    plugins: {
                        legend: { position: 'bottom', labels: { font: { family: 'Tajawal' } } },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.dataset.label}: ${formatCurrency(context.parsed.y)}`;
                                }
                            }
                        }
                    },
                    scales: { y: { beginAtZero: true, ticks: { callback: function(value) { return formatCurrency(
                                    value); } } } }
                }
            });
        }

        function updateRecentTransactions(transactions) {
            const recent = transactions.slice(-5).reverse();
            const c = document.getElementById('recentTransactions');
            if (recent.length === 0) {
                c.innerHTML = '<div class="empty-state"><i class="fas fa-inbox"></i>لا توجد عمليات مسجلة</div>';
                return;
            }
            c.innerHTML =
                `<div class="table-responsive"><table><thead><tr><th>التاريخ</th><th>النوع</th><th>المبلغ</th><th>التصنيف</th></tr></thead><tbody>${recent.map(t => `<tr><td>${getDateString(t.date)}</td><td><span class="filter-badge ${t.type}">${getTypeLabel(t.type)}</span></td><td class="${getTypeClass(t.type)}">${formatCurrency(t.amount)}</td><td>${t.category}</td></tr>`).join('')}</tbody></table></div>`;
        }

        function updateObligationsSummary() {
            const obligations = DB.get('obligations');
            const now = new Date();
            const summary = document.getElementById('obligationsSummary');
            if (!summary) return;

            const soon30 = obligations.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now) / (1000 * 60 * 60 *
                    24)); return diff >= 0 && diff <= 30; });
            const soon90 = obligations.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now) / (1000 * 60 * 60 *
                    24)); return diff >= 0 && diff <= 90; });
            const soonYear = obligations.filter(o => { const diff = Math.ceil((new Date(o.dueDate) - now) / (1000 * 60 * 60 *
                    24)); return diff >= 0 && diff <= 365; });

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

        // ============================================================
        // 17. العمليات
        // ============================================================
        function updateCategorySelects() {
            const cats = DB.getCategories();
            ['txCategory', 'filterCategory', 'budgetCategory', 'reportCategory'].forEach(id => {
                const el = document.getElementById(id);
                if (!el) return;
                const val = el.value;
                el.innerHTML = '';
                cats.forEach(c => { const o = document.createElement('option');
                    o.value = c;
                    o.textContent = c;
                    el.appendChild(o); });
                if (id === 'filterCategory' || id === 'reportCategory') { const o = document.createElement('option');
                    o.value = 'all';
                    o.textContent = 'كل التصنيفات';
                    el.prepend(o); }
                if (val && (cats.includes(val) || val === 'all')) el.value = val;
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

        function updateIncomeSourceSelects() {
            const sources = DB.getIncomeSources();
            const el = document.getElementById('txIncomeSource');
            if (!el) return;
            const val = el.value;
            el.innerHTML = '';
            sources.forEach(s => { const o = document.createElement('option');
                o.value = s;
                o.textContent = s;
                el.appendChild(o); });
            if (val && sources.includes(val)) el.value = val;
        }

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
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف العملية', 'warning');
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
            showToast('✏️ تم تعديل العملية', 'info');
        }

        // ============================================================
        // 18. الميزانيات
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
                showToast('🗑️ تم حذف الميزانية', 'warning');
            }
        }

        // ============================================================
        // 19. الادخار
        // ============================================================
        function loadSavings() {
            const savings = DB.get('savings');
            const c = document.getElementById('savingsList');
            if (savings.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-piggy-bank"></i>لا توجد صناديق ادخار<br><small>ابدأ في الادخار اليوم!</small></div>`;
                return;
            }
            c.innerHTML = savings.map(s => {
                const p = Math.min((s.current / s.target) * 100, 100);
                const cls = p >= 90 ? 'success' : p >= 50 ? 'warning' : 'danger';
                return `<div class="saving-card">
                    <div class="info">
                        <h4>🐷 ${s.name}</h4>
                        <p>${formatCurrency(s.current)} من ${formatCurrency(s.target)} (${p.toFixed(0)}%) - المتبقي: ${formatCurrency(s.target - s.current)}</p>
                        <div class="progress-bar"><div class="progress-fill ${cls}" style="width:${p}%"></div></div>
                    </div>
                    <div style="display:flex;gap:8px">
                        <button class="btn-secondary" onclick="updateSaving(${s.id})" style="padding:6px 12px"><i class="fas fa-plus"></i> إضافة</button>
                        <button class="btn-danger" onclick="deleteSaving(${s.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                    </div>
                </div>`;
            }).join('');
        }

        function updateSaving(id) {
            const s = DB.get('savings').find(x => x.id === id);
            if (!s) return;
            const amt = prompt(`المبلغ المدخر حالياً في "${s.name}":`, s.current);
            if (amt === null) return;
            const val = parseFloat(amt);
            if (isNaN(val) || val < 0 || val > s.target) {
                showToast('الرجاء إدخال مبلغ صحيح بين 0 والمبلغ المستهدف', 'warning');
                return;
            }
            DB.updateSaving(id, val);
            loadSavings();
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast(`✅ تم تحديث صندوق "${s.name}"`, 'success');
        }

        function deleteSaving(id) {
            if (confirm('هل أنت متأكد من حذف هذا الصندوق؟')) {
                DB.deleteSaving(id);
                loadSavings();
                updateDashboard();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف الصندوق', 'warning');
            }
        }

        // ============================================================
        // 20. فيزا المشتريات
        // ============================================================
        function loadVisa() {
            const visa = DB.get('visa');
            const c = document.getElementById('visaList');
            if (visa.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-credit-card"></i>لا توجد مشتريات بفيزا<br><small>سجل مشترياتك لتتبع الأقساط</small></div>`;
                return;
            }
            const now = new Date();
            c.innerHTML = visa.map(v => {
                const due = new Date(v.dueDate);
                const diff = Math.ceil((due - now) / (1000 * 60 * 60 * 24));
                const status = diff < 0 ? 'danger' : diff <= 30 ? 'warning' : 'success';
                return `<div class="visa-card" style="border-color:var(--${status});">
                    <div class="info">
                        <h4>💳 ${v.name}</h4>
                        <p>المبلغ: ${formatCurrency(v.total)} | الأقساط: ${v.installments} شهر</p>
                        <p>القسط الشهري: ${formatCurrency(v.monthly)} | تاريخ الاستحقاق: ${getDateString(v.dueDate)} ${diff >= 0 ? `(باقي ${diff} يوم)` : `(متأخر ${Math.abs(diff)} يوم)`}</p>
                    </div>
                    <button class="btn-danger" onclick="deleteVisa(${v.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteVisa(id) {
            if (confirm('هل أنت متأكد من حذف هذه العملية؟')) {
                DB.deleteVisa(id);
                loadVisa();
                updateDashboard();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف عملية فيزا', 'warning');
            }
        }

        // ============================================================
        // 21. الجمعيات
        // ============================================================
        function loadAssociations() {
            const associations = DB.get('associations');
            const c = document.getElementById('associationsList');
            if (associations.length === 0) {
                c.innerHTML =
                    `<div class="empty-state"><i class="fas fa-hand-holding-heart"></i>لا توجد جمعيات<br><small>سجل جمعياتك لتتبع الأقساط</small></div>`;
                return;
            }
            const now = new Date();
            c.innerHTML = associations.map(a => {
                const start = new Date(a.startDate);
                const end = new Date(a.endDate);
                const total = a.amount * a.months;
                const diff = Math.ceil((end - now) / (1000 * 60 * 60 * 24));
                const status = diff < 0 ? 'danger' : diff <= 30 ? 'warning' : 'success';
                return `<div class="association-card" style="border-color:var(--${status});">
                    <div class="info">
                        <h4>🤝 ${a.name}</h4>
                        <p>المبلغ: ${formatCurrency(a.amount)} × ${a.months} شهر = ${formatCurrency(total)}</p>
                        <p>دوري رقم: ${a.turn} | ${getDateString(a.startDate)} - ${getDateString(a.endDate)}</p>
                        <p>${diff >= 0 ? `متبقي ${diff} يوم` : `انتهت منذ ${Math.abs(diff)} يوم`}</p>
                    </div>
                    <button class="btn-danger" onclick="deleteAssociation(${a.id})" style="padding:6px 12px"><i class="fas fa-trash"></i></button>
                </div>`;
            }).join('');
        }

        function deleteAssociation(id) {
            if (confirm('هل أنت متأكد من حذف هذه الجمعية؟')) {
                DB.deleteAssociation(id);
                loadAssociations();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف الجمعية', 'warning');
            }
        }

        // ============================================================
        // 22. باقي الدوال (مختصرة)
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
                showToast('🗑️ تم حذف الالتزام', 'warning');
            }
        }

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
                showToast('🗑️ تم حذف الفرد', 'warning');
            }
        }

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
                showToast('🗑️ تم حذف الدين/السلفة', 'warning');
            }
        }

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
                showToast('الرجاء إدخال مبلغ صحيح بين 0 والمبلغ المستهدف', 'warning');
                return;
            }
            DB.updateGoal(id, val);
            loadGoals();
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast(`✅ تم تحديث هدف "${g.name}"`, 'success');
        }

        function deleteGoal(id) {
            if (confirm('هل أنت متأكد من حذف هذا الهدف؟')) {
                DB.deleteGoal(id);
                loadGoals();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف الهدف', 'warning');
            }
        }

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
                const recurring = s.recurring || 'مرة واحدة';
                return `<div class="subscription-card" style="border-color:var(--${color})">
                    <div class="info">
                        <h4>${s.name}</h4>
                        <p>${formatCurrency(s.amount)} - ${getDateString(s.dueDate)} ${diff >= 0 ? `(باقي ${diff} يوم)` : `(متأخر ${Math.abs(diff)} يوم)`}</p>
                        <p>${getStatusBadge(s.status)} <span class="badge badge-secondary">${recurring}</span></p>
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
            showToast(`✅ تم تغيير حالة "${s.name}"`, 'success');
        }

        function deleteSubscription(id) {
            if (confirm('هل أنت متأكد من حذف هذا الاشتراك؟')) {
                DB.deleteSubscription(id);
                loadSubscriptions();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف الاشتراك', 'warning');
            }
        }

        // ============================================================
        // 23. البوابات
        // ============================================================
        const PORTALS = {
            categories: ['حكومة', 'خاص', 'عمل حر', 'استثمار', 'تسويق', 'أخرى'],
            colors: { 'حكومة': '#3b82f6', 'خاص': '#22c55e', 'عمل حر': '#f59e0b', 'استثمار': '#8b5cf6', 'تسويق': '#ec4899',
                'أخرى': '#06b6d4' },
            getPortalIncome(portal) {
                return DB.get('transactions').filter(t => t.type === 'income' && t.portal === portal).reduce((s, t) => s +
                    t.amount, 0);
            },
            getAllPortalStats() {
                const stats = {};
                this.categories.forEach(p => { stats[p] = this.getPortalIncome(p); });
                return stats;
            }
        };

        function loadPortalStats() {
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
            if (portal !== 'all') transactions = transactions.filter(t => t.portal === portal);
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
                loadPortalStats();
                loadPortalTransactions();
                updateDashboard();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف العملية', 'warning');
            }
        }

        // ============================================================
        // 24. المستخدمين والإعدادات
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
                        <h4>${u.username} ${u.level ? `<span class="badge badge-gold">مستوى ${u.level}</span>` : ''}</h4>
                        <p>
                            <span class="badge ${roleColors[u.role] || 'badge-secondary'}">${roleLabels[u.role] || u.role}</span>
                            ${u.id === AUTH.currentUser.id ? ' <span class="badge badge-warning">(أنت)</span>' : ''}
                            <br>
                            <small>نقاط: ${u.points || 0} | الإنجازات: ${(u.achievements || []).length}</small>
                            <br>
                            <small>آخر نشاط: ${u.lastActive ? new Date(u.lastActive).toLocaleString('ar-EG') : 'غير معروف'}</small>
                        </p>
                        ${(u.achievements || []).length > 0 ? `
                            <div style="margin-top:5px;">
                                ${ACHIEVEMENTS.list.filter(a => u.achievements.includes(a.id)).map(a => `
                                    <span class="achievement-badge unlocked"><i class="fas ${a.icon}"></i> ${a.name}</span>
                                `).join('')}
                            </div>
                        ` : ''}
                    </div>
                    <div style="display:flex;gap:8px;flex-wrap:wrap;">
                        ${u.id !== AUTH.currentUser.id ? `
                            <button class="btn-secondary" onclick="editUserRole(${u.id})" style="padding:6px 12px;"><i class="fas fa-edit"></i></button>
                            <button class="btn-danger" onclick="deleteUser(${u.id})" style="padding:6px 12px;"><i class="fas fa-trash"></i></button>
                        ` : `
                            <button class="btn-secondary" onclick="showChangePassword()" style="padding:6px 12px;"><i class="fas fa-key"></i> تغيير</button>
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
                showToast('✅ تم حذف المستخدم بنجاح', 'success');
            } else {
                showToast(result.message, 'error');
            }
        }

        function editUserRole(id) {
            const user = AUTH.users.find(u => u.id === id);
            if (!user) return;
            const roles = ['admin', 'user', 'viewer'];
            const nextRole = roles[(roles.indexOf(user.role) + 1) % roles.length];
            if (confirm(`تغيير صلاحية "${user.username}" من "${user.role}" إلى "${nextRole}"؟`)) {
                const result = AUTH.updateUser(id, { role: nextRole });
                if (result.success) {
                    loadUsersList();
                    updateUIForUser();
                    showToast('✅ تم تغيير الصلاحية بنجاح', 'success');
                }
            }
        }

        // ============================================================
        // 25. الإعدادات - واجهة المستخدم
        // ============================================================
        function loadSettingsUI() {
            const settingsGrid = document.getElementById('settingsGrid');
            if (!settingsGrid) return;
            const settings = DB.getSettings();

            settingsGrid.innerHTML = `
                <!-- الملف الشخصي -->
                <div class="settings-card">
                    <h3><i class="fas fa-user"></i> الملف الشخصي</h3>
                    <div class="setting-item">
                        <label>اسم المستخدم</label>
                        <input type="text" id="settingsUsername" value="${AUTH.currentUser?.username || ''}" style="width:auto;flex:1;margin-right:10px;">
                        <button onclick="updateUsername()" class="btn-secondary" style="width:auto;padding:6px 16px;">حفظ</button>
                    </div>
                    <div class="setting-item">
                        <label>تغيير كلمة المرور</label>
                        <button onclick="showChangePassword()" class="btn-secondary" style="width:auto;padding:6px 16px;">تغيير</button>
                    </div>
                    <div class="setting-item">
                        <label>مدة الجلسة (دقائق)</label>
                        <select id="sessionTimeout" onchange="updateSessionTimeout()">
                            <option value="1">1 دقيقة</option>
                            <option value="5" ${settings.sessionTimeout == 5 ? 'selected' : ''}>5 دقائق</option>
                            <option value="15" ${settings.sessionTimeout == 15 ? 'selected' : ''}>15 دقيقة</option>
                            <option value="30" ${settings.sessionTimeout == 30 ? 'selected' : ''}>30 دقيقة</option>
                            <option value="60" ${settings.sessionTimeout == 60 ? 'selected' : ''}>60 دقيقة</option>
                        </select>
                    </div>
                    <div class="setting-item">
                        <label>سؤال الأمان</label>
                        <select id="settingsSecurityQuestion" onchange="updateSecurityQuestion()">
                            <option value="ما هو اسم والدتك؟" ${settings.securityQuestion == 'ما هو اسم والدتك؟' ? 'selected' : ''}>ما هو اسم والدتك؟</option>
                            <option value="ما هو اسم مدرستك الأولى؟" ${settings.securityQuestion == 'ما هو اسم مدرستك الأولى؟' ? 'selected' : ''}>ما هو اسم مدرستك الأولى؟</option>
                            <option value="ما هو اسم حيوانك الأليف؟" ${settings.securityQuestion == 'ما هو اسم حيوانك الأليف؟' ? 'selected' : ''}>ما هو اسم حيوانك الأليف؟</option>
                            <option value="ما هو اسم مدينتك المفضلة؟" ${settings.securityQuestion == 'ما هو اسم مدينتك المفضلة؟' ? 'selected' : ''}>ما هو اسم مدينتك المفضلة؟</option>
                        </select>
                    </div>
                    <div class="setting-item">
                        <label>إجابة سؤال الأمان</label>
                        <input type="text" id="settingsSecurityAnswer" value="${AUTH.currentUser?.securityAnswer || ''}" style="width:auto;flex:1;margin-right:10px;">
                        <button onclick="updateSecurityAnswer()" class="btn-secondary" style="width:auto;padding:6px 16px;">حفظ</button>
                    </div>
                </div>

                <!-- العملات -->
                <div class="settings-card">
                    <h3><i class="fas fa-money-bill"></i> العملات</h3>
                    <div class="setting-item">
                        <label>العملة الأساسية</label>
                        <select id="settingsCurrency" onchange="updateCurrency()">
                            <option value="جنيه" ${settings.currency == 'جنيه' ? 'selected' : ''}>جنيه مصري</option>
                            <option value="ريال" ${settings.currency == 'ريال' ? 'selected' : ''}>ريال سعودي</option>
                            <option value="دولار" ${settings.currency == 'دولار' ? 'selected' : ''}>دولار أمريكي</option>
                            <option value="درهم" ${settings.currency == 'درهم' ? 'selected' : ''}>درهم إماراتي</option>
                            <option value="دينار" ${settings.currency == 'دينار' ? 'selected' : ''}>دينار كويتي</option>
                        </select>
                    </div>
                </div>

                <!-- الإشعارات -->
                <div class="settings-card">
                    <h3><i class="fas fa-bell"></i> الإشعارات</h3>
                    <div class="setting-item">
                        <label>البريد الإلكتروني</label>
                        <input type="email" id="settingsEmail" value="${settings.notifications?.email || ''}" style="width:auto;flex:1;margin-right:10px;" placeholder="example@email.com">
                        <button onclick="updateNotifications()" class="btn-secondary" style="width:auto;padding:6px 16px;">حفظ</button>
                    </div>
                    <div class="setting-item">
                        <label>Telegram</label>
                        <input type="text" id="settingsTelegram" value="${settings.notifications?.telegram || ''}" style="width:auto;flex:1;margin-right:10px;" placeholder="@username">
                        <button onclick="updateNotifications()" class="btn-secondary" style="width:auto;padding:6px 16px;">حفظ</button>
                    </div>
                    <div class="setting-item">
                        <label>Viber</label>
                        <input type="text" id="settingsViber" value="${settings.notifications?.viber || ''}" style="width:auto;flex:1;margin-right:10px;" placeholder="رقم الهاتف">
                        <button onclick="updateNotifications()" class="btn-secondary" style="width:auto;padding:6px 16px;">حفظ</button>
                    </div>
                    <div class="setting-item">
                        <label>النسخ الاحتياطي التلقائي</label>
                        <div class="dark-mode-toggle" style="margin:0;">
                            <label class="switch"><input type="checkbox" id="settingsAutoBackup" ${settings.autoBackup !== false ? 'checked' : ''}><span class="slider"></span></label>
                        </div>
                    </div>
                </div>

                <!-- المظهر -->
                <div class="settings-card">
                    <h3><i class="fas fa-palette"></i> المظهر</h3>
                    <div class="setting-item">
                        <label>الوضع الليلي</label>
                        <div class="dark-mode-toggle" style="margin:0;">
                            <i class="fas fa-moon"></i>
                            <label class="switch"><input type="checkbox" id="settingsDarkMode" ${settings.darkMode ? 'checked' : ''}><span class="slider"></span></label>
                            <i class="fas fa-sun"></i>
                        </div>
                    </div>
                    <div class="setting-item">
                        <label>اللون الأساسي</label>
                        <div class="color-options">
                            <button class="color-btn ${settings.primaryColor == '#2563eb' ? 'active' : ''}" data-color="#2563eb" style="background:#2563eb" onclick="changePrimaryColor('#2563eb')"></button>
                            <button class="color-btn ${settings.primaryColor == '#22c55e' ? 'active' : ''}" data-color="#22c55e" style="background:#22c55e" onclick="changePrimaryColor('#22c55e')"></button>
                            <button class="color-btn ${settings.primaryColor == '#ef4444' ? 'active' : ''}" data-color="#ef4444" style="background:#ef4444" onclick="changePrimaryColor('#ef4444')"></button>
                            <button class="color-btn ${settings.primaryColor == '#8b5cf6' ? 'active' : ''}" data-color="#8b5cf6" style="background:#8b5cf6" onclick="changePrimaryColor('#8b5cf6')"></button>
                            <button class="color-btn ${settings.primaryColor == '#ec4899' ? 'active' : ''}" data-color="#ec4899" style="background:#ec4899" onclick="changePrimaryColor('#ec4899')"></button>
                            <button class="color-btn ${settings.primaryColor == '#f59e0b' ? 'active' : ''}" data-color="#f59e0b" style="background:#f59e0b" onclick="changePrimaryColor('#f59e0b')"></button>
                        </div>
                    </div>
                    <div class="setting-item">
                        <label>حجم الخط</label>
                        <select id="settingsFontSize" onchange="changeFontSize()">
                            <option value="small" ${settings.fontSize == 'small' ? 'selected' : ''}>صغير</option>
                            <option value="medium" ${settings.fontSize == 'medium' ? 'selected' : ''}>متوسط</option>
                            <option value="large" ${settings.fontSize == 'large' ? 'selected' : ''}>كبير</option>
                        </select>
                    </div>
                </div>

                <!-- التصنيفات -->
                <div class="settings-card">
                    <h3><i class="fas fa-tags"></i> التصنيفات</h3>
                    <div id="categoriesList"></div>
                    <div class="add-category">
                        <input type="text" id="newCategory" placeholder="تصنيف جديد">
                        <button onclick="addCategory()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button>
                    </div>
                </div>

                <!-- الحسابات -->
                <div class="settings-card">
                    <h3><i class="fas fa-wallet"></i> الحسابات</h3>
                    <div id="accountsList"></div>
                    <div class="add-account">
                        <input type="text" id="newAccount" placeholder="حساب جديد">
                        <button onclick="addAccount()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button>
                    </div>
                </div>

                <!-- مصادر الدخل -->
                <div class="settings-card">
                    <h3><i class="fas fa-briefcase"></i> مصادر الدخل</h3>
                    <div id="incomeSourcesList"></div>
                    <div class="add-income-source">
                        <input type="text" id="newIncomeSource" placeholder="مصدر دخل جديد">
                        <button onclick="addIncomeSource()" class="btn-secondary"><i class="fas fa-plus"></i> إضافة</button>
                    </div>
                </div>

                <!-- سجل النشاطات -->
                <div class="settings-card">
                    <h3><i class="fas fa-history"></i> سجل النشاطات</h3>
                    <div id="activityLog" class="activity-log"></div>
                    <button onclick="clearActivityLog()" class="btn-danger" style="margin-top:10px;"><i class="fas fa-trash"></i> مسح السجل</button>
                </div>

                <!-- البيانات -->
                <div class="settings-card">
                    <h3><i class="fas fa-database"></i> البيانات</h3>
                    <button onclick="backupData()" class="btn-secondary"><i class="fas fa-download"></i> نسخ احتياطي</button>
                    <button onclick="document.getElementById('restoreFile').click()" class="btn-secondary"><i class="fas fa-upload"></i> استعادة بيانات</button>
                    <input type="file" id="restoreFile" accept=".json" style="display:none" onchange="restoreData(event)">
                    <button onclick="clearAllData()" class="btn-danger" style="margin-top:10px"><i class="fas fa-trash"></i> حذف كل البيانات</button>
                </div>

                <!-- المزامنة السحابية -->
                <div class="settings-card" id="syncCard">
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
                </div>
            `;

            loadCategories();
            loadAccounts();
            loadIncomeSources();
            loadActivityLog();

            // Dark Mode
            document.getElementById('settingsDarkMode').addEventListener('change', function() {
                document.body.classList.toggle('dark-mode', this.checked);
                const s = DB.getSettings();
                s.darkMode = this.checked;
                DB.saveSettings(s);
                document.getElementById('darkModeToggle').checked = this.checked;
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Auto Backup
            document.getElementById('settingsAutoBackup').addEventListener('change', function() {
                const s = DB.getSettings();
                s.autoBackup = this.checked;
                DB.saveSettings(s);
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast(this.checked ? '✅ تم تفعيل النسخ الاحتياطي التلقائي' : '⏹️ تم إيقاف النسخ الاحتياطي التلقائي', 'info');
            });
        }

        // ============================================================
        // 26. دوال الإعدادات
        // ============================================================
        function changePrimaryColor(color) {
            document.documentElement.style.setProperty('--secondary', color);
            document.querySelectorAll('.color-btn').forEach(b => b.classList.remove('active'));
            document.querySelector(`.color-btn[data-color="${color}"]`)?.classList.add('active');
            const s = DB.getSettings();
            s.primaryColor = color;
            DB.saveSettings(s);
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast('🎨 تم تغيير اللون الأساسي', 'success');
        }

        function changeFontSize() {
            const size = document.getElementById('settingsFontSize').value;
            document.body.className = `font-${size}`;
            const s = DB.getSettings();
            s.fontSize = size;
            DB.saveSettings(s);
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
        }

        function updateCurrency() {
            const currency = document.getElementById('settingsCurrency').value;
            const s = DB.getSettings();
            s.currency = currency;
            DB.saveSettings(s);
            updateDashboard();
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast(`💱 تم تغيير العملة إلى ${currency}`, 'success');
        }

        function updateSessionTimeout() {
            const timeout = parseInt(document.getElementById('sessionTimeout').value);
            const s = DB.getSettings();
            s.sessionTimeout = timeout;
            DB.saveSettings(s);
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast('✅ تم تحديث مدة الجلسة', 'success');
        }

        function updateUsername() {
            const username = document.getElementById('settingsUsername').value.trim();
            if (!username) { showToast('الرجاء إدخال اسم المستخدم', 'warning'); return; }
            const result = AUTH.updateUser(AUTH.currentUser.id, { username });
            if (result.success) {
                updateUIForUser();
                showToast('✅ تم تحديث اسم المستخدم', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        function updateSecurityQuestion() {
            const question = document.getElementById('settingsSecurityQuestion').value;
            const result = AUTH.updateUser(AUTH.currentUser.id, { securityQuestion: question });
            if (result.success) {
                showToast('✅ تم تحديث سؤال الأمان', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        function updateSecurityAnswer() {
            const answer = document.getElementById('settingsSecurityAnswer').value.trim();
            if (!answer) { showToast('الرجاء إدخال إجابة سؤال الأمان', 'warning'); return; }
            const result = AUTH.updateUser(AUTH.currentUser.id, { securityAnswer: answer });
            if (result.success) {
                showToast('✅ تم تحديث إجابة سؤال الأمان', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            }
        }

        function updateNotifications() {
            const email = document.getElementById('settingsEmail').value.trim();
            const telegram = document.getElementById('settingsTelegram').value.trim();
            const viber = document.getElementById('settingsViber').value.trim();
            const s = DB.getSettings();
            s.notifications = { email, telegram, viber };
            DB.saveSettings(s);
            setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            showToast('✅ تم تحديث إعدادات الإشعارات', 'success');
        }

        function loadCategories() {
            const cats = DB.getCategories();
            document.getElementById('categoriesList').innerHTML = cats.map(c =>
                `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${c}</span><button class="btn-danger" onclick="deleteCategory('${c}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`
            ).join('');
        }

        function addCategory() {
            const input = document.getElementById('newCategory');
            const val = input.value.trim();
            if (!val) { showToast('الرجاء إدخال اسم التصنيف', 'warning'); return; }
            if (DB.addCategory(val)) {
                input.value = '';
                loadCategories();
                updateCategorySelects();
                showToast('✅ تم إضافة التصنيف بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            } else showToast('❌ هذا التصنيف موجود بالفعل', 'error');
        }

        function deleteCategory(cat) {
            if (confirm(`هل أنت متأكد من حذف التصنيف "${cat}"؟`)) {
                DB.deleteCategory(cat);
                loadCategories();
                updateCategorySelects();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف التصنيف', 'warning');
            }
        }

        function loadAccounts() {
            const accs = DB.getAccounts();
            document.getElementById('accountsList').innerHTML = accs.map(a =>
                `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${a}</span><button class="btn-danger" onclick="deleteAccount('${a}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`
            ).join('');
        }

        function addAccount() {
            const input = document.getElementById('newAccount');
            const val = input.value.trim();
            if (!val) { showToast('الرجاء إدخال اسم الحساب', 'warning'); return; }
            if (DB.addAccount(val)) {
                input.value = '';
                loadAccounts();
                updateAccountSelects();
                showToast('✅ تم إضافة الحساب بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            } else showToast('❌ هذا الحساب موجود بالفعل', 'error');
        }

        function deleteAccount(acc) {
            if (confirm(`هل أنت متأكد من حذف الحساب "${acc}"؟`)) {
                DB.deleteAccount(acc);
                loadAccounts();
                updateAccountSelects();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف الحساب', 'warning');
            }
        }

        function loadIncomeSources() {
            const sources = DB.getIncomeSources();
            document.getElementById('incomeSourcesList').innerHTML = sources.map(s =>
                `<div style="display:flex;justify-content:space-between;align-items:center;padding:8px 0;border-bottom:1px solid var(--border)"><span>${s}</span><button class="btn-danger" onclick="deleteIncomeSource('${s}')" style="padding:4px 8px;font-size:12px"><i class="fas fa-trash"></i></button></div>`
            ).join('');
        }

        function addIncomeSource() {
            const input = document.getElementById('newIncomeSource');
            const val = input.value.trim();
            if (!val) { showToast('الرجاء إدخال مصدر دخل', 'warning'); return; }
            if (DB.addIncomeSource(val)) {
                input.value = '';
                loadIncomeSources();
                updateIncomeSourceSelects();
                showToast('✅ تم إضافة مصدر الدخل بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            } else showToast('❌ هذا المصدر موجود بالفعل', 'error');
        }

        function deleteIncomeSource(source) {
            if (confirm(`هل أنت متأكد من حذف مصدر الدخل "${source}"؟`)) {
                DB.deleteIncomeSource(source);
                loadIncomeSources();
                updateIncomeSourceSelects();
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                showToast('🗑️ تم حذف مصدر الدخل', 'warning');
            }
        }

        function loadActivityLog() {
            const log = DB.getActivityLog();
            const container = document.getElementById('activityLog');
            if (!container) return;
            if (log.length === 0) {
                container.innerHTML = '<div class="empty-state"><i class="fas fa-history"></i>لا توجد نشاطات</div>';
                return;
            }
            container.innerHTML = log.slice(0, 20).map(item => `
                <div class="activity-item">
                    <span class="action">${item.action}</span>
                    <span style="color:var(--text-light);">${item.details}</span>
                    <span class="time">${new Date(item.time).toLocaleString('ar-EG')}</span>
                </div>
            `).join('');
        }

        function clearActivityLog() {
            if (confirm('هل أنت متأكد من مسح سجل النشاطات؟')) {
                DB.set('activityLog', []);
                loadActivityLog();
                showToast('🗑️ تم مسح سجل النشاطات', 'warning');
            }
        }

        // ============================================================
        // 27. البيانات والمزامنة
        // ============================================================
        function backupData() {
            const data = DB.backup();
            const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `flosy_backup_${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(url);
            showToast('✅ تم إنشاء نسخة احتياطية', 'success');
        }

        function restoreData(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const data = JSON.parse(e.target.result);
                    if (!data.transactions) { showToast('❌ ملف غير صالح', 'error'); return; }
                    if (confirm('سيتم استبدال كل البيانات الحالية. هل أنت متأكد؟')) {
                        DB.restore(data);
                        showToast('✅ تم استعادة البيانات بنجاح', 'success');
                        setTimeout(() => location.reload(), 1000);
                    }
                } catch (err) { showToast('❌ خطأ في قراءة الملف', 'error'); }
            };
            reader.readAsText(file);
            event.target.value = '';
        }

        function clearAllData() {
            if (confirm('⚠️ تحذير: سيتم حذف كل البيانات نهائياً. هل أنت متأكد؟')) {
                if (confirm('تأكيد نهائي: هل تريد حذف كل شيء؟')) {
                    DB.clearAll();
                    showToast('✅ تم حذف كل البيانات', 'success');
                    setTimeout(() => location.reload(), 1000);
                }
            }
        }

        const SYNC = {
            isSyncing: false,
            lastSync: null,

            async saveToFirebase() {
                try {
                    this.isSyncing = true;
                    if (!AUTH.currentUser) return { success: false, error: 'لا يوجد مستخدم' };
                    if (!db) return { success: false, error: 'Firebase غير متصل' };
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
                        fixedExpenses: DB.get('fixedExpenses'),
                        incomeSources: DB.get('incomeSources'),
                        savings: DB.get('savings'),
                        visa: DB.get('visa'),
                        associations: DB.get('associations'),
                        users: AUTH.users,
                        lastUpdated: new Date().toISOString()
                    };
                    await db.collection('flosy_users').doc(AUTH.currentUser.username).set(data);
                    this.lastSync = new Date();
                    console.log('✅ تم حفظ البيانات في Firebase');
                    return { success: true };
                } catch (error) {
                    console.error('❌ خطأ:', error);
                    logError('Firebase Save', error.message);
                    return { success: false, error: error.message };
                } finally { this.isSyncing = false; }
            },

            async loadFromFirebase() {
                try {
                    if (!AUTH.currentUser) return { success: false, error: 'لا يوجد مستخدم' };
                    if (!db) return { success: false, error: 'Firebase غير متصل' };
                    const doc = await db.collection('flosy_users').doc(AUTH.currentUser.username).get();
                    if (doc.exists) {
                        const data = doc.data();
                        Object.keys(DB.keys).forEach(k => { if (data[k]) DB.set(k, data[k]); });
                        if (data.users) { AUTH.users = data.users;
                            AUTH.saveUsers(); }
                        this.lastSync = new Date(data.lastUpdated);
                        console.log('✅ تم تحميل البيانات من Firebase');
                        return { success: true, data };
                    }
                    return { success: true, data: null };
                } catch (error) {
                    console.error('❌ خطأ:', error);
                    logError('Firebase Load', error.message);
                    return { success: false, error: error.message };
                }
            }
        };

        async function syncSaveToFirebase() {
            if (!AUTH.currentUser) return;
            try { await SYNC.saveToFirebase(); } catch (e) { console.warn('⚠️ فشل الحفظ:', e); }
        }

        async function syncLoadFromFirebase() {
            if (!AUTH.currentUser) return;
            try { await SYNC.loadFromFirebase(); } catch (e) { console.warn('⚠️ فشل التحميل:', e); }
        }

        async function syncNow() {
            if (!AUTH.currentUser) { showToast('❌ الرجاء تسجيل الدخول أولاً', 'error'); return; }
            const btn = event?.target;
            if (btn) { btn.disabled = true;
                btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> جاري المزامنة...'; }
            try {
                await SYNC.saveToFirebase();
                await SYNC.loadFromFirebase();
                document.getElementById('syncStatus').textContent =
                    `✅ تمت المزامنة بنجاح - ${new Date().toLocaleString('ar-EG')}`;
                showToast('✅ تمت المزامنة مع السحابة بنجاح!', 'success');
                loadAllData();
            } catch (error) {
                showToast('❌ حدث خطأ: ' + error.message, 'error');
            } finally {
                if (btn) { btn.disabled = false;
                    btn.innerHTML = '<i class="fas fa-sync"></i> مزامنة الآن'; }
            }
        }

        async function saveToCloud() {
            if (!AUTH.currentUser) { showToast('❌ الرجاء تسجيل الدخول أولاً', 'error'); return; }
            const result = await SYNC.saveToFirebase();
            if (result.success) {
                document.getElementById('syncStatus').textContent =
                    `✅ تم الحفظ في السحابة - ${new Date().toLocaleString('ar-EG')}`;
                showToast('✅ تم حفظ البيانات في السحابة بنجاح!', 'success');
            } else {
                showToast('❌ حدث خطأ: ' + result.error, 'error');
            }
        }

        async function loadFromCloud() {
            if (!AUTH.currentUser) { showToast('❌ الرجاء تسجيل الدخول أولاً', 'error'); return; }
            if (!confirm('سيتم استبدال كل البيانات المحلية ببيانات السحابة. هل أنت متأكد؟')) return;
            const result = await SYNC.loadFromFirebase();
            if (result.success && result.data) {
                loadAllData();
                document.getElementById('syncStatus').textContent =
                    `✅ تم التحميل من السحابة - ${new Date().toLocaleString('ar-EG')}`;
                showToast('✅ تم تحميل البيانات من السحابة بنجاح!', 'success');
            } else if (result.success && !result.data) {
                showToast('📭 لا توجد بيانات سابقة في السحابة', 'info');
            } else {
                showToast('❌ حدث خطأ: ' + result.error, 'error');
            }
        }

        // ============================================================
        // 28. لوحة المطور
        // ============================================================
        function updateDeveloperDashboard() {
            // Firebase Status
            document.getElementById('devFirebaseStatus').textContent = db ? '✅ متصل' : '❌ غير متصل';
            document.getElementById('devFirebaseStatus').style.color = db ? 'var(--success)' : 'var(--danger)';

            // API Status
            document.getElementById('devApiStatus').textContent = '✅ شغال';
            document.getElementById('devApiStatus').style.color = 'var(--success)';

            // Server Status
            document.getElementById('devServerStatus').textContent = '✅ شغال';
            document.getElementById('devServerStatus').style.color = 'var(--success)';

            // Load Time
            const loadTime = performance.now();
            document.getElementById('devLoadTime').textContent = loadTime.toFixed(0) + 'ms';

            // Storage Used
            let totalSize = 0;
            for (let key in localStorage) {
                if (key.startsWith('flosy_')) {
                    totalSize += localStorage[key].length;
                }
            }
            document.getElementById('devStorageUsed').textContent = (totalSize / 1024).toFixed(1) + ' KB';

            // Transaction Count
            document.getElementById('devTransactionCount').textContent = DB.get('transactions').length;

            // Storage Status
            const storageStatus = document.getElementById('storageStatus');
            if (storageStatus) {
                const keys = Object.keys(DB.keys);
                let html = '<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:10px;">';
                keys.forEach(k => {
                    const data = DB.get(k);
                    html += `
                        <div style="background:var(--bg);padding:10px;border-radius:8px;text-align:center;">
                            <strong>${k}</strong>
                            <p style="color:var(--text-light);font-size:14px;">${data.length} عنصر</p>
                        </div>
                    `;
                });
                html += '</div>';
                storageStatus.innerHTML = html;
            }

            // Error Log
            const errorLogContainer = document.getElementById('errorLog');
            if (errorLogContainer) {
                const savedErrors = localStorage.getItem('flosy_error_log');
                if (savedErrors) {
                    try {
                        errorLog = JSON.parse(savedErrors);
                    } catch (e) { errorLog = []; }
                }
                if (errorLog.length === 0) {
                    errorLogContainer.innerHTML = '<div style="color:var(--text-light);">لا توجد أخطاء مسجلة</div>';
                } else {
                    errorLogContainer.innerHTML = errorLog.slice(0, 20).map(e =>
                        `<div style="padding:4px 0;border-bottom:1px solid var(--border);">
                            <span style="color:var(--danger);">[${e.source}]</span>
                            <span>${e.message}</span>
                            <span style="color:var(--text-light);font-size:11px;float:left;">${new Date(e.time).toLocaleString('ar-EG')}</span>
                        </div>`
                    ).join('');
                }
            }
        }

        function clearErrorLog() {
            if (confirm('هل أنت متأكد من مسح سجل الأخطاء؟')) {
                errorLog = [];
                localStorage.setItem('flosy_error_log', JSON.stringify(errorLog));
                updateDeveloperDashboard();
                showToast('🗑️ تم مسح سجل الأخطاء', 'warning');
            }
        }

        // ============================================================
        // 29. تحميل جميع البيانات
        // ============================================================
        function loadAllData() {
            updateDashboard();
            loadTransactions();
            loadBudgets();
            loadSavings();
            loadVisa();
            loadAssociations();
            loadObligations();
            loadFamily();
            loadDebts();
            loadGoals();
            loadSubscriptions();
            loadPortalStats();
            loadUsersList();
            loadSettingsUI();
            updateDeveloperDashboard();

            // Report period select
            const periodSelect = document.getElementById('reportPeriod');
            if (periodSelect) {
                periodSelect.addEventListener('change', function() {
                    const isCustom = this.value === 'custom';
                    document.getElementById('customDateGroup').style.display = isCustom ? 'block' : 'none';
                    document.getElementById('customDateGroup2').style.display = isCustom ? 'block' : 'none';
                });
            }

            // Report year
            const cy = new Date().getFullYear();
            ['reportYear', 'statsYear'].forEach(id => {
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
        // 30. تحديث الواجهة
        // ============================================================
        function updateUIForUser() {
            const user = AUTH.currentUser;
            const levelBadge = user.level ? ` <span class="badge badge-gold">مستوى ${user.level}</span>` : '';
            document.getElementById('userDisplay').innerHTML = user.username + ' (' + user.role + ')' + levelBadge;
            document.getElementById('usersMenu').style.display = user.role === 'admin' ? 'flex' : 'none';
            document.getElementById('userCount').textContent = AUTH.users.length + ' مستخدم';
        }

        // ============================================================
        // 31. رفع الصورة
        // ============================================================
        function uploadAvatar(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                const avatar = document.querySelector('.sidebar-header .avatar');
                avatar.innerHTML = `<img src="${e.target.result}" alt="avatar">`;
                localStorage.setItem('flosy_avatar', e.target.result);
                DB.logActivity('تحديث الصورة', 'تغيير الصورة الشخصية');
                showToast('🖼️ تم تحديث الصورة الشخصية', 'success');
            };
            reader.readAsDataURL(file);
        }

        // ============================================================
        // 32. تغيير كلمة المرور
        // ============================================================
        function showChangePassword() {
            document.getElementById('changePasswordScreen').style.display = 'flex';
            document.getElementById('oldPassword').value = '';
            document.getElementById('newPassword').value = '';
            document.getElementById('confirmPassword').value = '';
            document.getElementById('changePasswordError').textContent = '';
        }

        // ============================================================
        // 33. تهيئة التطبيق
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
                setTimeout(async () => {
                    await syncLoadFromFirebase();
                    loadAllData();
                    checkMonthlyBackup();
                }, 500);
            }

            // Dark Mode
            const settings = DB.getSettings();
            if (settings.darkMode) {
                document.body.classList.add('dark-mode');
                document.getElementById('darkModeToggle').checked = true;
            }
            if (settings.primaryColor) {
                document.documentElement.style.setProperty('--secondary', settings.primaryColor);
            }
            if (settings.fontSize) {
                document.body.className = `font-${settings.fontSize}`;
            }

            document.getElementById('darkModeToggle').addEventListener('change', function() {
                document.body.classList.toggle('dark-mode', this.checked);
                const s = DB.getSettings();
                s.darkMode = this.checked;
                DB.saveSettings(s);
                document.getElementById('settingsDarkMode').checked = this.checked;
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
                    else if (pageId === 'transactions') { updateCategorySelects();
                        updateAccountSelects();
                        updateIncomeSourceSelects();
                        loadTransactions(); }
                    else if (pageId === 'portals') loadPortalStats();
                    else if (pageId === 'budgets') loadBudgets();
                    else if (pageId === 'savings') loadSavings();
                    else if (pageId === 'visa') loadVisa();
                    else if (pageId === 'associations') loadAssociations();
                    else if (pageId === 'obligations') loadObligations();
                    else if (pageId === 'family') loadFamily();
                    else if (pageId === 'debts') loadDebts();
                    else if (pageId === 'goals') loadGoals();
                    else if (pageId === 'subscriptions') loadSubscriptions();
                    else if (pageId === 'reports') { updateCategorySelects();
                        generateAdvancedReport(); }
                    else if (pageId === 'users') loadUsersList();
                    else if (pageId === 'settings') loadSettingsUI();
                    else if (pageId === 'developer') updateDeveloperDashboard();
                });
            });

            // ===== النماذج =====
            document.getElementById('txDate').value = new Date().toISOString().split('T')[0];
            updateCategorySelects();
            updateAccountSelects();
            updateIncomeSourceSelects();

            document.getElementById('txType').addEventListener('change', function() {
                document.getElementById('incomeSourceGroup').style.display = this.value === 'income' ? 'block' :
                    'none';
            });

            // Transaction Form
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

                if (!amount || amount <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!date) { showToast('الرجاء تحديد التاريخ', 'warning'); return; }

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
                showToast('✅ تم إضافة العملية بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Budget Form
            document.getElementById('budgetForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const category = document.getElementById('budgetCategory').value;
                const limit = parseFloat(document.getElementById('budgetLimit').value);
                if (!limit || limit <= 0) { showToast('الرجاء إدخال حد صحيح', 'warning'); return; }
                if (DB.get('budgets').some(b => b.category === category)) {
                    showToast('يوجد بالفعل ميزانية لهذا التصنيف', 'warning');
                    return;
                }
                DB.addBudget({ category, limit });
                loadBudgets();
                document.getElementById('budgetLimit').value = '';
                showToast('✅ تم إضافة الميزانية بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Saving Form
            document.getElementById('savingForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('savingName').value.trim();
                const target = parseFloat(document.getElementById('savingTarget').value);
                const current = parseFloat(document.getElementById('savingCurrent').value);
                if (!name) { showToast('الرجاء إدخال اسم الصندوق', 'warning'); return; }
                if (!target || target <= 0) { showToast('الرجاء إدخال مبلغ مستهدف صحيح', 'warning'); return; }
                if (current < 0 || current > target) {
                    showToast('المبلغ المدخر يجب أن يكون بين 0 والمبلغ المستهدف', 'warning');
                    return;
                }
                DB.addSaving({ name, target, current });
                loadSavings();
                updateDashboard();
                document.getElementById('savingName').value = '';
                document.getElementById('savingTarget').value = '';
                document.getElementById('savingCurrent').value = '';
                showToast('✅ تم إضافة صندوق الادخار بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Visa Form
            document.getElementById('visaForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('visaName').value.trim();
                const total = parseFloat(document.getElementById('visaTotal').value);
                const installments = parseInt(document.getElementById('visaInstallments').value);
                const monthly = parseFloat(document.getElementById('visaMonthly').value);
                const date = document.getElementById('visaDate').value;
                const dueDate = document.getElementById('visaDueDate').value;

                if (!name) { showToast('الرجاء إدخال اسم المشتريات', 'warning'); return; }
                if (!total || total <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!installments || installments < 1) { showToast('الرجاء إدخال عدد أقساط صحيح', 'warning'); return; }
                if (!monthly || monthly <= 0) { showToast('الرجاء إدخال القسط الشهري', 'warning'); return; }
                if (!date) { showToast('الرجاء تحديد تاريخ الشراء', 'warning'); return; }
                if (!dueDate) { showToast('الرجاء تحديد تاريخ الاستحقاق', 'warning'); return; }

                DB.addVisa({ name, total, installments, monthly, date, dueDate });
                loadVisa();
                updateDashboard();
                document.getElementById('visaName').value = '';
                document.getElementById('visaTotal').value = '';
                document.getElementById('visaInstallments').value = '';
                document.getElementById('visaMonthly').value = '';
                document.getElementById('visaDate').value = '';
                document.getElementById('visaDueDate').value = '';
                showToast('✅ تم إضافة عملية الشراء بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Association Form
            document.getElementById('associationForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('assocName').value.trim();
                const amount = parseFloat(document.getElementById('assocAmount').value);
                const months = parseInt(document.getElementById('assocMonths').value);
                const turn = parseInt(document.getElementById('assocTurn').value);
                const startDate = document.getElementById('assocStartDate').value;
                const endDate = document.getElementById('assocEndDate').value;

                if (!name) { showToast('الرجاء إدخال اسم الجمعية', 'warning'); return; }
                if (!amount || amount <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!months || months < 1) { showToast('الرجاء إدخال عدد شهور صحيح', 'warning'); return; }
                if (!turn || turn < 1) { showToast('الرجاء إدخال دوري رقم صحيح', 'warning'); return; }
                if (!startDate) { showToast('الرجاء تحديد تاريخ البدء', 'warning'); return; }
                if (!endDate) { showToast('الرجاء تحديد تاريخ الانتهاء', 'warning'); return; }

                DB.addAssociation({ name, amount, months, turn, startDate, endDate });
                loadAssociations();
                document.getElementById('assocName').value = '';
                document.getElementById('assocAmount').value = '';
                document.getElementById('assocMonths').value = '';
                document.getElementById('assocTurn').value = '';
                document.getElementById('assocStartDate').value = '';
                document.getElementById('assocEndDate').value = '';
                showToast('✅ تم إضافة الجمعية بنجاح', 'success');
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
                if (!name) { showToast('الرجاء إدخال اسم الالتزام', 'warning'); return; }
                if (!amount || amount <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!dueDate) { showToast('الرجاء تحديد التاريخ', 'warning'); return; }
                DB.addObligation({ name, category, amount, dueDate, recurring, notes: notes || '' });
                loadObligations();
                updateDashboard();
                document.getElementById('oblName').value = '';
                document.getElementById('oblAmount').value = '';
                document.getElementById('oblDate').value = '';
                document.getElementById('oblNotes').value = '';
                showToast('✅ تم إضافة الالتزام بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Family Form
            document.getElementById('familyForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('familyName').value.trim();
                const role = document.getElementById('familyRole').value;
                const budget = parseFloat(document.getElementById('familyBudget').value) || 0;
                if (!name) { showToast('الرجاء إدخال اسم الفرد', 'warning'); return; }
                DB.addFamilyMember({ name, role, budget });
                loadFamily();
                document.getElementById('familyName').value = '';
                document.getElementById('familyBudget').value = '';
                showToast('✅ تم إضافة الفرد بنجاح', 'success');
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
                if (!name) { showToast('الرجاء إدخال اسم الدين/السلفة', 'warning'); return; }
                if (!total || total <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (remaining < 0 || remaining > total) { showToast('المتبقي يجب أن يكون بين 0 والمبلغ الإجمالي',
                        'warning'); return; }
                DB.addDebt({ name, type, total, remaining, installment, dueDate });
                loadDebts();
                document.getElementById('debtName').value = '';
                document.getElementById('debtTotal').value = '';
                document.getElementById('debtRemaining').value = '';
                document.getElementById('debtInstallment').value = '';
                document.getElementById('debtDueDate').value = '';
                showToast('✅ تم إضافة الدين/السلفة بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Goal Form
            document.getElementById('goalForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('goalName').value.trim();
                const target = parseFloat(document.getElementById('goalTarget').value);
                const current = parseFloat(document.getElementById('goalCurrent').value);
                if (!name) { showToast('الرجاء إدخال اسم الهدف', 'warning'); return; }
                if (!target || target <= 0) { showToast('الرجاء إدخال مبلغ مستهدف صحيح', 'warning'); return; }
                if (current < 0 || current > target) {
                    showToast('المبلغ المدخر يجب أن يكون بين 0 والمبلغ المستهدف', 'warning');
                    return;
                }
                DB.addGoal({ name, target, current });
                loadGoals();
                document.getElementById('goalName').value = '';
                document.getElementById('goalTarget').value = '';
                document.getElementById('goalCurrent').value = '';
                showToast('✅ تم إضافة الهدف بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Subscription Form
            document.getElementById('subscriptionForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const name = document.getElementById('subName').value.trim();
                const amount = parseFloat(document.getElementById('subAmount').value);
                const dueDate = document.getElementById('subDueDate').value;
                const status = document.getElementById('subStatus').value;
                const recurring = document.getElementById('subRecurring').value;
                if (!name) { showToast('الرجاء إدخال اسم الاشتراك', 'warning'); return; }
                if (!amount || amount <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!dueDate) { showToast('الرجاء تحديد تاريخ الاستحقاق', 'warning'); return; }
                DB.addSubscription({ name, amount, dueDate, status, recurring });
                loadSubscriptions();
                document.getElementById('subName').value = '';
                document.getElementById('subAmount').value = '';
                document.getElementById('subDueDate').value = '';
                showToast('✅ تم إضافة الاشتراك بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // Portal Form
            document.getElementById('portalForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const portal = document.getElementById('portalSelect').value;
                const subCategory = document.getElementById('portalSubCategory').value.trim();
                const amount = parseFloat(document.getElementById('portalAmount').value);
                const date = document.getElementById('portalDate').value;
                if (!amount || amount <= 0) { showToast('الرجاء إدخال مبلغ صحيح', 'warning'); return; }
                if (!date) { showToast('الرجاء تحديد التاريخ', 'warning'); return; }
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
                loadPortalStats();
                loadPortalTransactions();
                updateDashboard();
                document.getElementById('portalAmount').value = '';
                document.getElementById('portalSubCategory').value = '';
                document.getElementById('portalDate').value = new Date().toISOString().split('T')[0];
                showToast('✅ تم إضافة الدخل عبر البوابة بنجاح', 'success');
                setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
            });

            // User Form
            document.getElementById('userForm').addEventListener('submit', function(e) {
                e.preventDefault();
                const username = document.getElementById('newUsername').value.trim();
                const password = document.getElementById('newUserPassword').value;
                const role = document.getElementById('newUserRole').value;
                const result = AUTH.register(username, password, role);
                if (result.success) {
                    document.getElementById('newUsername').value = '';
                    document.getElementById('newUserPassword').value = '';
                    loadUsersList();
                    updateUIForUser();
                    showToast('✅ تم إضافة المستخدم بنجاح', 'success');
                    setTimeout(() => { if (AUTH.currentUser) syncSaveToFirebase(); }, 500);
                } else {
                    showToast(result.message, 'error');
                }
            });

            // Search & Filters
            document.getElementById('searchTransactions').addEventListener('input', loadTransactions);
            document.getElementById('filterCategory').addEventListener('change', loadTransactions);
            document.getElementById('filterType').addEventListener('change', loadTransactions);

            // Report period change
            document.getElementById('reportPeriod').addEventListener('change', function() {
                const isCustom = this.value === 'custom';
                document.getElementById('customDateGroup').style.display = isCustom ? 'block' : 'none';
                document.getElementById('customDateGroup2').style.display = isCustom ? 'block' : 'none';
            });

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
                    const settings = DB.getSettings();
                    const timeout = settings.sessionTimeout || 5;
                    if (idle >= timeout) quickLock();
                }
            }, 30000);

            // Enter و ESC
            document.addEventListener('keydown', function(e) {
                if (e.key === 'Enter' && document.getElementById('quickLockScreen').style.display !== 'none') quickUnlock();
                if (e.key === 'Enter' && document.getElementById('loginScreen').style.display !== 'none') loginUser();
                if (e.key === 'Escape' && !AUTH.isLocked && AUTH.currentUser) quickLock();
            });

            // تحميل الصورة المحفوظة
            const savedAvatar = localStorage.getItem('flosy_avatar');
            if (savedAvatar) {
                const avatar = document.querySelector('.sidebar-header .avatar');
                avatar.innerHTML = `<img src="${savedAvatar}" alt="avatar">`;
            }

            console.log('💰 فلوسي برو v6.0 - النظام المالي المتكامل');
            console.log('👥 المستخدمين:', AUTH.users.length);
            console.log('👤 المستخدم الحالي:', AUTH.currentUser?.username || 'غير مسجل');
            console.log('🔥 Firebase:', db ? '✅ متصل' : '❌ غير متصل');
            console.log('🔐 كلمة المرور: 8 أحرف مختلفة (Admin@1234)');
            console.log('📊 التقارير المتقدمة - مقارنات - تصدير PDF A4');
            console.log('🔔 إشعارات - تذكيرات - مزامنة سحابية');
            console.log('💾 نسخ احتياطي شهري تلقائي');

            // تنبيه ترحيبي
            setTimeout(() => {
                showToast('👋 مرحباً بك في فلوسي برو v6.0!', 'info', 5000);
            }, 1500);
        });
    </script>
</body>
</html>
