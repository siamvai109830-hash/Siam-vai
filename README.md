<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Telegram Earning App</title>
<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
<style>
body { font-family: 'Poppins', sans-serif; margin:0; background:#0b1221; color:white; }
header { display:flex; justify-content:space-between; align-items:center; padding:10px; background:#141b2d; }
.nav { display:flex; justify-content:space-around; background:#1e2740; }
.nav-item { padding:10px; cursor:pointer; }
.nav-item.active { background:#273250; }
.page { display:none; padding:15px; }
.page.active { display:block; }
button { padding:10px 15px; margin:5px; cursor:pointer; border:none; border-radius:5px; background:#3b4a66; color:white; }
button:disabled { background:#555; cursor:not-allowed; }
#ad-progress-bar { height:10px; background:#4caf50; width:0%; border-radius:5px; margin-top:5px; }
img { border-radius:50%; }
input { padding:8px; border-radius:5px; border:none; margin:5px 0; width:100%; }
</style>
</head>
<body>

<header>
  <div>
    <img id="header-profile-pic" src="https://via.placeholder.com/40" width="40" height="40">
    <span id="header-user-name">User</span>
  </div>
</header>

<div class="nav">
  <div class="nav-item active" data-page="home-page">হোম</div>
  <div class="nav-item" data-page="earn-page">আর্ন</div>
  <div class="nav-item" data-page="withdraw-page">উইথড্র</div>
  <div class="nav-item" data-page="profile-page">প্রোফাইল</div>
</div>

<div id="home-page" class="page active">
  <h2>বর্তমান ব্যালান্স: <span id="current-balance">BDT 0</span></h2>
  <p>মোট আর্নিং: <span id="total-earnings">BDT 0</span></p>
  <p>দেখা অ্যাড: <span id="ads-watched">0</span></p>
  <p>মোট রেফারেল: <span id="total-referrals">0</span></p>
  <p>রেফারেল লিঙ্ক: <span id="referral-link"></span></p>
  <button id="start-earning-btn">আর্ন শুরু করুন</button>
</div>

<div id="earn-page" class="page">
  <h2>আর্নিং পেজ</h2>
  <p id="ad-progress-text">Completed: 0 / 50</p>
  <div id="ad-progress-bar"></div>
  <button id="watch-ad-btn"><span class="material-icons">play_circle_filled</span> বিজ্ঞাপন দেখুন</button>
</div>

<div id="withdraw-page" class="page">
  <h2>উইথড্র</h2>
  <p>আপনার ব্যালান্স: <span id="withdraw-balance-display">BDT 0</span></p>
  <form id="withdraw-form">
    <input type="number" id="withdraw-amount" placeholder="BDT amount" required min="1" step="0.01">
    <button type="submit" id="submit-withdraw-btn">উইথড্র</button>
  </form>
</div>

<div id="profile-page" class="page">
  <img id="profile-page-pic" src="https://via.placeholder.com/80" width="80" height="80">
  <h3 id="profile-user-name">User</h3>
  <p id="profile-user-username">@username</p>
  <p>মোট আর্নিং: <span id="profile-earnings">BDT 0</span></p>
  <p>বর্তমান ব্যালান্স: <span id="profile-balance">BDT 0</span></p>
  <p>দেখা অ্যাড: <span id="profile-ads-watched">0</span></p>
  <p>মোট রেফারেল: <span id="profile-referrals">0</span></p>
</div>

<!-- SDK Script -->
<script src='//libtl.com/sdk.js' data-zone='9936527' data-sdk='show_9936527'></script>

<script>
// Helper: Generate referral code
function generateReferralCode(length=6){
  const chars="ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
  let code=''; for(let i=0;i<length;i++) code+=chars.charAt(Math.floor(Math.random()*chars.length));
  return code;
}

document.addEventListener('DOMContentLoaded', () => {
  const tg = window.Telegram.WebApp; tg.ready(); tg.expand();
  const userData = tg.initDataUnsafe.user;
  const defaultProfilePic40='https://via.placeholder.com/40';
  const defaultProfilePic80='https://via.placeholder.com/80';

  const navItems=document.querySelectorAll('.nav-item');
  const pages=document.querySelectorAll('.page');
  const watchAdBtn=document.getElementById('watch-ad-btn');
  const startEarningBtn=document.getElementById('start-earning-btn');
  const withdrawForm=document.getElementById('withdraw-form');

  const DAILY_ADS_LIMIT=50, AD_REWARD=0.02, MIN_WITHDRAW_AMOUNT=100, REF_REWARD=10;

  if(!localStorage.getItem('earnings')) localStorage.setItem('earnings','0');
  if(!localStorage.getItem('adsWatched')) localStorage.setItem('adsWatched','0');
  if(!localStorage.getItem('totalReferrals')) localStorage.setItem('totalReferrals','0');
  if(!localStorage.getItem('referralCode')) localStorage.setItem('referralCode', generateReferralCode());

  const urlParams = new URLSearchParams(window.location.search);
  const ref = urlParams.get('ref');
  const userId = tg.initDataUnsafe.user?.id || 'guest';
  if(ref && !localStorage.getItem('refCredited')){
    // Real-time server call (fetch demo)
    fetch(`/ref?ref=${ref}&userId=${userId}`).then(()=>{
      let earnings=parseFloat(localStorage.getItem('earnings'))+REF_REWARD;
      localStorage.setItem('earnings',earnings.toString());
      localStorage.setItem('refCredited','true');
      alert(`রেফারেলের জন্য আপনি BDT ${REF_REWARD} পেয়েছেন!`);
      updateUI();
    });
  }

  function updateUI(){
    const earnings=parseFloat(localStorage.getItem('earnings'));
    const adsWatched=parseInt(localStorage.getItem('adsWatched'));
    const referrals=parseInt(localStorage.getItem('totalReferrals'));
    const formattedEarnings=earnings.toFixed(2);

    if(userData){
      const fullName=`${userData.first_name||''} ${userData.last_name||''}`.trim();
      document.getElementById('header-user-name').textContent=fullName||'User';
      document.getElementById('profile-user-name').textContent=fullName||'User';
      document.getElementById('profile-user-username').textContent=userData.username?`@${userData.username}`:'No username';

      if(userData.photo_url){
        const proxyUrl='https://api.allorigins.win/raw?url=';
        const proxiedPhotoUrl=`${proxyUrl}${encodeURIComponent(userData.photo_url)}`;
        document.getElementById('header-profile-pic').src=proxiedPhotoUrl;
        document.getElementById('profile-page-pic').src=proxiedPhotoUrl;
      }else{
        document.getElementById('header-profile-pic').src=defaultProfilePic40;
        document.getElementById('profile-page-pic').src=defaultProfilePic80;
      }
    }

    document.getElementById('current-balance').textContent=`BDT ${formattedEarnings}`;
    document.getElementById('total-earnings').textContent=`BDT ${formattedEarnings}`;
    document.getElementById('ads-watched').textContent=adsWatched;
    document.getElementById('total-referrals').textContent=referrals;
    document.getElementById('referral-link').textContent=`${window.location.origin}?ref=${localStorage.getItem('referralCode')}`;

    document.getElementById('ad-progress-text').textContent=`Completed: ${adsWatched} / ${DAILY_ADS_LIMIT}`;
    document.getElementById('ad-progress-bar').style.width=`${(adsWatched/DAILY_ADS_LIMIT)*100}%`;

    watchAdBtn.disabled=adsWatched>=DAILY_ADS_LIMIT;

    document.getElementById('withdraw-balance-display').textContent=`BDT ${formattedEarnings}`;
    document.getElementById('submit-withdraw-btn').disabled=earnings<MIN_WITHDRAW_AMOUNT;
    if(earnings<MIN_WITHDRAW_AMOUNT) document.getElementById('submit-withdraw-btn').textContent=`ন্যূনতম BDT ${MIN_WITHDRAW_AMOUNT প্রয়োজন`;

    document.getElementById('profile-balance').textContent=`BDT ${formattedEarnings}`;
    document.getElementById('profile-earnings').textContent=`BDT ${formattedEarnings}`;
    document.getElementById('profile-ads-watched').textContent=adsWatched;
    document.getElementById('profile-referrals').textContent=referrals;
  }

  function showPage(targetPageId){
    navItems.forEach(nav=>nav.classList.remove('active'));
    pages.forEach(page=>page.classList.remove('active'));
    const activeNavItem=document.querySelector(`[data-page="${targetPageId}"]`);
    if(activeNavItem) activeNavItem.classList.add('active');
    const activePage=document.getElementById(targetPageId);
    if(activePage) activePage.classList.add('active');
  }

  navItems.forEach(item=>{
    item.addEventListener('click',()=>showPage(item.getAttribute('data-page')));
  });

  startEarningBtn.addEventListener('click',()=>showPage('earn-page'));

  watchAdBtn.addEventListener('click',()=>{
    if(parseInt(localStorage.getItem('adsWatched'))>=DAILY_ADS_LIMIT){
      tg.showAlert('আপনার দৈনিক বিজ্ঞাপনের সীমা পৌঁছে গেছে।'); return;
    }
    watchAdBtn.disabled=true;

    if(typeof show_9936527==='function'){
      try{
        const result=show_9936527('pop');
        if(result && typeof result.then==='function'){
          result.then(()=>{
            let earnings=parseFloat(localStorage.getItem('earnings'))+AD_REWARD;
            let adsWatched=parseInt(localStorage.getItem('adsWatched'))+1;
            localStorage.setItem('earnings',earnings.toString());
            localStorage.setItem('adsWatched',adsWatched.toString());
            tg.showAlert(`বিজ্ঞাপনটি দেখার জন্য আপনি BDT ${AD_REWARD.toFixed(2)} পেয়েছেন!`);
            updateUI(); watchAdBtn.disabled=adsWatched>=DAILY_ADS_LIMIT;
          });
        }else{
          let earnings=parseFloat(localStorage.getItem('earnings'))+AD_REWARD;
          let adsWatched=parseInt(localStorage.getItem('adsWatched'))+1;
          localStorage.setItem('earnings',earnings.toString());
          localStorage.setItem('adsWatched',ads
