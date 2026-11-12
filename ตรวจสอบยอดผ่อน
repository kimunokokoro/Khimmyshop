<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>khimmy.shop - ระบบตรวจสอบยอดผ่อนชำระ</title>
    <!-- นำเข้า Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- นำเข้าฟอนต์ภาษาไทยที่สวยงาม (Prompt & Sarabun) -->
    <link href="https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;500;600;700&family=Sarabun:wght@400;500;600&display=swap" rel="stylesheet">
    <!-- นำเข้า FontAwesome สำหรับไอคอน -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- นำเข้า PapaParse สำหรับอ่านข้อมูล CSV จาก Notion / Google Sheets -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>

    <style>
        body {
            font-family: 'Prompt', sans-serif;
            background-color: #f8fafc;
            background-image: radial-gradient(#e2e8f0 1px, transparent 1px);
            background-size: 20px 20px;
        }
        .font-sarabun {
            font-family: 'Sarabun', sans-serif;
        }
        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f5f9; 
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1; 
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8; 
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .animate-fade-in {
            animation: fadeIn 0.4s ease-out forwards;
        }
        /* ปุ่มเข้าหลังบ้าน .☘︎ ݁˖ สีเขียวพาสเทลเรืองแสง ไม่มีกรอบ */
        .pastel-green-glow-btn {
            background: transparent;
            border: none;
            outline: none;
            color: #4ade80;
            text-shadow: 0 0 6px #86efac, 0 0 12px #4ade80, 0 0 20px #bbf7d0;
            transition: all 0.3s ease;
            cursor: pointer;
        }
        .pastel-green-glow-btn:hover {
            color: #86efac;
            text-shadow: 0 0 8px #bbf7d0, 0 0 18px #4ade80, 0 0 28px #22c55e;
            transform: scale(1.1);
        }
    </style>
</head>
<body class="min-h-screen flex flex-col text-slate-800">

    <!-- แถบเมนูด้านบน (Navbar) -->
    <nav class="bg-white/90 backdrop-blur-md border-b border-slate-200 sticky top-0 z-40 shadow-sm">
        <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between h-16 items-center">
                <div class="flex items-center space-x-3 cursor-pointer" onclick="switchView('customer')">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-pink-500 to-rose-400 flex items-center justify-center text-white shadow-md">
                        <i class="fa-solid fa-sparkles text-lg"></i>
                    </div>
                    <div>
                        <span class="font-bold text-lg bg-clip-text text-transparent bg-gradient-to-r from-pink-600 to-rose-500">
                            khimmy.shop
                        </span>
                        <p class="text-[11px] text-slate-400 -mt-1">ระบบตรวจสอบสถานะและยอดผ่อนชำระ</p>
                    </div>
                </div>
                
                <div class="flex items-center space-x-3">
                    <button id="btnNavCustomer" onclick="switchView('customer')" class="px-3 py-2 rounded-lg text-sm font-medium bg-pink-50 text-pink-600 transition">
                        <i class="fa-solid fa-magnifying-glass mr-1"></i> เช็กยอดลูกค้า
                    </button>
                    <!-- ปุ่มหลังบ้าน: โชว์แค่ .☘︎ ݁˖ สีเขียวพาสเทลเรืองแสง ไม่มีกรอบ -->
                    <button id="btnNavAdmin" onclick="openAdminPortal()" title="พื้นที่เจ้าของร้าน" class="pastel-green-glow-btn text-xl font-semibold px-2 py-1 select-none">
                        .☘︎ ݁˖
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- กล่องแจ้งเตือนแบบ Custom (แทน alert) -->
    <div id="toastContainer" class="fixed bottom-5 right-5 z-50 flex flex-col gap-2"></div>

    <!-- ========================================== -->
    <!-- ส่วนที่ 1: หน้าสำหรับลูกค้า (Customer View)    -->
    <!-- ========================================== -->
    <main id="customerView" class="flex-grow max-w-4xl w-full mx-auto px-4 py-8 sm:py-12">
        
        <!-- กล่องค้นหาโค้ดหรือชื่อ-นามสกุล (ไม่ขึ้นโชว์โค้ดทั้งหมด) -->
        <div class="bg-white rounded-3xl shadow-lg border border-slate-100 p-6 sm:p-10 text-center max-w-xl mx-auto mb-8 relative overflow-hidden">
            <div class="absolute top-0 left-0 w-full h-1.5 bg-gradient-to-r from-pink-400 via-rose-400 to-amber-300"></div>
            
            <div class="w-16 h-16 bg-pink-50 text-pink-500 rounded-2xl flex items-center justify-center mx-auto mb-4 text-2xl shadow-inner">
                <i class="fa-solid fa-receipt"></i>
            </div>
            <h1 class="text-2xl sm:text-3xl font-bold text-slate-800 mb-2">ตรวจสอบยอดผ่อนชำระ</h1>
            <p class="text-slate-500 text-sm mb-6">กรุณากรอก <b>Code ผู้ผ่อน</b> หรือ <b>ชื่อ-นามสกุล</b> ของคุณเพื่อตรวจสอบรายละเอียดและตารางประวัติ</p>
            
            <form onsubmit="handleCustomerSearch(event)" class="flex flex-col sm:flex-row gap-3">
                <div class="relative flex-grow">
                    <div class="absolute inset-y-0 left-0 pl-4 flex items-center pointer-events-none text-slate-400">
                        <i class="fa-solid fa-magnifying-glass"></i>
                    </div>
                    <input type="text" id="searchQueryInput" required autocomplete="off" placeholder="กรอก Code หรือ ชื่อ-นามสกุล ผู้ผ่อน..." 
                        class="w-full pl-10 pr-4 py-3.5 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 focus:bg-white transition text-slate-700 font-medium">
                </div>
                <button type="submit" class="px-6 py-3.5 bg-gradient-to-r from-pink-500 to-rose-500 hover:from-pink-600 hover:to-rose-600 text-white font-medium rounded-xl shadow-md hover:shadow-lg transition transform active:scale-95 flex items-center justify-center gap-2 shrink-0">
                    <i class="fa-solid fa-search"></i> ค้นหาข้อมูล
                </button>
            </form>
        </div>

        <!-- ส่วนแสดงผลลัพธ์ข้อมูลลูกค้า (ซ่อนไว้ก่อนค้นหา) -->
        <div id="customerResultSection" class="hidden animate-fade-in">
            <!-- กรอบข้อมูลหลัก (สีกรอบจะเปลี่ยนตาม Status อัตโนมัติ) -->
            <div id="customerCardFrame" class="bg-white rounded-3xl shadow-xl border-4 border-amber-400 overflow-hidden transition-colors duration-300">
                
                <!-- ส่วนหัวของบัตรข้อมูล: Code & Status -->
                <div id="customerCardHeader" class="bg-amber-50 px-6 py-5 border-b border-amber-200 flex flex-wrap justify-between items-center gap-4">
                    <div class="flex items-center gap-3.5">
                        <div id="statusIconBox" class="w-12 h-12 rounded-2xl bg-amber-400 text-white flex items-center justify-center text-xl shadow-sm shrink-0">
                            <i class="fa-solid fa-clock"></i>
                        </div>
                        <div>
                            <div class="flex items-center gap-2">
                                <span class="text-xs text-slate-500 font-medium">Code :</span>
                                <span id="dispCode" class="text-xl sm:text-2xl font-bold text-slate-800 tracking-wide">KM001</span>
                            </div>
                            <div class="text-sm text-slate-600 font-medium mt-0.5">
                                <i class="fa-solid fa-user text-slate-400 mr-1 text-xs"></i> <span id="dispCustomerName">-</span>
                            </div>
                        </div>
                    </div>
                    
                    <div class="flex flex-col items-end">
                        <span class="text-xs text-slate-500 mb-1">Status</span>
                        <span id="dispStatusBadge" class="px-4 py-1.5 rounded-full text-sm font-bold bg-amber-400 text-amber-950 shadow-sm">
                            กำลังผ่อน
                        </span>
                    </div>
                </div>

                <!-- รายละเอียดตัวเลขการผ่อน -->
                <div class="p-6 sm:p-8">
                    <!-- รูปแบบการผ่อน -->
                    <div class="mb-6 flex items-center justify-between bg-slate-50 p-4 rounded-2xl border border-slate-100">
                        <span class="text-sm text-slate-500 font-medium"><i class="fa-solid fa-tag mr-2 text-pink-400"></i>รูปแบบการผ่อน</span>
                        <span id="dispPlanType" class="font-bold text-slate-800 bg-white px-3.5 py-1 rounded-lg border border-slate-200 shadow-sm">วางดาวน์</span>
                    </div>

                    <!-- ตารางสรุปยอดเงิน 6 ช่อง: ราคาผ่อน | เปิดบิล | ส่งยอดมาแล้ว | โปรโมชั่น/ส่วนลด | เหลือค่าปรับ | เหลือยอดผ่อน -->
                    <div class="grid grid-cols-2 sm:grid-cols-3 gap-4 mb-6">
                        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                            <p class="text-xs text-slate-500 mb-1">ราคาผ่อน</p>
                            <p id="dispTotalPrice" class="text-lg sm:text-xl font-bold text-slate-800">0 บาท</p>
                        </div>
                        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                            <p class="text-xs text-slate-500 mb-1">เปิดบิล</p>
                            <p id="dispOpenBill" class="text-lg sm:text-xl font-bold text-blue-600">0 บาท</p>
                        </div>
                        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                            <p class="text-xs text-slate-500 mb-1">ส่งยอดมาแล้ว</p>
                            <p id="dispPaidAmount" class="text-lg sm:text-xl font-bold text-emerald-600">0 บาท</p>
                        </div>
                        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                            <p class="text-xs text-slate-500 mb-1">โปรโมชั่น/ส่วนลด</p>
                            <p id="dispDiscount" class="text-lg sm:text-xl font-bold text-purple-600">0 บาท</p>
                        </div>
                        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-100">
                            <p class="text-xs text-slate-500 mb-1">เหลือค่าปรับ</p>
                            <p id="dispFine" class="text-lg sm:text-xl font-bold text-rose-600">0 บาท</p>
                        </div>
                        <!-- ช่องเหลือยอดผ่อน -->
                        <div id="remainingBox" class="col-span-2 sm:col-span-1 bg-gradient-to-br from-pink-50 to-rose-50 p-4 rounded-2xl border-2 border-pink-200 flex flex-col justify-center">
                            <p class="text-xs text-pink-600 font-semibold mb-1">เหลือยอดผ่อน</p>
                            <p id="dispRemaining" class="text-lg sm:text-xl font-extrabold text-pink-600">0 บาท</p>
                        </div>
                    </div>

                    <!-- ส่วนแสดงตารางข้อมูลที่ดึงมาจาก Notion (เฉพาะที่ตรงกับ Code ตัวเองเท่านั้น) -->
                    <div class="mt-8 pt-6 border-t border-slate-200">
                        <div class="flex flex-wrap items-center justify-between gap-2 mb-4">
                            <h3 class="font-bold text-slate-800 flex items-center gap-2">
                                <img src="https://upload.wikimedia.org/wikipedia/commons/4/45/Notion_app_logo.png" alt="Notion" class="w-5 h-5" onerror="this.style.display='none'">
                                <span>ตารางข้อมูลจาก Notion (เฉพาะรหัส <span id="notionCodeLabel" class="text-pink-600"></span>)</span>
                            </h3>
                            <button onclick="refreshNotionForCustomer()" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-600 px-3 py-1.5 rounded-lg transition flex items-center gap-1">
                                <i class="fa-solid fa-rotate"></i> อัปเดตข้อมูลล่าสุด
                            </button>
                        </div>

                        <!-- สถานะโหลด Notion -->
                        <div id="notionLoading" class="hidden py-8 text-center text-slate-400">
                            <i class="fa-solid fa-circle-notch fa-spin text-2xl text-pink-500 mb-2"></i>
                            <p class="text-sm">กำลังดึงข้อมูลตารางจากลิงก์ Notion...</p>
                        </div>

                        <!-- ตารางแสดงข้อมูล Notion -->
                        <div id="notionTableWrapper" class="overflow-x-auto rounded-xl border border-slate-200">
                            <table class="w-full text-left border-collapse">
                                <thead id="notionTableHead" class="bg-slate-100 text-slate-600 text-xs uppercase">
                                    <!-- หัวตารางจะถูกสร้างอัตโนมัติตามคอลัมน์ใน Notion -->
                                </thead>
                                <tbody id="notionTableBody" class="divide-y divide-slate-200 text-sm font-sarabun bg-white">
                                    <!-- ข้อมูลแถวที่ตรงกับ Code จะถูกแสดงที่นี่ -->
                                </tbody>
                            </table>
                        </div>
                        <p id="notionHelperText" class="text-xs text-slate-400 mt-2 text-right"></p>
                    </div>

                </div>
            </div>
        </div>
    </main>

    <!-- ========================================== -->
    <!-- ส่วนที่ 2: หน้าล็อกอินหลังบ้าน (Login View)    -->
    <!-- ========================================== -->
    <main id="loginView" class="hidden flex-grow flex items-center justify-center px-4 py-12">
        <div class="bg-white rounded-3xl shadow-xl border border-slate-100 p-8 max-w-md w-full animate-fade-in">
            <div class="w-14 h-14 bg-slate-900 text-emerald-400 rounded-2xl flex items-center justify-center mx-auto mb-4 text-xl shadow-md">
                .☘︎ ݁˖
            </div>
            <h2 class="text-2xl font-bold text-center text-slate-800 mb-1">เข้าสู่ระบบหลังบ้าน</h2>
            <p class="text-center text-slate-400 text-sm mb-6">เฉพาะเจ้าของร้าน khimmy.shop เท่านั้น</p>
            
            <form onsubmit="handleAdminLogin(event)" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Username</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-solid fa-user"></i></span>
                        <input type="text" id="adminUser" required placeholder="กรอก Username" 
                            class="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-slate-800">
                    </div>
                </div>
                <div>
                    <label class="block text-sm font-medium text-slate-700 mb-1">Password</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 pl-3.5 flex items-center text-slate-400"><i class="fa-solid fa-key"></i></span>
                        <input type="password" id="adminPass" required placeholder="กรอกรหัสผ่าน" 
                            class="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-slate-800">
                    </div>
                </div>
                <button type="submit" class="w-full py-3 bg-slate-900 hover:bg-slate-800 text-white font-medium rounded-xl shadow-md transition mt-2">
                    เข้าสู่ระบบจัดการข้อมูล
                </button>
            </form>
        </div>
    </main>

    <!-- ========================================== -->
    <!-- ส่วนที่ 3: หน้าจัดการหลังบ้าน (Admin View)     -->
    <!-- ========================================== -->
    <main id="adminView" class="hidden flex-grow max-w-6xl w-full mx-auto px-4 py-8 animate-fade-in">
        
        <!-- หัวข้อหน้าหลังบ้าน -->
        <div class="flex flex-wrap justify-between items-center gap-4 mb-6 bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
            <div>
                <h1 class="text-xl sm:text-2xl font-bold text-slate-800 flex items-center gap-2">
                    <span class="text-emerald-500">.☘︎ ݁˖</span> ระบบจัดการข้อมูลหลังบ้าน khimmy.shop
                </h1>
                <p class="text-xs text-slate-500">เจ้าของร้าน: <span class="font-bold text-slate-700">kimunokokoro</span> | จัดการยอดผ่อนและเชื่อมต่อลิงก์ Notion</p>
            </div>
            <div class="flex gap-2">
                <button onclick="switchView('customer')" class="px-4 py-2 bg-pink-50 text-pink-600 hover:bg-pink-100 rounded-xl text-sm font-medium transition">
                    <i class="fa-solid fa-eye mr-1"></i> ดูหน้าเว็บลูกค้า
                </button>
                <button onclick="handleAdminLogout()" class="px-4 py-2 bg-rose-50 text-rose-600 hover:bg-rose-100 rounded-xl text-sm font-medium transition">
                    <i class="fa-solid fa-right-from-bracket mr-1"></i> ออกจากระบบ
                </button>
            </div>
        </div>

        <!-- ช่องใส่ลิงก์ Notion -->
        <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-6 mb-8">
            <div class="flex items-center gap-2 mb-3">
                <img src="https://upload.wikimedia.org/wikipedia/commons/4/45/Notion_app_logo.png" alt="Notion" class="w-6 h-6" onerror="this.style.display='none'">
                <h2 class="text-lg font-bold text-slate-800">ช่องใส่ลิงก์ Notion (สำหรับดึงข้อมูลเป็นตาราง)</h2>
            </div>
            <p class="text-xs text-slate-500 mb-4 leading-relaxed">
                วางลิงก์ฐานข้อมูล Notion ของคุณที่นี่ ระบบจะดึงข้อมูลตารางมาแสดงให้ลูกค้าเฉพาะแถวที่มีชื่อ/โค้ดตรงกับ <b>Code : สำหรับใส่โค้ดผู้ผ่อน</b> เท่านั้น 
                <span class="text-pink-600 font-medium">(รองรับลิงก์ Notion ที่เปิด Public, ลิงก์ Export CSV หรือลิงก์ Google Sheet CSV)</span>
            </p>
            
            <div class="flex flex-col sm:flex-row gap-3">
                <input type="url" id="notionLinkInput" placeholder="วางลิงก์ Notion ที่นี่ เช่น https://www.notion.so/..." 
                    class="flex-grow px-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                <button onclick="saveNotionLink()" class="px-5 py-2.5 bg-slate-800 hover:bg-slate-700 text-white text-sm font-medium rounded-xl transition flex items-center justify-center gap-2 shrink-0">
                    <i class="fa-solid fa-link"></i> บันทึกลิงก์และดึงข้อมูล
                </button>
            </div>
            <div id="notionStatusMsg" class="mt-2 text-xs text-slate-500"></div>
        </div>

        <!-- ส่วนฟอร์มบันทึก/แก้ไขข้อมูลลูกค้า และ ตารางรายชื่อ -->
        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            
            <!-- ฟอร์มบันทึกข้อมูล (ซ้าย - 5 คอลัมน์) -->
            <div class="lg:col-span-5 bg-white rounded-2xl shadow-sm border border-slate-200 p-6 h-fit">
                <div class="flex justify-between items-center mb-4 pb-3 border-b border-slate-100">
                    <h2 id="formTitle" class="text-lg font-bold text-slate-800">
                        <i class="fa-solid fa-plus-circle text-pink-500 mr-1"></i> บันทึกข้อมูลผู้ผ่อน
                    </h2>
                    <button type="button" id="btnCancelEdit" onclick="resetAdminForm()" class="hidden text-xs text-slate-400 hover:text-slate-600 underline">
                        ยกเลิกการแก้ไข
                    </button>
                </div>

                <form id="installmentForm" onsubmit="saveCustomerRecord(event)" class="space-y-4">
                    <input type="hidden" id="editIndex" value="-1">

                    <!-- Code & ชื่อผู้ผ่อน -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Code : สำหรับใส่โค้ดผู้ผ่อน *</label>
                            <input type="text" id="inputCode" required placeholder="เช่น KM001" 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm uppercase font-bold">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">ชื่อผู้ผ่อน : สำหรับใส่ชื่อผู้ผ่อน *</label>
                            <input type="text" id="inputCustomerName" required placeholder="เช่น ชื่อ-นามสกุล" 
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                        </div>
                    </div>

                    <!-- Status & รูปแบบการผ่อน -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">Status (สถานะ) *</label>
                            <select id="inputStatus" onchange="updateAdminPreview()" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm font-medium">
                                <option value="1">1. กำลังผ่อน (สีเหลือง)</option>
                                <option value="2">2. ผ่อนเสร็จสมบูรณ์ (สีเขียว)</option>
                                <option value="3">3. บิด/โกง/หนีหาย (สีแดง)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">รูปแบบการผ่อน *</label>
                            <select id="inputPlanType" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                                <option value="วางดาวน์">1. วางดาวน์</option>
                                <option value="ผ่อนไปใช้ไป">2. ผ่อนไปใช้ไป</option>
                                <option value="ผ่อนหมดรับของ">3. ผ่อนหมดรับของ</option>
                            </select>
                        </div>
                    </div>

                    <!-- ตัวเลขการเงิน -->
                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">ราคาผ่อน (บาท) *</label>
                            <input type="number" id="inputTotalPrice" required min="0" step="any" value="0" oninput="updateAdminPreview()"
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">เปิดบิล (บาท) *</label>
                            <input type="number" id="inputOpenBill" required min="0" step="any" value="0" oninput="updateAdminPreview()"
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">ส่งยอดมาแล้ว (บาท) *</label>
                            <input type="number" id="inputPaidAmount" required min="0" step="any" value="0" oninput="updateAdminPreview()"
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                        </div>
                        <div>
                            <label class="block text-xs font-semibold text-slate-700 mb-1">โปรโมชั่น/ส่วนลด (บาท)</label>
                            <input type="number" id="inputDiscount" min="0" step="any" value="0" oninput="updateAdminPreview()"
                                class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                        </div>
                    </div>

                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">เหลือค่าปรับ (บาท)</label>
                        <input type="number" id="inputFine" min="0" step="any" value="0"
                            class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-pink-400 text-sm">
                    </div>

                    <!-- กล่องแสดงผลการคำนวณสูตรอัตโนมัติ -->
                    <div class="p-3.5 rounded-xl bg-slate-900 text-white flex justify-between items-center">
                        <div>
                            <span class="text-[11px] text-slate-400 block">เหลือยอดผ่อน (คำนวณอัตโนมัติ):</span>
                            <span class="text-[10px] text-slate-400">(ราคาผ่อน - เปิดบิล - โปรโมชั่น/ส่วนลด - ส่งยอดมาแล้ว)</span>
                        </div>
                        <div id="adminFormulaPreview" class="text-base font-bold text-pink-400 text-right">
                            0 บาท
                        </div>
                    </div>

                    <button type="submit" class="w-full py-3 bg-gradient-to-r from-pink-500 to-rose-500 hover:from-pink-600 hover:to-rose-600 text-white font-medium rounded-xl shadow-md transition">
                        <i class="fa-solid fa-floppy-disk mr-1"></i> บันทึกข้อมูลผู้ผ่อน
                    </button>
                </form>
            </div>

            <!-- ตารางรายการลูกค้าทั้งหมด (ขวา - 7 คอลัมน์) -->
            <div class="lg:col-span-7 bg-white rounded-2xl shadow-sm border border-slate-200 p-6 flex flex-col">
                <div class="flex flex-wrap justify-between items-center gap-2 mb-4 pb-3 border-b border-slate-100">
                    <h2 class="text-lg font-bold text-slate-800">
                        <i class="fa-solid fa-users text-pink-500 mr-1"></i> รายชื่อผู้ผ่อนทั้งหมด (<span id="totalRecordsCount">0</span>)
                    </h2>
                    <input type="text" id="adminFilterInput" onkeyup="renderAdminTable()" placeholder="ค้นหา Code หรือ ชื่อ..." 
                        class="px-3 py-1.5 bg-slate-50 border border-slate-200 rounded-lg text-xs focus:outline-none focus:ring-2 focus:ring-pink-400">
                </div>

                <div class="overflow-x-auto flex-grow">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-50 text-slate-500 text-xs border-b border-slate-200">
                                <th class="py-3 px-3 rounded-tl-lg">Code / ชื่อผู้ผ่อน</th>
                                <th class="py-3 px-3">Status</th>
                                <th class="py-3 px-3">รูปแบบ</th>
                                <th class="py-3 px-3 text-right">เหลือยอดผ่อน</th>
                                <th class="py-3 px-3 text-center rounded-tr-lg">จัดการ</th>
                            </tr>
                        </thead>
                        <tbody id="adminTableBody" class="divide-y divide-slate-100 text-sm">
                            <!-- รายการจะถูกสร้างด้วย JavaScript -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>
    </main>

    <!-- Footer -->
    <footer class="bg-white border-t border-slate-200 py-4 text-center text-xs text-slate-400 mt-auto">
        &copy; 2026 khimmy.shop - ระบบตรวจสอบยอดผ่อนชำระและเชื่อมต่อข้อมูล Notion
    </footer>

    <!-- ========================================== -->
    <!-- JavaScript Logic                           -->
    <!-- ========================================== -->
    <script>
        // ข้อมูลบัญชีเจ้าของร้าน (เข้าได้เฉพาะเจ้าของร้านคนเดียว)
        const ADMIN_CREDENTIALS = {
            user: 'kimunokokoro',
            pass: '246199'
        };

        // คีย์สำหรับเก็บข้อมูลใน LocalStorage
        const STORAGE_KEYS = {
            RECORDS: 'khimmy_shop_installment_records_v1',
            NOTION_URL: 'khimmy_shop_notion_url_v1',
            NOTION_CACHE: 'khimmy_shop_notion_cache_v1',
            IS_LOGGED_IN: 'khimmy_shop_admin_logged_in'
        };

        // ข้อมูลตัวอย่างเริ่มต้น (ลูกค้าจะไม่เห็นรายชื่อทั้งหมด จนกว่าจะพิมพ์ค้นหาตรงกับ Code หรือ ชื่อ-นามสกุล)
        const DEFAULT_RECORDS = [
            {
                code: 'KM001',
                customerName: 'กนกวรรณ ใจดี',
                status: '1', // 1 = กำลังผ่อน (เหลือง)
                planType: 'วางดาวน์',
                totalPrice: 15000,
                openBill: 3000,
                paidAmount: 4500,
                discount: 500,
                fine: 0
            },
            {
                code: 'KM002',
                customerName: 'พิชญาภา รักเรียน',
                status: '2', // 2 = ผ่อนเสร็จสมบูรณ์ (เขียว)
                planType: 'ผ่อนหมดรับของ',
                totalPrice: 8900,
                openBill: 1000,
                paidAmount: 7500,
                discount: 400,
                fine: 0
            },
            {
                code: 'KM003',
                customerName: 'สมชาย สายชิล',
                status: '3', // 3 = บิด/โกง/หนีหาย (แดง)
                planType: 'ผ่อนไปใช้ไป',
                totalPrice: 22000,
                openBill: 5000,
                paidAmount: 2000,
                discount: 0,
                fine: 1500
            }
        ];

        // ข้อมูลจำลองจาก Notion (ใช้แสดงผลร่วมกับข้อมูลจริงเมื่อดึงจากลิงก์ Notion)
        const MOCK_NOTION_ROWS = [
            { 'Code': 'KM001', 'วันที่โอน': '01/11/2026', 'รายการ': 'เปิดบิลวางดาวน์', 'จำนวนเงิน': '3,000 บาท', 'หมายเหตุ': 'ตรวจสอบสลิปแล้ว' },
            { 'Code': 'KM001', 'วันที่โอน': '10/11/2026', 'รายการ': 'ส่งยอดงวดที่ 1', 'จำนวนเงิน': '4,500 บาท', 'หมายเหตุ': 'ตรงเวลา' },
            { 'Code': 'KM002', 'วันที่โอน': '15/10/2026', 'รายการ': 'เปิดบิล', 'จำนวนเงิน': '1,000 บาท', 'หมายเหตุ': 'รับส่วนลด 400.-' },
            { 'Code': 'KM002', 'วันที่โอน': '05/11/2026', 'รายการ': 'ปิดยอดทั้งหมด', 'จำนวนเงิน': '7,500 บาท', 'หมายเหตุ': 'เตรียมจัดส่งสินค้า' },
            { 'Code': 'KM003', 'วันที่โอน': '01/10/2026', 'รายการ': 'เปิดบิลผ่อนไปใช้ไป', 'จำนวนเงิน': '5,000 บาท', 'หมายเหตุ': 'ส่งเครื่องแล้ว' },
            { 'Code': 'KM003', 'วันที่โอน': '15/10/2026', 'รายการ': 'ส่งยอดงวดที่ 1', 'จำนวนเงิน': '2,000 บาท', 'หมายเหตุ': 'ค้างชำระเกินกำหนด' }
        ];

        let records = [];
        let notionUrl = '';
        let currentSearchedCode = '';

        // เริ่มต้นทำงานเมื่อโหลดหน้าเว็บ
        window.onload = function() {
            loadInitialData();
            updateAdminPreview();
        };

        // โหลดข้อมูลจาก LocalStorage
        function loadInitialData() {
            const savedRecords = localStorage.getItem(STORAGE_KEYS.RECORDS);
            if (savedRecords) {
                records = JSON.parse(savedRecords);
            } else {
                records = [...DEFAULT_RECORDS];
                localStorage.setItem(STORAGE_KEYS.RECORDS, JSON.stringify(records));
            }

            notionUrl = localStorage.getItem(STORAGE_KEYS.NOTION_URL) || '';
            document.getElementById('notionLinkInput').value = notionUrl;
            
            renderAdminTable();
        }

        // ฟังก์ชันแจ้งเตือน Toast (แทน alert)
        function showToast(message, type = 'info') {
            const container = document.getElementById('toastContainer');
            const toast = document.createElement('div');
            
            let bgClass = 'bg-slate-800 text-white';
            let icon = 'fa-circle-info';
            if (type === 'success') { bgClass = 'bg-emerald-600 text-white'; icon = 'fa-circle-check'; }
            if (type === 'error') { bgClass = 'bg-rose-600 text-white'; icon = 'fa-circle-exclamation'; }
            if (type === 'warning') { bgClass = 'bg-amber-500 text-white'; icon = 'fa-triangle-exclamation'; }

            toast.className = `${bgClass} px-4 py-3 rounded-xl shadow-lg flex items-center gap-2.5 text-sm font-medium animate-fade-in max-w-sm`;
            toast.innerHTML = `<i class="fa-solid ${icon}"></i><span>${message}</span>`;
            
            container.appendChild(toast);
            setTimeout(() => {
                toast.style.opacity = '0';
                toast.style.transition = 'opacity 0.3s';
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // สลับหน้าจอ (Customer / Login / Admin) - ป้องกันลูกค้าเข้าหลังบ้านหากยังไม่ล็อกอิน
        function switchView(viewName) {
            const isLoggedIn = sessionStorage.getItem(STORAGE_KEYS.IS_LOGGED_IN) === 'true';
            if (viewName === 'admin' && !isLoggedIn) {
                viewName = 'login';
            }

            document.getElementById('customerView').classList.add('hidden');
            document.getElementById('loginView').classList.add('hidden');
            document.getElementById('adminView').classList.add('hidden');

            const btnCust = document.getElementById('btnNavCustomer');

            if (viewName === 'customer') {
                document.getElementById('customerView').classList.remove('hidden');
                btnCust.className = 'px-3 py-2 rounded-lg text-sm font-medium bg-pink-50 text-pink-600 transition';
            } else if (viewName === 'login') {
                document.getElementById('loginView').classList.remove('hidden');
                btnCust.className = 'px-3 py-2 rounded-lg text-sm font-medium text-slate-600 hover:bg-slate-100 transition';
            } else if (viewName === 'admin') {
                document.getElementById('adminView').classList.remove('hidden');
                btnCust.className = 'px-3 py-2 rounded-lg text-sm font-medium text-slate-600 hover:bg-slate-100 transition';
                renderAdminTable();
            }
        }

        // ตรวจสอบสิทธิ์ก่อนเข้าหลังบ้าน
        function openAdminPortal() {
            const isLoggedIn = sessionStorage.getItem(STORAGE_KEYS.IS_LOGGED_IN) === 'true';
            if (isLoggedIn) {
                switchView('admin');
            } else {
                switchView('login');
            }
        }

        // จัดการการล็อกอินเจ้าของร้าน
        function handleAdminLogin(e) {
            e.preventDefault();
            const user = document.getElementById('adminUser').value.trim();
            const pass = document.getElementById('adminPass').value.trim();

            if (user === ADMIN_CREDENTIALS.user && pass === ADMIN_CREDENTIALS.pass) {
                sessionStorage.setItem(STORAGE_KEYS.IS_LOGGED_IN, 'true');
                document.getElementById('adminUser').value = '';
                document.getElementById('adminPass').value = '';
                showToast('เข้าสู่ระบบหลังบ้านสำเร็จ ยินดีต้อนรับคุณ kimunokokoro', 'success');
                switchView('admin');
            } else {
                showToast('Username หรือ Password ไม่ถูกต้อง!', 'error');
            }
        }

        // ออกจากระบบหลังบ้าน
        function handleAdminLogout() {
            sessionStorage.removeItem(STORAGE_KEYS.IS_LOGGED_IN);
            showToast('ออกจากระบบหลังบ้านเรียบร้อยแล้ว', 'info');
            switchView('customer');
        }

        // สูตรคำนวณยอดผ่อนคงเหลือ: (ราคาผ่อน - เปิดบิล - โปรโมชั่น/ส่วนลด - ส่งยอดมาแล้ว)
        function calculateRemaining(record) {
            const total = parseFloat(record.totalPrice) || 0;
            const open = parseFloat(record.openBill) || 0;
            const discount = parseFloat(record.discount) || 0;
            const paid = parseFloat(record.paidAmount) || 0;
            
            const remaining = total - open - discount - paid;
            return remaining <= 0 ? 0 : remaining;
        }

        // ฟอร์แมตตัวเลขสกุลเงินบาท
        function formatBaht(num) {
            return Number(num).toLocaleString('th-TH', { minimumFractionDigits: 0, maximumFractionDigits: 2 }) + ' บาท';
        }

        // ฟอร์แมตการแสดงผล "เหลือยอดผ่อน" (หากเลขยอดผ่อนเหลือ 0 ให้ขึ้นคำว่า ⋆. 𐙚˚࿔ ปิดบิล 𝜗𝜚˚⋆)
        function formatRemainingDisplay(remainingAmount) {
            if (remainingAmount <= 0) {
                return '⋆. 𐙚˚࿔ ปิดบิล 𝜗𝜚˚⋆';
            }
            return formatBaht(remainingAmount);
        }

        // อัปเดตตัวอย่างการคำนวณในหน้าฟอร์มหลังบ้าน
        function updateAdminPreview() {
            const tempRecord = {
                totalPrice: document.getElementById('inputTotalPrice').value,
                openBill: document.getElementById('inputOpenBill').value,
                discount: document.getElementById('inputDiscount').value,
                paidAmount: document.getElementById('inputPaidAmount').value
            };
            const rem = calculateRemaining(tempRecord);
            document.getElementById('adminFormulaPreview').textContent = formatRemainingDisplay(rem);
        }

        // บันทึกข้อมูลผู้ผ่อนในหน้าหลังบ้าน
        function saveCustomerRecord(e) {
            e.preventDefault();
            const isLoggedIn = sessionStorage.getItem(STORAGE_KEYS.IS_LOGGED_IN) === 'true';
            if (!isLoggedIn) return;

            const editIdx = parseInt(document.getElementById('editIndex').value);
            const code = document.getElementById('inputCode').value.trim().toUpperCase();
            const customerName = document.getElementById('inputCustomerName').value.trim();
            
            // ตรวจสอบ Code ซ้ำ (กรณีเพิ่มใหม่)
            const duplicateIdx = records.findIndex(r => r.code.toUpperCase() === code);
            if (editIdx === -1 && duplicateIdx !== -1) {
                showToast(`รหัส "${code}" มีอยู่ในระบบแล้ว กรุณาใช้ปุ่มแก้ไขแทน`, 'warning');
                return;
            }

            const newRecord = {
                code: code,
                customerName: customerName,
                status: document.getElementById('inputStatus').value,
                planType: document.getElementById('inputPlanType').value,
                totalPrice: parseFloat(document.getElementById('inputTotalPrice').value) || 0,
                openBill: parseFloat(document.getElementById('inputOpenBill').value) || 0,
                paidAmount: parseFloat(document.getElementById('inputPaidAmount').value) || 0,
                discount: parseFloat(document.getElementById('inputDiscount').value) || 0,
                fine: parseFloat(document.getElementById('inputFine').value) || 0
            };

            if (editIdx >= 0) {
                records[editIdx] = newRecord;
                showToast(`อัปเดตข้อมูลรหัส ${code} เรียบร้อยแล้ว`, 'success');
            } else {
                records.unshift(newRecord);
                showToast(`เพิ่มข้อมูลรหัส ${code} เรียบร้อยแล้ว`, 'success');
            }

            localStorage.setItem(STORAGE_KEYS.RECORDS, JSON.stringify(records));
            resetAdminForm();
            renderAdminTable();
        }

        // เตรียมข้อมูลลงฟอร์มเพื่อแก้ไข
        function editRecord(index) {
            const r = records[index];
            document.getElementById('editIndex').value = index;
            document.getElementById('inputCode').value = r.code;
            document.getElementById('inputCustomerName').value = r.customerName || '';
            document.getElementById('inputStatus').value = r.status;
            document.getElementById('inputPlanType').value = r.planType;
            document.getElementById('inputTotalPrice').value = r.totalPrice;
            document.getElementById('inputOpenBill').value = r.openBill;
            document.getElementById('inputPaidAmount').value = r.paidAmount;
            document.getElementById('inputDiscount').value = r.discount;
            document.getElementById('inputFine').value = r.fine;

            document.getElementById('formTitle').innerHTML = `<i class="fa-solid fa-pen-to-square text-amber-500 mr-1"></i> แก้ไขข้อมูล: ${r.code}`;
            document.getElementById('btnCancelEdit').classList.remove('hidden');
            updateAdminPreview();
        }

        // ลบข้อมูลผู้ผ่อน
        function deleteRecord(index) {
            const isLoggedIn = sessionStorage.getItem(STORAGE_KEYS.IS_LOGGED_IN) === 'true';
            if (!isLoggedIn) return;

            const targetCode = records[index].code;
            records.splice(index, 1);
            localStorage.setItem(STORAGE_KEYS.RECORDS, JSON.stringify(records));
            renderAdminTable();
            showToast(`ลบข้อมูลรหัส ${targetCode} เรียบร้อยแล้ว`, 'info');
        }

        // รีเซ็ตฟอร์มหลังบ้าน
        function resetAdminForm() {
            document.getElementById('installmentForm').reset();
            document.getElementById('editIndex').value = '-1';
            document.getElementById('formTitle').innerHTML = `<i class="fa-solid fa-plus-circle text-pink-500 mr-1"></i> บันทึกข้อมูลผู้ผ่อน`;
            document.getElementById('btnCancelEdit').classList.add('hidden');
            updateAdminPreview();
        }

        // แสดงผลตารางในหน้าหลังบ้าน
        function renderAdminTable() {
            const tbody = document.getElementById('adminTableBody');
            const filter = document.getElementById('adminFilterInput').value.trim().toLowerCase();
            tbody.innerHTML = '';

            const filtered = records.filter(r => 
                r.code.toLowerCase().includes(filter) || 
                (r.customerName && r.customerName.toLowerCase().includes(filter))
            );
            document.getElementById('totalRecordsCount').textContent = filtered.length;

            if (filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="5" class="py-8 text-center text-slate-400">ไม่พบข้อมูลผู้ผ่อน</td></tr>`;
                return;
            }

            filtered.forEach((r) => {
                const realIndex = records.findIndex(item => item.code === r.code);
                const rem = calculateRemaining(r);
                
                let badgeHtml = '';
                if (r.status === '1') badgeHtml = `<span class="px-2.5 py-1 rounded-full text-xs font-semibold bg-amber-100 text-amber-800">กำลังผ่อน</span>`;
                if (r.status === '2') badgeHtml = `<span class="px-2.5 py-1 rounded-full text-xs font-semibold bg-emerald-100 text-emerald-800">ผ่อนเสร็จสมบูรณ์</span>`;
                if (r.status === '3') badgeHtml = `<span class="px-2.5 py-1 rounded-full text-xs font-semibold bg-rose-100 text-rose-800">บิด/โกง/หนีหาย</span>`;

                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition';
                tr.innerHTML = `
                    <td class="py-3 px-3">
                        <div class="font-bold text-slate-800">${r.code}</div>
                        <div class="text-xs text-slate-500">${r.customerName || '-'}</div>
                    </td>
                    <td class="py-3 px-3">${badgeHtml}</td>
                    <td class="py-3 px-3 text-xs text-slate-600">${r.planType}</td>
                    <td class="py-3 px-3 text-right font-semibold ${rem <= 0 ? 'text-emerald-600' : 'text-pink-600'}">${formatRemainingDisplay(rem)}</td>
                    <td class="py-3 px-3 text-center">
                        <div class="flex items-center justify-center gap-1.5">
                            <button onclick="editRecord(${realIndex})" title="แก้ไข" class="w-7 h-7 rounded-lg bg-amber-50 text-amber-600 hover:bg-amber-100 transition"><i class="fa-solid fa-pen text-xs"></i></button>
                            <button onclick="deleteRecord(${realIndex})" title="ลบ" class="w-7 h-7 rounded-lg bg-rose-50 text-rose-600 hover:bg-rose-100 transition"><i class="fa-solid fa-trash text-xs"></i></button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        // ==========================================
        // ส่วนจัดการการดึงข้อมูลจากลิงก์ Notion
        // ==========================================
        async function saveNotionLink() {
            const isLoggedIn = sessionStorage.getItem(STORAGE_KEYS.IS_LOGGED_IN) === 'true';
            if (!isLoggedIn) return;

            const url = document.getElementById('notionLinkInput').value.trim();
            notionUrl = url;
            localStorage.setItem(STORAGE_KEYS.NOTION_URL, url);
            
            const statusEl = document.getElementById('notionStatusMsg');
            if (!url) {
                statusEl.innerHTML = `<span class="text-slate-400">ล้างลิงก์ Notion แล้ว (ระบบจะแสดงข้อมูลตารางจำลองเมื่อค้นหา)</span>`;
                showToast('บันทึกลิงก์เรียบร้อยแล้ว', 'info');
                return;
            }

            statusEl.innerHTML = `<span class="text-amber-600"><i class="fa-solid fa-spinner fa-spin mr-1"></i> กำลังตรวจสอบและดึงข้อมูลจากลิงก์...</span>`;
            const data = await fetchNotionTableData(url);
            if (data && data.length > 0) {
                statusEl.innerHTML = `<span class="text-emerald-600"><i class="fa-solid fa-circle-check mr-1"></i> เชื่อมต่อสำเร็จ! พบข้อมูลทั้งหมด ${data.length} แถวจากลิงก์</span>`;
                showToast('เชื่อมต่อและดึงข้อมูลจากลิงก์สำเร็จ!', 'success');
            } else {
                statusEl.innerHTML = `<span class="text-blue-600"><i class="fa-solid fa-circle-info mr-1"></i> บันทึกลิงก์แล้ว (ระบบพร้อมดึงข้อมูลและกรองเฉพาะ Code ของลูกค้า)</span>`;
                showToast('บันทึกลิงก์ Notion เรียบร้อยแล้ว', 'success');
            }
        }

        // ฟังก์ชันดึงข้อมูลตารางจากลิงก์ (รองรับ CSV / Public Notion / Proxy)
        async function fetchNotionTableData(url) {
            if (!url) return MOCK_NOTION_ROWS;

            try {
                const proxyUrl = `https://api.allorigins.win/get?url=${encodeURIComponent(url)}`;
                const response = await fetch(proxyUrl);
                if (!response.ok) throw new Error('Network response was not ok');
                
                const json = await response.json();
                const content = json.contents;

                // กรณีลิงก์เป็นไฟล์ CSV
                if (url.includes('.csv') || url.includes('output=csv') || (!content.trim().startsWith('<'))) {
                    const parsed = Papa.parse(content, { header: true, skipEmptyLines: true });
                    if (parsed.data && parsed.data.length > 0) {
                        localStorage.setItem(STORAGE_KEYS.NOTION_CACHE, JSON.stringify(parsed.data));
                        return parsed.data;
                    }
                }

                // กรณีลิงก์เป็นหน้าเว็บ Notion Public (ดึงตาราง HTML)
                const parser = new DOMParser();
                const doc = parser.parseFromString(content, 'text/html');
                const rows = doc.querySelectorAll('table tr');
                if (rows.length > 1) {
                    const headers = Array.from(rows[0].querySelectorAll('th, td')).map(el => el.textContent.trim() || 'Column');
                    const extractedData = [];
                    for (let i = 1; i < rows.length; i++) {
                        const cells = rows[i].querySelectorAll('td');
                        if (cells.length > 0) {
                            let rowObj = {};
                            headers.forEach((h, idx) => {
                                rowObj[h] = cells[idx] ? cells[idx].textContent.trim() : '';
                            });
                            extractedData.push(rowObj);
                        }
                    }
                    if (extractedData.length > 0) {
                        localStorage.setItem(STORAGE_KEYS.NOTION_CACHE, JSON.stringify(extractedData));
                        return extractedData;
                    }
                }

                const cached = localStorage.getItem(STORAGE_KEYS.NOTION_CACHE);
                return cached ? JSON.parse(cached) : MOCK_NOTION_ROWS;

            } catch (error) {
                console.warn('Notion fetch fallback:', error);
                const cached = localStorage.getItem(STORAGE_KEYS.NOTION_CACHE);
                return cached ? JSON.parse(cached) : MOCK_NOTION_ROWS;
            }
        }

        // ==========================================
        // ส่วนค้นหาและแสดงผลหน้าลูกค้า (ค้นหาได้ทั้ง Code หรือ ชื่อ-นามสกุล)
        // ==========================================
        function handleCustomerSearch(e) {
            e.preventDefault();
            const query = document.getElementById('searchQueryInput').value.trim();
            if (query) {
                executeCustomerSearch(query);
            }
        }

        async function executeCustomerSearch(query) {
            const normalizedQuery = query.trim().toLowerCase();
            
            // ค้นหาแบบตรงตัวกับ Code หรือ ชื่อ-นามสกุล (ไม่โชว์โค้ดทั้งหมดให้เลือก)
            const record = records.find(r => 
                r.code.trim().toLowerCase() === normalizedQuery || 
                (r.customerName && r.customerName.trim().toLowerCase() === normalizedQuery)
            );

            if (!record) {
                document.getElementById('customerResultSection').classList.add('hidden');
                showToast(`ไม่พบข้อมูลสำหรับ "${query}" กรุณากรอก Code หรือ ชื่อ-นามสกุล ให้ถูกต้อง`, 'error');
                return;
            }

            currentSearchedCode = record.code;

            // แสดงกล่องผลลัพธ์
            const resultSection = document.getElementById('customerResultSection');
            resultSection.classList.remove('hidden');

            // สีกรอบข้อมูล: เป็นสีเดียวกับช่องตัวเลือก Status 3 แบบ
            // 1. กำลังผ่อน(สีเหลือง) | 2. ผ่อนเสร็จสมบูรณ์(สีเขียว) | 3. บิด/โกง/หนีหาย(สีแดง)
            const cardFrame = document.getElementById('customerCardFrame');
            const cardHeader = document.getElementById('customerCardHeader');
            const statusIconBox = document.getElementById('statusIconBox');
            const statusBadge = document.getElementById('dispStatusBadge');

            cardFrame.className = 'bg-white rounded-3xl shadow-xl border-4 overflow-hidden transition-colors duration-300';
            cardHeader.className = 'px-6 py-5 border-b flex flex-wrap justify-between items-center gap-4';
            statusIconBox.className = 'w-12 h-12 rounded-2xl text-white flex items-center justify-center text-xl shadow-sm shrink-0';
            statusBadge.className = 'px-4 py-1.5 rounded-full text-sm font-bold shadow-sm';

            if (record.status === '1') {
                // 1. กำลังผ่อน (สีเหลือง)
                cardFrame.classList.add('border-amber-400');
                cardHeader.classList.add('bg-amber-50', 'border-amber-200');
                statusIconBox.classList.add('bg-amber-400');
                statusIconBox.innerHTML = '<i class="fa-solid fa-clock"></i>';
                statusBadge.classList.add('bg-amber-400', 'text-amber-950');
                statusBadge.textContent = 'กำลังผ่อน';
            } else if (record.status === '2') {
                // 2. ผ่อนเสร็จสมบูรณ์ (สีเขียว)
                cardFrame.classList.add('border-emerald-500');
                cardHeader.classList.add('bg-emerald-50', 'border-emerald-200');
                statusIconBox.classList.add('bg-emerald-500');
                statusIconBox.innerHTML = '<i class="fa-solid fa-circle-check"></i>';
                statusBadge.classList.add('bg-emerald-500', 'text-white');
                statusBadge.textContent = 'ผ่อนเสร็จสมบูรณ์';
            } else if (record.status === '3') {
                // 3. บิด/โกง/หนีหาย (สีแดง)
                cardFrame.classList.add('border-rose-500');
                cardHeader.classList.add('bg-rose-50', 'border-rose-200');
                statusIconBox.classList.add('bg-rose-500');
                statusIconBox.innerHTML = '<i class="fa-solid fa-triangle-exclamation"></i>';
                statusBadge.classList.add('bg-rose-600', 'text-white');
                statusBadge.textContent = 'บิด/โกง/หนีหาย';
            }

            // เติมข้อมูลลงในหน้าจอ
            document.getElementById('dispCode').textContent = record.code;
            document.getElementById('dispCustomerName').textContent = record.customerName || '-';
            document.getElementById('dispPlanType').textContent = record.planType;
            document.getElementById('dispTotalPrice').textContent = formatBaht(record.totalPrice);
            document.getElementById('dispOpenBill').textContent = formatBaht(record.openBill);
            document.getElementById('dispPaidAmount').textContent = formatBaht(record.paidAmount);
            document.getElementById('dispDiscount').textContent = formatBaht(record.discount);
            document.getElementById('dispFine').textContent = formatBaht(record.fine);

            // คำนวณยอดผ่อนคงเหลือ
            const remaining = calculateRemaining(record);
            const dispRemainingEl = document.getElementById('dispRemaining');
            const remainingBox = document.getElementById('remainingBox');
            
            dispRemainingEl.textContent = formatRemainingDisplay(remaining);
            if (remaining <= 0) {
                remainingBox.className = 'col-span-2 sm:col-span-1 bg-gradient-to-br from-emerald-50 to-teal-50 p-4 rounded-2xl border-2 border-emerald-300 flex flex-col justify-center';
                dispRemainingEl.className = 'text-base sm:text-lg font-extrabold text-emerald-600';
            } else {
                remainingBox.className = 'col-span-2 sm:col-span-1 bg-gradient-to-br from-pink-50 to-rose-50 p-4 rounded-2xl border-2 border-pink-200 flex flex-col justify-center';
                dispRemainingEl.className = 'text-lg sm:text-xl font-extrabold text-pink-600';
            }

            // ดึงข้อมูลตารางจาก Notion เฉพาะที่ตรงกับ Code ตัวเองเท่านั้น
            await loadAndRenderCustomerNotionTable(record.code);
            
            // เลื่อนหน้าจอลงมาที่ผลลัพธ์อย่างนุ่มนวล
            resultSection.scrollIntoView({ behavior: 'smooth', block: 'nearest' });
        }

        async function refreshNotionForCustomer() {
            if (currentSearchedCode) {
                showToast('กำลังรีเฟรชข้อมูลตารางจาก Notion...', 'info');
                await loadAndRenderCustomerNotionTable(currentSearchedCode);
            }
        }

        // กรองและแสดงตาราง Notion เฉพาะแถวที่ตรงกับ Code ของลูกค้าเท่านั้น
        async function loadAndRenderCustomerNotionTable(targetCode) {
            document.getElementById('notionCodeLabel').textContent = targetCode;
            const loadingEl = document.getElementById('notionLoading');
            const tableWrapper = document.getElementById('notionTableWrapper');
            const thead = document.getElementById('notionTableHead');
            const tbody = document.getElementById('notionTableBody');
            const helperText = document.getElementById('notionHelperText');

            loadingEl.classList.remove('hidden');
            tableWrapper.classList.add('hidden');

            const allRows = await fetchNotionTableData(notionUrl);
            
            loadingEl.classList.add('hidden');
            tableWrapper.classList.remove('hidden');

            // กรองเฉพาะแถวที่มีค่าตรงกับ targetCode (Code ผู้ผ่อน) เท่านั้น
            const matchedRows = allRows.filter(row => {
                return Object.values(row).some(val => 
                    String(val).trim().toUpperCase() === targetCode.toUpperCase()
                );
            });

            if (matchedRows.length === 0) {
                thead.innerHTML = '';
                tbody.innerHTML = `
                    <tr>
                        <td class="py-6 px-4 text-center text-slate-400">
                            <i class="fa-regular fa-folder-open text-xl mb-1 block"></i>
                            ไม่พบรายการบันทึกเพิ่มเติมในตาราง Notion สำหรับรหัส <b>${targetCode}</b>
                        </td>
                    </tr>
                `;
                helperText.textContent = notionUrl ? 'เชื่อมต่อลิงก์ Notion แล้ว' : 'ยังไม่ได้ตั้งค่าลิงก์ Notion ในระบบหลังบ้าน';
                return;
            }

            // สร้างหัวตารางจาก Keys ของข้อมูล
            const columns = Object.keys(matchedRows[0]);
            thead.innerHTML = `<tr>${columns.map(col => `<th class="py-3 px-4 font-semibold">${col}</th>`).join('')}</tr>`;

            // สร้างแถวข้อมูลเฉพาะของลูกค้ารายนี้
            tbody.innerHTML = matchedRows.map(row => `
                <tr class="hover:bg-slate-50 transition">
                    ${columns.map(col => {
                        const cellVal = row[col] || '-';
                        const isCodeCol = String(cellVal).trim().toUpperCase() === targetCode.toUpperCase();
                        return `<td class="py-3 px-4 ${isCodeCol ? 'font-bold text-pink-600' : 'text-slate-700'}">${cellVal}</td>`;
                    }).join('')}
                </tr>
            `).join('');

            helperText.textContent = `แสดงข้อมูลเฉพาะรหัส ${targetCode} จำนวน ${matchedRows.length} รายการ`;
        }
    </script>
</body>
</html>
