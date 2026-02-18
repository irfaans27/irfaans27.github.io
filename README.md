```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Ramadan Card</title>
<style>
  :root{
    --card-bg: #fff;
    --page-bg: linear-gradient(135deg,#0f172a 0%, #0b3b6f 100%);
    --accent: #ffcc00;
    --muted: rgba(255,255,255,0.85);
  }
  html,body{height:100%;margin:0;font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial}
  body{
    display:flex;
    align-items:center;
    justify-content:center;
    background: var(--page-bg);
    color:var(--muted);
  }
  .card{
    width:380px;
    max-width:92%;
    background:linear-gradient(180deg,#08263f 0%, #08394f 100%);
    border-radius:14px;
    padding:28px;
    box-shadow:0 10px 40px rgba(2,6,23,0.6);
    text-align:center;
    position:relative;
    overflow:hidden;
  }
  .title{
    font-size:20px;
    margin:0 0 8px 0;
    color:#fff;
  }
  .subtitle{
    font-size:14px;
    margin:0 0 20px 0;
    color:rgba(255,255,255,0.9);
  }
  .buttons{
    display:flex;
    gap:12px;
    justify-content:center;
    margin-top:8px;
  }
  button{
    border:0;
    cursor:pointer;
    padding:12px 18px;
    border-radius:10px;
    font-size:15px;
    font-weight:600;
    transition:transform .12s ease, box-shadow .12s ease;
    box-shadow:0 6px 18px rgba(2,6,23,0.4);
  }
  #yesBtn{
    background:linear-gradient(90deg,#ffb54d,#ffd166);
    color:#0b1a2b;
    transform-origin:center center;
  }
  #noBtn{
    background:transparent;
    border:2px solid rgba(255,255,255,0.14);
    color:var(--muted);
  }
  #yesBtn:active,#noBtn:active{transform:scale(.98)}
  .full-screen-yes{
    position:fixed!important;
    left:0!important;top:0!important;
    width:100vw!important;height:100vh!important;
    border-radius:0!important;
    margin:0!important;
    display:flex!important;
    align-items:center!important;
    justify-content:center!important;
    font-size:28px!important;
    z-index:9999!important;
  }

  /* Thank you message */
  .thanks {
    display:none;
    margin-top:18px;
    font-size:20px;
    color:#fff;
    font-weight:700;
    letter-spacing:0.2px;
    text-shadow:0 5px 20px rgba(0,0,0,0.6);
  }

  /* confetti */
  .confetti {
    position:fixed;
    width:10px;height:14px;
    z-index:10000;
    pointer-events:none;
    opacity:0;
    transform-origin:center;
    animation:fall 1600ms linear forwards;
  }
  @keyframes fall {
    0% { opacity:1; transform: translateY(-20vh) rotate(0deg) scale(1); }
    100% { opacity:1; transform: translateY(120vh) rotate(520deg) scale(.9); }
  }

  /* subtle fireworks burst (circle) */
  .burst {
    position:absolute;
    left:50%; top:18px;
    transform:translateX(-50%);
    width:0;height:0;
    pointer-events:none;
  }
  .spark {
    position:absolute;
    width:6px;height:18px;border-radius:3px;
    opacity:0;
  }

  @media (max-width:420px){
    .card{padding:18px}
    button{padding:10px 14px;font-size:14px}
  }
</style>
</head>
<body>
  <div class="card" id="card">
    <h1 class="title">Ramadan is almost here</h1>
    <p class="subtitle" id="prompt">Will you suck the tollie one last time before Ramadan starts?</p>

    <div class="buttons">
      <button id="yesBtn">Yes</button>
      <button id="noBtn">No</button>
    </div>

    <div class="thanks" id="thanks">Thank you, I love you</div>

    <div class="burst" id="burst" aria-hidden="true"></div>
  </div>

<script>
  // Elements
  const yesBtn = document.getElementById('yesBtn');
  const noBtn = document.getElementById('noBtn');
  const thanks = document.getElementById('thanks');
  const card = document.getElementById('card');
  const burst = document.getElementById('burst');

  // Growth state for the "Yes" button
  let scale = 3;
  const growthFactor = 1.22; // multiplies each click

  noBtn.addEventListener('click', () => {
    scale *= growthFactor;
    yesBtn.style.transform = `scale(${scale})`;

    // After scaling, check bounding box vs viewport
    const rect = yesBtn.getBoundingClientRect();
    // if it nearly covers the viewport, make it truly full-screen
    if (rect.width >= window.innerWidth * 0.92 || rect.height >= window.innerHeight * 0.92) {
      makeYesFullscreen();
    }
  });

  function makeYesFullscreen(){
    yesBtn.classList.add('full-screen-yes');
    yesBtn.style.transform = 'none';
    // optional: hide other UI
    noBtn.style.display = 'none';
    // clicking the full screen yes also triggers the yes action
    yesBtn.removeEventListener('click', yesClicked); // avoid duplicates
    yesBtn.addEventListener('click', yesClicked);
  }

  yesBtn.addEventListener('click', yesClicked);

  function yesClicked(){
    // Stop further growth behavior
    noBtn.disabled = true;
    yesBtn.disabled = true;

    // show thank-you message
    thanks.style.display = 'block';
    // small celebration: confetti and burst
    launchConfetti(48);
    fireworksBurst();
    // optional: change the prompt text
    document.getElementById('prompt').textContent = '';
  }

  // Confetti generator
  function launchConfetti(count = 30){
    const colors = ['#ff3b3b','#ff8c42','#ffd84b','#7ae582','#4bd1ff','#c18bff'];
    for(let i=0;i<count;i++){
      const c = document.createElement('div');
      c.className = 'confetti';
      const sizeW = 6 + Math.random()*12;
      const sizeH = sizeW * (1 + Math.random()*0.6);
      c.style.width = sizeW + 'px';
      c.style.height = sizeH + 'px';
      c.style.background = colors[Math.floor(Math.random()*colors.length)];
      c.style.left = Math.random() * 100 + 'vw';
      c.style.top = (-10 - Math.random()*20) + 'vh';
      c.style.transform = `rotate(${Math.random()*360}deg)`;
      c.style.opacity = 1;
      document.body.appendChild(c);
      // remove after animation
      setTimeout(()=> c.remove(), 2000 + Math.random()*800);
    }
  }

  // fireworks-like radial burst near top center
  function fireworksBurst(){
    const colors = ['#ffd166','#ff5e7a','#7ae582','#4bd1ff','#c18bff'];
    const cx = window.innerWidth/2;
    const topOffset = 40; // px from top of card
    const count = 12;
    for(let i=0;i<count;i++){
      const s = document.createElement('div');
      s.className = 'spark';
      s.style.background = colors[i % colors.length];
      s.style.left = '0px';
      s.style.top = '0px';
      const angle = (i/count) * Math.PI*2;
      const rad = 64 + Math.random()*40;
      const tx = Math.cos(angle) * rad;
      const ty = Math.sin(angle) * rad;
      s.style.transform = `translate(0px,0px)`;
      burst.appendChild(s);
      // animate with js for staggered effect
      setTimeout(()=> {
        s.style.transition = 'transform 700ms cubic-bezier(.22,.9,.34,1), opacity 700ms linear';
        s.style.transform = `translate(${tx}px, ${ty}px) rotate(${Math.random()*360}deg)`;
        s.style.opacity = 1;
      }, 30*i);

      // fade out and remove
      setTimeout(()=> {
        s.style.transition = 'opacity 500ms linear';
        s.style.opacity = 0;
        setTimeout(()=> s.remove(), 900);
      }, 900);
    }
  }

  // Accessibility: keyboard support
  noBtn.addEventListener('keydown', (e)=>{ if(e.key === 'Enter' || e.key === ' ') noBtn.click(); });
  yesBtn.addEventListener('keydown', (e)=>{ if(e.key === 'Enter' || e.key === ' ') yesBtn.click(); });

</script>
</body>
</html>
