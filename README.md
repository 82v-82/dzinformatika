запрос: сделай лекгую игру плантфорем css/html/js лимит строк кода 200 вообщем 
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Платформер стрелками</title>
<style>
body { margin:0; overflow:hidden; background:#87ceeb; font-family:sans-serif; }
#game { position:relative; width:800px; height:400px; margin:50px auto; background:#a3d9a5; border:2px solid #000; overflow:hidden; }
.player { position:absolute; width:30px; height:50px; bottom:0; left:50px; }
.player .head { width:20px; height:20px; background:#ffcc99; border-radius:50%; margin:0 auto; }
.player .body { width:20px; height:30px; background:red; margin:0 auto; }
.platform { position:absolute; width:100px; height:20px; background:#654321; }
.coin { position:absolute; width:15px; height:15px; background:gold; border-radius:50%; }
#score { position:absolute; top:5px; left:5px; color:#000; font-weight:bold; }
#finish { position:absolute; width:40px; height:60px; background:green; right:20px; bottom:20px; }
</style>
</head>
<body>
<div id="game">
  <div id="score">Монетки: 0</div>
  <div class="player" id="player">
    <div class="head"></div>
    <div class="body"></div>
  </div>
  <div class="platform" style="left:0; bottom:0;"></div>
  <div class="platform" style="left:150px; bottom:100px;"></div>
  <div class="platform" style="left:300px; bottom:200px;"></div>
  <div class="platform" style="left:500px; bottom:150px;"></div>
  <div class="coin" style="left:160px; bottom:130px;"></div>
  <div class="coin" style="left:310px; bottom:230px;"></div>
  <div class="coin" style="left:520px; bottom:180px;"></div>
  <div id="finish"></div>
</div>

<script>
const player = document.getElementById('player');
const game = document.getElementById('game');
const platforms = Array.from(document.getElementsByClassName('platform'));
const coins = Array.from(document.getElementsByClassName('coin'));
const scoreElem = document.getElementById('score');
const finish = document.getElementById('finish');

let pos = { x:50, y:0 };
let vel = { x:0, y:0 };
let keys = { left:false, right:false, up:false };
let gravity = 0.5;
let onGround = false;
let score = 0;

// Управление стрелками
document.addEventListener('keydown', e=>{
  if(e.code=='ArrowLeft') keys.left = true;
  if(e.code=='ArrowRight') keys.right = true;
  if(e.code=='ArrowUp') keys.up = true;
});
document.addEventListener('keyup', e=>{
  if(e.code=='ArrowLeft') keys.left = false;
  if(e.code=='ArrowRight') keys.right = false;
  if(e.code=='ArrowUp') keys.up = false;
});

function update() {
  vel.x = 0;
  if(keys.left) vel.x = -5;
  if(keys.right) vel.x = 5;
  if(keys.up && onGround) { vel.y = -10; onGround = false; }

  vel.y += gravity;
  pos.x += vel.x;
  pos.y += vel.y;

  // Границы
  if(pos.x < 0) pos.x = 0;
  if(pos.x > game.clientWidth - player.clientWidth) pos.x = game.clientWidth - player.clientWidth;
  if(pos.y > game.clientHeight - player.clientHeight) { pos.y = game.clientHeight - player.clientHeight; vel.y = 0; onGround = true; }

  onGround = false;
  // Коллизии с платформами
  platforms.forEach(p=>{
    let px=p.offsetLeft, py=p.offsetTop, pw=p.offsetWidth, ph=p.offsetHeight;
    if(pos.x + player.clientWidth > px && pos.x < px+pw && pos.y + player.clientHeight > py && pos.y + player.clientHeight < py+ph) {
      pos.y = py - player.clientHeight;
      vel.y = 0;
      onGround = true;
    }
  });

  // Сбор монет
  coins.forEach((c,i)=>{
    let cx=c.offsetLeft, cy=c.offsetTop, cw=c.offsetWidth, ch=c.offsetHeight;
    if(pos.x + player.clientWidth > cx && pos.x < cx+cw && pos.y + player.clientHeight > cy && pos.y < cy+ch){
      c.remove(); coins.splice(i,1); score++; scoreElem.textContent='Монетки: '+score;
    }
  });

  // Финиш
  let fx=finish.offsetLeft, fy=finish.offsetTop, fw=finish.offsetWidth, fh=finish.offsetHeight;
  if(pos.x + player.clientWidth > fx && pos.x < fx+fw && pos.y + player.clientHeight > fy && pos.y < fy+fh){
    alert('Поздравляем! Уровень пройден!');
    window.location.reload();
  }

  player.style.left = pos.x+'px';
  player.style.top = pos.y+'px';

  requestAnimationFrame(update);
}

update();
</script>
</body>
</html>
