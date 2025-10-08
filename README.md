
<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram Earning App</title>
    <!-- Tailwind CSS CDN for enhanced styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            margin: 0;
            background: #0b1221;
            color: white;
            padding-bottom: 70px; /* Space for fixed nav */
        }
        header { 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            padding: 12px 15px; 
            background: #141b2d; 
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        }
        .nav { 
            display: flex; 
            justify-content: space-around; 
            background: #1e2740; 
            position: fixed; 
            bottom: 0; 
            left: 0; 
            right: 0; 
            z-index: 10;
        }
        .nav-item { 
            padding: 12px; 
            cursor: pointer; 
            text-align: center;
            flex-grow: 1;
            transition: background 0.3s, color 0.3s;
            font-weight: 600;
            color: #94a3b8;
        }
        .nav-item.active { 
            background: #273250; 
            color: #3b82f6; /* Accent color for active state */
        }
        .page { 
            display: none; 
            padding: 15px; 
        }
        .page.active { 
            display: block; 
        }
        button { 
            padding: 12px 15px; 
            margin: 5px 0; 
            cursor: pointer; 
            border: none; 
            border-radius: 8px; 
            background: #3b4a66; 
            color: white; 
            transition: background 0.2s, transform 0.1s;
            font-weight: 600;
        }
        button:hover:not(:disabled) {
            background: #4a5c80;
        }
        button:active:not(:disabled) {
            transform: scale(0.98);
        }
        button:disabled { 
            background: #555; 
            cursor: not-allowed; 
        }
        #ad-progress-bar { 
            height: 10px; 
            background: #4caf50; 
            width: 0%; 
            border-radius: 5px; 
            margin-top: 5px; 
            transition: width 0.5s;
        }
        img { 
            border-radius: 50%; 
        }
        input, select { 
            padding: 10px; 
            border-radius: 5px; 
            border: 1px solid #475569;
            margin: 5px 0; 
            width: 100%;
            background-color: #1e293b;
            color: white;
            box-sizing: border-box;
        }
        
        /* Custom Modal Styling */
        .modal {
            position: fixed;
            inset: 0;
            background: rgba(0, 0, 0, 0.75);
            z-index: 50;
            display: none;
            align-items: center;
            justify-content: center;
        }
        .modal-content {
            background: #1e293b;
            padding: 24px;
            border-radius: 12px;
            max-width: 300px;
            width: 90%;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.5);
            text-align: center;
        }
        .modal-btn {
            padding: 8px 16px;
            border-radius: 6px;
            font-weight: 600;
            margin: 5px;
        }
    </style>
</head>
<body>

<!-- Header -->
<header>
    <div class="flex items-center space-x-3">
        <img id="header-profile-pic" src="https://via.placeholder.com/40/3b82f6/ffffff?text=U" width="40" height="40" alt="Profile Picture">
        <span id="header-user-name" class="font-semibold">Loading...</span>
    </div>
    <div id="loading-indicator" class="text-blue-400 text-sm">
        <!-- Spinner placeholder -->
    </div>
</header>

<!-- Navigation Tabs -->
<div class="nav">
    <div class="nav-item active" data-page="home-page">🏠 হোম</div>
    <div class="nav-item" data-page="earn-page">🎁 আর্ন</div>
    <div class="nav-item" data-page="withdraw-page">💸 উইথড্র</div>
    <div class="nav-item" data-page="profile-page">👤 প্রোফাইল</div>
</div>

<!-- Pages -->
<div class="max-w-md mx-auto">
    <!-- Home Page -->
    <div id="home-page" class="page active bg-[#141b2d] rounded-xl my-4 p-4 shadow-lg">
        <h2 class="text-xl font-bold mb-3 text-yellow-400">বর্তমান ব্যালান্স</h2>
        <p class="text-4xl font-extrabold mb-4"><span id="current-balance">৳0.00</span></p>
        <div class="space-y-2 text-sm text-gray-300">
            <p>মোট আর্নিং: <span id="total-earnings" class="font-bold text-green-400">৳0.00</span></p>
            <p>দেখা অ্যাড: <span id="ads-watched" class="font-bold">0</span></p>
            <p>মোট রেফারেল: <span id="total-referrals" class="font-bold">0</span></p>
        </div>
        <button id="start-earning-btn" class="w-full mt-4 bg-blue-600 hover:bg-blue-700">আর্ন শুরু করুন</button>
    </div>

    <!-- Earn Page -->
    <div id="earn-page" class="page bg-[#141b2d] rounded-xl my-4 p-4 shadow-lg">
        <h2 class="text-2xl font-bold mb-4">দৈনিক বিজ্ঞাপন</h2>
        <div class="bg-gray-800 p-3 rounded-lg mb-4">
            <p id="ad-progress-text" class="text-sm font-semibold mb-1 text-gray-300">Completed: 0 / 50</p>
            <div class="bg-gray-700 rounded-full">
                <div id="ad-progress-bar"></div>
            </div>
            <p class="text-xs text-green-400 mt-2">প্রতি বিজ্ঞাপনে ৳0.02</p>
        </div>
        
        <button id="watch-ad-btn" class="w-full bg-green-600 hover:bg-green-700 flex items-center justify-center space-x-2">
            <span class="material-icons">play_circle_filled</span>
            <span>বিজ্ঞাপন দেখুন এবং উপার্জন করুন</span>
        </button>
    </div>

    <!-- Withdraw Page -->
    <div id="withdraw-page" class="page bg-[#141b2d] rounded-xl my-4 p-4 shadow-lg">
        <h2 class="text-2xl font-bold mb-4">উত্তোলন অনুরোধ</h2>
        <p class="mb-4 text-lg text-gray-300">আপনার ব্যালান্স: <span id="withdraw-balance-display" class="font-bold text-yellow-400">৳0.00</span></p>
        
        <div class="bg-gray-800 p-4 rounded-xl space-y-4">
            <p class="text-sm text-gray-400 mb-2">ন্যূনতম উত্তোলন: ৳<span id="minWithdrawal">1000.00</span></p>
            
            <!-- Method Selection -->
            <div>
                <label for="withdrawalMethod" class="block text-sm font-medium text-gray-300 mb-1">উত্তোলন মাধ্যম:</label>
                <select id="withdrawalMethod" onchange="updateAccountPlaceholder()">
                    <option value="Bkash">বিকাশ (Bkash)</option>
                    <option value="Nagad">নগদ (Nagad)</option>
                    <option value="Binance">বাইন্যান্স (Binance ID)</option>
                </select>
            </div>

            <!-- Account Input -->
            <div>
                <label for="withdrawalAccount" class="block text-sm font-medium text-gray-300 mb-1">অ্যাকাউন্ট/মোবাইল নাম্বার:</label>
                <input type="text" id="withdrawalAccount" placeholder="বিকাশ মোবাইল নাম্বার দিন (যেমন: 01xxxxxxxxx)" required>
            </div>
            
            <button onclick="checkWithdrawal()" id="submit-withdraw-btn" class="w-full bg-red-600 hover:bg-red-700">উত্তোলন করুন</button>
        </div>
    </div>

    <!-- Profile Page -->
    <div id="profile-page" class="page bg-[#141b2d] rounded-xl my-4 p-4 shadow-lg text-center">
        <img id="profile-page-pic" src="https://via.placeholder.com/80/3b82f6/ffffff?text=U" width="80" height="80" class="mx-auto mb-3" alt="Profile Picture">
        <h3 id="profile-user-name" class="text-2xl font-bold">User</h3>
        <p id="profile-user-username" class="text-sm text-gray-400 mb-4">No username</p>

        <div class="space-y-3 bg-gray-800 p-4 rounded-xl text-left">
            <p class="text-lg font-semibold border-b border-gray-700 pb-2 mb-2 text-blue-300">ইউজার স্ট্যাটাস</p>
            <p>বর্তমান ব্যালান্স: <span id="profile-balance" class="font-bold text-yellow-400">৳0.00</span></p>
            <p>মোট আর্নিং: <span id="profile-earnings" class="font-bold text-green-400">৳0.00</span></p>
            <p>দেখা অ্যাড: <span id="profile-ads-watched" class="font-bold">0</span></p>
            <p>মোট রেফারেল: <span id="profile-referrals" class="font-bold">0</span></p>
            <p class="break-all text-xs text-gray-500 pt-2">ইউজার আইডি: <span id="userIdDisplay" class="font-mono"></span></p>
        </div>
        
        <div class="mt-4 bg-gray-800 p-4 rounded-xl">
             <p class="text-sm font-medium text-gray-300 mb-2">রেফারেল লিংক:</p>
             <div class="flex items-center mt-1">
                <input type="text" id="referral-link-input" readonly class="flex-grow bg-gray-700 border-gray-600 text-sm p-2 rounded-l-lg break-all" />
                <button onclick="copyToClipboard('referral-link-input')" class="bg-indigo-500 hover:bg-indigo-600 text-white py-2 px-3 rounded-r-lg text-sm">কপি</button>
            </div>
        </div>
    </div>
</div>

<!-- Custom Modal Structure (Replaces alert and prompt) -->
<div id="custom-modal" class="modal">
    <div id="modal-content" class="modal-content">
        <h3 id="modal-title" class="text-xl font-bold mb-3 text-white"></h3>
        <p id="modal-message" class="mb-5 text-gray-300"></p>
        <div id="modal-actions" class="flex justify-center space-x-4">
            <button id="modal-cancel" class="modal-btn bg-gray-500 hover:bg-gray-600 text-white hidden">বাতিল</button>
            <button id="modal-ok" class="modal-btn bg-blue-600 hover:bg-blue-700 text-white">ঠিক আছে</button>
        </div>
    </div>
</div>

<!-- Ad SDK Script -->
<script src='//libtl.com/sdk.js' data-zone='9936527' data-sdk='show_9936527'></script>

<!-- Firebase & Application Logic -->
<script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, doc, setDoc, onSnapshot, getDoc, runTransaction } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
    import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

    // Global Firebase Variables (Mandatory Canvas Setup)
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
    const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
    const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

    // --- App Constants ---
    const DAILY_ADS_LIMIT = 50;
    const AD_REWARD = 0.02;
    const MIN_WITHDRAWAL = 1000.00; // Updated from 100 to a more realistic 1000
    const REF_REWARD = 10.00;
    const USER_DOC_ID = 'data';
    
    // --- Telegram Bot Integration (Use your token) ---
    const TELEGRAM_BOT_TOKEN = '8395249628:AAGXxKapvqBmTt7kA4BFh9aCKIRlprqwZxU';
    // !!! IMPORTANT !!!: CHANGE 'YOUR_ADMIN_CHAT_ID' to your actual Telegram chat ID to receive withdrawal requests.
    const ADMIN_CHAT_ID = 'YOUR_ADMIN_CHAT_ID'; 
    // ------------------------------------

    // State and References
    let db, auth;
    let currentUserId = null;
    let userProfileRef = null;
    let userData = null;
    let isAuthReady = false;
    let tg = window.Telegram?.WebApp; // Use optional chaining for safety

    // --- Utility Functions ---

    // Generate a simple, unique-enough referral code
    function generateReferralCode(length = 6) {
        const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
        let code = '';
        for (let i = 0; i < length; i++) {
            code += chars.charAt(Math.floor(Math.random() * chars.length));
        }
        return code;
    }

    // Get the Firestore Document Reference
    function getProfileRef(userId) {
        // Path: /artifacts/{appId}/users/{userId}/profile/{USER_DOC_ID}
        return doc(db, 'artifacts', appId, 'users', userId, 'profile', USER_DOC_ID);
    }
    
    // Copy to clipboard function
    function copyToClipboard(elementId) {
        const copyText = document.getElementById(elementId);
        copyText.select();
        copyText.setSelectionRange(0, 99999);
        
        try {
            document.execCommand('copy');
            showModal("কপি সফল!", "রেফারেল লিংকটি কপি করা হয়েছে।", false, false);
        } catch (err) {
            console.error('Copy failed: ', err);
            showModal("কপি ব্যর্থ!", "দুঃখিত, স্বয়ংক্রিয় কপি সম্ভব হয়নি। ম্যানুয়ালি কপি করুন।", false, false);
        }
    }
    window.copyToClipboard = copyToClipboard; // Export globally

    // --- Modal Logic (Replaces alert/tg.showAlert) ---
    function showModal(title, message, isPrompt = false, isConfirm = false) {
        return new Promise(resolve => {
            const modal = document.getElementById('custom-modal');
            const modalTitle = document.getElementById('modal-title');
            const modalMessage = document.getElementById('modal-message');
            const modalOk = document.getElementById('modal-ok');
            const modalCancel = document.getElementById('modal-cancel');

            modalTitle.textContent = title;
            modalMessage.textContent = message;
            
            modalCancel.classList.add('hidden');
            
            if (isConfirm) {
                modalCancel.classList.remove('hidden');
            }

            modalOk.onclick = () => {
                modal.style.display = 'none';
                resolve(true); 
            };

            modalCancel.onclick = () => {
                modal.style.display = 'none';
                resolve(false); 
            };
            
            modal.style.display = 'flex';
            setTimeout(() => modalOk.focus(), 100);
        });
    }

    // --- View Switching Logic ---
    function showPage(targetPageId) {
        const navItems = document.querySelectorAll('.nav-item');
        const pages = document.querySelectorAll('.page');
        
        navItems.forEach(nav => nav.classList.remove('active'));
        pages.forEach(page => page.classList.remove('active'));
        
        const activeNavItem = document.querySelector(`[data-page="${targetPageId}"]`);
        if (activeNavItem) activeNavItem.classList.add('active');
        
        const activePage = document.getElementById(targetPageId);
        if (activePage) activePage.classList.add('active');
        
        if (targetPageId === 'withdraw-page') {
             updateAccountPlaceholder();
        }
    }

    document.querySelectorAll('.nav-item').forEach(item => {
        item.addEventListener('click', () => showPage(item.getAttribute('data-page')));
    });

    document.getElementById('start-earning-btn')?.addEventListener('click', () => showPage('earn-page'));

    // --- UI Rendering ---

    function updateProfilePicture(url, elementId) {
        const imgElement = document.getElementById(elementId);
        if (imgElement) {
            imgElement.src = url || `https://via.placeholder.com/${imgElement.width}/${imgElement.width}x${imgElement.height}/3b82f6/ffffff?text=U`;
        }
    }

    function renderUI() {
        if (!userData) return;

        const earnings = userData.balance || 0;
        const adsWatched = userData.ads_watched || 0;
        const referrals = userData.total_referrals || 0;
        const formattedEarnings = earnings.toFixed(2);
        
        document.getElementById("userIdDisplay").textContent = currentUserId;

        // User Data Display
        let fullName = 'User';
        let username = 'No username';
        let photoUrl = '';
        
        if (tg?.initDataUnsafe?.user) {
            const user = tg.initDataUnsafe.user;
            fullName = `${user.first_name || ''} ${user.last_name || ''}`.trim() || 'User';
            username = user.username ? `@${user.username}` : 'No username';
            photoUrl = user.photo_url;
        }

        document.getElementById('header-user-name').textContent = fullName;
        document.getElementById('profile-user-name').textContent = fullName;
        document.getElementById('profile-user-username').textContent = username;

        updateProfilePicture(photoUrl, 'header-profile-pic');
        updateProfilePicture(photoUrl, 'profile-page-pic');


        // Home Page
        document.getElementById('current-balance').textContent = `৳${formattedEarnings}`;
        document.getElementById('total-earnings').textContent = `৳${formattedEarnings}`;
        document.getElementById('ads-watched').textContent = adsWatched;
        document.getElementById('total-referrals').textContent = referrals;

        // Referral Link
        const referralLink = `${window.location.origin}${window.location.pathname}?ref=${userData.referral_code}`;
        document.getElementById('referral-link-input').value = referralLink;

        // Earn Page
        const progressBar = document.getElementById('ad-progress-bar');
        const progressText = document.getElementById('ad-progress-text');
        const watchAdBtn = document.getElementById('watch-ad-btn');
        
        progressText.textContent = `Completed: ${adsWatched} / ${DAILY_ADS_LIMIT}`;
        progressBar.style.width = `${(adsWatched / DAILY_ADS_LIMIT) * 100}%`;
        
        const isLimitReached = adsWatched >= DAILY_ADS_LIMIT;
        watchAdBtn.disabled = isLimitReached;
        watchAdBtn.textContent = isLimitReached 
            ? "দৈনিক সীমা পৌঁছে গেছে" 
            : `বিজ্ঞাপন দেখুন এবং উপার্জন করুন (৳${AD_REWARD.toFixed(2)})`;

        // Withdraw Page
        document.getElementById('withdraw-balance-display').textContent = `৳${formattedEarnings}`;
        document.getElementById('minWithdrawal').textContent = MIN_WITHDRAWAL.toFixed(2);
        const submitWithdrawBtn = document.getElementById('submit-withdraw-btn');
        
        const isWithdrawDisabled = earnings < MIN_WITHDRAWAL;
        submitWithdrawBtn.disabled = isWithdrawDisabled;
        if (isWithdrawDisabled) {
            submitWithdrawBtn.textContent = `ন্যূনতম ৳${MIN_WITHDRAWAL.toFixed(2)} প্রয়োজন`;
        } else {
            submitWithdrawBtn.textContent = 'উত্তোলন করুন';
        }

        // Profile Page
        document.getElementById('profile-balance').textContent = `৳${formattedEarnings}`;
        document.getElementById('profile-earnings').textContent = `৳${formattedEarnings}`;
        document.getElementById('profile-ads-watched').textContent = adsWatched;
        document.getElementById('profile-referrals').textContent = referrals;
    }
    
    // Update placeholder for withdrawal input based on method selection
    function updateAccountPlaceholder() {
        const method = document.getElementById('withdrawalMethod').value;
        const input = document.getElementById('withdrawalAccount');
        let placeholder = "";

        switch (method) {
            case 'Bkash':
                placeholder = "বিকাশ মোবাইল নাম্বার দিন (যেমন: 01xxxxxxxxx)";
                break;
            case 'Nagad':
                placeholder = "নগদ মোবাইল নাম্বার দিন (যেমন: 01xxxxxxxxx)";
                break;
            case 'Binance':
                placeholder = "Binance Pay ID অথবা Email দিন";
                break;
            default:
                placeholder = "অ্যাকাউন্ট/মোবাইল নাম্বার দিন";
        }
        input.placeholder = placeholder;
    }
    window.updateAccountPlaceholder = updateAccountPlaceholder;


    // --- Firestore Initialization and Listener ---

    async function initUser(refId) {
        const docSnap = await getDoc(userProfileRef);

        if (docSnap.exists()) {
            userData = docSnap.data();
            // Ensure fields are set if they are missing from old data
            if (typeof userData.ads_watched === 'undefined') userData.ads_watched = 0;
            if (typeof userData.total_referrals === 'undefined') userData.total_referrals = 0;
        } else {
            const defaultName = tg?.initDataUnsafe?.user?.first_name || "টেলিগ্রাম ইউজার"; 
            userData = {
                name: defaultName,
                balance: refId ? REF_REWARD : 0.00, // Initial referral bonus if applicable
                referral_code: generateReferralCode(),
                referred_by: refId || null,
                ads_watched: 0,
                total_referrals: 0,
                history: [{ entry: refId ? "রেফারেল বোনাস" : "শুরুর ব্যালেন্স", amount: refId ? REF_REWARD : 0.00 }] 
            };

            await setDoc(userProfileRef, userData);
            
            if (refId) {
                 await showModal("🎉 সফল!", `রেফারেল কোড: ${refId} ব্যবহারের জন্য আপনি ৳${REF_REWARD.toFixed(2)} বোনাস পেয়েছেন!`, false, false);
            }
        }
        
        setupRealtimeListener();
        document.getElementById('loading-indicator').innerHTML = ''; // Hide loading
    }

    function setupRealtimeListener() {
        if (!userProfileRef) return;

        onSnapshot(userProfileRef, (doc) => {
            if (doc.exists()) {
                userData = doc.data();
                renderUI();
            } else {
                console.error("User profile document missing during snapshot.");
            }
        }, (error) => {
            console.error("Error setting up snapshot listener:", error);
            showModal("ডাটাবেস ত্রুটি", "ডাটাবেস থেকে ডেটা লোড করা যায়নি।", false, false);
        });
    }

    // --- Action Handlers: Earning ---
    
    document.getElementById('watch-ad-btn').addEventListener('click', async () => {
        if (!isAuthReady || !userData) {
            return showModal("ত্রুটি", "অ্যাপ এখনও লোড হচ্ছে। অপেক্ষা করুন।", false, false);
        }
        
        if (userData.ads_watched >= DAILY_ADS_LIMIT) {
            return showModal("সীমা অতিক্রম", "আপনার দৈনিক বিজ্ঞাপনের সীমা পৌঁছে গেছে।", false, false);
        }

        // Show a modal indicating ad is loading
        await showModal("বিজ্ঞাপন লোড হচ্ছে...", "পুরস্কার পেতে অনুগ্রহ করে বিজ্ঞাপনটি দেখুন।", false, false);
        document.getElementById('custom-modal').style.display = 'none'; // Hide modal for ad to show
        document.getElementById('watch-ad-btn').disabled = true;

        if (typeof show_9936527 === 'function') {
            try {
                const result = show_9936527('pop');
                
                // Assuming the SDK returns a promise/thenable for rewarded ads
                if (result && typeof result.then === 'function') {
                    await result;
                    
                    // Transaction to update user balance and count
                    const transactionResult = await runTransaction(db, async (transaction) => {
                        const docSnap = await transaction.get(userProfileRef);
                        if (!docSnap.exists()) throw new Error("Profile does not exist.");
                        
                        const currentData = docSnap.data();
                        const newBalance = (currentData.balance || 0) + AD_REWARD;
                        const newAdsWatched = (currentData.ads_watched || 0) + 1;
                        
                        transaction.update(userProfileRef, {
                            balance: newBalance,
                            ads_watched: newAdsWatched,
                            history: [...(currentData.history || []), { entry: `বিজ্ঞাপন দেখে উপার্জন`, amount: AD_REWARD }]
                        });

                        return { newBalance, newAdsWatched };
                    });

                    await showModal("🎉 সফল!", `বিজ্ঞাপন দেখার জন্য আপনি ৳${AD_REWARD.toFixed(2)} পেয়েছেন!`, false, false);
                    document.getElementById('watch-ad-btn').disabled = transactionResult.newAdsWatched >= DAILY_ADS_LIMIT;

                } else {
                    // Fallback if ad SDK doesn't use promise or instantly closes
                    throw new Error("Ad display did not resolve correctly.");
                }

            } catch (e) {
                console.error("Ad or Reward transaction failed: ", e);
                await showModal("❌ ব্যর্থ!", "দুঃখিত, বিজ্ঞাপন লোড হতে ব্যর্থ হয়েছে অথবা আপনি বাতিল করেছেন।", false, false);
                document.getElementById('watch-ad-btn').disabled = false; // Re-enable button
            }
        } else {
            await showModal("ত্রুটি", "বিজ্ঞাপন লোডিং SDK পাওয়া যায়নি।", false, false);
            document.getElementById('watch-ad-btn').disabled = false;
        }
    });

    // --- Action Handlers: Withdrawal ---

    async function checkWithdrawal() {
        if (!isAuthReady || !userData) {
            return showModal("ত্রুটি", "অ্যাপ এখনও লোড হচ্ছে। অপেক্ষা করুন।", false, false);
        }
        
        const withdrawalMethod = document.getElementById('withdrawalMethod').value;
        const withdrawalAccount = document.getElementById('withdrawalAccount').value.trim();
        const earnings = userData.balance || 0;

        if (!withdrawalAccount) {
            return showModal("ত্রুটি", "দয়া করে আপনার অ্যাকাউন্ট/মোবাইল নাম্বার দিন।", false, false);
        }

        if (earnings < MIN_WITHDRAWAL) {
            return showModal(
                "❌ ব্যর্থ!",
                `উত্তোলনের জন্য ন্যূনতম ৳${MIN_WITHDRAWAL.toFixed(2)} প্রয়োজন! আপনার বর্তমান ব্যালেন্স: ৳${earnings.toFixed(2)}`,
                false, false
            );
        }

        const confirmWithdrawal = await showModal(
            "💵 উত্তোলন নিশ্চিত করুন",
            `আপনি ${withdrawalMethod}-এর মাধ্যমে এই অ্যাকাউন্টে (${withdrawalAccount}) ৳${earnings.toFixed(2)} উত্তোলন করতে চান?`,
            false, true // isConfirm: true
        );

        if (confirmWithdrawal) {
            handleWithdrawal(withdrawalMethod, withdrawalAccount, earnings);
        }
    }
    window.checkWithdrawal = checkWithdrawal; // Export globally

    async function handleWithdrawal(method, account, amount) {
        try {
            // 1. Firestore Transaction (Clear balance and log history)
            await runTransaction(db, async (transaction) => {
                const docSnap = await transaction.get(userProfileRef);
                if (!docSnap.exists()) throw new Error("Profile does not exist.");
                
                const currentData = docSnap.data();
                if (currentData.balance < MIN_WITHDRAWAL) throw new Error("Insufficient funds.");

                const newHistory = currentData.history || [];
                newHistory.push({ 
                    entry: `টাকা উত্তোলন (${method}: ${account})`, 
                    amount: -amount 
                });

                transaction.update(userProfileRef, {
                    balance: 0.00,
                    history: newHistory
                });
            });
            
            // 2. Telegram Notification (After successful Firestore update)
            const messageText = `
*💰 নতুন উত্তোলন অনুরোধ!*

ইউজার আইডি: \`${currentUserId}\`
পরিমাণ: ৳${amount.toFixed(2)}
মাধ্যম: *${method}*
অ্যাকাউন্ট: \`${account}\`

_দয়া করে যাচাই করে পেমেন্ট করুন।_
`;
            
            const telegramUrl = `https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage`;
            const maxRetries = 3;

            for (let i = 0; i < maxRetries; i++) {
                try {
                    const telegramResponse = await fetch(telegramUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify({
                            chat_id: ADMIN_CHAT_ID,
                            text: messageText,
                            parse_mode: 'Markdown'
                        })
                    });

                    if (telegramResponse.ok) break; 
                    
                } catch (error) {
                    console.error(`Error sending Telegram notification (Attempt ${i + 1}):`, error);
                }

                if (i < maxRetries - 1) {
                    const delay = Math.pow(2, i) * 1000;
                    await new Promise(resolve => setTimeout(resolve, delay));
                }
            }


            await showModal("✅ সফল!", `৳${amount.toFixed(2)} উত্তোলনের অনুরোধ জমা হয়েছে।`, false, false);

        } catch (e) {
            console.error("Withdrawal transaction failed: ", e.message);
            const msg = (e.message === "Insufficient funds.") ? "আপনার ব্যালেন্স যথেষ্ট নয়।" : "উত্তোলন প্রক্রিয়া ব্যর্থ হয়েছে।";
            await showModal("ত্রুটি", msg, false, false);
        }
    }


    // --- Initialization ---
    async function initApp() {
        if (Object.keys(firebaseConfig).length === 0) {
            document.querySelector('.max-w-md').innerHTML = '<p class="text-red-500 p-8 text-center">Firebase Configuration Missing. Cannot run app.</p>';
            return;
        }

        const app = initializeApp(firebaseConfig);
        db = getFirestore(app);
        auth = getAuth(app);
        
        // setLogLevel('debug'); 
        document.getElementById('loading-indicator').innerHTML = '<div class="animate-spin rounded-full h-4 w-4 border-b-2 border-blue-500"></div>';

        if (tg) {
            tg.ready(); 
            tg.expand();
        }

        const urlParams = new URLSearchParams(window.location.search);
        const refId = urlParams.get('ref');

        try {
            if (initialAuthToken) {
                const userCredential = await signInWithCustomToken(auth, initialAuthToken);
                currentUserId = userCredential.user.uid;
            } else {
                const userCredential = await signInAnonymously(auth);
                currentUserId = userCredential.user.uid;
            }
            
            isAuthReady = true;
            userProfileRef = getProfileRef(currentUserId);
            
            await initUser(refId);

        } catch (error) {
            console.error("Firebase Auth or Init failed:", error);
            document.getElementById('loading-indicator').innerHTML = `<p class="text-red-500 text-xs">Auth Failed</p>`;
        }
    }

    document.addEventListener('DOMContentLoaded', initApp);
</script>
</body>
</html>
