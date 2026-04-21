<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>WayPay - The Fair Way to Share</title>
    <link rel="manifest" href="manifest.json">
    <meta name="theme-color" content="#1e3a8a">
    <link rel="apple-touch-icon" href="icon.svg">
    <style>
        :root {
            --primary: #1e3a8a; --secondary: #3b82f6; --bg: #f3f4f6; --card: #ffffff; --text: #1f2937; --danger: #ef4444;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background-color: var(--bg); color: var(--text); display: flex; justify-content: center; padding: 15px; min-height: 100vh; }
        .app-container { background: var(--card); width: 100%; max-width: 450px; border-radius: 24px; box-shadow: 0 20px 40px rgba(0,0,0,0.1); padding: 24px; display: flex; flex-direction: column; position: relative; }
        
        /* Top Navigation */
        .top-bar { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .btn-install { background: #10b981; color: white; border: none; padding: 6px 14px; border-radius: 20px; font-size: 0.8rem; font-weight: bold; cursor: pointer; display: none; box-shadow: 0 4px 10px rgba(16,185,129,0.2); animation: pulse 2s infinite; }
        @keyframes pulse { 0% { transform: scale(1); } 50% { transform: scale(1.05); } 100% { transform: scale(1); } }
        .lang-toggle { background: #e5e7eb; border: none; padding: 6px 12px; border-radius: 20px; font-size: 0.8rem; font-weight: bold; cursor: pointer; color: var(--primary); }

        /* Header & Logo */
        .header { display: flex; align-items: center; justify-content: center; gap: 12px; padding-bottom: 20px; border-bottom: 2px solid #f3f4f6; margin-bottom: 25px; }
        .header h1 { font-size: 1.8rem; color: var(--primary); letter-spacing: 1px; font-weight: 800; }
        
        /* Form Styling */
        .form-group { margin-bottom: 18px; }
        label { display: block; font-size: 0.9rem; font-weight: 700; margin-bottom: 8px; color: #4b5563; }
        input { width: 100%; padding: 14px; border: 2px solid #e5e7eb; border-radius: 12px; font-size: 1rem; outline: none; transition: 0.2s; background: #f9fafb; }
        input:focus { border-color: var(--secondary); background: #fff; }
        
        button.btn-calc { width: 100%; padding: 16px; background: var(--primary); color: white; border: none; border-radius: 12px; font-size: 1.1rem; font-weight: bold; cursor: pointer; transition: 0.3s; margin-top: 10px; box-shadow: 0 4px 12px rgba(30,58,138,0.2); }
        button.btn-calc:active { transform: scale(0.97); }

        /* Result Area */
        .result-card { background: linear-gradient(135deg, #eff6ff 0%, #dbeafe 100%); border-radius: 18px; padding: 25px; margin-top: 25px; text-align: center; display: none; border: 1px solid #bfdbfe; }
        .result-card h2 { font-size: 1rem; color: var(--primary); margin-bottom: 10px; text-transform: uppercase; letter-spacing: 1px; }
        .amount { font-size: 3rem; font-weight: 800; color: #059669; margin-bottom: 15px; }
        button.btn-share { background: #2563eb; color: white; border: none; padding: 12px; border-radius: 10px; font-size: 1rem; cursor: pointer; font-weight: 600; display: flex; align-items: center; gap: 8px; width: 100%; justify-content: center; transition: 0.2s; }
        button.btn-share:hover { background: #1d4ed8; }

        /* History Area */
        .history { margin-top: 30px; flex-grow: 1; }
        .history-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; }
        .history h3 { font-size: 1rem; color: #6b7280; font-weight: 700; }
        .btn-clear { font-size: 0.8rem; color: var(--danger); background: none; border: none; cursor: pointer; font-weight: 600; }
        .history-item { background: #fff; padding: 14px; border-radius: 12px; margin-bottom: 10px; display: flex; justify-content: space-between; align-items: center; font-size: 0.95rem; border: 1px solid #f3f4f6; transition: 0.2s; }
        .history-item:hover { transform: translateX(5px); border-left: 4px solid var(--secondary); }
        .history-details { display: flex; flex-direction: column; }
        .history-date { font-size: 0.75rem; color: #9ca3af; margin-top: 4px; }
        
        /* Footer & Credits */
        .footer { margin-top: 40px; text-align: center; font-size: 0.8rem; color: #9ca3af; padding-top: 20px; border-top: 1px dashed #e5e7eb; line-height: 1.6; }
        .footer b { color: #6b7280; }
    </style>
</head>
<body>

<div class="app-container">
    <div class="top-bar">
        <button class="btn-install" id="btnInstall" onclick="installPWA()">⬇️ Install WayPay</button>
        <button class="lang-toggle" onclick="toggleLanguage()" id="langBtn">EN</button>
    </div>

    <div class="header">
        <svg width="45" height="45" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <rect width="100" height="100" rx="22" fill="#1e3a8a" />
            <path d="M 25 45 L 40 70 L 50 50 L 60 70 L 75 45" fill="none" stroke="#ffffff" stroke-width="8" stroke-linecap="round" stroke-linejoin="round"/>
            <circle cx="50" cy="30" r="7" fill="#3b82f6"/>
        </svg>
        <h1>WayPay</h1>
    </div>

    <div class="form-group">
        <label id="lblBillName" for="billName">ชื่อรายการ</label>
        <input type="text" id="billName" placeholder="ระบุชื่อรายการ">
    </div>
    <div class="form-group">
        <label id="lblTotalAmount" for="totalAmount">ยอดเงินรวม (บาท)</label>
        <input type="number" id="totalAmount" placeholder="0.00" inputmode="decimal">
    </div>
    <div class="form-group">
        <label id="lblPeopleCount" for="peopleCount">หารกี่คน?</label>
        <input type="number" id="peopleCount" placeholder="2" value="2" min="1" inputmode="numeric">
    </div>
    
    <button class="btn-calc" id="btnCalc" onclick="calculateSplit()">คำนวณยอดหาร</button>

    <div class="result-card" id="resultCard">
        <h2 id="lblResult">ยอดที่ต้องจ่าย (ต่อคน)</h2>
        <div class="amount" id="resultAmount">฿0</div>
        <button class="btn-share" id="btnShare" onclick="shareResult()">
            <svg width="20" height="20" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.368 2.684 3 3 0 00-5.368-2.684z"></path></svg>
            <span id="lblShareBtn">ส่งให้เพื่อน</span>
        </button>
    </div>

    <div class="history">
        <div class="history-header">
            <h3 id="lblHistory">ประวัติล่าสุด</h3>
            <button class="btn-clear" id="btnClear" onclick="clearHistory()">ล้างประวัติ</button>
        </div>
        <div id="historyList"></div>
    </div>

    <div class="footer" id="lblFooter">
        Developed by <br><b>Asst. Prof. Dr. Phisit Suvarnaphaet</b>
    </div>
</div>

<script>
    // 1. PWA & Service Worker
    let deferredPrompt;
    if ('serviceWorker' in navigator) {
        window.addEventListener('load', () => {
            navigator.serviceWorker.register('sw.js').then(reg => {
                console.log('WayPay SW Registered');
            }).catch(err => console.log('SW Reg Failed', err));
        });
    }

    window.addEventListener('beforeinstallprompt', (e) => {
        e.preventDefault();
        deferredPrompt = e;
        document.getElementById('btnInstall').style.display = 'block';
    });

    async function installPWA() {
        if (deferredPrompt) {
            deferredPrompt.prompt();
            const { outcome } = await deferredPrompt.userChoice;
            if (outcome === 'accepted') document.getElementById('btnInstall').style.display = 'none';
            deferredPrompt = null;
        }
    }

    // 2. Multi-language Data (Corrected Developer Name)
    const dict = {
        th: {
            lblBillName: "ชื่อรายการ (เช่น ค่าอาหาร, ค่าไฟ)", placeholderBill: "ระบุชื่อรายการ",
            lblTotalAmount: "ยอดเงินรวม (บาท)", lblPeopleCount: "หารกี่คน?",
            btnCalc: "คำนวณยอดหาร", lblResult: "ยอดที่ต้องจ่าย (ต่อคน)",
            lblHistory: "ประวัติล่าสุด", btnClear: "ล้างประวัติ",
            lblFooter: "พัฒนาโดย <b>ผศ.ดร.พิสิษฐ์ สุวรรณแพทย์</b>",
            alertMsg: "กรุณากรอกตัวเลขให้ถูกต้องครับ",
            perPerson: "คน", lblShareBtn: "ส่งให้เพื่อน", 
            shareText: "💰 ยอดหารค่า {name}\nยอดรวม: {amount} บาท\nจำนวน: {people} คน\n✅ จ่ายคนละ: {split} บาท"
        },
        en: {
            lblBillName: "Bill Description", placeholderBill: "e.g., Dinner, Netflix",
            lblTotalAmount: "Total Amount (THB)", lblPeopleCount: "Split between (pax)?",
            btnCalc: "Calculate Split", lblResult: "Amount to Pay (per pax)",
            lblHistory: "Recent History", btnClear: "Clear History",
            lblFooter: "Developed by <b>Asst. Prof. Dr. Phisit Suvarnaphaet</b>",
            alertMsg: "Please enter valid numbers.",
            perPerson: "pax", lblShareBtn: "Share to Friends", 
            shareText: "💰 {name} Bill Split\nTotal: {amount} THB\nFor: {people} pax\n✅ Each pays: {split} THB"
        }
    };

    let currentLang = localStorage.getItem('waypay_lang') || 'th';
    let lastCalc = null;
    updateLanguageUI();

    function toggleLanguage() {
        currentLang = currentLang === 'th' ? 'en' : 'th';
        localStorage.setItem('waypay_lang', currentLang);
        updateLanguageUI();
        renderHistory();
    }

    function updateLanguageUI() {
        document.getElementById('langBtn').innerText = currentLang === 'th' ? 'EN' : 'TH';
        ['lblBillName', 'lblTotalAmount', 'lblPeopleCount', 'btnCalc', 'lblResult', 'lblHistory', 'btnClear', 'lblFooter', 'lblShareBtn'].forEach(id => {
            document.getElementById(id).innerHTML = dict[currentLang][id];
        });
        document.getElementById('billName').placeholder = dict[currentLang].placeholderBill;
        if (!navigator.share) document.getElementById('btnShare').style.display = 'none';
    }

    // 3. App Logic
    let historyData = JSON.parse(localStorage.getItem('waypay_history')) || [];
    renderHistory();

    function calculateSplit() {
        const nameInput = document.getElementById('billName').value.trim();
        const name = nameInput || (currentLang === 'th' ? 'บิลทั่วไป' : 'General Bill');
        const amount = parseFloat(document.getElementById('totalAmount').value);
        const people = parseInt(document.getElementById('peopleCount').value);

        if (isNaN(amount) || amount <= 0 || isNaN(people) || people <= 0) {
            alert(dict[currentLang].alertMsg);
            return;
        }

        const splitAmount = (amount / people).toLocaleString(undefined, {minimumFractionDigits: 2, maximumFractionDigits: 2});
        lastCalc = { name, amount: amount.toLocaleString(), people, split: splitAmount };

        document.getElementById('resultAmount').innerText = `฿${splitAmount}`;
        document.getElementById('resultCard').style.display = 'block';

        const now = new Date();
        const dateStr = now.toLocaleString(currentLang === 'th' ? 'th-TH' : 'en-US', { dateStyle: 'short', timeStyle: 'short' });
        
        historyData.unshift({ name, amount: amount.toLocaleString(), people, splitAmount, dateStr });
        if (historyData.length > 15) historyData.pop(); 
        localStorage.setItem('waypay_history', JSON.stringify(historyData));
        
        renderHistory();
        if (navigator.vibrate) navigator.vibrate(40);
    }

    function renderHistory() {
        const list = document.getElementById('historyList');
        list.innerHTML = '';
        if(historyData.length === 0) {
            list.innerHTML = `<div style="text-align:center; color:#9ca3af; font-size:0.85rem; padding:20px; border: 1px dashed #e5e7eb; border-radius:12px;">No history yet</div>`;
            return;
        }
        historyData.forEach(item => {
            list.innerHTML += `
                <div class="history-item">
                    <div class="history-details">
                        <b>${item.name}</b>
                        <span class="history-date">${item.dateStr} • ${item.people} ${dict[currentLang].perPerson}</span>
                    </div>
                    <span style="color: #059669; font-weight: 800;">฿${item.splitAmount}</span>
                </div>
            `;
        });
    }

    function clearHistory() {
        if(confirm(currentLang === 'th' ? 'ล้างประวัติการคำนวณทั้งหมด?' : 'Clear all history records?')) {
            historyData = [];
            localStorage.removeItem('waypay_history');
            renderHistory();
        }
    }

    async function shareResult() {
        if (!navigator.share || !lastCalc) return;
        let text = dict[currentLang].shareText
            .replace('{name}', lastCalc.name).replace('{amount}', lastCalc.amount)
            .replace('{people}', lastCalc.people).replace('{split}', lastCalc.split);
        try {
            await navigator.share({ title: 'WayPay Split', text: text + '\n\nShared via WayPay' });
        } catch (err) {}
    }
</script>
</body>
</html>
