<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบขอซื้อวัสดุสำนักงาน - TSM</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Sarabun', sans-serif; }
        .gradient-bg { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 50%, #60a5fa 100%); }
        .card-shadow { box-shadow: 0 4px 20px rgba(59, 130, 246, 0.08), 0 1px 3px rgba(0, 0, 0, 0.05); }
        .btn-primary { background: linear-gradient(135deg, #2563eb 0%, #3b82f6 100%); }
        .btn-primary:hover { background: linear-gradient(135deg, #1d4ed8 0%, #2563eb 100%); transform: translateY(-1px); }
        .input-focus:focus { border-color: #3b82f6; box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1); }
        .table-header { background: linear-gradient(135deg, #f8fafc 0%, #e0f2fe 100%); }
        .status-pending { background-color: #fef3c7; color: #92400e; }
        .status-approved { background-color: #dbeafe; color: #1e40af; }
        .status-rejected { background-color: #fee2e2; color: #dc2626; }
        .status-processing { background-color: #dbeafe; color: #1e40af; }
        .nav-active { background: rgba(255,255,255,0.15); backdrop-filter: blur(10px); }
        .success-animation { animation: successPulse 0.6s ease-out; }
        @keyframes successPulse {
            0% { transform: scale(0.8); opacity: 0; }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); opacity: 1; }
        }
        .floating-animation { animation: float 3s ease-in-out infinite; }
        @keyframes float {
            0%, 100% { transform: translateY(0px); }
            50% { transform: translateY(-10px); }
        }
        .slide-in { animation: slideIn 0.5s ease-out; }
        @keyframes slideIn {
            from { transform: translateX(-100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }
        .login-bg { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 50%, #60a5fa 100%); }
        .user-type-card { transition: all 0.3s ease; border-radius: 12px; }
        .user-type-card:hover { transform: translateY(-3px); box-shadow: 0 10px 30px rgba(59, 130, 246, 0.15); }
        .user-type-selected { border: 2px solid #3b82f6; background: rgba(59, 130, 246, 0.05); }
    </style>
</head>
<body class="bg-gradient-to-br from-slate-50 to-blue-50 min-h-screen">

    <!-- Login Screen -->
    <div id="loginScreen" class="fixed inset-0 login-bg flex items-center justify-center z-50 overflow-y-auto">
        <div class="bg-white rounded-2xl shadow-2xl p-8 w-full max-w-4xl mx-4 my-8">
            <div class="text-center mb-8">
                <div class="w-20 h-20 bg-gradient-to-r from-blue-500 to-purple-600 rounded-full flex items-center justify-center mx-auto mb-4">
                    <span class="text-white font-bold text-2xl">TSM</span>
                </div>
                <h2 class="text-2xl font-bold text-gray-800 mb-2">เข้าสู่ระบบ</h2>
                <p class="text-gray-600">ระบบขอซื้อวัสดุสำนักงาน</p>
            </div>

            <!-- Usage Instructions -->
            <div class="bg-blue-50 border border-blue-200 rounded-lg p-6 mb-8">
                <div class="flex items-center mb-4">
                    <div class="w-8 h-8 bg-blue-500 rounded-full flex items-center justify-center mr-3">
                        <span class="text-white font-bold">📖</span>
                    </div>
                    <h3 class="text-lg font-bold text-blue-800">คู่มือการใช้งานระบบ</h3>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- Left Column -->
                    <div class="space-y-4">
                        <div class="bg-white rounded-lg p-4 border border-blue-100">
                            <h4 class="font-semibold text-blue-800 mb-2 flex items-center">
                                <span class="mr-2">👤</span> สำหรับพนักงาน
                            </h4>
                            <ul class="text-sm text-gray-700 space-y-1">
                                <li>• เลือก "พนักงาน" ด้านล่าง</li>
                                <li>• ใช้ Username: <code class="bg-gray-100 px-1 rounded">user</code> Password: <code class="bg-gray-100 px-1 rounded">password</code></li>
                                <li>• หรือใช้อีเมลบริษัท เช่น john.doe@taisin.co.th</li>
                                <li>• Password: <code class="bg-gray-100 px-1 rounded">123456</code></li>
                                <li>• สามารถขอซื้อวัสดุและติดตามสถานะได้</li>
                            </ul>
                        </div>
                        
                        <div class="bg-white rounded-lg p-4 border border-red-100">
                            <h4 class="font-semibold text-red-800 mb-2 flex items-center">
                                <span class="mr-2">👨‍💼</span> สำหรับ Admin
                            </h4>
                            <ul class="text-sm text-gray-700 space-y-1">
                                <li>• เลือก "Admin" ด้านล่าง</li>
                                <li>• Username: <code class="bg-gray-100 px-1 rounded">admin</code></li>
                                <li>• Password: <code class="bg-gray-100 px-1 rounded">123456</code></li>
                                <li>• จัดการระบบ อนุมัติคำขอ และดูรายงาน</li>
                            </ul>
                        </div>
                    </div>
                    
                    <!-- Right Column -->
                    <div class="space-y-4">
                        <div class="bg-white rounded-lg p-4 border border-purple-100">
                            <h4 class="font-semibold text-purple-700 mb-2 flex items-center">
                                <span class="mr-2">🔧</span> ขั้นตอนการใช้งาน
                            </h4>
                            <ol class="text-sm text-gray-700 space-y-1">
                                <li>1. เลือกประเภทผู้ใช้ (พนักงาน/Admin)</li>
                                <li>2. กรอกชื่อผู้ใช้และรหัสผ่าน</li>
                                <li>3. คลิก "เข้าสู่ระบบ"</li>
                                <li>4. เริ่มใช้งานระบบได้ทันที</li>
                            </ol>
                        </div>
                    </div>
                </div>
                
                <!-- Features Overview -->
                <div class="mt-6 bg-gradient-to-r from-blue-500 to-purple-600 rounded-lg p-4 text-white">
                    <h4 class="font-semibold mb-3 flex items-center">
                        <span class="mr-2">🌟</span> ฟีเจอร์หลักของระบบ
                    </h4>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 text-sm">
                        <div class="bg-white bg-opacity-20 rounded-lg p-3">
                            <div class="font-medium mb-1">📝 ขอซื้อวัสดุ</div>
                            <div class="text-blue-100">สร้างคำขอซื้อวัสดุสำนักงานได้ง่ายๆ</div>
                        </div>
                        <div class="bg-white bg-opacity-20 rounded-lg p-3">
                            <div class="font-medium mb-1">📋 ติดตามสถานะ</div>
                            <div class="text-blue-100">ดูสถานะคำขอแบบเรียลไทม์</div>
                        </div>
                        <div class="bg-white bg-opacity-20 rounded-lg p-3">
                            <div class="font-medium mb-1">✅ อนุมัติอัตโนมัติ</div>
                            <div class="text-blue-100">ระบบจัดส่งให้ผู้อนุมัติตามแผนก</div>
                        </div>
                    </div>
                </div>
                
                <!-- Security Notice -->
                <div class="mt-4 bg-yellow-50 border border-yellow-200 rounded-lg p-3">
                    <div class="flex items-center text-yellow-800">
                        <span class="mr-2">🔒</span>
                        <span class="text-sm">
                            <strong>หมายเหตุ:</strong> นี่เป็นระบบสาธิต ข้อมูลจะถูกเก็บในเครื่องของคุณเท่านั้น 
                            สามารถเปลี่ยนรหัสผ่านได้หลังเข้าสู่ระบบ
                        </span>
                    </div>
                </div>
            </div>

            <!-- User Type Selection -->
            <div id="userTypeSelection" class="space-y-4 mb-6">
                <p class="text-sm font-medium text-gray-700 text-center mb-4">เลือกประเภทผู้ใช้งาน</p>
                <div class="grid grid-cols-2 gap-4">
                    <div class="user-type-card bg-white border-2 border-gray-200 rounded-lg p-4 cursor-pointer text-center" data-type="user">
                        <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-2">
                            <span class="text-blue-800 text-xl">👤</span>
                        </div>
                        <h3 class="font-semibold text-gray-800">พนักงาน</h3>
                        <p class="text-xs text-gray-500 mt-1">ขอซื้อวัสดุ ดูสถานะ</p>
                    </div>
                    <div class="user-type-card bg-white border-2 border-gray-200 rounded-lg p-4 cursor-pointer text-center" data-type="admin">
                        <div class="w-12 h-12 bg-red-100 rounded-full flex items-center justify-center mx-auto mb-2">
                            <span class="text-red-800 text-xl">👨‍💼</span>
                        </div>
                        <h3 class="font-semibold text-gray-800">Admin</h3>
                        <p class="text-xs text-gray-500 mt-1">จัดการระบบ อนุมัติ</p>
                    </div>
                </div>
            </div>
            
            <form id="loginForm" class="space-y-6">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">ชื่อผู้ใช้</label>
                    <input type="text" id="loginUsername" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" placeholder="กรอกชื่อผู้ใช้" required>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">รหัสผ่าน</label>
                    <div class="relative">
                        <input type="password" id="loginPassword" class="w-full px-4 py-3 pr-12 border border-gray-300 rounded-lg input-focus transition-all" placeholder="กรอกรหัสผ่าน" required>
                        <button type="button" id="toggleLoginPassword" class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500 hover:text-gray-700">
                            <span id="loginPasswordIcon">👁️</span>
                        </button>
                    </div>
                </div>
                <div id="loginError" class="text-red-600 text-sm hidden">
                    ชื่อผู้ใช้หรือรหัสผ่านไม่ถูกต้อง
                </div>
                <button type="submit" class="w-full btn-primary text-white py-3 rounded-lg hover:shadow-lg transition-all">
                    เข้าสู่ระบบ
                </button>
            </form>
            
            <div class="mt-6 text-center text-sm text-gray-500">
                <div id="adminCredentials" class="hidden">
                    <p>ข้อมูลสำหรับ Admin:</p>
                    <p>Username: <span class="font-mono bg-gray-100 px-2 py-1 rounded">admin</span></p>
                    <p>Password: <span class="font-mono bg-gray-100 px-2 py-1 rounded">123456</span></p>
                </div>
                <div id="userCredentials" class="hidden">
                    <p>ข้อมูลสำหรับพนักงาน:</p>
                    <p>Username: <span class="font-mono bg-gray-100 px-2 py-1 rounded">user</span></p>
                    <p>Password: <span class="font-mono bg-gray-100 px-2 py-1 rounded">password</span></p>
                    <hr class="my-2">
                    <p class="text-xs">หรือใช้อีเมลของคุณ เช่น:</p>
                    <p>Username: <span class="font-mono bg-gray-100 px-2 py-1 rounded">john.doe@taisin.co.th</span></p>
                    <p>Password: <span class="font-mono bg-gray-100 px-2 py-1 rounded">123456</span></p>
                    <hr class="my-2">

                </div>
            </div>
        </div>
    </div>

    <!-- Header -->
    <header id="mainHeader" class="gradient-bg text-white shadow-lg hidden">
        <div class="container mx-auto px-6 py-4">
            <div class="flex items-center justify-between">
                <div class="flex items-center space-x-4">
                    <div class="w-12 h-12 bg-white rounded-lg flex items-center justify-center shadow-lg">
                        <span class="text-blue-600 font-bold text-lg">TSM</span>
                    </div>
                    <div>
                        <h1 class="text-2xl font-bold">บริษัท ไทยซิน แมนูแฟคเจอริ่ง จำกัด</h1>
                        <p class="text-xl font-semibold text-blue-100">ระบบขอซื้อวัสดุสำนักงาน</p>
                        <p class="text-lg text-blue-100 mt-1">พนักงาน: <span id="employeeName" class="font-semibold"></span></p>
                    </div>
                </div>
                <div class="flex items-center space-x-4">
                    <div class="text-right">
                        <p class="text-sm opacity-90">ยินดีต้อนรับ</p>
                        <p class="font-semibold" id="currentUser">คุณ</p>
                        <p class="text-xs opacity-75" id="currentUserTitle">พนักงาน</p>
                    </div>
                    <button id="logoutBtn" class="w-10 h-10 bg-slate-600 rounded-full flex items-center justify-center cursor-pointer hover:bg-slate-700 transition-colors" title="ออกจากระบบ" onclick="logout()">
                        <span class="text-white font-bold">🚪</span>
                    </button>
                    <div class="relative">
                        <button id="userMenuBtn" class="w-10 h-10 bg-blue-600 rounded-full flex items-center justify-center cursor-pointer hover:bg-blue-700 transition-colors">
                            <span class="text-white font-bold" id="userIcon">👤</span>
                        </button>
                        <div id="userMenu" class="absolute right-0 mt-2 w-48 bg-white rounded-lg shadow-lg border hidden z-50">
                            <div class="py-2">
                                <div class="px-4 py-2 text-sm text-gray-700 border-b">
                                    <div class="font-medium" id="menuUserName">พนักงานทั่วไป</div>
                                    <div class="text-xs text-gray-500" id="menuUserRole">พนักงาน</div>
                                </div>
                                <button onclick="showProfile()" class="w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">
                                    👤 ข้อมูลส่วนตัว
                                </button>
                                <button onclick="showSettings()" class="w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">
                                    ⚙️ ตั้งค่า
                                </button>
                                <hr class="my-1">
                                <button onclick="logout()" class="w-full text-left px-4 py-2 text-sm text-red-600 hover:bg-red-50">
                                    🚪 ออกจากระบบ
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <!-- Navigation -->
    <nav id="mainNav" class="bg-white shadow-sm border-b hidden">
        <div class="container mx-auto px-6">
            <div class="flex space-x-8">
                <button id="navRequest" class="nav-btn px-4 py-3 text-slate-600 hover:text-blue-600 border-b-2 border-transparent hover:border-blue-600 transition-all nav-active">
                    📝 ขอซื้อใหม่
                </button>
                <button id="navList" class="nav-btn px-4 py-3 text-slate-600 hover:text-blue-600 border-b-2 border-transparent hover:border-blue-600 transition-all">
                    📋 รายการของฉัน
                </button>
                <button id="navApproval" class="nav-btn px-4 py-3 text-slate-600 hover:text-blue-600 border-b-2 border-transparent hover:border-blue-600 transition-all admin-only hidden">
                    ✅ อนุมัติ/ซื้อ
                </button>
                <div class="relative admin-only hidden">
                    <button id="adminMenuBtn" class="nav-btn px-4 py-3 text-slate-600 hover:text-blue-600 border-b-2 border-transparent hover:border-blue-600 transition-all flex items-center">
                        👨‍💼 Admin
                        <span class="ml-1 text-xs">▼</span>
                    </button>
                    <div id="adminMenu" class="absolute top-full left-0 mt-1 w-48 bg-white rounded-lg shadow-lg border hidden z-50">
                        <div class="py-2">
                            <button id="navManage" class="admin-nav-btn w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100 flex items-center">
                                🛠️ จัดการระบบ
                            </button>
                            <button id="navReports" class="admin-nav-btn w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100 flex items-center">
                                📊 รายงาน
                            </button>
                            <button id="navDashboard" class="admin-nav-btn w-full text-left px-4 py-2 text-sm text-gray-700 hover:bg-gray-100 flex items-center">
                                📈 แดชบอร์ด
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </nav>

    <!-- Main Content -->
    <main id="mainContent" class="container mx-auto px-6 py-8 hidden">
        <!-- Request Form -->
        <div id="requestForm" class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-8 mb-8 slide-in border border-white/20">
            <div class="flex items-center mb-6">
                <div class="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center mr-3">
                    <span class="text-blue-600 font-bold">📝</span>
                </div>
                <h2 class="text-2xl font-bold text-gray-800">แบบคำร้องขอซื้อวัสดุสำนักงาน</h2>
            </div>

            <form id="form" class="space-y-6">
                <!-- Basic Info -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ชื่อพนักงาน *</label>
                        <input type="text" id="employeeNameField" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" placeholder="กรอกชื่อพนักงาน" required>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">รหัสพนักงาน</label>
                        <input type="text" id="employeeId" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" placeholder="กรอกรหัสพนักงาน (ถ้ามี)">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">แผนก / ฝ่าย *</label>
                        <select id="department" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" required>
                            <option value="">เลือกแผนก / ฝ่าย</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">วันที่ขอซื้อ *</label>
                        <input type="date" id="reqDate" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" required>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ความเร่งด่วน *</label>
                        <select id="urgency" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" required>
                            <option>ด่วน (วันนี้)</option>
                            <option>พรุ่งนี้</option>
                            <option>2 - 3 วัน</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">วัตถุประสงค์ *</label>
                        <select id="purpose" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" required>
                            <option>เลือกวัตถุประสงค์</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-2">ผู้อนุมัติ *</label>
                        <select id="approverEmail" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus transition-all" required>
                            <option value="">เลือกผู้อนุมัติ</option>
                        </select>
                    </div>
                </div>

                <!-- Items Table -->
                <div class="bg-blue-50/30 rounded-lg p-6 border border-blue-100/50">
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-lg font-semibold text-gray-800">รายการวัสดุที่ขอซื้อ</h3>
                        <button type="button" id="addItemBtn" class="bg-green-500 text-white px-4 py-2 rounded-lg hover:bg-green-600 transition-colors">
                            + เพิ่มรายการ
                        </button>
                    </div>
                    
                    <div class="overflow-x-auto">
                        <table class="w-full border border-gray-200 rounded-lg overflow-hidden">
                            <thead class="table-header">
                                <tr>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">ลำดับ</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">ชื่อวัสดุ</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">หน่วยนับ</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">จำนวน</th>
                                    <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">หมายเหตุ</th>
                                    <th class="px-4 py-3 text-center text-sm font-medium text-gray-700">จัดการ</th>
                                </tr>
                            </thead>
                            <tbody id="itemsBody" class="bg-white divide-y divide-gray-200">
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Submit Button -->
                <div class="flex justify-end space-x-4 pt-6">
                    <button type="button" id="resetBtn" class="px-6 py-3 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors">
                        ล้างข้อมูล
                    </button>
                    <button type="submit" class="btn-primary text-white px-8 py-3 rounded-lg hover:shadow-lg transition-all">
                        ส่งคำร้องขอซื้อ
                    </button>
                </div>
            </form>
        </div>

        <!-- Requests List -->
        <div id="requestsList" class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-8 hidden border border-white/20">
            <div class="flex items-center justify-between mb-6">
                <div class="flex items-center">
                    <div class="w-8 h-8 bg-blue-100 rounded-lg flex items-center justify-center mr-3">
                        <span class="text-blue-600 font-bold">📋</span>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800" id="requestsListTitle">รายการขอซื้อของฉัน</h2>
                </div>
                <div class="text-sm text-gray-500">
                    ทั้งหมด <span id="totalRequests" class="font-semibold text-blue-600">0</span> รายการ
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full border border-gray-200 rounded-lg overflow-hidden">
                    <thead class="table-header">
                        <tr>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">เลขที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">วันที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">แผนก</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">ความเร่งด่วน</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">สถานะ</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700 admin-only">ผู้อนุมัติ</th>
                            <th class="px-4 py-3 text-center text-sm font-medium text-gray-700">จัดการ</th>
                        </tr>
                    </thead>
                    <tbody id="requestsTableBody" class="bg-white divide-y divide-gray-200">
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Approval System -->
        <div id="approvalSystem" class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-8 hidden admin-only border border-white/20">
            <div class="flex items-center justify-between mb-6">
                <div class="flex items-center">
                    <div class="w-8 h-8 bg-green-100 rounded-lg flex items-center justify-center mr-3">
                        <span class="text-green-600 font-bold">✅</span>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">ระบบอนุมัติและจัดซื้อ</h2>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                <div class="bg-yellow-50 p-6 rounded-lg border border-yellow-200">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-yellow-600 text-sm font-medium">รอการอนุมัติ</p>
                            <p class="text-2xl font-bold text-yellow-700" id="pendingCount">0</p>
                        </div>
                        <div class="w-12 h-12 bg-yellow-100 rounded-lg flex items-center justify-center">
                            <span class="text-yellow-600 text-xl">⏳</span>
                        </div>
                    </div>
                </div>
                <div class="bg-blue-50 p-6 rounded-lg border border-blue-200">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-blue-600 text-sm font-medium">อนุมัติแล้ว</p>
                            <p class="text-2xl font-bold text-blue-700" id="approvedCount">0</p>
                        </div>
                        <div class="w-12 h-12 bg-blue-100 rounded-lg flex items-center justify-center">
                            <span class="text-blue-600 text-xl">✅</span>
                        </div>
                    </div>
                </div>
                <div class="bg-green-50 p-6 rounded-lg border border-green-200">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-green-600 text-sm font-medium">จัดเบิกแล้ว</p>
                            <p class="text-2xl font-bold text-green-700" id="dispensedCount">0</p>
                        </div>
                        <div class="w-12 h-12 bg-green-100 rounded-lg flex items-center justify-center">
                            <span class="text-green-600 text-xl">📦</span>
                        </div>
                    </div>
                </div>
            </div>

            <div id="approvalTable" class="overflow-x-auto">
                <table class="w-full border border-gray-200 rounded-lg overflow-hidden">
                    <thead class="table-header">
                        <tr>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">เลขที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">วันที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">แผนก</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">รายการ</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">สถานะ</th>
                            <th class="px-4 py-3 text-center text-sm font-medium text-gray-700">การดำเนินการ</th>
                        </tr>
                    </thead>
                    <tbody id="approvalTableBody" class="bg-white divide-y divide-gray-200">
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Admin Management System -->
        <div id="manageSystem" class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-8 hidden admin-only border border-white/20">
            <div class="flex items-center justify-between mb-6">
                <div class="flex items-center">
                    <div class="w-8 h-8 bg-red-100 rounded-lg flex items-center justify-center mr-3">
                        <span class="text-red-600 font-bold">🛠️</span>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">จัดการระบบ (Admin)</h2>
                </div>
                <div class="flex space-x-3">
                    <button id="bulkApprove" class="bg-green-500 text-white px-4 py-2 rounded-lg hover:bg-green-600 transition-colors">
                        อนุมัติทั้งหมด
                    </button>
                    <button id="exportAllData" class="bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600 transition-colors">
                        ส่งออกข้อมูล
                    </button>
                </div>
            </div>

            <!-- Filter Controls -->
            <div class="bg-blue-50/30 p-4 rounded-lg mb-6 border border-blue-100/50">
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">กรองตามสถานะ</label>
                        <select id="filterStatus" class="w-full px-3 py-2 border border-gray-300 rounded-md text-sm">
                            <option value="">ทั้งหมด</option>
                            <option value="รอการอนุมัติ">รอการอนุมัติ</option>
                            <option value="อนุมัติแล้ว">อนุมัติแล้ว</option>
                            <option value="เบิกจ่ายแล้ว">เบิกจ่ายแล้ว</option>
                            <option value="ปฏิเสธ">ปฏิเสธ</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">กรองตามแผนก</label>
                        <select id="filterDepartment" class="w-full px-3 py-2 border border-gray-300 rounded-md text-sm">
                            <option value="">ทั้งหมด</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">วันที่เริ่มต้น</label>
                        <input type="date" id="filterStartDate" class="w-full px-3 py-2 border border-gray-300 rounded-md text-sm">
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">วันที่สิ้นสุด</label>
                        <input type="date" id="filterEndDate" class="w-full px-3 py-2 border border-gray-300 rounded-md text-sm">
                    </div>
                </div>
                <div class="mt-4 flex space-x-3">
                    <button id="applyFilter" class="bg-blue-500 text-white px-4 py-2 rounded-lg hover:bg-blue-600 transition-colors">
                        ค้นหา
                    </button>
                    <button id="clearFilter" class="bg-gray-500 text-white px-4 py-2 rounded-lg hover:bg-gray-600 transition-colors">
                        ล้างตัวกรอง
                    </button>
                </div>
            </div>

            <!-- Management Table -->
            <div class="overflow-x-auto">
                <table class="w-full border border-gray-200 rounded-lg overflow-hidden">
                    <thead class="table-header">
                        <tr>
                            <th class="px-4 py-3 text-center">
                                <input type="checkbox" id="selectAll" class="rounded">
                            </th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">เลขที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">วันที่</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">แผนก</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">รายการ</th>
                            <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">สถานะ</th>
                            <th class="px-4 py-3 text-center text-sm font-medium text-gray-700">จัดการ</th>
                        </tr>
                    </thead>
                    <tbody id="manageTableBody" class="bg-white divide-y divide-gray-200">
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Reports -->
        <div id="reportsSection" class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-8 hidden admin-only border border-white/20">
            <div class="flex items-center justify-between mb-6">
                <div class="flex items-center">
                    <div class="w-8 h-8 bg-purple-100 rounded-lg flex items-center justify-center mr-3">
                        <span class="text-purple-600 font-bold">📊</span>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">รายงานและสถิติ</h2>
                </div>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">วันที่เริ่มต้น</label>
                    <input type="date" id="reportStartDate" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">วันที่สิ้นสุด</label>
                    <input type="date" id="reportEndDate" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus">
                </div>
            </div>

            <div class="flex space-x-4 mb-8">
                <button id="generateReport" class="btn-primary text-white px-6 py-3 rounded-lg hover:shadow-lg transition-all">
                    สร้างรายงาน
                </button>
                <button id="exportReport" class="bg-green-500 text-white px-6 py-3 rounded-lg hover:bg-green-600 transition-colors">
                    ส่งออก Excel
                </button>
                <button id="printReport" class="bg-gray-500 text-white px-6 py-3 rounded-lg hover:bg-gray-600 transition-colors">
                    พิมพ์รายงาน
                </button>
            </div>

            <div id="reportResults" class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <div class="bg-blue-50/50 p-6 rounded-lg border border-blue-100">
                    <h3 class="text-lg font-semibold mb-4">สถิติการขอซื้อตามแผนก</h3>
                    <canvas id="departmentChart" width="400" height="300"></canvas>
                </div>
                <div class="bg-blue-50/50 p-6 rounded-lg border border-blue-100">
                    <h3 class="text-lg font-semibold mb-4">แนวโน้มการขอซื้อรายเดือน</h3>
                    <canvas id="trendChart" width="400" height="300"></canvas>
                </div>
            </div>
        </div>

        <!-- Dashboard -->
        <div id="dashboardSection" class="hidden admin-only">
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                <div class="bg-gradient-to-r from-blue-500 to-blue-600 p-6 rounded-xl text-white">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-blue-100 text-sm">คำขอวันนี้</p>
                            <p class="text-3xl font-bold" id="todayRequests">0</p>
                        </div>
                        <div class="floating-animation">📝</div>
                    </div>
                </div>
                <div class="bg-gradient-to-r from-green-500 to-green-600 p-6 rounded-xl text-white">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-green-100 text-sm">อนุมัติแล้ว</p>
                            <p class="text-3xl font-bold" id="approvedToday">0</p>
                        </div>
                        <div class="floating-animation">✅</div>
                    </div>
                </div>
                <div class="bg-gradient-to-r from-yellow-500 to-yellow-600 p-6 rounded-xl text-white">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-yellow-100 text-sm">รอดำเนินการ</p>
                            <p class="text-3xl font-bold" id="pendingToday">0</p>
                        </div>
                        <div class="floating-animation">⏳</div>
                    </div>
                </div>
                <div class="bg-gradient-to-r from-purple-500 to-purple-600 p-6 rounded-xl text-white">
                    <div class="flex items-center justify-between">
                        <div>
                            <p class="text-purple-100 text-sm">รวมทั้งหมด</p>
                            <p class="text-3xl font-bold" id="totalAll">0</p>
                        </div>
                        <div class="floating-animation">📊</div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <div class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-6 border border-white/20">
                    <h3 class="text-lg font-semibold mb-4">การขอซื้อ 7 วันล่าสุด</h3>
                    <canvas id="weeklyChart" width="400" height="300"></canvas>
                </div>
                <div class="bg-white/80 backdrop-blur-sm rounded-xl card-shadow p-6 border border-white/20">
                    <h3 class="text-lg font-semibold mb-4">วัสดุที่ขอมากที่สุด</h3>
                    <canvas id="topItemsChart" width="400" height="300"></canvas>
                </div>
            </div>
        </div>
    </main>

    <!-- Enhanced Success Modal -->
    <div id="successModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-md mx-4 text-center success-animation">
            <div class="w-20 h-20 bg-gradient-to-r from-green-400 to-green-500 rounded-full flex items-center justify-center mx-auto mb-4 floating-animation">
                <span class="text-white text-3xl">✓</span>
            </div>
            <div class="mb-4">
                <div class="w-16 h-1 bg-gradient-to-r from-green-400 to-green-500 rounded mx-auto mb-2"></div>
                <div class="w-8 h-1 bg-gradient-to-r from-green-400 to-green-500 rounded mx-auto"></div>
            </div>
            <h3 class="text-2xl font-bold text-gray-800 mb-2">ส่งคำร้องสำเร็จ!</h3>
            <p class="text-gray-600 mb-6">คำร้องขอเบิกของคุณได้ถูกส่งไปยังผู้อนุมัติเรียบร้อยแล้ว</p>
            <div class="flex space-x-3 justify-center">
                <button id="closeModal" class="btn-primary text-white px-6 py-2 rounded-lg">
                    ตกลง
                </button>
                <button id="viewSubmitted" class="bg-gray-500 text-white px-6 py-2 rounded-lg hover:bg-gray-600">
                    ดูรายการ
                </button>
            </div>
        </div>
    </div>

    <!-- Detail Modal -->
    <div id="detailModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-2xl mx-4 max-h-96 overflow-y-auto">
            <div class="flex items-center justify-between mb-6">
                <h3 class="text-xl font-bold text-gray-800">รายละเอียดคำร้องขอเบิก</h3>
                <button id="closeDetailModal" class="text-gray-400 hover:text-gray-600">
                    <span class="text-2xl">×</span>
                </button>
            </div>
            <div id="detailContent" class="space-y-4">
            </div>
        </div>
    </div>

    <!-- Password Change Modal -->
    <div id="passwordChangeModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-md mx-4">
            <div class="text-center mb-6">
                <div class="w-16 h-16 bg-yellow-100 rounded-full flex items-center justify-center mx-auto mb-4">
                    <span class="text-yellow-600 text-2xl">🔐</span>
                </div>
                <h3 class="text-xl font-bold text-gray-800 mb-2">เปลี่ยนข้อมูลเข้าสู่ระบบ</h3>
                <p class="text-gray-600 text-sm">กรุณาเปลี่ยนชื่อผู้ใช้และรหัสผ่านใหม่ก่อนใช้งานระบบ</p>
            </div>
            <form id="passwordChangeForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">ชื่อผู้ใช้ใหม่</label>
                    <input type="text" id="newUsername" class="w-full px-4 py-3 border border-gray-300 rounded-lg input-focus" 
                           placeholder="กรอกชื่อผู้ใช้ใหม่" required>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">รหัสผ่านใหม่</label>
                    <div class="relative">
                        <input type="password" id="newPassword" class="w-full px-4 py-3 pr-12 border border-gray-300 rounded-lg input-focus" 
                               placeholder="กรอกรหัสผ่านใหม่ (อย่างน้อย 6 ตัวอักษร)" minlength="6" required>
                        <button type="button" id="toggleNewPassword" class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500 hover:text-gray-700">
                            <span id="newPasswordIcon">👁️</span>
                        </button>
                    </div>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-2">ยืนยันรหัสผ่านใหม่</label>
                    <div class="relative">
                        <input type="password" id="confirmPassword" class="w-full px-4 py-3 pr-12 border border-gray-300 rounded-lg input-focus" 
                               placeholder="กรอกรหัสผ่านใหม่อีกครั้ง" minlength="6" required>
                        <button type="button" id="toggleConfirmPassword" class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-500 hover:text-gray-700">
                            <span id="confirmPasswordIcon">👁️</span>
                        </button>
                    </div>
                </div>
                <div id="passwordError" class="text-red-600 text-sm hidden">
                    รหัสผ่านไม่ตรงกัน
                </div>
                <div id="passwordSuccess" class="text-green-600 text-sm hidden">
                    เปลี่ยนข้อมูลเข้าสู่ระบบสำเร็จ!
                </div>
                <button type="submit" class="w-full btn-primary text-white py-3 rounded-lg hover:shadow-lg transition-all">
                    เปลี่ยนข้อมูลเข้าสู่ระบบ
                </button>
            </form>
            <div class="mt-4 text-center text-xs text-gray-500">
                <p>⚠️ คุณต้องเปลี่ยนข้อมูลเข้าสู่ระบบก่อนจึงจะสามารถใช้งานได้</p>
            </div>
        </div>
    </div>

    <!-- Edit Modal -->
    <div id="editModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50">
        <div class="bg-white rounded-xl p-8 max-w-2xl mx-4 max-h-96 overflow-y-auto">
            <div class="flex items-center justify-between mb-6">
                <h3 class="text-xl font-bold text-gray-800">แก้ไขคำร้องขอเบิก</h3>
                <button id="closeEditModal" class="text-gray-400 hover:text-gray-600">
                    <span class="text-2xl">×</span>
                </button>
            </div>
            <form id="editForm" class="space-y-4">
                <input type="hidden" id="editRequestId">
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">แผนก</label>
                        <select id="editDepartment" class="w-full px-3 py-2 border border-gray-300 rounded-md">
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-gray-700 mb-1">สถานะ</label>
                        <select id="editStatus" class="w-full px-3 py-2 border border-gray-300 rounded-md">
                            <option value="รอการอนุมัติ">รอการอนุมัติ</option>
                            <option value="อนุมัติแล้ว">อนุมัติแล้ว</option>
                            <option value="เบิกจ่ายแล้ว">เบิกจ่ายแล้ว</option>
                            <option value="ปฏิเสธ">ปฏิเสธ</option>
                        </select>
                    </div>
                </div>
                <div class="flex justify-end space-x-3 pt-4">
                    <button type="button" id="cancelEdit" class="px-4 py-2 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50">
                        ยกเลิก
                    </button>
                    <button type="submit" class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600">
                        บันทึก
                    </button>
                </div>
            </form>
        </div>
    </div>

    <script>
        // Utility functions
        const $ = (sel, root=document) => root.querySelector(sel);
        const $$ = (sel, root=document) => Array.from(root.querySelectorAll(sel));
        const todayISO = () => { 
            const d = new Date(); 
            const tz = d.getTimezoneOffset(); 
            return new Date(d.getTime() - tz * 60000).toISOString().slice(0,10); 
        };
        const fmtDate = (iso) => iso ? new Date(iso).toLocaleDateString('th-TH', {
            year: 'numeric', month: 'short', day: 'numeric'
        }) : '-';

        // Authentication
        const ADMIN_CREDENTIALS = {
            username: 'admin',
            password: '123456'
        };

        let USER_CREDENTIALS = JSON.parse(localStorage.getItem('userCredentials')) || [
            { username: 'user', password: 'password', name: 'พนักงานทั่วไป', department: 'ฝ่าย IT' },
            { username: 'john.doe@taisin.co.th', password: '123456', name: 'John Doe', department: 'ฝ่าย IT' },
            { username: 'jane.smith@taisin.co.th', password: '123456', name: 'Jane Smith', department: 'ฝ่าย HR' },
            { username: 'mike.wilson@taisin.co.th', password: '123456', name: 'Mike Wilson', department: 'ฝ่าย Account' }
        ];

        let isLoggedIn = false;
        let currentUser = null;
        let userRole = null;
        let selectedUserType = null;
        let needsPasswordChange = false;

        // Master Data
        const FALLBACK_MD = {
            departments: [
                'ฝ่ายบริหาร', 'ฝ่าย HR', 'ฝ่าย IT', 'ฝ่าย CS', 'ฝ่าย Account', 'ฝ่าย Cosmetic',
                'ฝ่าย ผลิตภัณฑ์กระติก', 'ฝ่าย คลังสินค้า', 'ฝ่าย จัดซื้อ', 'ฝ่าย วางแผนการผลิต',
                'ฝ่าย วิศวกรรม', 'ฝ่าย ตรวจสอบคุณภาพ', 'ฝ่าย วิจัยและพัฒนาผลิตภัณฑ์', 'ฝ่าย QMS/RA', 'ฝ่าย ขาย'
            ],
            purposes: ['เลือกวัตถุประสงค์', 'ใช้งานประจำ', 'ใช้งานด่วน', 'โครงการพิเศษ', 'ทดแทนของเสีย', 'อื่นๆ'],
            urgencies: ['ด่วน (วันนี้)', 'พรุ่งนี้', '2 - 3 วัน'],
            approverList: [
                'hatairat.k@taisin.co.th', 'jidapa.a@taisin.co.th', 
                'thanaphat.n@taisin.co.th', 'ruangchai.c@taisin.co.th'
            ],
            approvers: {
                'ฝ่าย CS': 'hatairat.k@taisin.co.th',
                'ฝ่าย Account': 'hatairat.k@taisin.co.th',
                'ฝ่ายบริหาร': 'jidapa.a@taisin.co.th',
                'ฝ่าย Cosmetic': 'jidapa.a@taisin.co.th',
                'ฝ่าย IT': 'thanaphat.n@taisin.co.th',
                'ฝ่าย HR': 'thanaphat.n@taisin.co.th',
                'ฝ่าย จัดซื้อ': 'thanaphat.n@taisin.co.th',
                'ฝ่าย วางแผนการผลิต': 'thanaphat.n@taisin.co.th',
                'ฝ่าย วิศวกรรม': 'thanaphat.n@taisin.co.th',
                'ฝ่าย ตรวจสอบคุณภาพ': 'thanaphat.n@taisin.co.th',
                'ฝ่าย วิจัยและพัฒนาผลิตภัณฑ์': 'thanaphat.n@taisin.co.th',
                'ฝ่าย QMS/RA': 'thanaphat.n@taisin.co.th',
                'ฝ่าย คลังสินค้า': 'jidapa.a@taisin.co.th',
                'ฝ่าย ผลิตภัณฑ์กระติก': 'jidapa.a@taisin.co.th',
                'ฝ่าย ขาย': 'ruangchai.c@taisin.co.th'
            },
            itemCatalog: [
                'กระดาษ A4', 'กระดาษ A3', 'ปากกาสีน้ำเงิน', 'ปากกาสีแดง',
                'ปากกาไวท์บอร์ดสีดำ', 'ปากกาไวท์บอร์ดสีแดง', 'ปากกาไวท์บอร์ดสีน้ำเงิน',
                'ดินสอ', 'แฟ้ม 1 นิ้ว', 'แฟ้ม 3 นิ้ว', 'เครื่องเย็บกระดาษ', 'ลูกแม๊กซ์',
                'เครื่องเจาะกระดาษ', 'กรรไกร', 'สก็อตเทป', 'ลวดเสียบกระดาษ', 'อื่นๆ (ระบุ)'
            ],
            units: ['รีม', 'ด้าม', 'โหล', 'แท่ง', 'เล่ม', 'ใบ', 'ตัว', 'ชิ้น', 'กล่อง', 'ห่อ', 'ม้วน', 'ตลับ', 'อื่นๆ']
        };

        let MD = FALLBACK_MD;
        let requests = JSON.parse(localStorage.getItem('purchaseRequests') || '[]');
        let currentView = 'form';
        let filteredRequests = [];

        // Initialize
        function init() {
            checkLoginStatus();
            setupEventListeners();
            generateSampleData();
        }

        function checkLoginStatus() {
            const savedLogin = localStorage.getItem('userLoggedIn');
            const savedRole = localStorage.getItem('userRole');
            const savedUser = localStorage.getItem('currentUser');
            
            if (savedLogin === 'true' && savedRole && savedUser) {
                isLoggedIn = true;
                userRole = savedRole;
                currentUser = savedUser;
                showMainContent();
            } else {
                showLoginScreen();
            }
        }

        function showLoginScreen() {
            $('#loginScreen').classList.remove('hidden');
            $('#mainHeader').classList.add('hidden');
            $('#mainNav').classList.add('hidden');
            $('#mainContent').classList.add('hidden');
        }

        function showMainContent() {
            $('#loginScreen').classList.add('hidden');
            $('#mainHeader').classList.remove('hidden');
            $('#mainNav').classList.remove('hidden');
            $('#mainContent').classList.remove('hidden');
            
            // Update UI based on user role
            updateUIForRole();
            
            fillMasterData();
            addRow();
            updateRequestsList();
            updateDashboard();
            showView('request');
        }

        function updateUIForRole() {
            $('#currentUser').textContent = currentUser;
            $('#currentUserTitle').textContent = userRole === 'admin' ? 'ผู้ดูแลระบบ' : 'พนักงาน';
            $('#userIcon').textContent = userRole === 'admin' ? '👨‍💼' : '👤';
            // Menu user name will be updated below with employee name
            $('#menuUserRole').textContent = userRole === 'admin' ? 'ผู้ดูแลระบบ' : 'พนักงาน';
            
            // Get user info
            const currentUsername = localStorage.getItem('currentUsername');
            const userCredentials = JSON.parse(localStorage.getItem('userCredentials')) || USER_CREDENTIALS;
            const userInfo = userCredentials.find(u => u.username === currentUsername);
            
            // Update employee name in header and welcome message
            if (userRole === 'user') {
                const displayName = userInfo ? userInfo.name : currentUser;
                $('#employeeName').textContent = displayName;
                $('#currentUser').textContent = displayName;
                
                // Update menu user name with actual name
                $('#menuUserName').textContent = displayName;
                
                // Show login notification with actual name
                console.log(`พนักงาน ${displayName} (${currentUsername}) ได้เข้าสู่ระบบเรียบร้อยแล้ว`);
                
                // Show welcome notification with actual employee name
                setTimeout(() => {
                    const welcomeMessage = `🎉 ยินดีต้อนรับ ${displayName}!\n\n✅ คุณได้เข้าสู่ระบบขอซื้อวัสดุสำนักงานเรียบร้อยแล้ว\n📋 พร้อมใช้งานระบบแล้ว`;
                    alert(welcomeMessage);
                }, 800);
            } else {
                $('#employeeName').textContent = 'ผู้ดูแลระบบ';
                $('#currentUser').textContent = 'Admin';
                
                // Show admin login notification
                console.log('ผู้ดูแลระบบได้เข้าสู่ระบบเรียบร้อยแล้ว');
                
                setTimeout(() => {
                    alert('🎉 ยินดีต้อนรับ ผู้ดูแลระบบ\n\n✅ คุณได้เข้าสู่ระบบจัดการเรียบร้อยแล้ว\n🛠️ พร้อมจัดการระบบแล้ว');
                }, 800);
            }
            
            // Show/hide admin-only elements
            if (userRole === 'admin') {
                $$('.admin-only').forEach(el => el.classList.remove('hidden'));
                $('#requestsListTitle').textContent = 'รายการขอเบิกทั้งหมด';
                $('#navList').innerHTML = '📋 รายการทั้งหมด';
            } else {
                $$('.admin-only').forEach(el => el.classList.add('hidden'));
                $('#requestsListTitle').textContent = 'รายการขอเบิกของฉัน';
                $('#navList').innerHTML = '📋 รายการของฉัน';
            }
        }

        function generateSampleData() {
            if (requests.length === 0) {
                const sampleRequests = [
                    {
                        id: 'REQ-001',
                        employeeName: 'John Doe',
                        employeeId: 'EMP001',
                        reqDate: '2024-01-15',
                        department: 'ฝ่าย IT',
                        purpose: 'ใช้งานประจำ',
                        urgency: 'ด่วน (วันนี้)',
                        approverEmail: 'thanaphat.n@taisin.co.th',
                        status: 'รอการอนุมัติ',
                        requestedBy: 'john.doe@taisin.co.th',
                        items: [
                            { name: 'กระดาษ A4', unit: 'รีม', qty: 5, remark: 'สำหรับพิมพ์เอกสาร' }
                        ],
                        createdAt: new Date('2024-01-15').toISOString()
                    },
                    {
                        id: 'REQ-002',
                        employeeName: 'Jane Smith',
                        employeeId: 'EMP002',
                        reqDate: '2024-01-14',
                        department: 'ฝ่าย HR',
                        purpose: 'ใช้งานด่วน',
                        urgency: 'พรุ่งนี้',
                        approverEmail: 'thanaphat.n@taisin.co.th',
                        status: 'อนุมัติแล้ว',
                        requestedBy: 'jane.smith@taisin.co.th',
                        items: [
                            { name: 'ปากกาสีน้ำเงิน', unit: 'ด้าม', qty: 10, remark: '' }
                        ],
                        createdAt: new Date('2024-01-14').toISOString()
                    },
                    {
                        id: 'REQ-003',
                        employeeName: 'Mike Wilson',
                        employeeId: 'EMP003',
                        reqDate: '2024-01-13',
                        department: 'ฝ่าย Account',
                        purpose: 'ใช้งานประจำ',
                        urgency: '2 - 3 วัน',
                        approverEmail: 'hatairat.k@taisin.co.th',
                        status: 'เบิกจ่ายแล้ว',
                        requestedBy: 'mike.wilson@taisin.co.th',
                        items: [
                            { name: 'แฟ้ม 1 นิ้ว', unit: 'เล่ม', qty: 20, remark: 'สำหรับจัดเก็บเอกสาร' }
                        ],
                        createdAt: new Date('2024-01-13').toISOString()
                    }
                ];
                requests = sampleRequests;
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
            }
        }

        function fillMasterData() {
            // Fill dropdowns
            $('#department').innerHTML = '<option value="">เลือกแผนก / ฝ่าย</option>' + 
                MD.departments.map(d => `<option value="${d}">${d}</option>`).join('');
            
            $('#purpose').innerHTML = MD.purposes.map(p => `<option value="${p}">${p}</option>`).join('');
            $('#urgency').innerHTML = MD.urgencies.map(u => `<option value="${u}">${u}</option>`).join('');
            $('#approverEmail').innerHTML = '<option value="">เลือกผู้อนุมัติ</option>' + 
                MD.approverList.map(e => `<option value="${e}">${e}</option>`).join('');

            // Fill filter dropdowns
            $('#filterDepartment').innerHTML = '<option value="">ทั้งหมด</option>' + 
                MD.departments.map(d => `<option value="${d}">${d}</option>`).join('');
            
            $('#editDepartment').innerHTML = MD.departments.map(d => `<option value="${d}">${d}</option>`).join('');

            // Set defaults
            $('#reqDate').value = todayISO();
            $('#urgency').value = 'ด่วน (วันนี้)';
            $('#reportStartDate').value = todayISO();
            $('#reportEndDate').value = todayISO();
            $('#filterStartDate').value = todayISO();
            $('#filterEndDate').value = todayISO();

            // Auto-fill data for regular users
            if (userRole === 'user') {
                const currentUsername = localStorage.getItem('currentUsername');
                const userCredentials = JSON.parse(localStorage.getItem('userCredentials')) || USER_CREDENTIALS;
                const userInfo = userCredentials.find(u => u.username === currentUsername);
                if (userInfo) {
                    $('#employeeNameField').value = userInfo.name;
                    if (userInfo.department) {
                        $('#department').value = userInfo.department;
                        // Trigger change event to auto-select approver
                        $('#department').dispatchEvent(new Event('change'));
                    }
                }
            }
        }

        function setupEventListeners() {
            // User type selection
            $$('.user-type-card').forEach(card => {
                card.addEventListener('click', () => {
                    $$('.user-type-card').forEach(c => c.classList.remove('user-type-selected'));
                    card.classList.add('user-type-selected');
                    selectedUserType = card.dataset.type;
                    
                    // Show appropriate credentials
                    $('#adminCredentials').classList.add('hidden');
                    $('#userCredentials').classList.add('hidden');
                    
                    if (selectedUserType === 'admin') {
                        $('#adminCredentials').classList.remove('hidden');
                        $('#loginUsername').placeholder = 'admin';
                        $('#loginPassword').placeholder = '123456';
                    } else {
                        $('#userCredentials').classList.remove('hidden');
                        $('#loginUsername').placeholder = 'user หรือ อีเมล';
                        $('#loginPassword').placeholder = 'password หรือ 123456';
                    }
                });
            });

            // Navigation
            $$('.nav-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    if (e.target.id === 'adminMenuBtn') return; // Skip admin menu button
                    const view = e.target.id.replace('nav', '').toLowerCase();
                    showView(view);
                });
            });

            // Admin menu navigation
            $$('.admin-nav-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const view = e.target.id.replace('nav', '').toLowerCase();
                    showView(view);
                    $('#adminMenu').classList.add('hidden');
                });
            });

            // Admin menu toggle
            $('#adminMenuBtn').addEventListener('click', (e) => {
                e.stopPropagation();
                const menu = $('#adminMenu');
                menu.classList.toggle('hidden');
            });

            // Auto-select approver based on department
            $('#department').addEventListener('change', () => {
                const dept = $('#department').value;
                const autoApprover = MD.approvers[dept];
                if (autoApprover) {
                    $('#approverEmail').value = autoApprover;
                }
            });

            // Add item row
            $('#addItemBtn').addEventListener('click', () => addRow());

            // Login form submission
            $('#loginForm').addEventListener('submit', handleLogin);

            // Form submission
            $('#form').addEventListener('submit', handleSubmit);

            // Reset form
            $('#resetBtn').addEventListener('click', resetForm);

            // Modal controls
            $('#closeModal').addEventListener('click', closeSuccessModal);
            $('#viewSubmitted').addEventListener('click', () => {
                closeSuccessModal();
                showView('list');
            });
            $('#closeDetailModal').addEventListener('click', () => {
                $('#detailModal').classList.add('hidden');
                $('#detailModal').classList.remove('flex');
            });

            // Password change modal controls
            $('#passwordChangeForm').addEventListener('submit', handlePasswordChange);
            
            // Password visibility toggles
            $('#toggleLoginPassword').addEventListener('click', () => {
                const passwordField = $('#loginPassword');
                const icon = $('#loginPasswordIcon');
                if (passwordField.type === 'password') {
                    passwordField.type = 'text';
                    icon.textContent = '🙈';
                } else {
                    passwordField.type = 'password';
                    icon.textContent = '👁️';
                }
            });
            
            $('#toggleNewPassword').addEventListener('click', () => {
                const passwordField = $('#newPassword');
                const icon = $('#newPasswordIcon');
                if (passwordField.type === 'password') {
                    passwordField.type = 'text';
                    icon.textContent = '🙈';
                } else {
                    passwordField.type = 'password';
                    icon.textContent = '👁️';
                }
            });
            
            $('#toggleConfirmPassword').addEventListener('click', () => {
                const passwordField = $('#confirmPassword');
                const icon = $('#confirmPasswordIcon');
                if (passwordField.type === 'password') {
                    passwordField.type = 'text';
                    icon.textContent = '🙈';
                } else {
                    passwordField.type = 'password';
                    icon.textContent = '👁️';
                }
            });

            // Edit modal controls
            $('#closeEditModal').addEventListener('click', closeEditModal);
            $('#cancelEdit').addEventListener('click', closeEditModal);
            $('#editForm').addEventListener('submit', handleEditSubmit);

            // Reports
            $('#generateReport').addEventListener('click', generateReport);
            $('#exportReport').addEventListener('click', exportReport);
            $('#printReport').addEventListener('click', printReport);

            // Admin management
            $('#bulkApprove').addEventListener('click', bulkApprove);
            $('#exportAllData').addEventListener('click', exportAllData);
            $('#applyFilter').addEventListener('click', applyFilter);
            $('#clearFilter').addEventListener('click', clearFilter);
            $('#selectAll').addEventListener('change', toggleSelectAll);

            // User menu toggle
            $('#userMenuBtn').addEventListener('click', (e) => {
                e.stopPropagation();
                const menu = $('#userMenu');
                menu.classList.toggle('hidden');
            });

            // Logout button - multiple ways to ensure it works
            $('#logoutBtn').addEventListener('click', logout);
            $('#logoutBtn').onclick = logout;

            // Close menus when clicking outside
            document.addEventListener('click', (e) => {
                const userMenu = $('#userMenu');
                const userBtn = $('#userMenuBtn');
                const adminMenu = $('#adminMenu');
                const adminBtn = $('#adminMenuBtn');
                
                if (!userMenu.contains(e.target) && !userBtn.contains(e.target)) {
                    userMenu.classList.add('hidden');
                }
                
                if (!adminMenu.contains(e.target) && !adminBtn.contains(e.target)) {
                    adminMenu.classList.add('hidden');
                }
            });
        }

        function handleLogin(e) {
            e.preventDefault();
            
            if (!selectedUserType) {
                alert('กรุณาเลือกประเภทผู้ใช้งาน');
                return;
            }
            
            const username = $('#loginUsername').value;
            const password = $('#loginPassword').value;
            
            if (selectedUserType === 'admin') {
                // Check for saved admin password first
                const savedAdminPassword = localStorage.getItem('adminPassword');
                const adminPassword = savedAdminPassword || ADMIN_CREDENTIALS.password;
                
                if (username === ADMIN_CREDENTIALS.username && password === adminPassword) {
                    isLoggedIn = true;
                    currentUser = 'Admin';
                    userRole = 'admin';
                    localStorage.setItem('userLoggedIn', 'true');
                    localStorage.setItem('userRole', 'admin');
                    localStorage.setItem('currentUser', 'Admin');
                    showMainContent();
                    $('#loginError').classList.add('hidden');
                } else {
                    $('#loginError').classList.remove('hidden');
                    $('#loginPassword').value = '';
                }
            } else {
                // Get updated user credentials from localStorage
                const userCredentials = JSON.parse(localStorage.getItem('userCredentials')) || USER_CREDENTIALS;
                const user = userCredentials.find(u => 
                    u.username === username && u.password === password
                );
                
                if (user) {
                    // Check if this is the default user with default password
                    if (username === 'user' && password === 'password') {
                        needsPasswordChange = true;
                        currentUser = user.name;
                        userRole = 'user';
                        localStorage.setItem('currentUser', user.name);
                        localStorage.setItem('currentUsername', user.username);
                        showPasswordChangeModal();
                        $('#loginError').classList.add('hidden');
                    } else {
                        isLoggedIn = true;
                        currentUser = user.name;
                        userRole = 'user';
                        localStorage.setItem('userLoggedIn', 'true');
                        localStorage.setItem('userRole', 'user');
                        localStorage.setItem('currentUser', user.name);
                        localStorage.setItem('currentUsername', user.username);
                        showMainContent();
                        $('#loginError').classList.add('hidden');
                    }
                } else {
                    $('#loginError').classList.remove('hidden');
                    $('#loginPassword').value = '';
                }
            }
        }

        function showView(view) {
            // Hide all sections
            $('#requestForm').classList.add('hidden');
            $('#requestsList').classList.add('hidden');
            $('#approvalSystem').classList.add('hidden');
            $('#manageSystem').classList.add('hidden');
            $('#reportsSection').classList.add('hidden');
            $('#dashboardSection').classList.add('hidden');

            // Remove active class from all nav buttons
            $$('.nav-btn').forEach(btn => {
                btn.classList.remove('nav-active', 'text-blue-800', 'border-blue-800', 'text-red-600', 'border-red-600');
                btn.classList.add('text-gray-600', 'border-transparent');
            });

            // Show selected section and activate nav
            switch(view) {
                case 'request':
                    $('#requestForm').classList.remove('hidden');
                    $('#navRequest').classList.add('nav-active', 'text-blue-800', 'border-blue-800');
                    break;
                case 'list':
                    $('#requestsList').classList.remove('hidden');
                    $('#navList').classList.add('nav-active', 'text-blue-800', 'border-blue-800');
                    updateRequestsList();
                    break;
                case 'approval':
                    $('#approvalSystem').classList.remove('hidden');
                    $('#navApproval').classList.add('nav-active', 'text-red-600', 'border-red-600');
                    updateApprovalSystem();
                    break;
                case 'manage':
                    $('#manageSystem').classList.remove('hidden');
                    $('#navManage').classList.add('nav-active', 'text-red-600', 'border-red-600');
                    updateManageSystem();
                    break;
                case 'reports':
                    $('#reportsSection').classList.remove('hidden');
                    $('#navReports').classList.add('nav-active', 'text-red-600', 'border-red-600');
                    break;
                case 'dashboard':
                    $('#dashboardSection').classList.remove('hidden');
                    $('#navDashboard').classList.add('nav-active', 'text-red-600', 'border-red-600');
                    updateDashboard();
                    setTimeout(initCharts, 100);
                    break;
            }
            currentView = view;
        }

        function addRow(item = {name: '', unit: '', qty: 1, remark: ''}) {
            const tbody = $('#itemsBody');
            const rowIndex = tbody.children.length + 1;
            
            const tr = document.createElement('tr');
            tr.className = 'hover:bg-gray-50';
            tr.innerHTML = `
                <td class="px-4 py-3 text-sm text-gray-600">${rowIndex}</td>
                <td class="px-4 py-3">
                    <select class="item-name w-full px-3 py-2 border border-gray-300 rounded-md text-sm input-focus" required>
                        <option value="">เลือกวัสดุ</option>
                        ${MD.itemCatalog.map(item => `<option value="${item}">${item}</option>`).join('')}
                    </select>
                </td>
                <td class="px-4 py-3">
                    <select class="item-unit w-full px-3 py-2 border border-gray-300 rounded-md text-sm input-focus" required>
                        <option value="">หน่วย</option>
                        ${MD.units.map(unit => `<option value="${unit}">${unit}</option>`).join('')}
                    </select>
                </td>
                <td class="px-4 py-3">
                    <input type="number" class="item-qty w-full px-3 py-2 border border-gray-300 rounded-md text-sm input-focus" 
                           min="1" value="${item.qty}" required>
                </td>
                <td class="px-4 py-3">
                    <input type="text" class="item-remark w-full px-3 py-2 border border-gray-300 rounded-md text-sm input-focus" 
                           placeholder="หมายเหตุ" value="${item.remark}">
                </td>
                <td class="px-4 py-3 text-center">
                    <button type="button" class="remove-row text-red-500 hover:text-red-700 px-2 py-1 rounded">
                        🗑️
                    </button>
                </td>
            `;

            // Set values if provided
            if (item.name) tr.querySelector('.item-name').value = item.name;
            if (item.unit) tr.querySelector('.item-unit').value = item.unit;

            // Add remove functionality
            tr.querySelector('.remove-row').addEventListener('click', () => {
                if (tbody.children.length > 1) {
                    tr.remove();
                    updateRowNumbers();
                }
            });

            tbody.appendChild(tr);
        }

        function updateRowNumbers() {
            const rows = $$('#itemsBody tr');
            rows.forEach((row, index) => {
                row.querySelector('td:first-child').textContent = index + 1;
            });
        }

        function handleSubmit(e) {
            e.preventDefault();
            
            // Collect form data
            const formData = {
                id: 'REQ-' + Date.now(),
                employeeName: $('#employeeNameField').value,
                employeeId: $('#employeeId').value || '',
                reqDate: $('#reqDate').value,
                department: $('#department').value,
                purpose: $('#purpose').value,
                urgency: $('#urgency').value,
                approverEmail: $('#approverEmail').value,
                status: 'รอการอนุมัติ',
                requestedBy: userRole === 'admin' ? 'admin' : localStorage.getItem('currentUsername'),
                items: [],
                createdAt: new Date().toISOString()
            };

            // Collect items
            const itemRows = $$('#itemsBody tr');
            itemRows.forEach(row => {
                const name = row.querySelector('.item-name').value;
                const unit = row.querySelector('.item-unit').value;
                const qty = parseInt(row.querySelector('.item-qty').value);
                const remark = row.querySelector('.item-remark').value;
                
                if (name && unit && qty) {
                    formData.items.push({ name, unit, qty, remark });
                }
            });

            if (formData.items.length === 0) {
                alert('กรุณาเพิ่มรายการวัสดุอย่างน้อย 1 รายการ');
                return;
            }

            // Save to localStorage
            requests.unshift(formData);
            localStorage.setItem('purchaseRequests', JSON.stringify(requests));

            // Show enhanced success modal
            $('#successModal').classList.remove('hidden');
            $('#successModal').classList.add('flex');

            // Reset form
            resetForm();
            updateDashboard();
        }

        function closeSuccessModal() {
            $('#successModal').classList.add('hidden');
            $('#successModal').classList.remove('flex');
        }

        function resetForm() {
            $('#form').reset();
            $('#reqDate').value = todayISO();
            $('#urgency').value = 'ด่วน (วันนี้)';
            $('#itemsBody').innerHTML = '';
            addRow();
            
            // Auto-fill data for regular users
            if (userRole === 'user') {
                const currentUsername = localStorage.getItem('currentUsername');
                const userInfo = USER_CREDENTIALS.find(u => u.username === currentUsername);
                if (userInfo) {
                    $('#employeeNameField').value = userInfo.name;
                    if (userInfo.department) {
                        $('#department').value = userInfo.department;
                        $('#department').dispatchEvent(new Event('change'));
                    }
                }
            }
        }

        function updateRequestsList() {
            let displayRequests = requests;
            
            // Filter requests for regular users
            if (userRole === 'user') {
                const currentUsername = localStorage.getItem('currentUsername');
                displayRequests = requests.filter(req => req.requestedBy === currentUsername);
            }
            
            const tbody = $('#requestsTableBody');
            $('#totalRequests').textContent = displayRequests.length;

            if (displayRequests.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="7" class="px-4 py-8 text-center text-gray-500">
                            ${userRole === 'user' ? 'คุณยังไม่มีรายการขอซื้อ' : 'ยังไม่มีรายการขอซื้อ'}
                        </td>
                    </tr>
                `;
                return;
            }

            tbody.innerHTML = displayRequests.map(req => `
                <tr class="hover:bg-gray-50">
                    <td class="px-4 py-3 text-sm font-medium text-gray-900">${req.id}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${fmtDate(req.reqDate)}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${req.department}</td>
                    <td class="px-4 py-3">
                        <span class="inline-flex px-2 py-1 text-xs font-medium rounded-full ${
                            req.urgency === 'ด่วน (วันนี้)' ? 'bg-red-100 text-red-800' :
                            req.urgency === 'พรุ่งนี้' ? 'bg-yellow-100 text-yellow-800' :
                            'bg-green-100 text-green-800'
                        }">
                            ${req.urgency}
                        </span>
                    </td>
                    <td class="px-4 py-3">
                        <span class="inline-flex px-2 py-1 text-xs font-medium rounded-full ${getStatusClass(req.status)}">
                            ${req.status}
                        </span>
                    </td>
                    ${userRole === 'admin' ? `<td class="px-4 py-3 text-sm text-gray-600">${req.approverEmail}</td>` : ''}
                    <td class="px-4 py-3 text-center">
                        <button onclick="viewRequestDetail('${req.id}')" class="text-blue-600 hover:text-blue-800 text-sm font-medium">
                            ดูรายละเอียด
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        function updateApprovalSystem() {
            const pending = requests.filter(r => r.status === 'รอการอนุมัติ').length;
            const approved = requests.filter(r => r.status === 'อนุมัติแล้ว').length;
            const dispensed = requests.filter(r => r.status === 'เบิกจ่ายแล้ว').length;

            $('#pendingCount').textContent = pending;
            $('#approvedCount').textContent = approved;
            $('#dispensedCount').textContent = dispensed;

            const tbody = $('#approvalTableBody');
            tbody.innerHTML = requests.map(req => `
                <tr class="hover:bg-gray-50">
                    <td class="px-4 py-3 text-sm font-medium text-gray-900">${req.id}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${fmtDate(req.reqDate)}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${req.department}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${req.items.length} รายการ</td>
                    <td class="px-4 py-3">
                        <span class="inline-flex px-2 py-1 text-xs font-medium rounded-full ${getStatusClass(req.status)}">
                            ${req.status}
                        </span>
                    </td>
                    <td class="px-4 py-3 text-center">
                        <div class="flex space-x-2 justify-center">
                            ${req.status === 'รอการอนุมัติ' ? `
                                <button onclick="approveRequest('${req.id}')" class="bg-green-500 text-white px-3 py-1 rounded text-xs hover:bg-green-600">
                                    อนุมัติ
                                </button>
                                <button onclick="rejectRequest('${req.id}')" class="bg-red-500 text-white px-3 py-1 rounded text-xs hover:bg-red-600">
                                    ปฏิเสธ
                                </button>
                            ` : req.status === 'อนุมัติแล้ว' ? `
                                <button onclick="dispenseRequest('${req.id}')" class="bg-blue-500 text-white px-3 py-1 rounded text-xs hover:bg-blue-600">
                                    เบิกจ่าย
                                </button>
                            ` : `
                                <span class="text-gray-500 text-xs">เสร็จสิ้น</span>
                            `}
                        </div>
                    </td>
                </tr>
            `).join('');
        }

        function updateManageSystem() {
            filteredRequests = [...requests];
            renderManageTable();
        }

        function renderManageTable() {
            const tbody = $('#manageTableBody');
            
            if (filteredRequests.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="7" class="px-4 py-8 text-center text-gray-500">
                            ไม่พบข้อมูลที่ตรงกับเงื่อนไข
                        </td>
                    </tr>
                `;
                return;
            }

            tbody.innerHTML = filteredRequests.map(req => `
                <tr class="hover:bg-gray-50">
                    <td class="px-4 py-3 text-center">
                        <input type="checkbox" class="request-checkbox rounded" value="${req.id}">
                    </td>
                    <td class="px-4 py-3 text-sm font-medium text-gray-900">${req.id}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${fmtDate(req.reqDate)}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${req.department}</td>
                    <td class="px-4 py-3 text-sm text-gray-600">${req.items.length} รายการ</td>
                    <td class="px-4 py-3">
                        <span class="inline-flex px-2 py-1 text-xs font-medium rounded-full ${getStatusClass(req.status)}">
                            ${req.status}
                        </span>
                    </td>
                    <td class="px-4 py-3 text-center">
                        <div class="flex space-x-1 justify-center">
                            <button onclick="viewRequestDetail('${req.id}')" class="bg-blue-500 text-white px-2 py-1 rounded text-xs hover:bg-blue-600" title="ดู">
                                👁️
                            </button>
                            <button onclick="editRequest('${req.id}')" class="bg-yellow-500 text-white px-2 py-1 rounded text-xs hover:bg-yellow-600" title="แก้ไข">
                                ✏️
                            </button>
                            <button onclick="deleteRequest('${req.id}')" class="bg-red-500 text-white px-2 py-1 rounded text-xs hover:bg-red-600" title="ลบ">
                                🗑️
                            </button>
                        </div>
                    </td>
                </tr>
            `).join('');
        }

        function updateDashboard() {
            const today = todayISO();
            const todayReqs = requests.filter(r => r.reqDate === today).length;
            const approvedToday = requests.filter(r => r.status === 'อนุมัติแล้ว' && r.reqDate === today).length;
            const pendingToday = requests.filter(r => r.status === 'รอการอนุมัติ').length;

            $('#todayRequests').textContent = todayReqs;
            $('#approvedToday').textContent = approvedToday;
            $('#pendingToday').textContent = pendingToday;
            $('#totalAll').textContent = requests.length;
        }

        function getStatusClass(status) {
            switch(status) {
                case 'รอการอนุมัติ': return 'status-pending';
                case 'อนุมัติแล้ว': return 'status-approved';
                case 'เบิกจ่ายแล้ว': return 'status-approved';
                case 'ปฏิเสธ': return 'status-rejected';
                default: return 'status-pending';
            }
        }

        function viewRequestDetail(id) {
            const req = requests.find(r => r.id === id);
            if (!req) return;

            const itemsList = req.items.map(item => 
                `<div class="flex justify-between py-2 border-b">
                    <span>${item.name} (${item.qty} ${item.unit})</span>
                    <span class="text-gray-500">${item.remark || '-'}</span>
                </div>`
            ).join('');

            $('#detailContent').innerHTML = `
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div><strong>เลขที่:</strong> ${req.id}</div>
                    <div><strong>ชื่อพนักงาน:</strong> ${req.employeeName || '-'}</div>
                    <div><strong>รหัสพนักงาน:</strong> ${req.employeeId || '-'}</div>
                    <div><strong>วันที่:</strong> ${fmtDate(req.reqDate)}</div>
                    <div><strong>แผนก:</strong> ${req.department}</div>
                    <div><strong>วัตถุประสงค์:</strong> ${req.purpose}</div>
                    <div><strong>ความเร่งด่วน:</strong> ${req.urgency}</div>
                    <div><strong>ผู้อนุมัติ:</strong> ${req.approverEmail}</div>
                    ${userRole === 'admin' ? `<div><strong>ผู้ขอ:</strong> ${req.requestedBy}</div>` : ''}
                </div>
                <div class="mb-4">
                    <strong>สถานะ:</strong> 
                    <span class="inline-flex px-2 py-1 text-xs font-medium rounded-full ${getStatusClass(req.status)} ml-2">
                        ${req.status}
                    </span>
                </div>
                <div>
                    <strong>รายการวัสดุ:</strong>
                    <div class="mt-2 border rounded-lg p-4 bg-gray-50">
                        ${itemsList}
                    </div>
                </div>
            `;

            $('#detailModal').classList.remove('hidden');
            $('#detailModal').classList.add('flex');
        }

        function editRequest(id) {
            const req = requests.find(r => r.id === id);
            if (!req) return;

            $('#editRequestId').value = req.id;
            $('#editDepartment').value = req.department;
            $('#editStatus').value = req.status;

            $('#editModal').classList.remove('hidden');
            $('#editModal').classList.add('flex');
        }

        function closeEditModal() {
            $('#editModal').classList.add('hidden');
            $('#editModal').classList.remove('flex');
        }

        function handleEditSubmit(e) {
            e.preventDefault();
            const id = $('#editRequestId').value;
            const reqIndex = requests.findIndex(r => r.id === id);
            
            if (reqIndex !== -1) {
                requests[reqIndex].department = $('#editDepartment').value;
                requests[reqIndex].status = $('#editStatus').value;
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                
                closeEditModal();
                updateManageSystem();
                updateDashboard();
                alert('แก้ไขข้อมูลเรียบร้อยแล้ว');
            }
        }

        function deleteRequest(id) {
            if (confirm('ต้องการลบรายการนี้หรือไม่?')) {
                const reqIndex = requests.findIndex(r => r.id === id);
                if (reqIndex !== -1) {
                    requests.splice(reqIndex, 1);
                    localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                    updateManageSystem();
                    updateDashboard();
                    alert('ลบรายการเรียบร้อยแล้ว');
                }
            }
        }

        function approveRequest(id) {
            const reqIndex = requests.findIndex(r => r.id === id);
            if (reqIndex !== -1) {
                requests[reqIndex].status = 'อนุมัติแล้ว';
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                updateApprovalSystem();
                updateDashboard();
            }
        }

        function rejectRequest(id) {
            const reqIndex = requests.findIndex(r => r.id === id);
            if (reqIndex !== -1) {
                requests[reqIndex].status = 'ปฏิเสธ';
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                updateApprovalSystem();
                updateDashboard();
            }
        }

        function dispenseRequest(id) {
            const reqIndex = requests.findIndex(r => r.id === id);
            if (reqIndex !== -1) {
                requests[reqIndex].status = 'เบิกจ่ายแล้ว';
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                updateApprovalSystem();
                updateDashboard();
            }
        }

        function applyFilter() {
            const status = $('#filterStatus').value;
            const department = $('#filterDepartment').value;
            const startDate = $('#filterStartDate').value;
            const endDate = $('#filterEndDate').value;

            filteredRequests = requests.filter(req => {
                let match = true;
                
                if (status && req.status !== status) match = false;
                if (department && req.department !== department) match = false;
                if (startDate && req.reqDate < startDate) match = false;
                if (endDate && req.reqDate > endDate) match = false;
                
                return match;
            });

            renderManageTable();
        }

        function clearFilter() {
            $('#filterStatus').value = '';
            $('#filterDepartment').value = '';
            $('#filterStartDate').value = '';
            $('#filterEndDate').value = '';
            filteredRequests = [...requests];
            renderManageTable();
        }

        function toggleSelectAll() {
            const selectAll = $('#selectAll').checked;
            $$('.request-checkbox').forEach(checkbox => {
                checkbox.checked = selectAll;
            });
        }

        function bulkApprove() {
            const selectedIds = $$('.request-checkbox:checked').map(cb => cb.value);
            if (selectedIds.length === 0) {
                alert('กรุณาเลือกรายการที่ต้องการอนุมัติ');
                return;
            }

            if (confirm(`ต้องการอนุมัติรายการที่เลือก ${selectedIds.length} รายการหรือไม่?`)) {
                selectedIds.forEach(id => {
                    const reqIndex = requests.findIndex(r => r.id === id);
                    if (reqIndex !== -1 && requests[reqIndex].status === 'รอการอนุมัติ') {
                        requests[reqIndex].status = 'อนุมัติแล้ว';
                    }
                });
                
                localStorage.setItem('purchaseRequests', JSON.stringify(requests));
                updateManageSystem();
                updateDashboard();
                alert('อนุมัติรายการที่เลือกเรียบร้อยแล้ว');
            }
        }

        function exportAllData() {
            const csvContent = "data:text/csv;charset=utf-8," + 
                "เลขที่,วันที่,แผนก,วัตถุประสงค์,ความเร่งด่วน,สถานะ,ผู้อนุมัติ,ผู้ขอ,รายการ\n" +
                requests.map(req => 
                    `${req.id},${req.reqDate},${req.department},${req.purpose},${req.urgency},${req.status},${req.approverEmail},${req.requestedBy || '-'},"${req.items.map(item => `${item.name} (${item.qty} ${item.unit})`).join('; ')}"`
                ).join('\n');

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `purchase_requests_${todayISO()}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function generateReport() {
            const startDate = $('#reportStartDate').value;
            const endDate = $('#reportEndDate').value;
            
            if (!startDate || !endDate) {
                alert('กรุณาเลือกช่วงวันที่');
                return;
            }

            // Generate department chart
            const deptData = {};
            requests.forEach(req => {
                if (req.reqDate >= startDate && req.reqDate <= endDate) {
                    deptData[req.department] = (deptData[req.department] || 0) + 1;
                }
            });

            const deptChart = new Chart($('#departmentChart'), {
                type: 'doughnut',
                data: {
                    labels: Object.keys(deptData),
                    datasets: [{
                        data: Object.values(deptData),
                        backgroundColor: [
                            '#3B82F6', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6',
                            '#06B6D4', '#84CC16', '#F97316', '#EC4899', '#6B7280'
                        ]
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        }
                    }
                }
            });

            // Generate trend chart
            const trendChart = new Chart($('#trendChart'), {
                type: 'line',
                data: {
                    labels: ['ม.ค.', 'ก.พ.', 'มี.ค.', 'เม.ย.', 'พ.ค.', 'มิ.ย.'],
                    datasets: [{
                        label: 'จำนวนคำขอ',
                        data: [12, 19, 15, 25, 22, 18],
                        borderColor: '#3B82F6',
                        backgroundColor: 'rgba(59, 130, 246, 0.1)',
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    }
                }
            });
        }

        function exportReport() {
            const startDate = $('#reportStartDate').value;
            const endDate = $('#reportEndDate').value;
            
            if (!startDate || !endDate) {
                alert('กรุณาเลือกช่วงวันที่ก่อนส่งออกรายงาน');
                return;
            }

            // Filter requests by date range
            const filteredData = requests.filter(req => {
                return req.reqDate >= startDate && req.reqDate <= endDate;
            });

            if (filteredData.length === 0) {
                alert('ไม่พบข้อมูลในช่วงวันที่ที่เลือก');
                return;
            }

            // Create Excel-compatible CSV content with Thai headers
            const headers = [
                'เลขที่คำขอ',
                'วันที่ขอเบิก', 
                'ชื่อพนักงาน',
                'รหัสพนักงาน',
                'แผนก/ฝ่าย',
                'วัตถุประสงค์',
                'ความเร่งด่วน',
                'ผู้อนุมัติ',
                'สถานะ',
                'ผู้ขอ',
                'รายการวัสดุ',
                'จำนวนรายการ',
                'วันที่สร้างคำขอ'
            ];

            const csvRows = [
                headers.join(','),
                ...filteredData.map(req => {
                    const itemsList = req.items.map(item => 
                        `${item.name} (${item.qty} ${item.unit}${item.remark ? ' - ' + item.remark : ''})`
                    ).join('; ');
                    
                    return [
                        req.id,
                        req.reqDate,
                        `"${req.employeeName || '-'}"`,
                        req.employeeId || '-',
                        `"${req.department}"`,
                        `"${req.purpose}"`,
                        `"${req.urgency}"`,
                        req.approverEmail,
                        `"${req.status}"`,
                        req.requestedBy || '-',
                        `"${itemsList}"`,
                        req.items.length,
                        req.createdAt ? new Date(req.createdAt).toLocaleDateString('th-TH') : '-'
                    ].join(',');
                })
            ];

            // Create CSV content with BOM for proper Thai character display in Excel
            const csvContent = '\uFEFF' + csvRows.join('\n');
            
            // Create and download file
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            
            if (link.download !== undefined) {
                const url = URL.createObjectURL(blob);
                link.setAttribute('href', url);
                link.setAttribute('download', `รายงานการขอซื้อ_${startDate}_ถึง_${endDate}.csv`);
                link.style.visibility = 'hidden';
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
                
                // Show success message
                setTimeout(() => {
                    alert(`✅ ส่งออกรายงานสำเร็จ!\n\n📊 ข้อมูล: ${filteredData.length} รายการ\n📅 ช่วงวันที่: ${startDate} ถึง ${endDate}\n💾 ไฟล์: รายงานการขอซื้อ_${startDate}_ถึง_${endDate}.csv\n\n📝 หมายเหตุ: ไฟล์ CSV สามารถเปิดด้วย Excel ได้`);
                }, 500);
            } else {
                alert('เบราว์เซอร์ของคุณไม่รองรับการดาวน์โหลดไฟล์');
            }
        }

        function printReport() {
            window.print();
        }

        function logout() {
            console.log('Logout function called'); // Debug log
            
            if (confirm('🚪 ต้องการออกจากระบบหรือไม่?\n\n✅ คลิก OK เพื่อออกจากระบบ\n❌ คลิก Cancel เพื่อยกเลิก')) {
                try {
                    // Clear all localStorage data
                    localStorage.removeItem('userLoggedIn');
                    localStorage.removeItem('userRole');
                    localStorage.removeItem('currentUser');
                    localStorage.removeItem('currentUsername');
                    
                    // Reset global variables
                    isLoggedIn = false;
                    currentUser = null;
                    userRole = null;
                    selectedUserType = null;
                    
                    // Reset login form
                    $$('.user-type-card').forEach(c => c.classList.remove('user-type-selected'));
                    $('#adminCredentials').classList.add('hidden');
                    $('#userCredentials').classList.add('hidden');
                    $('#loginUsername').value = '';
                    $('#loginPassword').value = '';
                    $('#loginUsername').placeholder = 'กรอกชื่อผู้ใช้';
                    $('#loginPassword').placeholder = 'กรอกรหัสผ่าน';
                    $('#loginError').classList.add('hidden');
                    
                    // Show login screen
                    showLoginScreen();
                    
                    // Show success message
                    setTimeout(() => {
                        alert('✅ ออกจากระบบเรียบร้อยแล้ว\n\n👋 ขอบคุณที่ใช้บริการ');
                    }, 500);
                    
                    console.log('Logout completed successfully'); // Debug log
                } catch (error) {
                    console.error('Error during logout:', error);
                    alert('เกิดข้อผิดพลาดในการออกจากระบบ กรุณาลองใหม่');
                }
            }
        }

        function showProfile() {
            $('#userMenu').classList.add('hidden');
            
            const userInfo = userRole === 'admin' ? 
                { name: 'ผู้ดูแลระบบ', username: 'admin', role: 'ผู้ดูแลระบบ' } :
                USER_CREDENTIALS.find(u => u.username === localStorage.getItem('currentUsername')) || 
                { name: currentUser, username: currentUser, role: 'พนักงาน' };
            
            alert(`ข้อมูลส่วนตัว:\n\nชื่อ: ${userInfo.name}\nชื่อผู้ใช้: ${userInfo.username}\nตำแหน่ง: ${userInfo.role}\nแผนก: ${userInfo.department || 'ไม่ระบุ'}`);
        }

        function showSettings() {
            $('#userMenu').classList.add('hidden');
            
            const settingsOptions = [
                '1. เปลี่ยนรหัสผ่าน',
                '2. ตั้งค่าการแจ้งเตือน',
                '3. ตั้งค่าภาษา',
                '4. ตั้งค่าการแสดงผล',
                '5. แสดงรายชื่อพนักงานที่เข้าใช้งาน'
            ].join('\n');
            
            const choice = prompt(`ตั้งค่าระบบ:\n\n${settingsOptions}\n\nเลือกหมายเลข (1-5):`);
            
            if (choice === '1') {
                showChangePasswordDialog();
            } else if (choice === '5') {
                showActiveEmployees();
            } else if (choice >= '2' && choice <= '4') {
                alert('ฟีเจอร์นี้จะพร้อมใช้งานในเร็วๆ นี้');
            }
        }

        function showChangePasswordDialog() {
            const currentUsername = localStorage.getItem('currentUsername') || 'admin';
            const newPassword = prompt('กรุณากรอกรหัสผ่านใหม่ (อย่างน้อย 6 ตัวอักษร):');
            
            if (newPassword === null) return; // User cancelled
            
            if (newPassword.length < 6) {
                alert('❌ รหัสผ่านต้องมีอย่างน้อย 6 ตัวอักษร');
                return;
            }
            
            const confirmPassword = prompt('กรุณายืนยันรหัสผ่านใหม่:');
            
            if (confirmPassword === null) return; // User cancelled
            
            if (newPassword !== confirmPassword) {
                alert('❌ รหัสผ่านไม่ตรงกัน กรุณาลองใหม่');
                return;
            }
            
            // Update password
            if (userRole === 'admin') {
                // Update admin credentials in memory
                ADMIN_CREDENTIALS.password = newPassword;
                
                // Also save to localStorage for persistence
                localStorage.setItem('adminPassword', newPassword);
                
                alert('✅ เปลี่ยนรหัสผ่าน Admin สำเร็จ!\n\n🔐 รหัสผ่านใหม่: ' + newPassword + '\n📝 กรุณาจดจำรหัสผ่านใหม่');
            } else {
                // Get current user credentials
                let userCredentials = JSON.parse(localStorage.getItem('userCredentials')) || USER_CREDENTIALS;
                const userIndex = userCredentials.findIndex(u => u.username === currentUsername);
                
                if (userIndex !== -1) {
                    // Update password in the array
                    userCredentials[userIndex].password = newPassword;
                    
                    // Update both localStorage and global variable
                    localStorage.setItem('userCredentials', JSON.stringify(userCredentials));
                    USER_CREDENTIALS = userCredentials;
                    
                    const userName = userCredentials[userIndex].name;
                    
                    alert(`✅ เปลี่ยนรหัสผ่านสำเร็จ!\n\n👤 ผู้ใช้: ${userName} (พนักงาน)\n🔐 รหัสผ่านใหม่: ${newPassword}\n📝 กรุณาจดจำรหัสผ่านใหม่\n\n💡 รหัสผ่านใหม่จะใช้งานได้ทันทีในการเข้าสู่ระบบครั้งถัดไป`);
                } else {
                    alert('❌ เกิดข้อผิดพลาด ไม่พบข้อมูลผู้ใช้');
                }
            }
        }
        
        function showActiveEmployees() {
            // Get all employees who have made requests
            const activeEmployees = new Set();
            
            requests.forEach(req => {
                if (req.requestedBy && req.requestedBy !== 'admin') {
                    const userInfo = USER_CREDENTIALS.find(u => u.username === req.requestedBy);
                    if (userInfo) {
                        activeEmployees.add(`${userInfo.name} (${userInfo.username}) - ${userInfo.department || 'ไม่ระบุแผนก'}`);
                    } else {
                        activeEmployees.add(`${req.requestedBy} - ไม่ระบุข้อมูล`);
                    }
                }
            });
            
            // Add currently logged in user if not already in list
            if (userRole === 'user') {
                const currentUsername = localStorage.getItem('currentUsername');
                const currentUserInfo = USER_CREDENTIALS.find(u => u.username === currentUsername);
                if (currentUserInfo) {
                    activeEmployees.add(`${currentUserInfo.name} (${currentUserInfo.username}) - ${currentUserInfo.department || 'ไม่ระบุแผนก'} [กำลังใช้งาน]`);
                }
            }
            
            const employeeList = Array.from(activeEmployees).sort().join('\n');
            
            if (employeeList) {
                alert(`รายชื่อพนักงานที่เข้าใช้งานระบบ:\n\n${employeeList}\n\nรวมทั้งหมด: ${activeEmployees.size} คน`);
            } else {
                alert('ยังไม่มีพนักงานเข้าใช้งานระบบ');
            }
        }

        function showPasswordChangeModal() {
            $('#loginScreen').classList.add('hidden');
            $('#passwordChangeModal').classList.remove('hidden');
            $('#passwordChangeModal').classList.add('flex');
        }

        function handlePasswordChange(e) {
            e.preventDefault();
            
            const newUsername = $('#newUsername').value.trim();
            const newPassword = $('#newPassword').value;
            const confirmPassword = $('#confirmPassword').value;
            
            // Hide previous messages
            $('#passwordError').classList.add('hidden');
            $('#passwordSuccess').classList.add('hidden');
            
            // Validate username
            if (newUsername.length < 3) {
                $('#passwordError').textContent = 'ชื่อผู้ใช้ต้องมีอย่างน้อย 3 ตัวอักษร';
                $('#passwordError').classList.remove('hidden');
                return;
            }
            
            // Check if username already exists (except current user)
            const existingUser = USER_CREDENTIALS.find(u => u.username === newUsername && u.username !== 'user');
            if (existingUser) {
                $('#passwordError').textContent = 'ชื่อผู้ใช้นี้มีอยู่แล้ว กรุณาเลือกชื่อใหม่';
                $('#passwordError').classList.remove('hidden');
                return;
            }
            
            // Validate passwords
            if (newPassword.length < 6) {
                $('#passwordError').textContent = 'รหัสผ่านต้องมีอย่างน้อย 6 ตัวอักษร';
                $('#passwordError').classList.remove('hidden');
                return;
            }
            
            if (newPassword !== confirmPassword) {
                $('#passwordError').textContent = 'รหัสผ่านไม่ตรงกัน';
                $('#passwordError').classList.remove('hidden');
                return;
            }
            
            // Update user credentials
            const userIndex = USER_CREDENTIALS.findIndex(u => u.username === 'user');
            if (userIndex !== -1) {
                USER_CREDENTIALS[userIndex].username = newUsername;
                USER_CREDENTIALS[userIndex].password = newPassword;
                
                // Save to localStorage for persistence
                localStorage.setItem('userCredentials', JSON.stringify(USER_CREDENTIALS));
                
                // Show success message
                $('#passwordSuccess').classList.remove('hidden');
                
                // Complete login process after 1.5 seconds
                setTimeout(() => {
                    needsPasswordChange = false;
                    isLoggedIn = true;
                    localStorage.setItem('userLoggedIn', 'true');
                    localStorage.setItem('userRole', 'user');
                    localStorage.setItem('currentUsername', newUsername);
                    
                    $('#passwordChangeModal').classList.add('hidden');
                    $('#passwordChangeModal').classList.remove('flex');
                    
                    showMainContent();
                }, 1500);
            }
        }

        function initCharts() {
            // Weekly chart
            const weeklyChart = new Chart($('#weeklyChart'), {
                type: 'bar',
                data: {
                    labels: ['จันทร์', 'อังคาร', 'พุธ', 'พฤหัส', 'ศุกร์', 'เสาร์', 'อาทิตย์'],
                    datasets: [{
                        label: 'คำขอซื้อ',
                        data: [3, 5, 2, 8, 6, 1, 0],
                        backgroundColor: 'rgba(59, 130, 246, 0.8)',
                        borderColor: '#3B82F6',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true
                        }
                    }
                }
            });

            // Top items chart
            const topItemsChart = new Chart($('#topItemsChart'), {
                type: 'horizontalBar',
                data: {
                    labels: ['กระดาษ A4', 'ปากกาสีน้ำเงิน', 'แฟ้ม 1 นิ้ว', 'สก็อตเทป', 'ดินสอ'],
                    datasets: [{
                        label: 'จำนวนครั้ง',
                        data: [15, 12, 8, 6, 4],
                        backgroundColor: [
                            '#3B82F6', '#10B981', '#F59E0B', '#EF4444', '#8B5CF6'
                        ]
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: {
                            beginAtZero: true
                        }
                    }
                }
            });
        }

        // Initialize the application
        init();
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'96e19105834fee31',t:'MTc1NTAxODQyMi4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
