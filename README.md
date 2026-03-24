<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>VERMAJI DIGITAL PORTAL</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs2@0.0.2/qrcode.min.js"></script>
    <style>
        .gradient-bg {
            background: linear-gradient(135deg, #ff6b6b 0%, #4ecdc4 100%);
        }
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.8);
            z-index: 1000;
        }
        .modal-content {
            position: relative;
            top: 50%;
            transform: translateY(-50%);
            max-width: 450px;
            margin: auto;
            padding: 20px;
        }
        .toast {
            visibility: hidden;
            position: fixed;
            bottom: 80px;
            right: 20px;
            background: #333;
            color: white;
            padding: 12px;
            border-radius: 8px;
            z-index: 1000;
        }
        .toast.show {
            visibility: visible;
        }
        .service-btn {
            transition: all 0.3s;
            cursor: pointer;
        }
        .service-btn:active {
            transform: scale(0.95);
        }
    </style>
</head>
<body class="bg-gray-100">

<div id="toast" class="toast"></div>

<!-- लॉगिन मोडल -->
<div id="loginModal" class="modal">
    <div class="modal-content">
        <div class="bg-white rounded-2xl shadow-2xl p-6">
            <div class="text-center mb-6">
                <i class="fas fa-user-circle text-6xl text-orange-500"></i>
                <h2 class="text-2xl font-bold">VERMAJI DIGITAL</h2>
                <p class="text-gray-500">लॉगिन करें</p>
            </div>
            <div id="loginForm">
                <input type="text" id="loginId" placeholder="लॉगिन ID (6 डिजिट)" class="w-full border rounded-lg p-3 mb-3 text-center text-lg">
                <input type="password" id="password" placeholder="पासवर्ड" class="w-full border rounded-lg p-3 mb-4 text-center">
                <button onclick="login()" class="w-full bg-orange-600 text-white py-3 rounded-lg font-bold text-lg">लॉगिन करें</button>
                <button onclick="showRegister()" class="w-full bg-gray-600 text-white py-3 rounded-lg font-bold mt-3">नया अकाउंट बनाएं (₹250)</button>
            </div>
            <div id="registerForm" style="display:none;">
                <input type="text" id="regName" placeholder="आपका नाम" class="w-full border rounded-lg p-3 mb-3">
                <input type="text" id="regMobile" placeholder="मोबाइल नंबर" class="w-full border rounded-lg p-3 mb-4">
                <button onclick="register()" class="w-full bg-green-600 text-white py-3 rounded-lg font-bold text-lg">₹250 भुगतान करें और रजिस्टर करें</button>
                <button onclick="showLogin()" class="w-full bg-gray-600 text-white py-3 rounded-lg font-bold mt-3">वापस लॉगिन पर जाएं</button>
            </div>
        </div>
    </div>
</div>

<!-- पेमेंट मोडल -->
<div id="paymentModal" class="modal">
    <div class="modal-content">
        <div class="bg-white rounded-2xl shadow-2xl p-6">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-2xl font-bold">💳 भुगतान करें</h3>
                <button onclick="closePaymentModal()" class="text-gray-500 text-2xl">&times;</button>
            </div>
            <div id="paymentServiceDetails" class="mb-4 p-3 bg-gray-100 rounded-lg">
                <p class="font-bold">सेवा: <span id="payServiceName">-</span></p>
                <p class="font-bold text-green-600">राशि: ₹<span id="payAmount">0</span></p>
                <p class="text-sm text-gray-500">आपका बैलेंस: ₹<span id="userBalance">0</span></p>
            </div>
            <div class="text-center mb-4">
                <p class="font-bold">📱 QR Code स्कैन करें</p>
                <div id="qrCodeContainer" class="flex justify-center"></div>
            </div>
            <div class="mb-4 p-3 bg-blue-50 rounded-lg text-center">
                <p class="font-bold">UPI ID:</p>
                <code class="bg-white px-3 py-2 rounded border">vermaji7623-2@okhdfcbank</code>
                <button onclick="copyUPI()" class="bg-blue-600 text-white px-3 py-1 rounded ml-2">कॉपी</button>
            </div>
            <input type="text" id="transactionId" placeholder="Transaction ID" class="w-full border rounded-lg p-3 mb-3">
            <button onclick="confirmPayment()" class="bg-green-600 text-white py-3 rounded-lg w-full font-bold">✅ पेमेंट कन्फर्म करें</button>
        </div>
    </div>
</div>

<!-- मेन डैशबोर्ड -->
<div id="mainDashboard" style="display:none;">
    <header class="gradient-bg text-white p-4">
        <div class="container mx-auto flex justify-between">
            <div>
                <h1 class="text-xl font-bold">🇮🇳 VERMAJI DIGITAL</h1>
                <p>वेलकम, <span id="userName">-</span></p>
            </div>
            <div>
                <p>ID: <span id="displayUserId">-</span></p>
                <button onclick="logout()" class="bg-red-500 px-3 py-1 rounded">लॉगआउट</button>
            </div>
        </div>
    </header>
    
    <div class="container mx-auto p-4">
        <div class="bg-gradient-to-r from-purple-600 to-pink-600 rounded-2xl p-6 text-white mb-6">
            <div class="flex justify-between">
                <div>
                    <p>वॉलेट बैलेंस</p>
                    <p class="text-3xl font-bold">₹<span id="walletBalance">0</span></p>
                </div>
                <button onclick="addMoney()" class="bg-yellow-500 text-black px-4 py-2 rounded">+ पैसे डालें</button>
            </div>
        </div>
        
        <h2 class="text-xl font-bold mb-4">सभी सर्विसेज (सिर्फ ₹250)</h2>
        <div class="grid grid-cols-2 gap-4">
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('PAN Download', 250)"><i class="fas fa-id-card text-3xl text-orange-500"></i><p class="font-bold">PAN Download</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('PAN Find', 250)"><i class="fas fa-search text-3xl text-orange-500"></i><p class="font-bold">PAN Find</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('PAN Verify', 250)"><i class="fas fa-check-circle text-3xl text-orange-500"></i><p class="font-bold">PAN Verify</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('Aadhaar Download', 250)"><i class="fas fa-fingerprint text-3xl text-orange-500"></i><p class="font-bold">Aadhaar Download</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('New Enrollment', 250)"><i class="fas fa-sync-alt text-3xl text-orange-500"></i><p class="font-bold">New Enrollment</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('Address Update', 250)"><i class="fas fa-address-card text-3xl text-orange-500"></i><p class="font-bold">Address Update</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('PAN from Aadhaar', 250)"><i class="fas fa-exchange-alt text-3xl text-orange-500"></i><p class="font-bold">PAN from Aadhaar</p><p class="text-green-600">₹250</p></div>
            <div class="bg-white rounded-xl p-4 text-center service-btn" onclick="useService('PAN-Aadhaar Link', 250)"><i class="fas fa-link text-3xl text-orange-500"></i><p class="font-bold">PAN-Aadhaar Link</p><p class="text-green-600">₹250</p></div>
        </div>
        
        <div class="mt-4 bg-gradient-to-r from-green-600 to-blue-600 rounded-xl p-4 text-white text-center service-btn" onclick="useService('कॉम्बो पैक (सभी 8)', 250)">
            <p class="font-bold text-xl">🎁 कॉम्बो पैक - सभी 8 सर्विस</p>
            <p>सिर्फ ₹250</p>
        </div>
    </div>
    
    <footer class="bg-gray-800 text-white text-center p-4 mt-8">
        <p>VERMAJI DIGITAL PORTAL</p>
        <p class="text-sm">UPI: vermaji7623-2@okhdfcbank</p>
    </footer>
</div>

<script>
let users = JSON.parse(localStorage.getItem('vermaji_users') || '{}');
let currentUser = null;

function showRegister() {
    document.getElementById('loginForm').style.display = 'none';
    document.getElementById('registerForm').style.display = 'block';
}
function showLogin() {
    document.getElementById('loginForm').style.display = 'block';
    document.getElementById('registerForm').style.display = 'none';
}
function generateUserId() {
    return Math.floor(100000 + Math.random() * 900000).toString();
}
function register() {
    let name = document.getElementById('regName').value;
    let mobile = document.getElementById('regMobile').value;
    if(!name || !mobile) { showToast('❌ नाम और मोबाइल डालें'); return; }
    if(mobile.length !== 10) { showToast('❌ 10 डिजिट मोबाइल डालें'); return; }
    window.regData = {name, mobile};
    showPaymentForRegistration(250);
}
function showPaymentForRegistration(amount) {
    document.getElementById('payServiceName').innerText = 'नया अकाउंट रजिस्ट्रेशन';
    document.getElementById('payAmount').innerText = amount;
    document.getElementById('userBalance').innerText = '0';
    let upi = `upi://pay?pa=vermaji7623-2@okhdfcbank&pn=VERMAJI&am=${amount}&cu=INR`;
    let qrDiv = document.getElementById('qrCodeContainer');
    qrDiv.innerHTML = '';
    new QRCode(qrDiv, { text: upi, width: 200, height: 200 });
    document.getElementById('paymentModal').style.display = 'block';
    window.paymentType = 'registration';
}
function confirmPayment() {
    let txn = document.getElementById('transactionId').value;
    if(!txn) { showToast('❌ Transaction ID डालें'); return; }
    if(window.paymentType === 'registration' && window.regData) {
        let userId = generateUserId();
        let password = Math.floor(100000 + Math.random() * 900000).toString();
        users[userId] = {
            id: userId, password: password, name: window.regData.name,
            mobile: window.regData.mobile, balance: 0, transactions: []
        };
        localStorage.setItem('vermaji_users', JSON.stringify(users));
        showToast(`✅ अकाउंट बन गया! ID: ${userId}, पास: ${password}`);
        window.open(`https://wa.me/919999999999?text=नया रजिस्ट्रेशन! ID:${userId} Pass:${password}`, '_blank');
        closePaymentModal();
        showLogin();
        window.regData = null;
    } else if(window.paymentType === 'addmoney') {
        if(currentUser) {
            currentUser.balance += window.addAmount;
            users[currentUser.id] = currentUser;
            localStorage.setItem('vermaji_users', JSON.stringify(users));
            updateDashboard();
            showToast(`✅ ₹${window.addAmount} वॉलेट में जमा!`);
        }
        closePaymentModal();
    } else if(window.paymentType === 'service') {
        if(currentUser.balance >= 250) {
            currentUser.balance -= 250;
            users[currentUser.id] = currentUser;
            localStorage.setItem('vermaji_users', JSON.stringify(users));
            updateDashboard();
            showToast(`✅ ${window.selectedService} बुक हो गई!`);
            window.open('https://www.protean-tinpan.com/', '_blank');
        } else {
            showToast(`❌ बैलेंस कम! ₹${250 - currentUser.balance} और डालें`);
        }
        closePaymentModal();
    }
    document.getElementById('transactionId').value = '';
}
function closePaymentModal() {
    document.getElementById('paymentModal').style.display = 'none';
}
function copyUPI() {
    navigator.clipboard.writeText('vermaji7623-2@okhdfcbank');
    showToast('✅ UPI ID कॉपी!');
}
function login() {
    let id = document.getElementById('loginId').value;
    let pwd = document.getElementById('password').value;
    if(users[id] && users[id].password === pwd) {
        currentUser = users[id];
        localStorage.setItem('vermaji_current', id);
        document.getElementById('loginModal').style.display = 'none';
        document.getElementById('mainDashboard').style.display = 'block';
        updateDashboard();
    } else {
        showToast('❌ गलत ID या पासवर्ड');
    }
}
function updateDashboard() {
    document.getElementById('userName').innerText = currentUser.name;
    document.getElementById('displayUserId').innerText = currentUser.id;
    document.getElementById('walletBalance').innerText = currentUser.balance;
}
function logout() {
    currentUser = null;
    localStorage.removeItem('vermaji_current');
    document.getElementById('mainDashboard').style.display = 'none';
    document.getElementById('loginModal').style.display = 'block';
}
function useService(service, amount) {
    window.selectedService = service;
    if(currentUser.balance >= amount) {
        window.paymentType = 'service';
        document.getElementById('payServiceName').innerText = service;
        document.getElementById('payAmount').innerText = amount;
        document.getElementById('userBalance').innerText = currentUser.balance;
        let upi = `upi://pay?pa=vermaji7623-2@okhdfcbank&pn=VERMAJI&am=${amount}&cu=INR`;
        let qrDiv = document.getElementById('qrCodeContainer');
        qrDiv.innerHTML = '';
        new QRCode(qrDiv, { text: upi, width: 200, height: 200 });
        document.getElementById('paymentModal').style.display = 'block';
    } else {
        showToast(`❌ बैलेंस कम! ₹${amount - currentUser.balance} और डालें`);
    }
}
function addMoney() {
    let amt = prompt('कितने रुपए डालने हैं? (न्यूनतम ₹250)', '250');
    if(amt && parseInt(amt) >= 250) {
        window.addAmount = parseInt(amt);
        window.paymentType = 'addmoney';
        document.getElementById('payServiceName').innerText = 'वॉलेट में पैसे डालें';
        document.getElementById('payAmount').innerText = amt;
        document.getElementById('userBalance').innerText = currentUser.balance;
        let upi = `upi://pay?pa=vermaji7623-2@okhdfcbank&pn=VERMAJI&am=${amt}&cu=INR`;
        let qrDiv = document.getElementById('qrCodeContainer');
        qrDiv.innerHTML = '';
        new QRCode(qrDiv, { text: upi, width: 200, height: 200 });
        document.getElementById('paymentModal').style.display = 'block';
    }
}
function showToast(msg) {
    let toast = document.getElementById('toast');
    toast.innerText = msg;
    toast.className = "toast show";
    setTimeout(() => { toast.className = "toast"; }, 3000);
}
let saved = localStorage.getItem('vermaji_current');
if(saved && users[saved]) {
    currentUser = users[saved];
    document.getElementById('loginModal').style.display = 'none';
    document.getElementById('mainDashboard').style.display = 'block';
    updateDashboard();
} else {
    document.getElementById('loginModal').style.display = 'block';
}
</script>
</body>
</html>
