##‼️⚠️紧急通知：我的作品似乎被js13k删除了，因此以下相关链接已经失效404，需要下载本地运行！

目前已经重新上传，另起仓库

source code :
https://github.com/unknowdevforjs13k2026/js13k2026/blob/main/source%20code.html

--------------------------------------------------------------



# 		**⚡雷霆独角兽 ⚡**

## **已提交在[js13kgames](https://play.js13kgames.com/thunder-unicorn-13/#play)**

## 主页预览：

### Thunder Unicorn 13 | js13kGames 2026](https://js13kgames.com/games/thunder-unicorn-13)

# ***To player :***

# 1· **lang=zh**

## **你可以在游戏开始界面右键鼠标选择翻译**(lang=en)

### js13k网站翻译失败需要使用鼠标中键在新标签页打开游戏链接[js13kgames](https://play.js13kgames.com/thunder-unicorn-13/#play)

## 2· **debug选项**

## **可以适当调节难度，以及一些开发者选项，**

## **如果关闭独角兽碰撞箱，你可以观察大小来决定解救顺序**

## 远处的独角兽会被更多云层遮挡



## 享受！

注：图片仅供参考



---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 开发日志：
（点击上方的三横杠可以快速索引    ↑↑↑）

## ***第一步* **  **构思玩法与demo**

1· **rain bow 雨弓 双关** 

（操控/武器/玩家交互）

### 2· **独角兽生活在云朵中，所以场景应该与天空，云朵，彩虹有关，彩虹出现在雨过天晴之后，因此要加入下雨的场景**

（场景设计/玩法基础/故事背景/丰富场景元素）

#### 3· **基础玩法，操控一把雨弓射中独角兽“解救”，云朵会干扰箭**

（玩法确立/丰富交互元素）

以下为初版demo

```html
<!doctype html>
<html lang="zh-CN">
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>雨弓与云中的独角兽</title>
<style>
*{box-sizing:border-box}html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#07182d;font-family:system-ui;color:white}
canvas{display:block;width:100%;height:100%;touch-action:none}
#ui{position:fixed;inset:0;pointer-events:none;text-align:center}
#top{padding:18px;font-size:16px;text-shadow:0 2px 5px #000}
#msg{position:absolute;top:15%;left:50%;transform:translateX(-50%);
font-size:22px;font-weight:bold;text-shadow:0 2px 8px #000;max-width:90%}
#hint{position:absolute;bottom:8%;left:50%;transform:translateX(-50%);
padding:9px 18px;border-radius:20px;background:#0006;font-size:15px}
#start{position:absolute;inset:0;background:#07182dcc;display:grid;place-items:center;pointer-events:auto}
#start div{max-width:600px;padding:30px;text-align:center}
button{font-size:18px;padding:12px 30px;border:0;border-radius:30px;background:#fff;color:#345;cursor:pointer}
h1{font-size:42px;margin:0 0 10px}
</style>
<canvas id="c"></canvas>
<div id="ui">
 <div id="top">雨弓计划　< b id="lv">1</b> / 13　✦ <b id="saved">0</b></div>
 <div id="msg"></div>
 <div id="hint">拖动瞄准，按住蓄力，松开发射</div>
</div>
<div id="start">
 <div>
  <h1>🌈 雨弓</h1>
  <p>云层深处困住了 13 只独角兽。</p>
  <p>用雨弓射中它们，让天空降雨，再等待雨过天晴。</p>
  <button onclick="begin()">开始解救</button>
 </div>
</div>
<script>
const C=document.querySelector('#c'),x=C.getContext('2d');
let W,H,dpr,t=0,level=0,saved=0,state='idle',drag=0,power=0;
let aim={x:0,y:0},arrow=null,rain=[],stars=[];
const tips=[
'第一只独角兽就在云层中央。',
'跟着金色角的位置瞄准。',
'它躲在更高的云层里。',
'云朵正在移动，耐心等待。',
'独角兽害怕雷声，但雨弓不会伤害它。',
'看见彩色鬃毛了吗？',
'这一只被厚厚的云遮住了。',
'瞄准云层之间的缝隙。',
'它正在向右移动。',
'雨弓可以穿过薄云。',
'最后三只越来越难找了。',
'它就在彩虹出现的位置附近。',
'最后一只独角兽等待着天空放晴。'
];
function resize(){
 dpr=devicePixelRatio||1;W=innerWidth;H=innerHeight;
 C.width=W*dpr;C.height=H*dpr;x.setTransform(dpr,0,0,dpr,0,0);
}
addEventListener('resize',resize);resize();

function rnd(a,b){return a+Math.random()*(b-a)}
function cloud(z){
 let s=1/z, cx=W/2+rnd(-W*.42,W*.42),cy=H*.28+rnd(-70,120);
 return {x:cx,y:cy,z,s,w:rnd(100,240)*s};
}
let clouds=Array.from({length:30},()=>cloud(rnd(.7,2.3)));

let uni={x:0,y:0,z:1,dx:0,dy:0};
function newUni(){
 uni={x:W/2+rnd(-W*.25,W*.25),y:H*.28+rnd(-50,110),
 z:rnd(.65,1.25),dx:rnd(-.3,.3),dy:rnd(-.12,.12)};
}
function begin(){
 document.querySelector('#start').style.display='none';
 level=0;saved=0;newUni();next();
}
function next(){
 state='idle';arrow=null;rain=[];power=0;level++;
 document.querySelector('#lv').textContent=level;
 document.querySelector('#saved').textContent=saved;
 document.querySelector('#msg').textContent=tips[level-1];
 setTimeout(()=>document.querySelector('#msg').textContent='',2200);
}

function sky(){
 let g=x.createLinearGradient(0,0,0,H);
 g.addColorStop(0,'#081b39');g.addColorStop(.5,'#3e7faf');
 g.addColorStop(1,'#b8e4ee');x.fillStyle=g;x.fillRect(0,0,W,H);
}
function starsDraw(){
 for(let i=0;i<45;i++){
  let sx=(i*97)%W,sy=(i*53)%H*.45;
  x.fillStyle='#ffffff88';x.fillRect(sx,sy,1.5,1.5);
 }
}
function drawCloud(o){
 let q=o.s,xx=o.x,yy=o.y;
 x.save();x.globalAlpha=.75;
 x.fillStyle='#eaf7ff';
 x.beginPath();
 x.ellipse(xx,yy,75*q,28*q,0,0,7);
 x.ellipse(xx-45*q,yy+3*q,45*q,25*q,0,0,7);
 x.ellipse(xx+40*q,yy-5*q,55*q,31*q,0,0,7);
 x.fill();x.restore();
}
function drawUni(){
 let q=uni.z;
 x.save();x.translate(uni.x,uni.y);x.scale(q,q);
 // shadow
 x.fillStyle='#0002';x.beginPath();x.ellipse(0,30,38,9,0,0,7);x.fill();
 // body
 x.fillStyle='#fff';x.beginPath();x.ellipse(0,0,32,18,0,0,7);x.fill();
 // neck/head
 x.beginPath();x.ellipse(28,-15,15,14,0,0,7);x.fill();
 // legs
 x.strokeStyle='#fff';x.lineWidth=6;
 for(let i=-1;i<2;i++){x.beginPath();x.moveTo(i*16,10);x.lineTo(i*17,29);x.stroke()}
 // mane
 x.fillStyle='#ff8acb';x.beginPath();x.arc(18,-25,13,0,7);x.fill();
 x.fillStyle='#222';x.beginPath();x.arc(33,-18,2,0,7);x.fill();
 // horn
 x.fillStyle='#ffe98a';x.beginPath();x.moveTo(38,-27);x.lineTo(55,-48);x.lineTo(43,-23);x.fill();
 // tail
 x.strokeStyle='#9b8cff';x.lineWidth=8;x.beginPath();x.moveTo(-27,0);
 x.quadraticCurveTo(-55,-12,-48,-29);x.stroke();
 x.restore();
}
function bow(){
 let bx=48,by=H-70;
 let dx=aim.x-bx,dy=aim.y-by,L=Math.hypot(dx,dy)||1;
 let a=Math.atan2(dy,dx);
 x.save();x.translate(bx,by);x.rotate(a);
 x.strokeStyle='#c9904a';x.lineWidth=9;
 x.beginPath();x.arc(0,0,48,-1.15,1.15);x.stroke();
 x.strokeStyle='#e9e1c9';x.lineWidth=2;
 x.beginPath();x.moveTo(Math.cos(-1.15)*48,Math.sin(-1.15)*48);
 x.lineTo(power*35,0);
 x.lineTo(Math.cos(1.15)*48,Math.sin(1.15)*48);x.stroke();
 x.strokeStyle='#7cecff';x.lineWidth=3;
 x.beginPath();x.moveTo(0,0);x.lineTo(70+power*20,0);x.stroke();
 x.restore();
}
function shoot(){
 if(state!='idle')return;
 let bx=48,by=H-70;
 let dx=aim.x-bx,dy=aim.y-by,L=Math.hypot(dx,dy)||1;
 arrow={x:bx,y:by,vx:dx/L*(7+power*13),vy:dy/L*(7+power*13)};
 state='fly';power=0;
}
function drawArrow(){
 if(!arrow)return;
 x.save();x.translate(arrow.x,arrow.y);
 x.rotate(Math.atan2(arrow.vy,arrow.vx));
 x.strokeStyle='#8cecff';x.lineWidth=4;
 x.beginPath();x.moveTo(-25,0);x.lineTo(12,0);x.stroke();
 x.fillStyle='#fff';x.beginPath();x.moveTo(15,0);x.lineTo(5,-5);x.lineTo(5,5);x.fill();
 x.restore();
}
function makeRain(){
 rain=Array.from({length:170},()=>({x:rnd(0,W),y:rnd(-H,0),v:rnd(7,15),l:rnd(8,20)}));
}
function rainDraw(){
 x.strokeStyle='#9deaff88';x.lineWidth=1.5;
 for(let r of rain){
  x.beginPath();x.moveTo(r.x,r.y);x.lineTo(r.x-3,r.y+r.l);x.stroke();
  r.y+=r.v;if(r.y>H)r.y=-20;
 }
}
function rainbow(){
 let cx=W*.62,cy=H*.73,R=Math.min(W,H)*.5;
 x.save();x.globalAlpha=.65;
 let cols=['#ff5b6e','#ffb44e','#ffe65c','#68dc83','#63b8ff','#9b7cff'];
 for(let i=0;i<6;i++){
  x.strokeStyle=cols[i];x.lineWidth=9;
  x.beginPath();x.arc(cx,cy,R-i*11,Math.PI,Math.PI*2);x.stroke();
 }
 x.restore();
}
function hit(){
 let dx=arrow.x-uni.x,dy=arrow.y-uni.y;
 return Math.hypot(dx,dy)<42*uni.z;
}
function update(){
 t++;
 clouds.forEach(o=>{o.x+=Math.sin(t/700+o.y)*.12});
 if(state==='idle'){
  uni.x+=uni.dx;uni.y+=uni.dy;
  if(uni.x<80||uni.x>W-80)uni.dx*=-1;
  if(uni.y<H*.18||uni.y>H*.5)uni.dy*=-1;
 }
 if(state==='fly'&&arrow){
  arrow.x+=arrow.vx;arrow.y+=arrow.vy;
  arrow.vy+=.035;
  if(hit()){
   arrow=null;state='rain';makeRain();
   saved++;document.querySelector('#saved').textContent=saved;
   document.querySelector('#msg').textContent='命中！云层开始下雨……';
   setTimeout(()=>state='sun',3000);
  }else if(arrow.x<0||arrow.x>W||arrow.y<0||arrow.y>H)state='idle';
 }
}
function draw(){
 sky();starsDraw();
 clouds.sort((a,b)=>b.z-a.z).forEach(drawCloud);
 if(state==='idle'||state==='fly')drawUni();
 if(state==='rain')rainDraw();
 if(state==='sun'){
  rainbow();drawUni();
  x.fillStyle='#ffffff';x.globalAlpha=Math.max(0,1-(t%180)/180);
  x.fillRect(0,0,W,H);x.globalAlpha=1;
 }
 if(state==='idle')bow();
 drawArrow();
}
function loop(){update();draw();requestAnimationFrame(loop)}
loop();

function pointer(e){
 let p=e.touches?e.touches[0]:e;
 aim.x=p.clientX;aim.y=p.clientY;
}
C.addEventListener('pointerdown',e=>{
 pointer(e);
 if(state==='idle'){drag=1;power=.2}
});
C.addEventListener('pointermove',e=>{
 pointer(e);
 if(drag)power=Math.min(1,power+.015);
});
addEventListener('pointerup',e=>{
 if(drag){drag=0;shoot()}
});
setInterval(()=>{
 if(state==='sun'){
  state='idle';
  if(level<13){
   document.querySelector('#msg').textContent='雨过天晴！独角兽获救！';
   setTimeout(next,1500);
  }else{
   document.querySelector('#msg').textContent='🌈 13只独角兽全部获救！';
   document.querySelector('#hint').textContent='天空重新恢复了光明';
  }
 }
},100);

</script>
</html>
```

## ***第二步* ** 测试demo并补充想法和修bug

### 1· 下雨有时候会打雷

（丰富交互元素）

以下为加入打雷元素的demo

```html
<!doctype html>
<html lang="zh-CN">
<head>

<meta charset="utf-8">

<meta
 name="viewport"
 content="width=device-width,
 initial-scale=1,
 maximum-scale=1,
 user-scalable=no">

<title>🌈 雷雨救援</title>

<style>

*{
 box-sizing:border-box;
}

html,body{
 margin:0;
 width:100%;
 height:100%;
 overflow:hidden;
 background:#07182d;
 color:white;
 font-family:
 system-ui,
 -apple-system,
 BlinkMacSystemFont,
 "Segoe UI",
 sans-serif;
}

canvas{
 width:100%;
 height:100%;
 display:block;
 touch-action:none;
}

button{
 border:0;
 border-radius:25px;
 padding:12px 25px;
 font-size:17px;
 cursor:pointer;
}

input{
 width:100%;
 border:0;
 border-radius:12px;
 padding:12px;
 margin:5px 0;
 font-size:16px;
}

#menu{
 position:fixed;
 inset:0;
 display:grid;
 place-items:center;
 background:#06162ded;
 z-index:50;
}

.panel{
 width:min(92%,430px);
 padding:30px;
 border-radius:25px;
 background:#102a46ee;
 text-align:center;
 box-shadow:0 20px 80px #0009;
}

.panel h1{
 font-size:44px;
 margin:0 0 10px;
}

.panel p{
 line-height:1.6;
 opacity:.85;
}

.menuButton{
 width:100%;
 margin:6px 0;
}

#roomBox{
 display:none;
}

#ui{
 position:fixed;
 inset:0;
 pointer-events:none;
 z-index:10;
}

#top{
 position:absolute;
 left:50%;
 top:12px;
 transform:translateX(-50%);
 padding:8px 18px;
 border-radius:22px;
 background:#06152caa;
 backdrop-filter:blur(8px);
 text-shadow:0 2px 5px #000;
 white-space:nowrap;
}

#msg{
 position:absolute;
 top:14%;
 left:50%;
 transform:translateX(-50%);
 width:90%;
 text-align:center;
 font-size:22px;
 font-weight:bold;
 text-shadow:0 3px 10px #000;
}

#cooldown{
 position:absolute;
 bottom:62px;
 left:50%;
 transform:translateX(-50%);
 padding:5px 14px;
 border-radius:15px;
 background:#0005;
 color:#fff;
 transition:.15s;
}

#cooldown.cooling{
 color:#777;
 opacity:.65;
}

#hint{
 position:absolute;
 bottom:20px;
 left:50%;
 transform:translateX(-50%);
 padding:7px 15px;
 border-radius:18px;
 background:#0008;
 font-size:13px;
 white-space:nowrap;
}

#scoreboard{
 position:absolute;
 right:12px;
 top:55px;
 min-width:160px;
 padding:10px;
 border-radius:13px;
 background:#06152cdd;
 backdrop-filter:blur(8px);
 font-size:14px;
}

.player{
 display:flex;
 justify-content:space-between;
 gap:20px;
 margin:4px 0;
}

#roomInfo{
 position:absolute;
 left:12px;
 top:55px;
 padding:7px 12px;
 border-radius:12px;
 background:#06152cdd;
}

.hidden{
 display:none!important;
}

@media(max-width:700px){

 #scoreboard{
  right:7px;
  bottom:70px;
  top:auto;
  min-width:125px;
  font-size:12px;
 }

 #roomInfo{
  left:7px;
  top:55px;
  font-size:12px;
 }

 #hint{
  font-size:12px;
 }

}

</style>
</head>

<body>

<canvas id="c"></canvas>


<div id="menu">

 <div class="panel">

  <h1>🌈 雷雨救援</h1>

  <p>
   云层深处困住了13只独角兽。<br>
   用闪电穿过正确的云层，解救它们。
  </p>

  <button
   class="menuButton"
   onclick="startSingle()">
   单人游戏
  </button>

  <button
   class="menuButton"
   onclick="showRoom()">
   多人联机
  </button>

  <div id="roomBox">

   <input
    id="name"
    maxlength="12"
    placeholder="昵称">

   <input
    id="room"
    maxlength="8"
    placeholder="房间号">

   <button
    class="menuButton"
    onclick="startOnline()">
    创建 / 加入房间
   </button>

  </div>

 </div>

</div>


<div id="ui">

 <div id="top">
  🌈 第 <b id="lv">0</b> / 13
 　🦄 <b id="saved">0</b>
 </div>

 <div
  id="roomInfo"
  class="hidden">
 </div>

 <div
  id="scoreboard"
  class="hidden">
 </div>

 <div id="msg"></div>

 <div id="cooldown">
  ⚡ READY
 </div>

 <div id="hint">
  鼠标 / 手指瞄准 · 按住蓄力 · 松开发射
 </div>

</div>


<script>

/* =====================================================
   Canvas
===================================================== */

const C=document.getElementById('c');
const X=C.getContext('2d');

let W=innerWidth;
let H=innerHeight;
let DPR=devicePixelRatio||1;

function resize(){

 W=innerWidth;
 H=innerHeight;

 DPR=devicePixelRatio||1;

 C.width=W*DPR;
 C.height=H*DPR;

 X.setTransform(
  DPR,0,0,DPR,0,0
 );

}

addEventListener(
 'resize',
 resize
);

resize();


function rnd(a,b){

 return a+
  Math.random()*
  (b-a);

}


/* =====================================================
   游戏状态
===================================================== */

let state='menu';

let mode='single';

let level=0;

let saved=0;

let clouds=[];

let unicorn=null;

let rainbow=null;

let rain=[];

let projectiles=[];

let particles=[];

let flash=0;

let t=0;


/* =====================================================
   输入
===================================================== */

let pointerDown=false;

let power=0;

let aim={
 x:W*.65,
 y:H*.45
};


/* =====================================================
   射击冷却
===================================================== */

const BASE_COOLDOWN=700;

const COOLDOWN_ADD=180;

const MAX_COOLDOWN=3000;

let shotCount=0;

let cooldown=0;

let lastShot=0;

let lastActivity=0;


/* =====================================================
   联机
===================================================== */

let ws=null;

let playerId='local';

let playerName='你';

let roomCode='';

let players=[];

let serverCooldown=0;


/* =====================================================
   关卡提示
===================================================== */

const tips=[

 '第一只独角兽就在云层中央。',

 '观察独角兽所在的高度。',

 '穿过远处的薄云。',

 '不要被前景云层欺骗。',

 '寻找云层之间的空隙。',

 '独角兽正在移动。',

 '厚云会阻挡闪电。',

 '这一关需要从下方射击。',

 '观察云层的高度差。',

 '不要急着射击。',

 '寻找最短路径。',

 '最后两只越来越难找到。',

 '最后一只独角兽等待天空放晴。'

];


/* =====================================================
   音效
===================================================== */

let audioCtx=null;

function thunder(){

 try{

  if(!audioCtx){

   audioCtx=
    new(
     window.AudioContext||
     window.webkitAudioContext
    )();

  }

  const o=
   audioCtx.createOscillator();

  const g=
   audioCtx.createGain();

  o.type='sawtooth';

  o.frequency.setValueAtTime(
   120,
   audioCtx.currentTime
  );

  o.frequency.exponentialRampToValueAtTime(
   35,
   audioCtx.currentTime+.45
  );

  g.gain.setValueAtTime(
   .0001,
   audioCtx.currentTime
  );

  g.gain.exponentialRampToValueAtTime(
   .2,
   audioCtx.currentTime+.015
  );

  g.gain.exponentialRampToValueAtTime(
   .0001,
   audioCtx.currentTime+.5
  );

  o.connect(g);

  g.connect(
   audioCtx.destination
  );

  o.start();

  o.stop(
   audioCtx.currentTime+.5
  );

 }catch(e){}

}


/* =====================================================
   开始
===================================================== */

function startSingle(){

 mode='single';

 playerId='local';

 playerName='你';

 players=[
  {
   id:'local',
   name:'你',
   score:0
  }
 ];

 document
  .getElementById('menu')
  .classList.add('hidden');

 document
  .getElementById('scoreboard')
  .classList.remove('hidden');

 resetGame();

}


function showRoom(){

 document
  .getElementById('roomBox')
  .style.display='block';

}


function startOnline(){

 mode='online';

 playerName=
  document
   .getElementById('name')
   .value
   .trim()||'玩家';

 roomCode=
  document
   .getElementById('room')
   .value
   .trim();

 const protocol=
  location.protocol==='https:'
   ?'wss:'
   :'ws:';

 ws=new WebSocket(
  protocol+'//'+location.host
 );

 ws.onopen=()=>{

  ws.send(
   JSON.stringify({

    type:'join',

    room:roomCode,

    name:playerName

   })
  );

 };

 ws.onmessage=e=>{

  try{

   networkMessage(
    JSON.parse(e.data)
   );

  }catch(err){}

 };

 ws.onclose=()=>{

  showMessage(
   '联机连接已断开'
  );

 };

}


/* =====================================================
   游戏初始化
===================================================== */

function resetGame(){

 level=0;

 saved=0;

 shotCount=0;

 cooldown=0;

 serverCooldown=0;

 projectiles=[];

 particles=[];

 rainbow=null;

 rain=[];

 nextLevel();

}


/* =====================================================
   生成云
===================================================== */

function generateClouds(){

 clouds=[];

 /*
  * 每关随机重新生成。
  * 但根据关卡保证：
  *
  * 远云 > unicorn.z
  * 前景云 < unicorn.z
  */

 for(let i=0;i<12;i++){

  clouds.push(
   makeCloud(
    rnd(1.8,2.5)
   )
  );

 }

 for(let i=0;i<10;i++){

  clouds.push(
   makeCloud(
    rnd(.65,1.45)
   )
  );

 }

}


function makeCloud(z){

 return {

  x:rnd(-80,W+80),

  y:rnd(
   H*.10,
   H*.67
  ),

  z:z,

  size:
   40+
   115*
   ((2.5-z)/1.85),

  speed:rnd(.04,.20),

  phase:rnd(
   0,
   Math.PI*2
  ),

  hit:0

 };

}


/* =====================================================
   独角兽
===================================================== */

function newUnicorn(){

 /*
  * 不再固定在最高层。
  */

 unicorn={

  x:
   W*.50+
   rnd(-W*.25,W*.25),

  y:
   H*.22+
   rnd(
    0,
    H*.30
   ),

  z:
   rnd(.85,1.45),

  dx:
   rnd(-.25,.25),

  dy:
   rnd(-.08,.08)

 };

}


/* =====================================================
   重新整理云层
===================================================== */

function rebuildCloudDepth(){

 /*
  * 根据独角兽高度决定哪些云
  * 在它前面。
  */

 for(const c of clouds){

  c.collidable=
   c.z<
   unicorn.z-.03;

  /*
   * 距离独角兽越远越透明
   */

  if(c.z>unicorn.z){

   c.opacity=
    Math.max(
     .16,
     .48-
     (c.z-unicorn.z)*.22
    );

  }else{

   c.opacity=
    Math.min(
     .95,
     .68+
     (unicorn.z-c.z)*.15
    );

  }

 }

}


/* =====================================================
   下一关
===================================================== */

function nextLevel(){

 level++;

 if(level>13){

  state='finish';

  showMessage(
   '🌈 13只独角兽全部获救！'
  );

  return;

 }

 projectiles=[];

 particles=[];

 rainbow=null;

 rain=[];

 generateClouds();

 newUnicorn();

 rebuildCloudDepth();

 state='idle';

 document
  .getElementById('lv')
  .textContent=level;

 document
  .getElementById('saved')
  .textContent=saved;

 showMessage(
  tips[level-1]
 );

}


/* =====================================================
   消息
===================================================== */

function showMessage(text){

 const el=
  document.getElementById('msg');

 el.textContent=text;

 clearTimeout(
  showMessage.timer
 );

 showMessage.timer=
  setTimeout(
   ()=>{
    el.textContent='';
   },
   2200
  );

}


/* =====================================================
   射击冷却
===================================================== */

function getCooldown(){

 return Math.min(
  MAX_COOLDOWN,
  BASE_COOLDOWN+
  shotCount*
  COOLDOWN_ADD
 );

}


/* =====================================================
   发射
===================================================== */

function shoot(){

 if(state!=='idle')
  return;

 const now=
  performance.now();

 const cd=
  mode==='online'
   ?serverCooldown
   :cooldown;

 if(cd>0)
  return;

 lastShot=now;

 lastActivity=now;

 shotCount++;

 cooldown=
  getCooldown();

 power=
  Math.max(
   .25,
   Math.min(
    1,
    power
   )
  );

 thunder();

 /*
  * 真正的短闪电弹
  */

 createProjectile(
  power
 );

 power=0;

}


/* =====================================================
   短闪电弹
===================================================== */

function createProjectile(power){

 const bx=70;

 const by=H-75;

 const dx=
  aim.x-bx;

 const dy=
  aim.y-by;

 const len=
  Math.hypot(
   dx,
   dy
  )||1;

 const speed=
  14+
  power*13;

 projectiles.push({

  x:bx,

  y:by,

  vx:
   dx/len*speed,

  vy:
   dy/len*speed,

  length:
   28+
   power*15,

  life:180,

  power:power,

  trail:[]

 });

}


/* =====================================================
   碰撞
===================================================== */

function pointSegmentDistance(
 px,
 py,
 ax,
 ay,
 bx,
 by
){

 const dx=bx-ax;

 const dy=by-ay;

 if(
  dx===0&&
  dy===0
 )
  return Math.hypot(
   px-ax,
   py-ay
  );

 const tt=Math.max(
  0,
  Math.min(
   1,
   (
    (px-ax)*dx+
    (py-ay)*dy
   )/
   (
    dx*dx+
    dy*dy
   )
  )
 );

 const x=
  ax+
  tt*dx;

 const y=
  ay+
  tt*dy;

 return Math.hypot(
  px-x,
  py-y
 );

}


/*
 * 计算闪电弹当前线段
 */

function projectileSegment(p){

 const len=
  Math.hypot(
   p.vx,
   p.vy
  )||1;

 const nx=
  p.vx/len;

 const ny=
  p.vy/len;

 return {

  x1:p.x,
  y1:p.y,

  x2:
   p.x-
   nx*p.length,

  y2:
   p.y-
   ny*p.length

 };

}


/*
 * 云层碰撞
 */

function checkCloudCollision(p){

 const s=
  projectileSegment(p);

 let hit=-1;

 let bestZ=-Infinity;

 for(let i=0;i<clouds.length;i++){

  const c=clouds[i];

  /*
   * 远处云不碰撞
   */

  if(!c.collidable)
   continue;

  const d=
   pointSegmentDistance(
    c.x,
    c.y,
    s.x1,
    s.y1,
    s.x2,
    s.y2
   );

  if(
   d<
   c.size*.58
  ){

   /*
    * 取离玩家最近的前景云
    */

   if(c.z>bestZ){

    bestZ=c.z;

    hit=i;

   }

  }

 }

 return hit;

}


/*
 * 独角兽碰撞
 */

function checkUnicornCollision(p){

 if(!unicorn)
  return false;

 const s=
  projectileSegment(p);

 /*
  * 如果前面还有碰撞云，
  * 已经在前面的检查中停止。
  */

 const d=
  pointSegmentDistance(
   unicorn.x,
   unicorn.y,
   s.x1,
   s.y1,
   s.x2,
   s.y2
  );

 return d<
  34*unicorn.z;

}


/* =====================================================
   更新闪电弹
===================================================== */

function updateProjectiles(){

 for(
  let i=projectiles.length-1;
  i>=0;
  i--
 ){

  const p=
   projectiles[i];

  const oldX=p.x;

  const oldY=p.y;

  p.x+=p.vx;

  p.y+=p.vy;

  /*
   * 轨迹
   */

  p.trail.push({

   x:p.x,
   y:p.y

  });

  if(
   p.trail.length>8
  )
   p.trail.shift();


  /*
   * 云层
   */

  const cloud=
   checkCloudCollision(p);

  if(cloud>=0){

   clouds[cloud].hit=15;

   createSpark(
    p.x,
    p.y
   );

   flash=7;

   state='flash';

   showMessage(
    '⚡ 闪电击中了云层！'
   );

   addScore(10);

   projectiles.splice(
    i,
    1
   );

   setTimeout(
    ()=>{
     if(state==='flash')
      state='idle';
    },
    280
   );

   continue;

  }


  /*
   * 独角兽
   */

  if(
   checkUnicornCollision(p)
  ){

   projectiles.splice(
    i,
    1
   );

   rescue();

   continue;

  }


  p.life--;

  if(
   p.life<=0||
   p.x<-100||
   p.x>W+100||
   p.y<-100||
   p.y>H+100
  ){

   projectiles.splice(
    i,
    1
   );

  }

 }

}


/* =====================================================
   救援
===================================================== */

function rescue(){

 if(state!=='idle')
  return;

 state='rain';

 saved++;

 addScore(100);

 document
  .getElementById('saved')
  .textContent=saved;

 makeRain();

 rainbow={
  z:
   unicorn.z-.05,

  progress:0

 };

 showMessage(
  '🦄 独角兽获救！'
 );

 /*
  * 雨
  */

 setTimeout(
  ()=>{
   if(state==='rain')
    state='sun';
  },
  2600
 );

 /*
  * 彩虹阶段结束后真正进入下一关
  */

 setTimeout(
  ()=>{
   if(
    state==='sun'
   ){

    nextLevel();

   }
  },
  6000
 );

}


/* =====================================================
   得分
===================================================== */

function addScore(n){

 const p=
  players.find(
   p=>p.id===playerId
  );

 if(!p)
  return;

 p.score+=n;

 updateBoard();

}


/* =====================================================
   排行榜
===================================================== */

function updateBoard(){

 const board=
  document.getElementById(
   'scoreboard'
  );

 let html=
  '<b>🏆 贡献榜</b>';

 players
  .slice()
  .sort(
   (a,b)=>
    b.score-a.score
  )
  .forEach(
   (p,i)=>{

    const medal=
     i===0?'🥇':
     i===1?'🥈':
     i===2?'🥉':'';

    html+=
     '<div class="player">'+
     '<span>'+
     medal+
     escapeHtml(p.name)+
     '</span>'+
     '<b>'+
     p.score+
     '</b>'+
     '</div>';

   }
  );

 board.innerHTML=html;

}


function escapeHtml(s){

 return String(s)
  .replaceAll(
   '&',
   '&amp;'
  )
  .replaceAll(
   '<',
   '&lt;'
  )
  .replaceAll(
   '>',
   '&gt;'
  );

}


/* =====================================================
   雨
===================================================== */

function makeRain(){

 rain=[];

 for(let i=0;i<180;i++){

  rain.push({

   x:rnd(0,W),

   y:rnd(-H,0),

   v:rnd(7,15),

   l:rnd(8,20)

  });

 }

}


function updateRain(){

 for(const r of rain){

  r.y+=r.v;

  if(
   r.y>H
  )
   r.y=-20;

 }

}


function drawRain(){

 X.save();

 X.strokeStyle=
  '#9deaff99';

 X.lineWidth=1.5;

 for(const r of rain){

  X.beginPath();

  X.moveTo(
   r.x,
   r.y
  );

  X.lineTo(
   r.x-3,
   r.y+r.l
  );

  X.stroke();

 }

 X.restore();

}


/* =====================================================
   彩虹
===================================================== */

function drawRainbow(){

 if(!rainbow)
  return;

 /*
  * 彩虹的 z 在独角兽之后，
  * 但在真正无碰撞的远云之前。
  *
  * 实际绘制时我们把它放在
  * 独角兽绘制之后、远云之前。
  */

 const cx=W*.62;

 const cy=H*.73;

 const R=
  Math.min(W,H)*.5;

 const colors=[
  '#ff5b6e',
  '#ffb44e',
  '#ffe65c',
  '#68dc83',
  '#63b8ff',
  '#9b7cff'
 ];

 X.save();

 X.globalAlpha=.65;

 for(let i=0;i<6;i++){

  X.strokeStyle=
   colors[i];

  X.lineWidth=9;

  X.beginPath();

  X.arc(
   cx,
   cy,
   R-i*11,
   Math.PI,
   Math.PI*2
  );

  X.stroke();

 }

 X.restore();

}


/* =====================================================
   云绘制
===================================================== */

function drawCloud(c){

 const s=c.size;

 /*
  * 透明度由云和独角兽高度决定。
  */

 let alpha=c.opacity||.6;

 X.save();

 X.globalAlpha=alpha;

 /*
  * 前景云更加有实体感
  */

 if(c.collidable){

  X.fillStyle=
   '#244c6b66';

 }else{

  X.fillStyle=
   '#244c6b33';

 }


 /*
  * 阴影
  */

 X.beginPath();

 X.ellipse(
  c.x,
  c.y+18,
  s*1.15,
  s*.34,
  0,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 云主体
  */

 const g=
  X.createLinearGradient(
   c.x,
   c.y-s*.5,
   c.x,
   c.y+s*.5
  );

 if(c.collidable){

  g.addColorStop(
   0,
   '#ffffff'
  );

  g.addColorStop(
   .55,
   '#e7f5fc'
  );

  g.addColorStop(
   1,
   '#a8c9d9'
  );

 }else{

  g.addColorStop(
   0,
   '#ffffffbb'
  );

  g.addColorStop(
   1,
   '#d8edf555'
  );

 }

 X.fillStyle=g;

 X.beginPath();

 X.ellipse(
  c.x,
  c.y,
  s*1.05,
  s*.40,
  0,
  0,
  Math.PI*2
 );

 X.ellipse(
  c.x-s*.55,
  c.y+s*.02,
  s*.65,
  s*.34,
  0,
  0,
  Math.PI*2
 );

 X.ellipse(
  c.x+s*.5,
  c.y-s*.04,
  s*.7,
  s*.42,
  0,
  0,
  Math.PI*2
 );

 X.ellipse(
  c.x+s*.08,
  c.y-s*.25,
  s*.55,
  s*.52,
  0,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 被雷击
  */

 if(c.hit>0){

  X.globalAlpha=
   c.hit/15;

  X.strokeStyle='#7fffff';

  X.shadowBlur=20;

  X.shadowColor='#5ff';

  X.lineWidth=5;

  X.beginPath();

  X.moveTo(
   c.x,
   c.y-s*.5
  );

  X.lineTo(
   c.x-s*.16,
   c.y
  );

  X.lineTo(
   c.x+s*.08,
   c.y
  );

  X.lineTo(
   c.x-s*.08,
   c.y+s*.55
  );

  X.stroke();

  c.hit--;

 }

 X.restore();

}


/* =====================================================
   独角兽
===================================================== */

function drawUnicorn(){

 if(!unicorn)
  return;

 const q=unicorn.z;

 X.save();

 X.translate(
  unicorn.x,
  unicorn.y
 );

 X.scale(q,q);

 /*
  * 阴影
  */

 X.fillStyle='#0002';

 X.beginPath();

 X.ellipse(
  0,
  30,
  38,
  9,
  0,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 身体
  */

 X.fillStyle='#fff';

 X.beginPath();

 X.ellipse(
  0,
  0,
  32,
  18,
  0,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 头
  */

 X.beginPath();

 X.ellipse(
  28,
  -15,
  15,
  14,
  0,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 腿
  */

 X.strokeStyle='#fff';

 X.lineWidth=6;

 for(
  let i=-1;
  i<2;
  i++
 ){

  X.beginPath();

  X.moveTo(
   i*16,
   10
  );

  X.lineTo(
   i*17,
   29
  );

  X.stroke();

 }


 /*
  * 鬃毛
  */

 X.fillStyle='#ff8acb';

 X.beginPath();

 X.arc(
  18,
  -25,
  13,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 眼睛
  */

 X.fillStyle='#222';

 X.beginPath();

 X.arc(
  33,
  -18,
  2,
  0,
  Math.PI*2
 );

 X.fill();


 /*
  * 独角
  */

 X.fillStyle='#ffe98a';

 X.beginPath();

 X.moveTo(
  38,
  -27
 );

 X.lineTo(
  55,
  -48
 );

 X.lineTo(
  43,
  -23
 );

 X.fill();


 /*
  * 尾巴
  */

 X.strokeStyle='#9b8cff';

 X.lineWidth=8;

 X.beginPath();

 X.moveTo(
  -27,
  0
 );

 X.quadraticCurveTo(
  -55,
  -12,
  -48,
  -29
 );

 X.stroke();

 X.restore();

}


/* =====================================================
   闪电弹
===================================================== */

function drawProjectile(p){

 const s=
  projectileSegment(p);

 /*
  * 运动轨迹
  */

 X.save();

 X.globalAlpha=.3;

 X.strokeStyle='#5ff';

 X.lineWidth=5;

 X.beginPath();

 p.trail.forEach(
  (q,i)=>{

   if(i===0)
    X.moveTo(
     q.x,
     q.y
    );
   else
    X.lineTo(
     q.x,
     q.y
    );

  }
 );

 X.stroke();

 X.restore();


 /*
  * 当前短闪电
  */

 const dx=
  s.x1-s.x2;

 const dy=
  s.y1-s.y2;

 const len=
  Math.hypot(
   dx,
   dy
  )||1;

 const nx=
  -dy/len;

 const ny=
  dx/len;

 const wiggle=7;

 X.save();

 X.shadowBlur=25;

 X.shadowColor='#5ff';

 X.strokeStyle='#dfffff';

 X.lineWidth=7;

 X.beginPath();

 X.moveTo(
  s.x1,
  s.y1
 );

 X.lineTo(
  s.x1+
  dx*.35+
  nx*wiggle,
  s.y1+
  dy*.35+
  ny*wiggle
 );

 X.lineTo(
  s.x1+
  dx*.65-
  nx*wiggle,
  s.y1+
  dy*.65-
  ny*wiggle
 );

 X.lineTo(
  s.x2,
  s.y2
 );

 X.stroke();

 X.shadowBlur=0;

 X.strokeStyle='#fff';

 X.lineWidth=2;

 X.beginPath();

 X.moveTo(
  s.x1,
  s.y1
 );

 X.lineTo(
  s.x1+
  dx*.35+
  nx*wiggle,
  s.y1+
  dy*.35+
  ny*wiggle
 );

 X.lineTo(
  s.x1+
  dx*.65-
  nx*wiggle,
  s.y1+
  dy*.65-
  ny*wiggle
 );

 X.lineTo(
  s.x2,
  s.y2
 );

 X.stroke();

 X.restore();

}


/* =====================================================
   闪电弓
===================================================== */

function drawBow(){

 /*
  * 即使进入 rain/sun 阶段也显示。
  * 不再出现“弓消失”的问题。
  */

 const bx=70;

 const by=H-75;

 const dx=
  aim.x-bx;

 const dy=
  aim.y-by;

 const angle=
  Math.atan2(
   dy,
   dx
  );

 X.save();

 X.translate(
  bx,
  by
 );

 X.rotate(angle);


 /*
  * 弓身
  */

 X.shadowBlur=25;

 X.shadowColor='#4df';

 X.strokeStyle='#57ddff';

 X.lineWidth=7;

 X.beginPath();

 X.moveTo(-5,-50);

 X.lineTo(10,-25);

 X.lineTo(-4,-5);

 X.lineTo(12,20);

 X.lineTo(-8,48);

 X.stroke();


 /*
  * 弓弦
  */

 X.shadowBlur=0;

 X.strokeStyle='#e8ffff';

 X.lineWidth=2;

 X.beginPath();

 X.moveTo(-5,-50);

 X.lineTo(
  power*35,
  0
 );

 X.lineTo(-8,48);

 X.stroke();


 /*
  * 核心
  */

 X.fillStyle='#fff';

 X.shadowBlur=30;

 X.shadowColor='#7ff';

 X.beginPath();

 X.arc(
  0,
  0,
  8+
  power*5,
  0,
  Math.PI*2
 );

 X.fill();

 X.restore();

}


/* =====================================================
   火花
===================================================== */

function createSpark(x,y){

 for(let i=0;i<18;i++){

  particles.push({

   x:x,

   y:y,

   vx:rnd(-4,4),

   vy:rnd(-4,4),

   life:rnd(15,30)

  });

 }

}


function updateParticles(){

 for(
  let i=particles.length-1;
  i>=0;
  i--
 ){

  const p=
   particles[i];

  p.x+=p.vx;

  p.y+=p.vy;

  p.vy+=.05;

  p.life--;

  if(p.life<=0)
   particles.splice(i,1);

 }

}


function drawParticles(){

 for(const p of particles){

  X.globalAlpha=
   Math.max(
    0,
    p.life/30
   );

  X.fillStyle='#9fffff';

  X.fillRect(
   p.x,
   p.y,
   3,
   3
  );

 }

 X.globalAlpha=1;

}


/* =====================================================
   天空
===================================================== */

function drawSky(){

 const g=
  X.createLinearGradient(
   0,
   0,
   0,
   H
  );

 g.addColorStop(
  0,
  '#071a38'
 );

 g.addColorStop(
  .5,
  '#427fa8'
 );

 g.addColorStop(
  1,
  '#b9e4ee'
 );

 X.fillStyle=g;

 X.fillRect(
  0,
  0,
  W,
  H
 );

}


function drawStars(){

 for(let i=0;i<45;i++){

  X.fillStyle='#ffffff88';

  X.fillRect(
   (i*97)%W,
   ((i*53)%H)*.45,
   1.5,
   1.5
  );

 }

}


/* =====================================================
   深度绘制
===================================================== */

function drawWorld(){

 /*
  * 1. 最远的无碰撞云
  */

 clouds
  .filter(
   c=>c.z>unicorn.z
  )
  .sort(
   (a,b)=>b.z-a.z
  )
  .forEach(
   drawCloud
  );


 /*
  * 2. 彩虹
  *
  * 位于独角兽后方。
  */

 if(
  state==='sun'&&
  rainbow
 )
  drawRainbow();


 /*
  * 3. 独角兽
  */

 if(unicorn)
  drawUnicorn();


 /*
  * 4. 独角兽前方的碰撞云
  */

 clouds
  .filter(
   c=>c.z<=unicorn.z
  )
  .sort(
   (a,b)=>b.z-a.z
  )
  .forEach(
   drawCloud
  );


 /*
  * 5. 闪电弹
  */

 for(
  const p of projectiles
 )
  drawProjectile(p);


 /*
  * 6. 雨
  */

 if(
  state==='rain'
 )
  drawRain();


 /*
  * 7. 火花
  */

 drawParticles();

}


/* =====================================================
   闪屏
===================================================== */

function drawFlash(){

 if(flash<=0)
  return;

 X.save();

 X.fillStyle='#fff';

 X.globalAlpha=
  flash/12;

 X.fillRect(
  0,
  0,
  W,
  H
 );

 X.restore();

 flash--;

}


/* =====================================================
   冷却 UI
===================================================== */

function drawCooldown(){

 const el=
  document.getElementById(
   'cooldown'
  );

 const cd=
  mode==='online'
   ?serverCooldown
   :cooldown;

 if(cd>0){

  el.classList.add(
   'cooling'
  );

  el.textContent=
   '⚡ 冷却 '+
   (cd/1000)
    .toFixed(1)+
   ' 秒';

 }else{

  el.classList.remove(
   'cooling'
  );

  el.textContent=
   '⚡ READY';

 }

}


/* =====================================================
   更新
===================================================== */

function update(){

 t++;

 /*
  * 冷却
  */

 if(mode==='single'){

  cooldown=
   Math.max(
    0,
    cooldown-16
   );

  /*
   * 5秒不射击，疲劳缓慢恢复
   */

  if(
   shotCount>0&&
   performance.now()-
   lastActivity>
   5000
  ){

   shotCount=
    Math.max(
     0,
     shotCount-.015
    );

  }

 }else{

  serverCooldown=
   Math.max(
    0,
    serverCooldown-16
   );

 }


 /*
  * 云移动
  */

 for(const c of clouds){

  c.x+=
   Math.sin(
    t/700+c.phase
   )*
   c.speed;

  if(
   c.x<-150
  )
   c.x=W+150;

  if(
   c.x>W+150
  )
   c.x=-150;

 }


 /*
  * 独角兽
  */

 if(
  unicorn&&
  state==='idle'
 ){

  unicorn.x+=
   unicorn.dx;

  unicorn.y+=
   unicorn.dy;

  if(
   unicorn.x<60||
   unicorn.x>W-60
  )
   unicorn.dx*=-1;

  if(
   unicorn.y<H*.13||
   unicorn.y>H*.57
  )
   unicorn.dy*=-1;

 }


 /*
  * 重新计算云层深度
  */

 if(unicorn)
  rebuildCloudDepth();


 /*
  * 闪电
  */

 if(
  state==='idle'
 )
  updateProjectiles();


 /*
  * 雨
  */

 if(state==='rain')
  updateRain();


 updateParticles();

}


/* =====================================================
   输入
===================================================== */

C.addEventListener(
 'pointerdown',
 e=>{

  pointerDown=true;

  aim.x=e.clientX;

  aim.y=e.clientY;

  if(
   state==='idle'
  ){

   power=.25;

  }

 }
);


C.addEventListener(
 'pointermove',
 e=>{

  aim.x=e.clientX;

  aim.y=e.clientY;

  if(
   pointerDown&&
   state==='idle'
  ){

   power=
    Math.min(
     1,
     power+.012
    );

  }

 }
);


C.addEventListener(
 'pointerup',
 e=>{

  if(!pointerDown)
   return;

  pointerDown=false;

  if(
   state==='idle'
  )
   shoot();

 });


C.addEventListener(
 'pointercancel',
 ()=>{
  pointerDown=false;
  power=0;
 }
);


document.addEventListener(
 'contextmenu',
 e=>e.preventDefault()
);


/* =====================================================
   网络
===================================================== */

function networkMessage(m){

 if(m.type==='welcome'){

  playerId=m.id;

  roomCode=m.room;

  document
   .getElementById('menu')
   .classList.add('hidden');

  document
   .getElementById('roomInfo')
   .classList.remove('hidden');

  document
   .getElementById('roomInfo')
   .textContent=
   '房间：'+roomCode;

  document
   .getElementById('scoreboard')
   .classList.remove('hidden');

  resetGame();

  return;

 }


 if(m.type==='players'){

  players=m.players;

  updateBoard();

  return;

 }


 if(m.type==='shoot'){

  /*
   * 其他玩家的闪电
   */

  if(
   m.player!==playerId
  ){

   const oldX=aim.x;
   const oldY=aim.y;

   aim.x=m.x;
   aim.y=m.y;

   createProjectile(
    Math.max(
     .25,
     Math.min(
      1,
      Number(m.power)||.25
     )
    )
   );

   aim.x=oldX;
   aim.y=oldY;

  }

  return;

 }


 if(m.type==='cloudHit'){

  if(
   clouds[m.index]
  ){

   clouds[m.index].hit=15;

   flash=7;

   thunder();

  }

  return;

 }


 if(m.type==='rescued'){

  saved=m.saved;

  state='rain';

  makeRain();

  rainbow={
   z:
    unicorn
     ?unicorn.z-.05
     :1
  };

  showMessage(
   '🦄 '+
   m.player+
   ' 解救了独角兽！'
  );

  setTimeout(
   ()=>{
    state='sun';
   },
   2600
  );

  setTimeout(
   ()=>{
    if(state==='sun')
     nextLevel();
   },
   6000
  );

  return;

 }


 if(m.type==='cooldown'){

  serverCooldown=
   Math.max(
    0,
    Number(m.remaining)||0
   );

 }

}


/* =====================================================
   主循环
===================================================== */

function draw(){

 drawSky();

 drawStars();

 drawWorld();

 drawBow();

 drawFlash();

 drawCooldown();

}


function loop(){

 update();

 draw();

 requestAnimationFrame(
  loop
 );

}


loop();

</script>

</body>
</html>
```

## ***第三步* **  测试demo并补充想法和修bug

### 1·  经过完整测试发现玩法与场景太过于重复枯燥，因此

#### a· 一次性显示所有的独角兽

#### b· 加入黑云来配合闪电

#### c· 弓箭交互大改：能量条限制，蓄力限制，可选抛物线系统，

#### d· 引入debug方便测试新功能

#### e· 修复了一些bug

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Thunder Unicorn 13</title>

<style>
*{
    box-sizing:border-box;
    user-select:none;
    -webkit-user-select:none;
}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#071525;
    touch-action:none;
    font-family:Arial,"Microsoft YaHei",sans-serif;
}

canvas{
    position:fixed;
    inset:0;
    width:100%;
    height:100%;
    touch-action:none;
}

#ui{
    position:fixed;
    inset:0;
    z-index:20;
    pointer-events:none;
    color:#fff;
    text-shadow:0 2px 5px #000;
}

#top{
    position:absolute;
    top:12px;
    left:50%;
    transform:translateX(-50%);
    width:min(600px,82vw);
    text-align:center;
}

#level{
    font-size:14px;
    margin-bottom:6px;
}

#energyBox{
    height:12px;
    border:1px solid #ffffff55;
    border-radius:10px;
    overflow:hidden;
    background:#0008;
}

#energy{
    width:100%;
    height:100%;
    background:linear-gradient(90deg,#36d9ff,#e9ffff);
}

#energyText,
#cooldown{
    font-size:11px;
    margin-top:4px;
}

#score{
    position:absolute;
    right:15px;
    top:15px;
    font-size:13px;
}

#players{
    position:absolute;
    left:15px;
    top:15px;
    font-size:12px;
}

#hint{
    position:absolute;
    bottom:14px;
    left:50%;
    transform:translateX(-50%);
    width:90%;
    text-align:center;
    font-size:12px;
    opacity:.75;
}

.button{
    pointer-events:auto;
    padding:11px 28px;
    margin:5px;
    border-radius:8px;
    border:1px solid #67eaff;
    background:#062638ee;
    color:white;
    cursor:pointer;
}

#startScreen,
#finish{
    position:fixed;
    inset:0;
    z-index:100;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    text-align:center;
    color:white;
}

#startScreen{
    background:radial-gradient(circle,#174d68,#061321 72%);
}

#startScreen h1{
    margin:0 0 10px;
    font-size:clamp(32px,8vw,56px);
    text-shadow:0 0 30px #67eaff;
}

#startScreen p{
    opacity:.75;
    font-size:13px;
    margin-bottom:25px;
}

#finish{
    display:none;
    background:#020d16f5;
}

#finish.show{
    display:flex;
}

#finish h1{
    font-size:clamp(28px,7vw,48px);
    text-shadow:0 0 30px #7fefff;
}

#debugButton{
    position:fixed;
    right:12px;
    bottom:12px;
    z-index:50;
    pointer-events:auto;
    padding:7px 12px;
    border:1px solid #ffffff55;
    border-radius:6px;
    background:#0009;
    color:white;
    font-size:11px;
}

#debugPanel{
    display:none;
    position:fixed;
    right:12px;
    bottom:50px;
    width:250px;
    padding:12px;
    z-index:50;
    color:white;
    font-size:12px;
    background:#03101aec;
    border:1px solid #ffffff44;
    border-radius:8px;
}

#debugPanel.show{
    display:block;
}

.debugRow{
    display:flex;
    justify-content:space-between;
    align-items:center;
    margin:7px 0;
}

.debugRow button{
    min-width:42px;
    border:0;
    border-radius:4px;
    padding:3px 8px;
}

.on{
    background:#43e5ff;
    color:#001018;
}

.off{
    background:#444;
    color:#aaa;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="ui">

    <div id="top">
        <div id="level">等待开始</div>

        <div id="energyBox">
            <div id="energy"></div>
        </div>

        <div id="energyText">
            能量系统 ON
        </div>

        <div id="cooldown">
            ⚡ READY
        </div>
    </div>

    <div id="players">
        玩家1 · 0/13
    </div>

    <div id="score">
        得分：0
    </div>

    <div id="hint">
        移动鼠标/手指瞄准 · 长按蓄力 · 松开发射
    </div>

</div>

<div id="startScreen">

    <h1>⚡ Thunder Unicorn</h1>

    <p>
        穿过云层，按照顺序解救13只独角兽
    </p>

    <button id="startButton" class="button">
        开始游戏
    </button>

</div>

<button id="debugButton">
    DEBUG
</button>

<div id="debugPanel">

    <b>DEBUG</b>

    <div class="debugRow">
        <span>能量系统</span>
        <button id="energyDebug" class="on">ON</button>
    </div>

    <div class="debugRow">
        <span>射击固定耗能</span>
        <button id="shotEnergyDebug" class="on">ON</button>
    </div>

    <div class="debugRow">
        <span>黑云机制</span>
        <button id="blackCloudDebug" class="on">ON</button>
    </div>

    <div class="debugRow">
        <span>目标高亮</span>
        <button id="targetDebug" class="on">ON</button>
    </div>

    <div class="debugRow">
        <span>UI等级显示</span>
        <button id="uiDebug" class="off">OFF</button>
    </div>

    <div class="debugRow">
        <span>碰撞框</span>
        <button id="collisionDebug" class="off">OFF</button>
    </div>

    <div class="debugRow">
        <span>抛物线功能</span>
        <button id="trajectoryDebug" class="off">OFF</button>
    </div>

    <div class="debugRow">
        <span>抛物线显示</span>
        <button id="trajectoryDisplayDebug" class="off">OFF</button>
    </div>

    <div class="debugRow">
        <span>蓄力抖动</span>
        <button id="shakeDebug" class="on">ON</button>
    </div>

    <div class="debugRow">
        <span>高亮提示</span>
        <button id="hintDebug" class="on">ON</button>
    </div>

</div>

<div id="finish">

    <h1>
        🌈 13只独角兽全部获救
    </h1>

    <p id="finalScore">
        最终得分：0
    </p>

    <button id="restart" class="button">
        再来一次
    </button>

</div>

<script>
"use strict";

/* =========================================================
   基础
========================================================= */

const canvas=document.getElementById("game");
const ctx=canvas.getContext("2d");

let W=innerWidth;
let H=innerHeight;
let DPR=Math.min(2,devicePixelRatio||1);

const TOTAL=13;

const COOLDOWN=1100;

const MAX_ENERGY=100;

/* 蓄力持续耗能 */
const ENERGY_DRAIN=14;

/* 未蓄力时恢复 */
const ENERGY_REGEN=9;

/* 每次射击额外固定消耗 */
const SHOT_ENERGY_COST=18;

/* 最大蓄力时间 */
const MAX_CHARGE=3000;

/* 箭速度 */
const ARROW_SPEED=850;

/* 箭最大存在时间 */
const ARROW_LIFE=5000;


/* =========================================================
   Debug
========================================================= */

const debug={

    energy:true,

    shotEnergy:true,

    blackCloud:true,

    target:true,

    ui:false,

    collision:false,

    trajectory:false,

    trajectoryDisplay:false,

    shake:true,

    hint:true

};


/* =========================================================
   游戏状态
========================================================= */

let started=false;
let finished=false;

let score=0;
let rescued=0;


/* =========================================================
   瞄准
========================================================= */

const aim={
    x:W*.72,
    y:H*.45
};


/* =========================================================
   武器
========================================================= */

const weapon={

    energy:MAX_ENERGY,

    cooldown:0,

    charging:false,

    charge:0,

    shake:0

};


/* =========================================================
   对象
========================================================= */

const clouds=[];
const unicorns=[];
const backgroundClouds=[];
const arrows=[];
const particles=[];
const impacts=[];
const rainbows=[];
const rain=[];


/* =========================================================
   天气
========================================================= */

const weather={

    raining:false,

    timer:0,

    duration:1800,

    rainbow:null

};


/* =========================================================
   辅助提示
========================================================= */

let assistTimer=0;
let assistFlash=0;


/* =========================================================
   工具
========================================================= */

function clamp(v,a,b){
    return Math.max(a,Math.min(b,v));
}

function rnd(a,b){
    return a+Math.random()*(b-a);
}

function dist(ax,ay,bx,by){
    return Math.hypot(ax-bx,ay-by);
}


/* =========================================================
   Resize
========================================================= */

function resize(){

    W=innerWidth;
    H=innerHeight;

    DPR=Math.min(2,devicePixelRatio||1);

    canvas.width=W*DPR;
    canvas.height=H*DPR;

    canvas.style.width=W+"px";
    canvas.style.height=H+"px";

    ctx.setTransform(
        DPR,0,0,DPR,0,0
    );

    for(const c of clouds){
        clampCloud(c);
    }
}

addEventListener("resize",resize);


/* =========================================================
   云生成
========================================================= */

function createClouds(){

    clouds.length=0;

    /*
     * 每个UI等级随机1~3朵。
     */

    for(let level=1;level<=TOTAL;level++){

        const count=
            1+
            Math.floor(
                Math.random()*3
            );

        for(let n=0;n<count;n++){

            const radius=rnd(48,92);

            let x=0;
            let y=0;
            let valid=false;

            for(let attempt=0;attempt<40;attempt++){

                x=rnd(
                    radius+10,
                    Math.max(
                        radius+11,
                        W-radius-10
                    )
                );

                y=rnd(
                    H*.12,
                    H*.67
                );

                valid=true;

                for(const old of clouds){

                    if(
                        dist(
                            x,
                            y,
                            old.x,
                            old.y
                        )<
                        radius+
                        old.radius+
                        20
                    ){

                        valid=false;
                        break;

                    }
                }

                if(valid)break;
            }

            clouds.push({

                id:clouds.length,

                level:level,

                x:x,

                y:y,

                radius:radius,

                /*
                 * HP代表UI等级，
                 * 也决定碰撞资格。
                 */
                hp:level,

                maxHP:level,

                ui:level,

                /*
                 * 黑云状态
                 */
                black:false,

                lightningTimer:
                    rnd(0,1000),

                lightningFlash:0,

                opacity:1,

                flash:0,

                dx:rnd(-.5,.5),

                dy:rnd(-.18,.18)

            });
        }
    }

    /*
     * 随机打乱。
     */
    for(
        let i=clouds.length-1;
        i>0;
        i--
    ){

        const j=
            Math.floor(
                Math.random()*(i+1)
            );

        [
            clouds[i],
            clouds[j]
        ]=[
            clouds[j],
            clouds[i]
        ];
    }
}


/* =========================================================
   云边界
========================================================= */

function clampCloud(c){

    const marginX=c.radius+5;

    const minY=c.radius+5;

    const maxY=Math.max(
        minY,
        H*.70-c.radius
    );

    c.x=clamp(
        c.x,
        marginX,
        Math.max(
            marginX,
            W-marginX
        )
    );

    c.y=clamp(
        c.y,
        minY,
        maxY
    );
}


/* =========================================================
   独角兽
========================================================= */

function createUnicorns(){

    unicorns.length=0;

    for(let i=0;i<TOTAL;i++){

        const d=i/(TOTAL-1);

        unicorns.push({

            id:i,

            ui:i+1,

            x:rnd(
                70,
                Math.max(71,W-70)
            ),

            y:rnd(
                H*.22,
                H*.58
            ),

            /*
             * 越往后越小。
             */
            scale:
                1-d*.68,

            /*
             * 越往后越淡。
             */
            opacity:
                1-d*.58,

            radius:
                36-d*23,

            dx:
                Math.random()<.5?-1:1,

            dy:
                Math.random()<.5?-1:1,

            speed:
                .35+d*.8,

            rescued:false

        });
    }
}


/* =========================================================
   背景云
========================================================= */

function createBackgroundClouds(){

    backgroundClouds.length=0;

    const count=Math.max(
        30,
        Math.floor(W/25)
    );

    for(let i=0;i<count;i++){

        backgroundClouds.push({

            x:rnd(-100,W+100),

            y:rnd(20,H*.7),

            size:rnd(25,100),

            speed:rnd(.01,.04),

            opacity:rnd(.05,.18)

        });
    }
}


/* =========================================================
   当前独角兽
========================================================= */

function getCurrentTarget(){

    if(rescued>=TOTAL){
        return null;
    }

    return unicorns[rescued];
}


/* =========================================================
   当前箭UI
========================================================= */

function getArrowUI(){

    const target=getCurrentTarget();

    return target
        ?target.ui
        :Infinity;
}


/* =========================================================
   云碰撞判定
========================================================= */

function canCollideCloud(c){

    /*
     * 黑云是特殊实体，
     * 无论UI都可以阻挡。
     */
    if(
        debug.blackCloud &&
        c.black
    ){
        return true;
    }

    /*
     * 普通云：
     * HP >= 当前箭UI才阻挡。
     */
    return c.hp>=getArrowUI();
}


/* =========================================================
   瞄准点
========================================================= */

function getAimPoint(){

    if(
        !weapon.charging||
        !debug.shake
    ){

        return {
            x:aim.x,
            y:aim.y
        };
    }

    return {

        x:
            aim.x+
            Math.sin(
                performance.now()/38
            )*
            weapon.shake,

        y:
            aim.y+
            Math.cos(
                performance.now()/44
            )*
            weapon.shake

    };
}


/* =========================================================
   创建箭
========================================================= */

function createArrow(){

    const startX=W*.09;
    const startY=H*.82;

    const p=getAimPoint();

    const dx=p.x-startX;
    const dy=p.y-startY;

    const angle=Math.atan2(dy,dx);

    const ratio=clamp(
        weapon.charge/MAX_CHARGE,
        0,
        1
    );

    /*
     * 抛物线：
     *
     * 蓄力越久
     * 曲率越小
     * 轨迹越直。
     */
    const curvature=
        debug.trajectory
            ?
            (1-ratio)*.0015
            :
            0;

    arrows.push({

        x:startX,

        y:startY,

        vx:
            Math.cos(angle)*
            ARROW_SPEED,

        vy:
            Math.sin(angle)*
            ARROW_SPEED,

        curvature:curvature,

        ui:getArrowUI(),

        life:0,

        trail:[]

    });
}


/* =========================================================
   射击
========================================================= */

function fire(){

    if(
        !started||
        finished
    ){
        return false;
    }

    if(
        weapon.cooldown>0
    ){
        return false;
    }

    /*
     * 固定射击耗能。
     *
     * 只有能量系统和固定耗能
     * 两个开关都开启时执行。
     */
    if(
        debug.energy&&
        debug.shotEnergy
    ){

        /*
         * 能量不足不能发射。
         */
        if(
            weapon.energy<
            SHOT_ENERGY_COST
        ){

            return false;
        }

        weapon.energy-=SHOT_ENERGY_COST;

        weapon.energy=clamp(
            weapon.energy,
            0,
            MAX_ENERGY
        );
    }

    createArrow();

    weapon.cooldown=COOLDOWN;

    playThunder();

    return true;
}


/* =========================================================
   开始蓄力
========================================================= */

function beginCharge(){

    if(
        !started||
        finished
    ){
        return;
    }

    if(
        weapon.cooldown>0
    ){
        return;
    }

    /*
     * 如果启用固定耗能，
     * 能量低于最低发射成本时不允许开始蓄力。
     */
    if(
        debug.energy&&
        debug.shotEnergy&&
        weapon.energy<
        SHOT_ENERGY_COST
    ){
        return;
    }

    weapon.charging=true;
    weapon.charge=0;
}


/* =========================================================
   结束蓄力
========================================================= */

function endCharge(){

    if(
        !weapon.charging
    ){
        return;
    }

    weapon.charging=false;

    /*
     * 所有能量/冷却检查
     * 统一由fire执行。
     */
    fire();

    weapon.charge=0;
    weapon.shake=0;
}


/* =========================================================
   武器更新
========================================================= */

function updateWeapon(dt){

    weapon.cooldown=
        Math.max(
            0,
            weapon.cooldown-
            dt*1000
        );

    /*
     * 非蓄力时恢复能量。
     */
    if(
        !weapon.charging
    ){

        if(debug.energy){

            weapon.energy+=
                ENERGY_REGEN*dt;

            weapon.energy=clamp(
                weapon.energy,
                0,
                MAX_ENERGY
            );
        }

        weapon.shake=0;

        return;
    }

    /*
     * 蓄力时暂停恢复，
     * 并持续消耗。
     */
    if(debug.energy){

        weapon.energy-=
            ENERGY_DRAIN*dt;

        weapon.energy=clamp(
            weapon.energy,
            0,
            MAX_ENERGY
        );
    }

    weapon.charge+=
        dt*1000;

    /*
     * 长时间蓄力抖动。
     */
    if(debug.shake){

        const ratio=clamp(
            (
                weapon.charge-1000
            )/2000,
            0,
            1
        );

        weapon.shake=ratio*14;
    }

    /*
     * 能量耗尽：
     *
     * 只有能量系统开启时
     * 才触发自动发射。
     */
    if(
        debug.energy&&
        weapon.energy<=0
    ){

        weapon.energy=0;

        weapon.charging=false;

        fire();

        weapon.charge=0;

        weapon.shake=0;

        return;
    }

    /*
     * 即使关闭能量系统，
     * 最大蓄力时间仍然限制长按。
     */
    if(
        weapon.charge>=MAX_CHARGE
    ){

        weapon.charging=false;

        fire();

        weapon.charge=0;

        weapon.shake=0;
    }
}


/* =========================================================
   线段-圆碰撞
========================================================= */

function segmentCircle(
    x1,y1,
    x2,y2,
    cx,cy,
    radius
){

    const dx=x2-x1;
    const dy=y2-y1;

    const len2=
        dx*dx+
        dy*dy;

    if(len2===0){

        return dist(
            x1,y1,
            cx,cy
        )<=radius;
    }

    let t=
        (
            (cx-x1)*dx+
            (cy-y1)*dy
        )/
        len2;

    t=clamp(t,0,1);

    const px=x1+dx*t;
    const py=y1+dy*t;

    return dist(
        px,py,
        cx,cy
    )<=radius;
}


/* =========================================================
   箭更新
========================================================= */

function updateArrows(dt){

    for(
        let i=arrows.length-1;
        i>=0;
        i--
    ){

        const a=arrows[i];

        const oldX=a.x;
        const oldY=a.y;

        a.life+=dt*1000;

        /*
         * 真实抛物线。
         */
        a.vy+=
            a.curvature*
            100000*
            dt;

        a.x+=a.vx*dt;
        a.y+=a.vy*dt;

        /*
         * 短闪电轨迹。
         */
        a.trail.push({
            x:a.x,
            y:a.y
        });

        if(
            a.trail.length>12
        ){
            a.trail.shift();
        }

        if(
            checkCollision(
                a,
                oldX,
                oldY
            )
        ){

            arrows.splice(i,1);
            continue;
        }

        if(
            a.life>ARROW_LIFE||
            a.x<-100||
            a.x>W+100||
            a.y<-100||
            a.y>H+100
        ){

            arrows.splice(i,1);
        }
    }
}


/* =========================================================
   箭碰撞
========================================================= */

function checkCollision(
    arrow,
    oldX,
    oldY
){

    const target=getCurrentTarget();

    if(!target){
        return false;
    }

    let nearest=null;
    let nearestDistance=Infinity;

    /*
     * 找出箭路径上的最近有效云。
     */
    for(const c of clouds){

        if(c.hp<=0&&!c.black){
            continue;
        }

        if(
            !canCollideCloud(c)
        ){
            continue;
        }

        if(
            segmentCircle(
                oldX,
                oldY,
                arrow.x,
                arrow.y,
                c.x,
                c.y,
                c.radius
            )
        ){

            const d=dist(
                oldX,
                oldY,
                c.x,
                c.y
            );

            if(
                d<
                nearestDistance
            ){

                nearestDistance=d;
                nearest=c;
            }
        }
    }

    /*
     * 云优先于独角兽。
     */
    if(nearest){

        hitCloud(nearest);

        return true;
    }

    /*
     * 只有当前独角兽有效。
     */
    if(
        segmentCircle(
            oldX,
            oldY,
            arrow.x,
            arrow.y,
            target.x,
            target.y,
            target.radius
        )
    ){

        rescue(target);

        return true;
    }

    return false;
}


/* =========================================================
   云：普通打淡
========================================================= */

function fadeCloud(c){

    if(c.black){
        return;
    }

    c.hp=Math.max(
        0,
        c.hp-1
    );

    c.ui=Math.max(
        0,
        c.hp
    );

    c.opacity=clamp(
        c.hp/c.maxHP,
        .08,
        1
    );

    c.flash=1;
}


/* =========================================================
   云：凝实成黑云
========================================================= */

function condenseCloud(c){

    c.hp=Math.min(
        c.maxHP+1,
        c.hp+1
    );

    c.ui=c.hp;

    c.opacity=1;

    c.black=true;

    c.lightningTimer=
        performance.now()+
        rnd(300,1000);

    c.lightningFlash=1;

    lightningImpact(
        c.x,
        c.y
    );
}


/* =========================================================
   云被击中
========================================================= */

function hitCloud(c){

    /*
     * 黑云机制关闭：
     * 完全恢复旧的普通云逻辑。
     */
    if(
        !debug.blackCloud
    ){

        fadeCloud(c);

        score+=1;

        lightningImpact(
            c.x,
            c.y
        );

        return;
    }

    /*
     * 黑云：
     *
     * 无法继续打淡，
     * 但仍然算命中。
     */
    if(c.black){

        c.lightningFlash=1;
        c.flash=1;

        lightningImpact(
            c.x,
            c.y
        );

        score+=1;

        return;
    }

    /*
     * 白云：
     *
     * 75%打淡
     * 25%凝实成黑云
     */
    const condense=
        Math.random()<.25;

    if(condense){

        condenseCloud(c);

    }else{

        fadeCloud(c);

    }

    /*
     * 击中云固定+1。
     */
    score+=1;

    lightningImpact(
        c.x,
        c.y
    );
}


/* =========================================================
   黑云恢复
========================================================= */

function restoreBlackClouds(){

    for(const c of clouds){

        if(!c.black){
            continue;
        }

        c.black=false;

        /*
         * 保留当前HP，
         * 但恢复为普通白云。
         */
        c.hp=Math.min(
            c.hp,
            c.maxHP
        );

        c.ui=c.hp;

        c.opacity=clamp(
            c.hp/c.maxHP,
            .08,
            1
        );

        c.lightningFlash=0;

        c.flash=1;
    }
}


/* =========================================================
   解救独角兽
========================================================= */

function rescue(u){

    if(u.rescued){
        return;
    }

    /*
     * 严格顺序。
     */
    if(
        u.id!==rescued
    ){
        return;
    }

    u.rescued=true;

    /*
     * 独角兽得分保持原机制。
     */
    score+=
        100+
        u.id*25;

    rescued++;

    lightningImpact(
        u.x,
        u.y
    );

    /*
     * 当前独角兽被救出：
     * 黑云恢复白云。
     */
    if(debug.blackCloud){

        restoreBlackClouds();
    }

    startRain(u);

    if(
        rescued>=TOTAL
    ){

        setTimeout(
            finishGame,
            1900
        );
    }
}


/* =========================================================
   下雨
========================================================= */

function startRain(u){

    weather.raining=true;

    weather.timer=0;

    weather.rainbow={

        x:u.x,

        y:u.y,

        /*
         * 彩虹继承当前独角兽UI等级。
         */
        ui:u.ui
    };

    rain.length=0;

    for(let i=0;i<200;i++){

        rain.push({

            x:rnd(0,W),

            y:rnd(-H,H),

            speed:rnd(600,1100),

            length:rnd(10,22),

            opacity:rnd(.3,.8)

        });
    }
}


/* =========================================================
   天气
========================================================= */

function updateWeather(dt){

    if(!weather.raining){
        return;
    }

    weather.timer+=dt*1000;

    for(const r of rain){

        r.y+=r.speed*dt;

        if(r.y>H+30){

            r.y=-rnd(20,200);
        }
    }

    if(
        weather.timer>=
        weather.duration
    ){

        weather.raining=false;

        if(weather.rainbow){

            rainbows.push({

                x:weather.rainbow.x,

                y:weather.rainbow.y,

                ui:weather.rainbow.ui,

                size:Math.max(
                    280,
                    H*.55
                ),

                life:12000,

                maxLife:12000
            });
        }

        weather.rainbow=null;
    }
}


/* =========================================================
   彩虹
========================================================= */

function updateRainbows(dt){

    for(
        let i=rainbows.length-1;
        i>=0;
        i--
    ){

        const r=rainbows[i];

        r.life-=dt*1000;

        if(r.life<=0){

            rainbows.splice(i,1);
        }
    }
}


/* =========================================================
   云移动
========================================================= */

function updateClouds(dt){

    for(const c of clouds){

        c.x+=c.dx*dt*60;
        c.y+=c.dy*dt*60;

        const oldDx=c.dx;
        const oldDy=c.dy;

        clampCloud(c);

        if(
            c.x<=c.radius+5||
            c.x>=W-c.radius-5
        ){
            c.dx=-oldDx;
        }

        if(
            c.y<=c.radius+5||
            c.y>=H*.70-c.radius
        ){
            c.dy=-oldDy;
        }

        c.flash=Math.max(
            0,
            c.flash-dt*5
        );

        if(c.black){

            c.lightningFlash=Math.max(
                0,
                c.lightningFlash-dt*4
            );

            /*
             * 黑云随机闪电。
             */
            if(
                performance.now()>
                c.lightningTimer
            ){

                c.lightningFlash=1;

                c.lightningTimer=
                    performance.now()+
                    rnd(500,1700);
            }
        }
    }
}


/* =========================================================
   独角兽移动
========================================================= */

function updateUnicorns(dt){

    for(const u of unicorns){

        if(u.rescued){
            continue;
        }

        u.x+=
            u.dx*
            u.speed*
            dt*
            60;

        u.y+=
            u.dy*
            u.speed*
            dt*
            30;

        const margin=u.radius+15;

        if(u.x<margin){

            u.x=margin;
            u.dx=1;
        }

        if(u.x>W-margin){

            u.x=W-margin;
            u.dx=-1;
        }

        if(u.y<H*.13){

            u.y=H*.13;
            u.dy=1;
        }

        if(u.y>H*.62){

            u.y=H*.62;
            u.dy=-1;
        }
    }
}


/* =========================================================
   背景云移动
========================================================= */

function updateBackgroundClouds(dt){

    for(const c of backgroundClouds){

        c.x+=c.speed*dt*60;

        if(c.x>W+150){

            c.x=-150;
        }
    }
}


/* =========================================================
   提示
========================================================= */

function updateAssist(dt){

    if(!debug.hint){

        assistFlash=0;
        assistTimer=0;

        return;
    }

    assistTimer+=dt*1000;

    if(assistTimer>6500){

        assistTimer=0;
        assistFlash=2500;
    }

    if(assistFlash>0){

        assistFlash-=dt*1000;
    }
}


/* =========================================================
   闪电特效
========================================================= */

function lightningImpact(x,y){

    impacts.push({

        x:x,

        y:y,

        radius:8,

        life:420,

        electric:true
    });

    for(let i=0;i<24;i++){

        const angle=rnd(
            0,
            Math.PI*2
        );

        const speed=rnd(2,8);

        particles.push({

            x:x,

            y:y,

            vx:
                Math.cos(angle)*
                speed,

            vy:
                Math.sin(angle)*
                speed,

            life:rnd(180,500),

            size:rnd(2,5),

            electric:true
        });
    }
}


/* =========================================================
   Particle
========================================================= */

function updateParticles(dt){

    for(
        let i=particles.length-1;
        i>=0;
        i--
    ){

        const p=particles[i];

        p.life-=dt*1000;

        p.x+=p.vx;
        p.y+=p.vy;

        p.vy+=.025;

        if(p.life<=0){

            particles.splice(i,1);
        }
    }
}


function drawParticles(){

    for(const p of particles){

        ctx.globalAlpha=clamp(
            p.life/500,
            0,
            1
        );

        ctx.fillStyle=
            p.electric
                ?"white"
                :"#bdf8ff";

        ctx.beginPath();

        ctx.arc(
            p.x,
            p.y,
            p.size,
            0,
            Math.PI*2
        );

        ctx.fill();
    }

    ctx.globalAlpha=1;
}


/* =========================================================
   Impact
========================================================= */

function updateImpacts(dt){

    for(
        let i=impacts.length-1;
        i>=0;
        i--
    ){

        const p=impacts[i];

        p.radius+=dt*180;
        p.life-=dt*1000;

        if(p.life<=0){

            impacts.splice(i,1);
        }
    }
}


function drawImpacts(){

    for(const p of impacts){

        ctx.save();

        ctx.globalAlpha=p.life/420;

        ctx.strokeStyle=
            p.electric
                ?"white"
                :"#75edff";

        ctx.lineWidth=
            p.electric
                ?4
                :3;

        ctx.shadowBlur=
            p.electric
                ?25
                :0;

        ctx.shadowColor="#6ceaff";

        ctx.beginPath();

        ctx.arc(
            p.x,
            p.y,
            p.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }
}


/* =========================================================
   天空
========================================================= */

function drawSky(){

    const g=ctx.createLinearGradient(
        0,
        0,
        0,
        H
    );

    g.addColorStop(
        0,
        "#07192f"
    );

    g.addColorStop(
        .55,
        "#4c91ab"
    );

    g.addColorStop(
        1,
        "#d5f3f3"
    );

    ctx.fillStyle=g;

    ctx.fillRect(
        0,
        0,
        W,
        H
    );
}


/* =========================================================
   背景云
========================================================= */

function drawBackgroundClouds(){

    for(const c of backgroundClouds){

        ctx.save();

        ctx.globalAlpha=c.opacity;

        ctx.fillStyle="white";

        const s=c.size;

        ctx.beginPath();

        ctx.ellipse(
            c.x,
            c.y+15,
            s*1.5,
            s*.4,
            0,
            0,
            Math.PI*2
        );

        ctx.fill();

        ctx.beginPath();

        ctx.arc(
            c.x-s*.5,
            c.y,
            s*.45,
            0,
            Math.PI*2
        );

        ctx.arc(
            c.x,
            c.y-s*.2,
            s*.58,
            0,
            Math.PI*2
        );

        ctx.arc(
            c.x+s*.5,
            c.y,
            s*.45,
            0,
            Math.PI*2
        );

        ctx.fill();

        ctx.restore();
    }
}


/* =========================================================
   彩虹绘制
========================================================= */

function drawRainbow(r){

    ctx.save();

    const alpha=clamp(
        r.life/r.maxLife,
        0,
        1
    );

    ctx.globalAlpha=alpha;

    const colors=[
        "#ff4d6d",
        "#ff9f43",
        "#ffe66d",
        "#5de36b",
        "#4ddcff",
        "#6675ff",
        "#d56cff"
    ];

    /*
     * 大彩虹。
     */
    for(
        let i=0;
        i<colors.length;
        i++
    ){

        ctx.strokeStyle=colors[i];

        ctx.lineWidth=10;

        ctx.beginPath();

        ctx.arc(
            r.x,
            r.y+r.size*.42,
            r.size-i*11,
            Math.PI,
            Math.PI*2
        );

        ctx.stroke();
    }

    ctx.restore();
}


/* =========================================================
   云绘制
========================================================= */

function drawCloud(c){

    ctx.save();

    ctx.globalAlpha=
        clamp(
            c.opacity,
            .08,
            1
        );

    const s=c.radius;

    /*
     * 白云 / 黑云。
     */
    ctx.fillStyle=
        (
            debug.blackCloud&&
            c.black
        )
            ?"20252d"
            :"#effcff";

    ctx.beginPath();

    ctx.ellipse(
        c.x,
        c.y+15,
        s*1.2,
        s*.35,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.beginPath();

    ctx.arc(
        c.x-s*.5,
        c.y,
        s*.5,
        0,
        Math.PI*2
    );

    ctx.arc(
        c.x,
        c.y-s*.12,
        s*.62,
        0,
        Math.PI*2
    );

    ctx.arc(
        c.x+s*.5,
        c.y,
        s*.47,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 普通命中爆闪。
     */
    if(c.flash>0){

        ctx.globalAlpha=c.flash;

        ctx.fillStyle="white";

        ctx.beginPath();

        ctx.arc(
            c.x,
            c.y,
            s*.9,
            0,
            Math.PI*2
        );

        ctx.fill();
    }

    /*
     * 黑云内部闪电。
     */
    if(
        debug.blackCloud&&
        c.black
    ){

        if(c.lightningFlash>0){

            ctx.globalAlpha=
                c.lightningFlash;

            ctx.strokeStyle="#dffcff";

            ctx.lineWidth=2;

            ctx.shadowBlur=18;

            ctx.shadowColor="#8beaff";

            ctx.beginPath();

            ctx.moveTo(
                c.x-12,
                c.y-c.radius*.5
            );

            ctx.lineTo(
                c.x+4,
                c.y-c.radius*.1
            );

            ctx.lineTo(
                c.x-5,
                c.y+c.radius*.15
            );

            ctx.lineTo(
                c.x+13,
                c.y+c.radius*.55
            );

            ctx.stroke();
        }
    }

    /*
     * 目标高亮。
     */
    const target=getCurrentTarget();

    if(
        debug.target&&
        target
    ){

        if(
            canCollideCloud(c)
        ){

            ctx.globalAlpha=
                .65+
                Math.sin(
                    performance.now()/130
                )*.2;

            ctx.strokeStyle="#6ceaff";

            ctx.lineWidth=3;

            ctx.beginPath();

            ctx.ellipse(
                c.x,
                c.y,
                s*1.25,
                s*.72,
                0,
                0,
                Math.PI*2
            );

            ctx.stroke();
        }
    }

    /*
     * Debug碰撞框。
     */
    if(
        debug.collision&&
        canCollideCloud(c)
    ){

        ctx.globalAlpha=.9;

        ctx.strokeStyle="#ff3333";

        ctx.lineWidth=1;

        ctx.beginPath();

        ctx.arc(
            c.x,
            c.y,
            c.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();
    }

    /*
     * Debug UI。
     */
    if(debug.ui){

        ctx.globalAlpha=1;

        ctx.fillStyle="#ffff55";

        ctx.font="11px monospace";

        ctx.fillText(
            "LV:"+c.level+
            " HP:"+c.hp+
            " UI:"+c.ui+
            (
                c.black
                    ?" BLACK"
                    :""
            ),
            c.x-50,
            c.y-c.radius-8
        );
    }

    ctx.restore();
}


/* =========================================================
   独角兽绘制
========================================================= */

function drawUnicorn(u){

    if(u.rescued){
        return;
    }

    ctx.save();

    ctx.translate(
        u.x,
        u.y
    );

    ctx.scale(
        u.scale,
        u.scale
    );

    ctx.globalAlpha=u.opacity;

    ctx.shadowBlur=16;

    ctx.shadowColor="#f3aaff";

    ctx.fillStyle="#f3ddff";

    /*
     * 身体
     */
    ctx.beginPath();

    ctx.ellipse(
        0,
        5,
        30,
        17,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 头
     */
    ctx.beginPath();

    ctx.arc(
        29,
        -8,
        14,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 独角
     */
    ctx.fillStyle="#fff0a0";

    ctx.beginPath();

    ctx.moveTo(
        31,
        -18
    );

    ctx.lineTo(
        39,
        -38
    );

    ctx.lineTo(
        26,
        -20
    );

    ctx.closePath();

    ctx.fill();

    /*
     * 腿
     */
    ctx.strokeStyle="#f3ddff";

    ctx.lineWidth=6;

    for(
        const x of [-18,-4,10,22]
    ){

        ctx.beginPath();

        ctx.moveTo(
            x,
            15
        );

        ctx.lineTo(
            x-3,
            31
        );

        ctx.stroke();
    }

    /*
     * 尾巴
     */
    ctx.strokeStyle="#c99aff";

    ctx.lineWidth=7;

    ctx.beginPath();

    ctx.moveTo(
        -28,
        5
    );

    ctx.quadraticCurveTo(
        -54,
        0,
        -43,
        -20
    );

    ctx.stroke();

    /*
     * 眼睛
     */
    ctx.shadowBlur=0;

    ctx.fillStyle="#3d2150";

    ctx.beginPath();

    ctx.arc(
        34,
        -9,
        2.5,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.restore();

    const target=getCurrentTarget();

    /*
     * 当前目标高亮。
     */
    if(
        debug.target&&
        target===u
    ){

        ctx.save();

        ctx.globalAlpha=
            .7+
            Math.sin(
                performance.now()/100
            )*.25;

        ctx.strokeStyle="white";

        ctx.lineWidth=3;

        ctx.shadowBlur=18;

        ctx.shadowColor="white";

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius+9,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * 周期性保底提示。
     */
    if(
        debug.hint&&
        assistFlash>0&&
        target===u
    ){

        ctx.save();

        ctx.globalAlpha=
            assistFlash/2500;

        ctx.strokeStyle="#ffe85c";

        ctx.lineWidth=4;

        ctx.shadowBlur=25;

        ctx.shadowColor="#ffe85c";

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius+14,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * 碰撞 Debug。
     */
    if(
        debug.collision&&
        target===u
    ){

        ctx.save();

        ctx.strokeStyle="#ff3333";

        ctx.lineWidth=1;

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * UI Debug。
     */
    if(debug.ui){

        ctx.fillStyle="#ffff55";

        ctx.font="11px monospace";

        ctx.fillText(
            "UI:"+u.ui,
            u.x+18,
            u.y-25
        );
    }
}


/* =========================================================
   闪电箭绘制
========================================================= */

function drawArrows(){

    for(const a of arrows){

        ctx.save();

        /*
         * 短运动轨迹。
         */
        for(
            let i=1;
            i<a.trail.length;
            i++
        ){

            const p1=a.trail[i-1];
            const p2=a.trail[i];

            ctx.globalAlpha=
                (
                    i/a.trail.length
                )*.55;

            ctx.strokeStyle="#62eaff";

            ctx.lineWidth=3;

            ctx.beginPath();

            ctx.moveTo(
                p1.x,
                p1.y
            );

            ctx.lineTo(
                p2.x,
                p2.y
            );

            ctx.stroke();
        }

        /*
         * 短闪电箭体。
         */
        const len=38;

        const v=
            Math.hypot(
                a.vx,
                a.vy
            )||1;

        const bx=
            a.x-
            a.vx/v*
            len;

        const by=
            a.y-
            a.vy/v*
            len;

        ctx.globalAlpha=1;

        ctx.strokeStyle="white";

        ctx.lineWidth=4;

        ctx.shadowBlur=20;

        ctx.shadowColor="#55eaff";

        ctx.beginPath();

        ctx.moveTo(
            bx,
            by
        );

        ctx.lineTo(
            a.x,
            a.y
        );

        ctx.stroke();

        ctx.restore();
    }
}


/* =========================================================
   雨
========================================================= */

function drawRain(){

    if(!weather.raining){
        return;
    }

    ctx.save();

    ctx.strokeStyle="#d5f8ff";

    ctx.lineWidth=1;

    for(const r of rain){

        ctx.globalAlpha=r.opacity;

        ctx.beginPath();

        ctx.moveTo(
            r.x,
            r.y
        );

        ctx.lineTo(
            r.x-3,
            r.y+r.length
        );

        ctx.stroke();
    }

    ctx.restore();
}


/* =========================================================
   弓
========================================================= */

function drawBow(){

    const bx=W*.09;
    const by=H*.82;

    const p=getAimPoint();

    const angle=Math.atan2(
        p.y-by,
        p.x-bx
    );

    ctx.save();

    ctx.translate(
        bx,
        by
    );

    ctx.rotate(angle);

    ctx.strokeStyle="#61eaff";

    ctx.lineWidth=7;

    ctx.shadowBlur=18;

    ctx.shadowColor="#61eaff";

    /*
     * 弓。
     */
    ctx.beginPath();

    ctx.moveTo(
        -7,
        -52
    );

    ctx.quadraticCurveTo(
        18,
        0,
        -7,
        52
    );

    ctx.stroke();

    /*
     * 弓弦。
     *
     * 向后拉。
     */
    const pull=
        weapon.charging
            ?
            weapon.charge/
            MAX_CHARGE*
            40
            :
            0;

    ctx.shadowBlur=0;

    ctx.strokeStyle="#efffff";

    ctx.lineWidth=2;

    ctx.beginPath();

    ctx.moveTo(
        -7,
        -52
    );

    ctx.lineTo(
        -pull,
        0
    );

    ctx.lineTo(
        -7,
        52
    );

    ctx.stroke();

    /*
     * 雷电核心。
     */
    ctx.fillStyle="white";

    ctx.shadowBlur=22;

    ctx.shadowColor="#63eaff";

    ctx.beginPath();

    ctx.arc(
        -pull,
        0,
        6,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.restore();
}


/* =========================================================
   准星
========================================================= */

function drawCrosshair(){

    const p=getAimPoint();

    ctx.save();

    ctx.translate(
        p.x,
        p.y
    );

    const r=
        12+
        weapon.shake*.25;

    ctx.strokeStyle=
        weapon.charging
            ?" #ffe95c"
            :"#ffffffcc";

    ctx.lineWidth=2;

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        r,
        0,
        Math.PI*2
    );

    ctx.stroke();

    ctx.beginPath();

    ctx.moveTo(-r-8,0);
    ctx.lineTo(-r+2,0);

    ctx.moveTo(r-2,0);
    ctx.lineTo(r+8,0);

    ctx.moveTo(0,-r-8);
    ctx.lineTo(0,-r+2);

    ctx.moveTo(0,r-2);
    ctx.lineTo(0,r+8);

    ctx.stroke();

    ctx.restore();
}


/* =========================================================
   抛物线 Debug
========================================================= */

function drawTrajectoryDebug(){

    /*
     * 这是“显示”开关。
     *
     * 与抛物线功能开关独立。
     */
    if(
        !debug.trajectoryDisplay
    ){
        return;
    }

    const startX=W*.09;
    const startY=H*.82;

    const p=getAimPoint();

    const dx=p.x-startX;
    const dy=p.y-startY;

    const angle=Math.atan2(dy,dx);

    const ratio=clamp(
        weapon.charge/MAX_CHARGE,
        0,
        1
    );

    const curvature=
        debug.trajectory
            ?
            (1-ratio)*.0015
            :
            0;

    let x=startX;
    let y=startY;

    let vx=
        Math.cos(angle)*
        ARROW_SPEED;

    let vy=
        Math.sin(angle)*
        ARROW_SPEED;

    ctx.save();

    ctx.strokeStyle="#ffff66aa";

    ctx.setLineDash([4,4]);

    ctx.beginPath();

    ctx.moveTo(x,y);

    for(let i=0;i<120;i++){

        const step=.025;

        vy+=
            curvature*
            100000*
            step;

        x+=vx*step;
        y+=vy*step;

        ctx.lineTo(x,y);

        if(
            x<0||
            x>W||
            y<0||
            y>H
        ){
            break;
        }
    }

    ctx.stroke();

    ctx.restore();
}


/* =========================================================
   UI
========================================================= */

function updateUI(){

    const energyBox=
        document.getElementById(
            "energyBox"
        );

    const energyText=
        document.getElementById(
            "energyText"
        );

    /*
     * 能量条。
     */
    if(debug.energy){

        energyBox.style.display="";
        energyText.style.display="";

        document
        .getElementById("energy")
        .style.width=
            weapon.energy+"%";

        energyText.textContent=
            "能量系统 ON · "+
            Math.round(
                weapon.energy
            )+
            "%";

    }else{

        energyBox.style.display="none";
        energyText.style.display="none";
    }

    /*
     * 冷却。
     *
     * 永远显示。
     */
    const cd=
        document.getElementById(
            "cooldown"
        );

    if(
        weapon.cooldown>0
    ){

        cd.textContent=
            "⚡ 冷却 "+
            (
                weapon.cooldown/1000
            ).toFixed(1)+
            " 秒";

        cd.style.color="#777";

    }else if(
        weapon.charging
    ){

        cd.textContent=
            "⚡ 蓄力 "+
            Math.round(
                weapon.charge/
                MAX_CHARGE*
                100
            )+
            "%";

        cd.style.color="white";

    }else{

        cd.textContent="⚡ READY";

        cd.style.color="";
    }

    /*
     * 当前目标。
     */
    const target=
        getCurrentTarget();

    if(target){

        document
        .getElementById(
            "level"
        )
        .textContent=
            "🦄 第"+
            (target.id+1)+
            "只 · UI "+
            target.ui+
            " · ⚡箭UI "+
            getArrowUI();

    }else{

        document
        .getElementById(
            "level"
        )
        .textContent=
            "🌈 全部完成";
    }

    document
    .getElementById("score")
    .textContent=
        "得分："+score;

    document
    .getElementById("players")
    .textContent=
        "玩家1 · "+
        rescued+
        "/13";
}


/* =========================================================
   输入
========================================================= */

/*
 * pointermove：
 * 只负责瞄准。
 */
canvas.addEventListener(
    "pointermove",
    e=>{

        aim.x=e.clientX;
        aim.y=e.clientY;
    }
);


/*
 * pointerdown：
 * 开始蓄力。
 */
canvas.addEventListener(
    "pointerdown",
    e=>{

        if(
            !started||
            finished
        ){
            return;
        }

        aim.x=e.clientX;
        aim.y=e.clientY;

        try{
            canvas.setPointerCapture(
                e.pointerId
            );
        }catch(_){}

        beginCharge();
    }
);


/*
 * pointerup：
 * 松开即发射。
 */
canvas.addEventListener(
    "pointerup",
    e=>{

        aim.x=e.clientX;
        aim.y=e.clientY;

        endCharge();

        try{
            canvas.releasePointerCapture(
                e.pointerId
            );
        }catch(_){}
    }
);


canvas.addEventListener(
    "pointercancel",
    ()=>{
        weapon.charging=false;
        weapon.charge=0;
        weapon.shake=0;
    }
);


canvas.addEventListener(
    "contextmenu",
    e=>e.preventDefault()
);


/* =========================================================
   键盘
========================================================= */

addEventListener(
    "keydown",
    e=>{

        if(
            e.code==="Space"&&
            !e.repeat
        ){

            beginCharge();
        }
    }
);


addEventListener(
    "keyup",
    e=>{

        if(
            e.code==="Space"
        ){

            endCharge();
        }
    }
);


/* =========================================================
   音效
========================================================= */

let audioContext=null;

function playThunder(){

    try{

        const AC=
            window.AudioContext||
            window.webkitAudioContext;

        if(!AC){
            return;
        }

        if(!audioContext){

            audioContext=
                new AC();
        }

        if(
            audioContext.state===
            "suspended"
        ){

            audioContext.resume();
        }

        const osc=
            audioContext.createOscillator();

        const gain=
            audioContext.createGain();

        osc.type="sawtooth";

        osc.frequency.setValueAtTime(
            180,
            audioContext.currentTime
        );

        osc.frequency.exponentialRampToValueAtTime(
            38,
            audioContext.currentTime+.3
        );

        gain.gain.setValueAtTime(
            .0001,
            audioContext.currentTime
        );

        gain.gain.exponentialRampToValueAtTime(
            .18,
            audioContext.currentTime+.01
        );

        gain.gain.exponentialRampToValueAtTime(
            .0001,
            audioContext.currentTime+.3
        );

        osc.connect(gain);

        gain.connect(
            audioContext.destination
        );

        osc.start();

        osc.stop(
            audioContext.currentTime+.3
        );

    }catch(_){}
}


/* =========================================================
   开始
========================================================= */

document
.getElementById("startButton")
.addEventListener(
    "click",
    ()=>{

        started=true;

        document
        .getElementById(
            "startScreen"
        )
        .style.display="none";

        try{

            if(
                audioContext&&
                audioContext.state===
                "suspended"
            ){

                audioContext.resume();
            }

        }catch(_){}
    }
);


/* =========================================================
   Debug按钮
========================================================= */

document
.getElementById("debugButton")
.addEventListener(
    "click",
    ()=>{

        document
        .getElementById(
            "debugPanel"
        )
        .classList.toggle("show");
    }
);


function bindToggle(id,key){

    const button=
        document.getElementById(id);

    button.addEventListener(
        "click",
        ()=>{

            debug[key]=
                !debug[key];

            button.textContent=
                debug[key]
                    ?"ON"
                    :"OFF";

            button.className=
                debug[key]
                    ?"on"
                    :"off";
        }
    );
}


bindToggle(
    "energyDebug",
    "energy"
);

bindToggle(
    "shotEnergyDebug",
    "shotEnergy"
);

bindToggle(
    "blackCloudDebug",
    "blackCloud"
);

bindToggle(
    "targetDebug",
    "target"
);

bindToggle(
    "uiDebug",
    "ui"
);

bindToggle(
    "collisionDebug",
    "collision"
);

bindToggle(
    "trajectoryDebug",
    "trajectory"
);

bindToggle(
    "trajectoryDisplayDebug",
    "trajectoryDisplay"
);

bindToggle(
    "shakeDebug",
    "shake"
);

bindToggle(
    "hintDebug",
    "hint"
);


/* =========================================================
   完成
========================================================= */

function finishGame(){

    finished=true;

    document
    .getElementById(
        "finalScore"
    )
    .textContent=
        "最终得分："+score;

    document
    .getElementById(
        "finish"
    )
    .classList.add("show");
}


document
.getElementById("restart")
.addEventListener(
    "click",
    ()=>{
        location.reload();
    }
);


/* =========================================================
   初始化
========================================================= */

createClouds();

createUnicorns();

createBackgroundClouds();

resize();


/* =========================================================
   主循环
========================================================= */

let last=performance.now();

function loop(now){

    const dt=
        Math.min(
            .05,
            (now-last)/1000
        );

    last=now;

    if(
        started&&
        !finished
    ){

        updateWeapon(dt);

        updateArrows(dt);

        updateClouds(dt);

        updateUnicorns(dt);

        updateBackgroundClouds(dt);

        updateWeather(dt);

        updateRainbows(dt);

        updateParticles(dt);

        updateImpacts(dt);

        updateAssist(dt);
    }

    /*
     * 天空
     */
    drawSky();

    /*
     * 背景云
     */
    drawBackgroundClouds();

    /*
     * 景深排序。
     *
     * UI越高越远。
     *
     * 这里保留：
     *
     * 彩虹 < 独角兽 < 云
     *
     * 的视觉层次，
     * 具体云与独角兽再按照UI排序。
     */
    const objects=[];

    for(const r of rainbows){

        objects.push({
            type:"rainbow",
            obj:r,
            ui:r.ui
        });
    }

    for(const c of clouds){

        objects.push({
            type:"cloud",
            obj:c,
            ui:c.ui
        });
    }

    for(const u of unicorns){

        if(!u.rescued){

            objects.push({
                type:"unicorn",
                obj:u,
                ui:u.ui
            });
        }
    }

    objects.sort(
        (a,b)=>{
            return b.ui-a.ui;
        }
    );

    for(const o of objects){

        if(
            o.type==="rainbow"
        ){

            drawRainbow(o.obj);

        }else if(
            o.type==="cloud"
        ){

            drawCloud(o.obj);

        }else{

            drawUnicorn(o.obj);
        }
    }

    /*
     * 箭。
     */
    drawArrows();

    /*
     * 雨。
     */
    drawRain();

    /*
     * 粒子。
     */
    drawParticles();

    /*
     * 命中特效。
     */
    drawImpacts();

    /*
     * 弓。
     */
    drawBow();

    /*
     * 准星。
     */
    drawCrosshair();

    /*
     * Debug抛物线。
     */
    drawTrajectoryDebug();

    /*
     * UI。
     */
    updateUI();

    requestAnimationFrame(loop);
}

requestAnimationFrame(loop);

</script>

</body>
</html>
```

## ***第四步* **  最终测试  ，修bug ,打包阶段

#### 进行打包测试，先确定可参赛的版本，同时在剩余比赛时间思考其他玩法和设计的可行性，

以下为打包前的源码，约70k字节，且直接zip打包就以达到13k的参赛要求，但是隔天我重新上传了经过压缩的版本，内容上没有变化

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport"
      content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>Thunder Unicorn 13</title>

<style>
*{
    box-sizing:border-box;
    user-select:none;
    -webkit-user-select:none;
}

html,body{
    margin:0;
    width:100%;
    height:100%;
    overflow:hidden;
    background:#071525;
    touch-action:none;
    font-family:Arial,"Microsoft YaHei",sans-serif;
}

canvas{
    position:fixed;
    inset:0;
    width:100%;
    height:100%;
    touch-action:none;
}

#ui{
    position:fixed;
    inset:0;
    z-index:20;
    pointer-events:none;
    color:white;
    text-shadow:0 2px 5px #000;
}

#top{
    position:absolute;
    top:12px;
    left:50%;
    transform:translateX(-50%);
    width:min(600px,82vw);
    text-align:center;
}

#level{
    font-size:14px;
    margin-bottom:6px;
}

#energyBox{
    height:12px;
    border:1px solid #ffffff55;
    border-radius:10px;
    overflow:hidden;
    background:#0008;
}

#energy{
    width:100%;
    height:100%;
    background:linear-gradient(90deg,#36d9ff,#e9ffff);
    transition:width .05s linear;
}

#energyText,
#cooldown{
    font-size:11px;
    margin-top:4px;
}

#score{
    position:absolute;
    right:15px;
    top:15px;
    font-size:13px;
}

#players{
    position:absolute;
    left:15px;
    top:15px;
    font-size:12px;
}

#hint{
    position:absolute;
    bottom:14px;
    left:50%;
    transform:translateX(-50%);
    width:90%;
    text-align:center;
    font-size:12px;
    opacity:.75;
}

.button{
    pointer-events:auto;
    padding:11px 28px;
    margin:5px;
    border-radius:8px;
    border:1px solid #67eaff;
    background:#062638ee;
    color:white;
    cursor:pointer;
}

#startScreen,
#finish{
    position:fixed;
    inset:0;
    z-index:100;
    display:flex;
    flex-direction:column;
    justify-content:center;
    align-items:center;
    text-align:center;
    color:white;
}

#startScreen{
    background:radial-gradient(circle,#174d68,#061321 72%);
}

#startScreen h1{
    margin:0 0 10px;
    font-size:clamp(32px,8vw,56px);
    text-shadow:0 0 30px #67eaff;
}

#startScreen p{
    opacity:.75;
    font-size:13px;
    margin-bottom:25px;
}

#finish{
    display:none;
    background:#020d16f5;
}

#finish.show{
    display:flex;
}

#finish h1{
    font-size:clamp(28px,7vw,48px);
    text-shadow:0 0 30px #7fefff;
}

#debugButton{
    position:fixed;
    right:12px;
    bottom:12px;
    z-index:50;
    pointer-events:auto;
    padding:7px 12px;
    border:1px solid #ffffff55;
    border-radius:6px;
    background:#0009;
    color:white;
    font-size:11px;
}

#debugPanel{
    display:none;
    position:fixed;
    right:12px;
    bottom:50px;
    width:255px;
    max-height:75vh;
    overflow:auto;
    padding:12px;
    z-index:50;
    color:white;
    font-size:12px;
    background:#03101aec;
    border:1px solid #ffffff44;
    border-radius:8px;
}

#debugPanel.show{
    display:block;
}

.debugRow{
    display:flex;
    justify-content:space-between;
    align-items:center;
    margin:7px 0;
}

.debugRow button{
    min-width:42px;
    border:0;
    border-radius:4px;
    padding:3px 8px;
}

.on{
    background:#43e5ff;
    color:#001018;
}

.off{
    background:#444;
    color:#aaa;
}
</style>
</head>

<body>

<canvas id="game"></canvas>

<div id="ui">

    <div id="top">

        <div id="level">
            等待开始
        </div>

        <div id="energyBox">
            <div id="energy"></div>
        </div>

        <div id="energyText">
            能量系统 ON
        </div>

        <div id="cooldown">
            ⚡ READY
        </div>

    </div>

    <div id="players">
        玩家1 · 0/13
    </div>

    <div id="score">
        得分：0
    </div>

    <div id="hint">
        移动鼠标/手指瞄准 · 长按蓄力 · 松开发射
    </div>

</div>


<div id="startScreen">

    <h1>
        ⚡ Thunder Unicorn
    </h1>

    <p>
        穿过层层云海，按照顺序解救13只独角兽
    </p>

    <button id="startButton" class="button">
        开始游戏
    </button>

</div>


<button id="debugButton">
    DEBUG
</button>


<div id="debugPanel">

    <b>DEBUG</b>

    <div class="debugRow">
        <span>能量系统</span>
        <button id="energyDebug" class="on">
            ON
        </button>
    </div>

    <div class="debugRow">
        <span>射击固定耗能</span>
        <button id="shotEnergyDebug" class="on">
            ON
        </button>
    </div>

    <div class="debugRow">
        <span>黑云机制</span>
        <button id="blackCloudDebug" class="on">
            ON
        </button>
    </div>

    <div class="debugRow">
        <span>目标高亮</span>
        <button id="targetDebug" class="on">
            ON
        </button>
    </div>

    <div class="debugRow">
        <span>高亮提示</span>
        <button id="hintDebug" class="on">
            ON
        </button>
    </div>

    <div class="debugRow">
        <span>云等级显示</span>
        <button id="cloudLevelDebug" class="off">
            OFF
        </button>
    </div>

    <div class="debugRow">
        <span>UI等级显示</span>
        <button id="uiDebug" class="off">
            OFF
        </button>
    </div>

    <div class="debugRow">
        <span>碰撞框</span>
        <button id="collisionDebug" class="off">
            OFF
        </button>
    </div>

    <div class="debugRow">
        <span>抛物线功能</span>
        <button id="trajectoryDebug" class="off">
            OFF
        </button>
    </div>

    <div class="debugRow">
        <span>抛物线显示</span>
        <button id="trajectoryDisplayDebug" class="off">
            OFF
        </button>
    </div>

    <div class="debugRow">
        <span>蓄力抖动</span>
        <button id="shakeDebug" class="on">
            ON
        </button>
    </div>

</div>


<div id="finish">

    <h1>
        🌈 13只独角兽全部获救
    </h1>

    <p id="finalScore">
        最终得分：0
    </p>

    <button id="restart" class="button">
        再来一次
    </button>

</div>


<script>

"use strict";


/* =========================================================
   基础
========================================================= */

const canvas=
    document.getElementById("game");

const ctx=
    canvas.getContext("2d");

let W=innerWidth;
let H=innerHeight;

let DPR=
    Math.min(
        2,
        devicePixelRatio||1
    );


const TOTAL=13;


/* =========================================================
   参数
========================================================= */

const MAX_ENERGY=100;

const ENERGY_REGEN=9;

const ENERGY_DRAIN=14;

const SHOT_ENERGY_COST=18;

const MAX_CHARGE=3000;

const COOLDOWN=1100;

const ARROW_SPEED=850;

const ARROW_LIFE=5000;


/* =========================================================
   游戏
========================================================= */

let started=false;

let finished=false;

let score=0;

let rescued=0;


/* =========================================================
   Debug
========================================================= */

const debug={

    energy:true,

    shotEnergy:true,

    blackCloud:true,

    target:true,

    hint:true,

    cloudLevel:false,

    ui:false,

    collision:false,

    trajectory:false,

    trajectoryDisplay:false,

    shake:true
};


/* =========================================================
   瞄准
========================================================= */

const aim={

    x:W*.72,

    y:H*.45
};


/* =========================================================
   武器
========================================================= */

const weapon={

    energy:MAX_ENERGY,

    cooldown:0,

    charging:false,

    charge:0,

    shake:0
};


/* =========================================================
   对象
========================================================= */

const clouds=[];

const unicorns=[];

const backgroundClouds=[];

const arrows=[];

const particles=[];

const impacts=[];

const rainbows=[];

const rain=[];


/* =========================================================
   天气
========================================================= */

const weather={

    raining:false,

    timer:0,

    duration:1800,

    rainbow:null
};


/* =========================================================
   提示
========================================================= */

let assistTimer=0;

let assistFlash=0;


/* =========================================================
   工具
========================================================= */

function clamp(v,a,b){

    return Math.max(
        a,
        Math.min(b,v)
    );
}


function rnd(a,b){

    return a+
        Math.random()*
        (b-a);
}


function dist(
    ax,
    ay,
    bx,
    by
){

    return Math.hypot(
        ax-bx,
        ay-by
    );
}


/* =========================================================
   Resize
========================================================= */

function resize(){

    W=innerWidth;

    H=innerHeight;

    DPR=
        Math.min(
            2,
            devicePixelRatio||1
        );

    canvas.width=
        W*DPR;

    canvas.height=
        H*DPR;

    canvas.style.width=
        W+"px";

    canvas.style.height=
        H+"px";

    ctx.setTransform(
        DPR,
        0,
        0,
        DPR,
        0,
        0
    );

    for(const c of clouds){

        clampCloud(c);
    }

    for(const u of unicorns){

        u.x=
            clamp(
                u.x,
                u.radius+10,
                W-u.radius-10
            );

        u.y=
            clamp(
                u.y,
                H*.13,
                H*.62
            );
    }
}


addEventListener(
    "resize",
    resize
);


/* =========================================================
   云生成
========================================================= */

function createClouds(){

    clouds.length=0;

    /*
     * 每个等级独立生成1~3朵。
     *
     * 13等级：
     *
     * LV1  1~3
     * LV2  1~3
     * ...
     * LV13 1~3
     */

    for(
        let level=1;
        level<=TOTAL;
        level++
    ){

        const count=
            Math.floor(
                Math.random()*3
            )+1;

        for(
            let n=0;
            n<count;
            n++
        ){

            const radius=
                rnd(48,92);

            let x=0;

            let y=0;

            let valid=false;

            for(
                let attempt=0;
                attempt<80;
                attempt++
            ){

                x=
                    rnd(
                        radius+15,
                        Math.max(
                            radius+16,
                            W-radius-15
                        )
                    );

                y=
                    rnd(
                        H*.12,
                        Math.max(
                            H*.13,
                            H*.68-radius
                        )
                    );

                valid=true;

                /*
                 * 只避免完全重叠。
                 *
                 * 不要求所有云都严格分开，
                 * 允许自然堆叠。
                 */
                for(
                    const old of clouds
                ){

                    const d=
                        dist(
                            x,
                            y,
                            old.x,
                            old.y
                        );

                    if(
                        d<
                        (
                            radius+
                            old.radius+
                            12
                        )
                    ){

                        valid=false;

                        break;
                    }
                }

                if(valid){
                    break;
                }
            }

            /*
             * 这里绝对不能：
             *
             * hp = TOTAL
             *
             * 或
             *
             * ui = TOTAL
             *
             * 必须使用当前 level。
             */
            clouds.push({

                id:clouds.length,

                level:level,

                maxHP:level,

                hp:level,

                ui:level,

                x:x,

                y:y,

                radius:radius,

                opacity:1,

                black:false,

                flash:0,

                lightningFlash:0,

                lightningTimer:
                    performance.now()+
                    rnd(
                        500,
                        2500
                    ),

                dx:rnd(
                    -.35,
                    .35
                ),

                dy:rnd(
                    -.12,
                    .12
                )
            });
        }
    }

    /*
     * 随机位置顺序。
     *
     * 不改变等级。
     */
    for(
        let i=clouds.length-1;
        i>0;
        i--
    ){

        const j=
            Math.floor(
                Math.random()*
                (i+1)
            );

        const temp=
            clouds[i];

        clouds[i]=
            clouds[j];

        clouds[j]=
            temp;
    }
}


/* =========================================================
   云边界
========================================================= */

function clampCloud(c){

    const minX=
        c.radius+5;

    const maxX=
        Math.max(
            minX,
            W-c.radius-5
        );

    const minY=
        c.radius+5;

    const maxY=
        Math.max(
            minY,
            H*.70-c.radius
        );

    c.x=
        clamp(
            c.x,
            minX,
            maxX
        );

    c.y=
        clamp(
            c.y,
            minY,
            maxY
        );
}


/* =========================================================
   独角兽
========================================================= */

function createUnicorns(){
    unicorns.length=0;

    for(let i=0;i<TOTAL;i++){

        const d=i/(TOTAL-1);

        unicorns.push({
            id:i,
            ui:TOTAL-i,

            x:rnd(
                80,
                Math.max(81,W-80)
            ),

            y:rnd(
                H*.22,
                H*.58
            ),

            scale:1-d*.68,
            opacity:1-d*.58,
            radius:36-d*23,

            dx:Math.random()<.5?-1:1,
            dy:Math.random()<.5?-1:1,

            speed:.35+d*.8,

            rescued:false
        });
    }
}


/* =========================================================
   背景云
========================================================= */

function createBackgroundClouds(){

    backgroundClouds.length=0;

    const count=
        Math.max(
            30,
            Math.floor(W/25)
        );

    for(
        let i=0;
        i<count;
        i++
    ){

        backgroundClouds.push({

            x:rnd(
                -100,
                W+100
            ),

            y:rnd(
                20,
                H*.7
            ),

            size:rnd(
                25,
                100
            ),

            speed:rnd(
                .01,
                .04
            ),

            opacity:rnd(
                .05,
                .18
            )
        });
    }
}


/* =========================================================
   当前独角兽
========================================================= */

function getCurrentTarget(){

    if(
        rescued>=TOTAL
    ){

        return null;
    }

    return unicorns[rescued];
}


/* =========================================================
   当前箭等级
========================================================= */

function getArrowUI(){

    const target=
        getCurrentTarget();

    return target
        ?target.ui
        :Infinity;
}


/* =========================================================
   云碰撞等级
========================================================= */

function canCollideCloud(c){

    /*
     * 黑云永远阻挡。
     */
    if(
        debug.blackCloud&&
        c.black
    ){

        return true;
    }

    /*
     * 普通云：
     *
     * HP >= 箭UI
     *
     * 才产生碰撞。
     */
    return c.hp>=getArrowUI();
}


/* =========================================================
   瞄准
========================================================= */

function getAimPoint(){

    if(
        !weapon.charging||
        !debug.shake
    ){

        return {

            x:aim.x,

            y:aim.y
        };
    }

    return {

        x:
            aim.x+
            Math.sin(
                performance.now()/38
            )*
            weapon.shake,

        y:
            aim.y+
            Math.cos(
                performance.now()/44
            )*
            weapon.shake
    };
}


/* =========================================================
   创建箭
========================================================= */

function createArrow(){

    const startX=
        W*.09;

    const startY=
        H*.82;

    const p=
        getAimPoint();

    const dx=
        p.x-startX;

    const dy=
        p.y-startY;

    const angle=
        Math.atan2(
            dy,
            dx
        );

    /*
     * 蓄力比例。
     */
    const chargeRatio=
        clamp(
            weapon.charge/
            MAX_CHARGE,
            0,
            1
        );

    /*
     * 固定初速度。
     *
     * 蓄力不会增加射程。
     */
    const speed=
        ARROW_SPEED;

    /*
     * 抛物线系统。
     *
     * OFF：
     * gravity=0
     *
     * ON：
     * 存在真实重力。
     *
     * 蓄力越久，
     * 重力越小。
     */
    const gravity=
        debug.trajectory
            ?
            520-
            chargeRatio*360
            :
            0;

    arrows.push({

        x:startX,

        y:startY,

        vx:
            Math.cos(angle)*
            speed,

        vy:
            Math.sin(angle)*
            speed,

        gravity:gravity,

        ui:getArrowUI(),

        life:0,

        trail:[]
    });
}


/* =========================================================
   发射
========================================================= */

function fire(){

    if(
        !started||
        finished
    ){

        return false;
    }

    if(
        weapon.cooldown>0
    ){

        return false;
    }

    /*
     * 固定射击耗能。
     */
    if(
        debug.energy&&
        debug.shotEnergy
    ){

        if(
            weapon.energy<
            SHOT_ENERGY_COST
        ){

            return false;
        }

        weapon.energy-=
            SHOT_ENERGY_COST;

        weapon.energy=
            clamp(
                weapon.energy,
                0,
                MAX_ENERGY
            );
    }

    createArrow();

    weapon.cooldown=
        COOLDOWN;

    playThunder();

    return true;
}


/* =========================================================
   开始蓄力
========================================================= */

function beginCharge(){

    if(
        !started||
        finished
    ){

        return;
    }

    if(
        weapon.cooldown>0
    ){

        return;
    }

    /*
     * 只有在能量系统+固定耗能
     * 都开启时才需要最低能量检查。
     */
    if(
        debug.energy&&
        debug.shotEnergy&&
        weapon.energy<
        SHOT_ENERGY_COST
    ){

        return;
    }

    weapon.charging=true;

    weapon.charge=0;
}


/* =========================================================
   结束蓄力
========================================================= */

function endCharge(){

    if(
        !weapon.charging
    ){

        return;
    }

    weapon.charging=false;

    fire();

    weapon.charge=0;

    weapon.shake=0;
}


/* =========================================================
   武器更新
========================================================= */

function updateWeapon(dt){

    weapon.cooldown=
        Math.max(
            0,
            weapon.cooldown-
            dt*1000
        );

    /*
     * 不蓄力：
     *
     * 能量恢复。
     */
    if(
        !weapon.charging
    ){

        if(debug.energy){

            weapon.energy+=
                ENERGY_REGEN*
                dt;

            weapon.energy=
                clamp(
                    weapon.energy,
                    0,
                    MAX_ENERGY
                );
        }

        weapon.shake=0;

        return;
    }

    /*
     * 蓄力：
     *
     * 暂停恢复。
     *
     * 持续消耗。
     */
    if(debug.energy){

        weapon.energy-=
            ENERGY_DRAIN*
            dt;

        weapon.energy=
            clamp(
                weapon.energy,
                0,
                MAX_ENERGY
            );
    }

    weapon.charge+=
        dt*1000;

    /*
     * 蓄力抖动。
     */
    if(debug.shake){

        const ratio=
            clamp(
                (
                    weapon.charge-
                    1000
                )/
                2000,
                0,
                1
            );

        weapon.shake=
            ratio*14;
    }

    /*
     * 只有能量系统开启时，
     * 能量耗尽才强制发射。
     *
     * 关闭能量系统后绝对不会
     * 因为“能量=0”而自动发射。
     */
    if(
        debug.energy&&
        weapon.energy<=0
    ){

        weapon.energy=0;

        weapon.charging=false;

        fire();

        weapon.charge=0;

        weapon.shake=0;

        return;
    }

    /*
     * 最大蓄力时间仍然有效。
     */
    if(
        weapon.charge>=
        MAX_CHARGE
    ){

        weapon.charging=false;

        fire();

        weapon.charge=0;

        weapon.shake=0;
    }
}


/* =========================================================
   线段圆碰撞
========================================================= */

function segmentCircle(
    x1,
    y1,
    x2,
    y2,
    cx,
    cy,
    radius
){

    const dx=
        x2-x1;

    const dy=
        y2-y1;

    const len2=
        dx*dx+
        dy*dy;

    if(
        len2===0
    ){

        return (
            dist(
                x1,
                y1,
                cx,
                cy
            )<=radius
        );
    }

    let t=
        (
            (cx-x1)*dx+
            (cy-y1)*dy
        )/
        len2;

    t=
        clamp(
            t,
            0,
            1
        );

    const px=
        x1+
        dx*t;

    const py=
        y1+
        dy*t;

    return (
        dist(
            px,
            py,
            cx,
            cy
        )<=radius
    );
}


/* =========================================================
   箭更新
========================================================= */

function updateArrows(dt){

    for(
        let i=arrows.length-1;
        i>=0;
        i--
    ){

        const a=
            arrows[i];

        const oldX=a.x;

        const oldY=a.y;

        a.life+=
            dt*1000;

        /*
         * 真实重力。
         */
        a.vy+=
            a.gravity*
            dt;

        a.x+=
            a.vx*
            dt;

        a.y+=
            a.vy*
            dt;

        /*
         * 短闪电轨迹。
         */
        a.trail.push({

            x:a.x,

            y:a.y
        });

        if(
            a.trail.length>12
        ){

            a.trail.shift();
        }

        /*
         * 真实碰撞。
         */
        if(
            checkCollision(
                a,
                oldX,
                oldY
            )
        ){

            arrows.splice(
                i,
                1
            );

            continue;
        }

        /*
         * 生命周期。
         */
        if(
            a.life>
            ARROW_LIFE||
            a.x<-100||
            a.x>W+100||
            a.y<-100||
            a.y>H+100
        ){

            arrows.splice(
                i,
                1
            );
        }
    }
}


/* =========================================================
   碰撞
========================================================= */

function checkCollision(
    arrow,
    oldX,
    oldY
){

    const target=
        getCurrentTarget();

    if(!target){

        return false;
    }

    let nearest=null;

    let nearestDistance=
        Infinity;

    /*
     * 云优先。
     */
    for(
        const c of clouds
    ){

        if(
            c.hp<=0&&
            !c.black
        ){

            continue;
        }

        if(
            !canCollideCloud(c)
        ){

            continue;
        }

        if(
            segmentCircle(
                oldX,
                oldY,
                arrow.x,
                arrow.y,
                c.x,
                c.y,
                c.radius
            )
        ){

            const d=
                dist(
                    oldX,
                    oldY,
                    c.x,
                    c.y
                );

            if(
                d<
                nearestDistance
            ){

                nearestDistance=d;

                nearest=c;
            }
        }
    }

    if(nearest){

        hitCloud(nearest);

        return true;
    }

    /*
     * 只有当前独角兽能被救。
     */
    if(
        segmentCircle(
            oldX,
            oldY,
            arrow.x,
            arrow.y,
            target.x,
            target.y,
            target.radius
        )
    ){

        rescue(target);

        return true;
    }

    return false;
}


/* =========================================================
   云：打淡
========================================================= */

function fadeCloud(c){

    if(c.black){

        return;
    }

    c.hp=
        Math.max(
            0,
            c.hp-1
        );

    /*
     * UI等级跟随当前HP。
     *
     * 但level永远不变。
     */
    c.ui=c.hp;

    c.opacity=
        clamp(
            c.hp/
            c.maxHP,
            .08,
            1
        );

    c.flash=1;
}


/* =========================================================
   云：凝实
========================================================= */

function condenseCloud(c){

    /*
     * level不改变。
     *
     * maxHP也不改变。
     */
    c.hp=
        Math.min(
            c.maxHP+1,
            c.hp+1
        );

    c.ui=c.hp;

    c.opacity=1;

    c.black=true;

    c.lightningTimer=
        performance.now()+
        rnd(
            300,
            1000
        );

    c.lightningFlash=1;

    lightningImpact(
        c.x,
        c.y
    );
}


/* =========================================================
   云命中
========================================================= */

function hitCloud(c){

    /*
     * 黑云机制关闭：
     *
     * 一律普通打淡。
     */
    if(
        !debug.blackCloud
    ){

        fadeCloud(c);

        score+=1;

        lightningImpact(
            c.x,
            c.y
        );

        return;
    }

    /*
     * 黑云：
     *
     * 无法打淡，
     * 但仍有闪电反馈。
     */
    if(c.black){

        c.lightningFlash=1;

        c.flash=1;

        score+=1;

        lightningImpact(
            c.x,
            c.y
        );

        return;
    }

    /*
     * 普通白云：
     *
     * 75%打淡
     * 25%凝实
     */
    if(
        Math.random()<.25
    ){

        condenseCloud(c);

    }else{

        fadeCloud(c);
    }

    /*
     * 云命中固定+1。
     */
    score+=1;

    lightningImpact(
        c.x,
        c.y
    );
}


/* =========================================================
   黑云恢复
========================================================= */

function restoreBlackClouds(){

    for(
        const c of clouds
    ){

        if(!c.black){

            continue;
        }

        c.black=false;

        /*
         * 不改变原始等级。
         */
        c.hp=
            Math.min(
                c.hp,
                c.maxHP
            );

        c.ui=c.hp;

        c.opacity=
            clamp(
                c.hp/
                c.maxHP,
                .08,
                1
            );

        c.lightningFlash=0;

        c.flash=1;
    }
}


/* =========================================================
   解救独角兽
========================================================= */

function rescue(u){

    if(u.rescued){

        return;
    }

    /*
     * 严格顺序。
     */
    if(
        u.id!==rescued
    ){

        return;
    }

    u.rescued=true;

    /*
     * 独角兽分数。
     */
    score+=
        100+
        u.id*25;

    rescued++;

    lightningImpact(
        u.x,
        u.y
    );

    /*
     * 救出当前目标后，
     * 黑云全部恢复。
     */
    if(
        debug.blackCloud
    ){

        restoreBlackClouds();
    }

    /*
     * 先下雨。
     */
    startRain(u);

    /*
     * 最后一只。
     */
    if(
        rescued>=TOTAL
    ){

        setTimeout(
            finishGame,
            1900
        );
    }
}


/* =========================================================
   下雨
========================================================= */

function startRain(u){

    weather.raining=true;

    weather.timer=0;

    weather.rainbow={

        x:u.x,

        y:u.y,

        ui:u.ui
    };

    rain.length=0;

    for(
        let i=0;
        i<200;
        i++
    ){

        rain.push({

            x:rnd(
                0,
                W
            ),

            y:rnd(
                -H,
                H
            ),

            speed:rnd(
                600,
                1100
            ),

            length:rnd(
                10,
                22
            ),

            opacity:rnd(
                .3,
                .8
            )
        });
    }
}


/* =========================================================
   天气更新
========================================================= */

function updateWeather(dt){

    if(
        !weather.raining
    ){

        return;
    }

    weather.timer+=
        dt*1000;

    for(
        const r of rain
    ){

        r.y+=
            r.speed*
            dt;

        if(
            r.y>
            H+30
        ){

            r.y=
                -rnd(
                    20,
                    200
                );
        }
    }

    if(
        weather.timer>=
        weather.duration
    ){

        weather.raining=false;

        if(weather.rainbow){

            rainbows.push({

                x:
                    weather.rainbow.x,

                y:
                    weather.rainbow.y,

                ui:
                    weather.rainbow.ui,

                size:
                    Math.max(
                        280,
                        H*.55
                    ),

                life:12000,

                maxLife:12000
            });
        }

        weather.rainbow=null;
    }
}


/* =========================================================
   彩虹更新
========================================================= */

function updateRainbows(dt){

    for(
        let i=rainbows.length-1;
        i>=0;
        i--
    ){

        const r=
            rainbows[i];

        r.life-=
            dt*1000;

        if(
            r.life<=0
        ){

            rainbows.splice(
                i,
                1
            );
        }
    }
}


/* =========================================================
   云移动
========================================================= */

function updateClouds(dt){

    for(
        const c of clouds
    ){

        c.x+=
            c.dx*
            dt*
            60;

        c.y+=
            c.dy*
            dt*
            60;

        const oldDx=c.dx;

        const oldDy=c.dy;

        clampCloud(c);

        if(
            c.x<=c.radius+5||
            c.x>=W-c.radius-5
        ){

            c.dx=-oldDx;
        }

        if(
            c.y<=c.radius+5||
            c.y>=H*.70-c.radius
        ){

            c.dy=-oldDy;
        }

        c.flash=
            Math.max(
                0,
                c.flash-
                dt*5
            );

        if(c.black){

            c.lightningFlash=
                Math.max(
                    0,
                    c.lightningFlash-
                    dt*4
                );

            if(
                performance.now()>
                c.lightningTimer
            ){

                c.lightningFlash=1;

                c.lightningTimer=
                    performance.now()+
                    rnd(
                        500,
                        1700
                    );
            }
        }
    }
}


/* =========================================================
   独角兽更新
========================================================= */

function updateUnicorns(dt){

    for(
        const u of unicorns
    ){

        if(u.rescued){

            continue;
        }

        u.x+=
            u.dx*
            u.speed*
            dt*
            60;

        u.y+=
            u.dy*
            u.speed*
            dt*
            30;

        const margin=
            u.radius+15;

        if(
            u.x<margin
        ){

            u.x=margin;

            u.dx=1;
        }

        if(
            u.x>
            W-margin
        ){

            u.x=
                W-margin;

            u.dx=-1;
        }

        if(
            u.y<H*.13
        ){

            u.y=H*.13;

            u.dy=1;
        }

        if(
            u.y>H*.62
        ){

            u.y=H*.62;

            u.dy=-1;
        }
    }
}


/* =========================================================
   背景云更新
========================================================= */

function updateBackgroundClouds(dt){

    for(
        const c of backgroundClouds
    ){

        c.x+=
            c.speed*
            dt*
            60;

        if(
            c.x>
            W+150
        ){

            c.x=-150;
        }
    }
}


/* =========================================================
   提示更新
========================================================= */

function updateAssist(dt){

    if(
        !debug.hint
    ){

        assistFlash=0;

        assistTimer=0;

        return;
    }

    assistTimer+=
        dt*1000;

    if(
        assistTimer>
        6500
    ){

        assistTimer=0;

        assistFlash=2500;
    }

    if(
        assistFlash>0
    ){

        assistFlash-=
            dt*1000;
    }
}


/* =========================================================
   闪电效果
========================================================= */

function lightningImpact(
    x,
    y
){

    impacts.push({

        x:x,

        y:y,

        radius:8,

        life:420,

        electric:true
    });

    for(
        let i=0;
        i<24;
        i++
    ){

        const angle=
            rnd(
                0,
                Math.PI*2
            );

        const speed=
            rnd(
                2,
                8
            );

        particles.push({

            x:x,

            y:y,

            vx:
                Math.cos(angle)*
                speed,

            vy:
                Math.sin(angle)*
                speed,

            life:rnd(
                180,
                500
            ),

            size:rnd(
                2,
                5
            ),

            electric:true
        });
    }
}


/* =========================================================
   粒子
========================================================= */

function updateParticles(dt){

    for(
        let i=particles.length-1;
        i>=0;
        i--
    ){

        const p=
            particles[i];

        p.life-=
            dt*1000;

        p.x+=p.vx;

        p.y+=p.vy;

        p.vy+=.025;

        if(
            p.life<=0
        ){

            particles.splice(
                i,
                1
            );
        }
    }
}


function drawParticles(){

    for(
        const p of particles
    ){

        ctx.globalAlpha=
            clamp(
                p.life/500,
                0,
                1
            );

        ctx.fillStyle=
            p.electric
                ?"white"
                :"#bdf8ff";

        ctx.beginPath();

        ctx.arc(
            p.x,
            p.y,
            p.size,
            0,
            Math.PI*2
        );

        ctx.fill();
    }

    ctx.globalAlpha=1;
}


/* =========================================================
   Impact
========================================================= */

function updateImpacts(dt){

    for(
        let i=impacts.length-1;
        i>=0;
        i--
    ){

        const p=
            impacts[i];

        p.radius+=
            dt*180;

        p.life-=
            dt*1000;

        if(
            p.life<=0
        ){

            impacts.splice(
                i,
                1
            );
        }
    }
}


function drawImpacts(){

    for(
        const p of impacts
    ){

        ctx.save();

        ctx.globalAlpha=
            p.life/420;

        ctx.strokeStyle=
            "white";

        ctx.lineWidth=4;

        ctx.shadowBlur=25;

        ctx.shadowColor=
            "#6ceaff";

        ctx.beginPath();

        ctx.arc(
            p.x,
            p.y,
            p.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }
}


/* =========================================================
   天空
========================================================= */

function drawSky(){

    const g=
        ctx.createLinearGradient(
            0,
            0,
            0,
            H
        );

    g.addColorStop(
        0,
        "#07192f"
    );

    g.addColorStop(
        .55,
        "#4c91ab"
    );

    g.addColorStop(
        1,
        "#d5f3f3"
    );

    ctx.fillStyle=g;

    ctx.fillRect(
        0,
        0,
        W,
        H
    );
}


/* =========================================================
   背景云
========================================================= */

function drawBackgroundClouds(){

    for(
        const c of backgroundClouds
    ){

        ctx.save();

        ctx.globalAlpha=
            c.opacity;

        ctx.fillStyle=
            "white";

        const s=c.size;

        ctx.beginPath();

        ctx.ellipse(
            c.x,
            c.y+15,
            s*1.5,
            s*.4,
            0,
            0,
            Math.PI*2
        );

        ctx.fill();

        ctx.beginPath();

        ctx.arc(
            c.x-s*.5,
            c.y,
            s*.45,
            0,
            Math.PI*2
        );

        ctx.arc(
            c.x,
            c.y-s*.2,
            s*.58,
            0,
            Math.PI*2
        );

        ctx.arc(
            c.x+s*.5,
            c.y,
            s*.45,
            0,
            Math.PI*2
        );

        ctx.fill();

        ctx.restore();
    }
}


/* =========================================================
   彩虹
========================================================= */

function drawRainbow(r){

    ctx.save();

    const alpha=
        clamp(
            r.life/r.maxLife,
            0,
            1
        );

    ctx.globalAlpha=alpha;

    const colors=[
        "#ff4d6d",
        "#ff9f43",
        "#ffe66d",
        "#5de36b",
        "#4ddcff",
        "#6675ff",
        "#d56cff"
    ];

    /*
     * 大彩虹。
     */
    for(
        let i=0;
        i<colors.length;
        i++
    ){

        ctx.strokeStyle=
            colors[i];

        ctx.lineWidth=10;

        ctx.beginPath();

        ctx.arc(
            r.x,
            r.y+r.size*.42,
            r.size-i*11,
            Math.PI,
            Math.PI*2
        );

        ctx.stroke();
    }

    ctx.restore();
}


/* =========================================================
   云
========================================================= */

function drawCloud(c){

    ctx.save();

    ctx.globalAlpha=
        clamp(
            c.opacity,
            .08,
            1
        );

    const s=
        c.radius;

    ctx.fillStyle=
        (
            debug.blackCloud&&
            c.black
        )
            ?
            "#20252d"
            :
            "#effcff";

    ctx.beginPath();

    ctx.ellipse(
        c.x,
        c.y+15,
        s*1.2,
        s*.35,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.beginPath();

    ctx.arc(
        c.x-s*.5,
        c.y,
        s*.5,
        0,
        Math.PI*2
    );

    ctx.arc(
        c.x,
        c.y-s*.12,
        s*.62,
        0,
        Math.PI*2
    );

    ctx.arc(
        c.x+s*.5,
        c.y,
        s*.47,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 命中闪光。
     */
    if(
        c.flash>0
    ){

        ctx.globalAlpha=
            c.flash;

        ctx.fillStyle=
            "white";

        ctx.beginPath();

        ctx.arc(
            c.x,
            c.y,
            s*.9,
            0,
            Math.PI*2
        );

        ctx.fill();
    }

    /*
     * 黑云闪电。
     */
    if(
        debug.blackCloud&&
        c.black&&
        c.lightningFlash>0
    ){

        ctx.globalAlpha=
            c.lightningFlash;

        ctx.strokeStyle=
            "#dffcff";

        ctx.lineWidth=2;

        ctx.shadowBlur=18;

        ctx.shadowColor=
            "#8beaff";

        ctx.beginPath();

        ctx.moveTo(
            c.x-12,
            c.y-c.radius*.5
        );

        ctx.lineTo(
            c.x+4,
            c.y-c.radius*.1
        );

        ctx.lineTo(
            c.x-5,
            c.y+c.radius*.15
        );

        ctx.lineTo(
            c.x+13,
            c.y+c.radius*.55
        );

        ctx.stroke();
    }

    /*
     * 当前可碰撞云高亮。
     */
    const target=
        getCurrentTarget();

    if(
        debug.target&&
        target&&
        canCollideCloud(c)
    ){

        ctx.globalAlpha=
            .65+
            Math.sin(
                performance.now()/130
            )*.2;

        ctx.strokeStyle=
            "#6ceaff";

        ctx.lineWidth=3;

        ctx.beginPath();

        ctx.ellipse(
            c.x,
            c.y,
            s*1.25,
            s*.72,
            0,
            0,
            Math.PI*2
        );

        ctx.stroke();
    }

    /*
     * 碰撞框。
     */
    if(
        debug.collision
    ){

        ctx.globalAlpha=.9;

        ctx.strokeStyle=
            "#ff3333";

        ctx.lineWidth=1;

        ctx.beginPath();

        ctx.arc(
            c.x,
            c.y,
            c.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();
    }

    /*
     * 云等级 Debug。
     */
    if(
        debug.cloudLevel
    ){

        ctx.globalAlpha=1;

        ctx.textAlign="center";

        ctx.font=
            "bold 12px monospace";

        ctx.fillStyle=
            "#fff45c";

        ctx.fillText(
            "LV."+c.level+
            " HP."+c.hp+
            (
                c.black
                    ?" BLACK"
                    :""
            ),
            c.x,
            c.y-c.radius-12
        );

        ctx.textAlign="left";
    }

    /*
     * UI Debug。
     */
    if(
        debug.ui
    ){

        ctx.globalAlpha=1;

        ctx.fillStyle=
            "#7ff7ff";

        ctx.font=
            "11px monospace";

        ctx.fillText(
            "UI:"+c.ui,
            c.x-25,
            c.y+c.radius+15
        );
    }

    ctx.restore();
}


/* =========================================================
   独角兽
========================================================= */

function drawUnicorn(u){

    if(
        u.rescued
    ){

        return;
    }

    ctx.save();

    ctx.translate(
        u.x,
        u.y
    );

    ctx.scale(
        u.scale,
        u.scale
    );

    ctx.globalAlpha=
        u.opacity;

    ctx.shadowBlur=16;

    ctx.shadowColor=
        "#f3aaff";

    ctx.fillStyle=
        "#f3ddff";

    /*
     * 身体。
     */
    ctx.beginPath();

    ctx.ellipse(
        0,
        5,
        30,
        17,
        0,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 头。
     */
    ctx.beginPath();

    ctx.arc(
        29,
        -8,
        14,
        0,
        Math.PI*2
    );

    ctx.fill();

    /*
     * 独角。
     */
    ctx.fillStyle=
        "#fff0a0";

    ctx.beginPath();

    ctx.moveTo(
        31,
        -18
    );

    ctx.lineTo(
        39,
        -38
    );

    ctx.lineTo(
        26,
        -20
    );

    ctx.closePath();

    ctx.fill();

    /*
     * 腿。
     */
    ctx.strokeStyle=
        "#f3ddff";

    ctx.lineWidth=6;

    for(
        const x of [
            -18,
            -4,
            10,
            22
        ]
    ){

        ctx.beginPath();

        ctx.moveTo(
            x,
            15
        );

        ctx.lineTo(
            x-3,
            31
        );

        ctx.stroke();
    }

    /*
     * 尾巴。
     */
    ctx.strokeStyle=
        "#c99aff";

    ctx.lineWidth=7;

    ctx.beginPath();

    ctx.moveTo(
        -28,
        5
    );

    ctx.quadraticCurveTo(
        -54,
        0,
        -43,
        -20
    );

    ctx.stroke();

    /*
     * 眼睛。
     */
    ctx.shadowBlur=0;

    ctx.fillStyle=
        "#3d2150";

    ctx.beginPath();

    ctx.arc(
        34,
        -9,
        2.5,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.restore();


    const target=
        getCurrentTarget();

    /*
     * 当前目标高亮。
     */
    if(
        debug.target&&
        target===u
    ){

        ctx.save();

        ctx.globalAlpha=
            .7+
            Math.sin(
                performance.now()/100
            )*.25;

        ctx.strokeStyle=
            "white";

        ctx.lineWidth=3;

        ctx.shadowBlur=18;

        ctx.shadowColor=
            "white";

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius+9,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * 保底高亮。
     */
    if(
        debug.hint&&
        assistFlash>0&&
        target===u
    ){

        ctx.save();

        ctx.globalAlpha=
            assistFlash/2500;

        ctx.strokeStyle=
            "#ffe85c";

        ctx.lineWidth=4;

        ctx.shadowBlur=25;

        ctx.shadowColor=
            "#ffe85c";

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius+14,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * 碰撞框。
     */
    if(
        debug.collision&&
        target===u
    ){

        ctx.save();

        ctx.strokeStyle=
            "#ff3333";

        ctx.lineWidth=1;

        ctx.beginPath();

        ctx.arc(
            u.x,
            u.y,
            u.radius,
            0,
            Math.PI*2
        );

        ctx.stroke();

        ctx.restore();
    }

    /*
     * UI。
     */
    if(
        debug.ui
    ){

        ctx.fillStyle=
            "#ffff55";

        ctx.font=
            "11px monospace";

        ctx.fillText(
            "UI:"+u.ui,
            u.x+18,
            u.y-25
        );
    }
}


/* =========================================================
   箭
========================================================= */

function drawArrows(){

    for(
        const a of arrows
    ){

        ctx.save();

        /*
         * 短轨迹。
         */
        for(
            let i=1;
            i<a.trail.length;
            i++
        ){

            const p1=
                a.trail[i-1];

            const p2=
                a.trail[i];

            ctx.globalAlpha=
                (
                    i/
                    a.trail.length
                )*.55;

            ctx.strokeStyle=
                "#62eaff";

            ctx.lineWidth=3;

            ctx.beginPath();

            ctx.moveTo(
                p1.x,
                p1.y
            );

            ctx.lineTo(
                p2.x,
                p2.y
            );

            ctx.stroke();
        }

        /*
         * 短闪电箭体。
         */
        const len=38;

        const v=
            Math.hypot(
                a.vx,
                a.vy
            )||1;

        const bx=
            a.x-
            a.vx/v*
            len;

        const by=
            a.y-
            a.vy/v*
            len;

        ctx.globalAlpha=1;

        ctx.strokeStyle=
            "white";

        ctx.lineWidth=4;

        ctx.shadowBlur=20;

        ctx.shadowColor=
            "#55eaff";

        ctx.beginPath();

        ctx.moveTo(
            bx,
            by
        );

        ctx.lineTo(
            a.x,
            a.y
        );

        ctx.stroke();

        ctx.restore();
    }
}


/* =========================================================
   雨
========================================================= */

function drawRain(){

    if(
        !weather.raining
    ){

        return;
    }

    ctx.save();

    ctx.strokeStyle=
        "#d5f8ff";

    ctx.lineWidth=1;

    for(
        const r of rain
    ){

        ctx.globalAlpha=
            r.opacity;

        ctx.beginPath();

        ctx.moveTo(
            r.x,
            r.y
        );

        ctx.lineTo(
            r.x-3,
            r.y+r.length
        );

        ctx.stroke();
    }

    ctx.restore();
}


/* =========================================================
   弓
========================================================= */

function drawBow(){

    const bx=
        W*.09;

    const by=
        H*.82;

    const p=
        getAimPoint();

    const angle=
        Math.atan2(
            p.y-by,
            p.x-bx
        );

    ctx.save();

    ctx.translate(
        bx,
        by
    );

    ctx.rotate(angle);

    ctx.strokeStyle=
        "#61eaff";

    ctx.lineWidth=7;

    ctx.shadowBlur=18;

    ctx.shadowColor=
        "#61eaff";

    /*
     * 弓。
     */
    ctx.beginPath();

    ctx.moveTo(
        -7,
        -52
    );

    ctx.quadraticCurveTo(
        18,
        0,
        -7,
        52
    );

    ctx.stroke();

    /*
     * 弓弦。
     *
     * 向后拉。
     */
    const pull=
        weapon.charging
            ?
            weapon.charge/
            MAX_CHARGE*
            40
            :
            0;

    ctx.shadowBlur=0;

    ctx.strokeStyle=
        "#efffff";

    ctx.lineWidth=2;

    ctx.beginPath();

    ctx.moveTo(
        -7,
        -52
    );

    ctx.lineTo(
        -pull,
        0
    );

    ctx.lineTo(
        -7,
        52
    );

    ctx.stroke();

    /*
     * 雷核心。
     */
    ctx.fillStyle=
        "white";

    ctx.shadowBlur=22;

    ctx.shadowColor=
        "#63eaff";

    ctx.beginPath();

    ctx.arc(
        -pull,
        0,
        6,
        0,
        Math.PI*2
    );

    ctx.fill();

    ctx.restore();
}


/* =========================================================
   准星
========================================================= */

function drawCrosshair(){

    const p=
        getAimPoint();

    ctx.save();

    ctx.translate(
        p.x,
        p.y
    );

    const r=
        12+
        weapon.shake*.25;

    ctx.strokeStyle=
        weapon.charging
            ?
            "#ffe95c"
            :
            "#ffffffcc";

    ctx.lineWidth=2;

    ctx.beginPath();

    ctx.arc(
        0,
        0,
        r,
        0,
        Math.PI*2
    );

    ctx.stroke();

    ctx.beginPath();

    ctx.moveTo(
        -r-8,
        0
    );

    ctx.lineTo(
        -r+2,
        0
    );

    ctx.moveTo(
        r-2,
        0
    );

    ctx.lineTo(
        r+8,
        0
    );

    ctx.moveTo(
        0,
        -r-8
    );

    ctx.lineTo(
        0,
        -r+2
    );

    ctx.moveTo(
        0,
        r-2
    );

    ctx.lineTo(
        0,
        r+8
    );

    ctx.stroke();

    ctx.restore();
}


/* =========================================================
   抛物线 Debug
========================================================= */

function drawTrajectoryDebug(){

    /*
     * 显示和功能独立。
     */
    if(
        !debug.trajectoryDisplay
    ){

        return;
    }

    const startX=
        W*.09;

    const startY=
        H*.82;

    const p=
        getAimPoint();

    const dx=
        p.x-startX;

    const dy=
        p.y-startY;

    const angle=
        Math.atan2(
            dy,
            dx
        );

    const chargeRatio=
        clamp(
            weapon.charge/
            MAX_CHARGE,
            0,
            1
        );

    /*
     * 必须与箭真实物理完全一致。
     */
    const gravity=
        debug.trajectory
            ?
            520-
            chargeRatio*360
            :
            0;

    let x=startX;

    let y=startY;

    let vx=
        Math.cos(angle)*
        ARROW_SPEED;

    let vy=
        Math.sin(angle)*
        ARROW_SPEED;

    ctx.save();

    ctx.strokeStyle=
        "#fff45caa";

    ctx.lineWidth=1.5;

    ctx.setLineDash([
        5,
        5
    ]);

    ctx.beginPath();

    ctx.moveTo(
        x,
        y
    );

    for(
        let i=0;
        i<180;
        i++
    ){

        const step=.016;

        vy+=
            gravity*
            step;

        x+=
            vx*
            step;

        y+=
            vy*
            step;

        ctx.lineTo(
            x,
            y
        );

        if(
            x<0||
            x>W||
            y<0||
            y>H
        ){

            break;
        }
    }

    ctx.stroke();

    ctx.setLineDash([]);

    ctx.restore();
}


/* =========================================================
   UI
========================================================= */

function updateUI(){

    const energyBox=
        document.getElementById(
            "energyBox"
        );

    const energyText=
        document.getElementById(
            "energyText"
        );

    /*
     * 能量。
     */
    if(
        debug.energy
    ){

        energyBox.style.display="";

        energyText.style.display="";

        document
        .getElementById(
            "energy"
        )
        .style.width=
            weapon.energy+
            "%";

        energyText.textContent=
            "能量系统 ON · "+
            Math.round(
                weapon.energy
            )+
            "%";

    }else{

        energyBox.style.display=
            "none";

        energyText.style.display=
            "none";
    }

    /*
     * 冷却始终显示。
     */
    const cd=
        document.getElementById(
            "cooldown"
        );

    if(
        weapon.cooldown>0
    ){

        cd.textContent=
            "⚡ 冷却 "+
            (
                weapon.cooldown/
                1000
            ).toFixed(1)+
            " 秒";

        cd.style.color=
            "#777";

    }else if(
        weapon.charging
    ){

        cd.textContent=
            "⚡ 蓄力 "+
            Math.round(
                weapon.charge/
                MAX_CHARGE*
                100
            )+
            "%";

        cd.style.color=
            "white";

    }else{

        cd.textContent=
            "⚡ READY";

        cd.style.color="";
    }

    /*
     * 当前目标。
     */
    const target=
        getCurrentTarget();

    if(target){

        document
        .getElementById(
            "level"
        )
        .textContent=
            "🦄 第"+
            (target.id+1)+
            "只 · UI "+
            target.ui+
            " · ⚡箭UI "+
            getArrowUI();

    }else{

        document
        .getElementById(
            "level"
        )
        .textContent=
            "🌈 全部完成";
    }

    document
    .getElementById(
        "score"
    )
    .textContent=
        "得分："+score;

    document
    .getElementById(
        "players"
    )
    .textContent=
        "玩家1 · "+
        rescued+
        "/13";
}


/* =========================================================
   鼠标 / 手机
========================================================= */

canvas.addEventListener(
    "pointermove",
    e=>{

        /*
         * 鼠标移动只负责瞄准。
         */
        aim.x=e.clientX;

        aim.y=e.clientY;
    }
);


canvas.addEventListener(
    "pointerdown",
    e=>{

        if(
            !started||
            finished
        ){

            return;
        }

        /*
         * 点击位置就是瞄准位置。
         */
        aim.x=e.clientX;

        aim.y=e.clientY;

        try{

            canvas.setPointerCapture(
                e.pointerId
            );

        }catch(_){}

        /*
         * 按下开始蓄力。
         */
        beginCharge();
    }
);


canvas.addEventListener(
    "pointerup",
    e=>{

        aim.x=e.clientX;

        aim.y=e.clientY;

        /*
         * 松开发射。
         */
        endCharge();

        try{

            canvas.releasePointerCapture(
                e.pointerId
            );

        }catch(_){}
    }
);


canvas.addEventListener(
    "pointercancel",
    ()=>{

        weapon.charging=false;

        weapon.charge=0;

        weapon.shake=0;
    }
);


canvas.addEventListener(
    "contextmenu",
    e=>{
        e.preventDefault();
    }
);


/* =========================================================
   键盘
========================================================= */

addEventListener(
    "keydown",
    e=>{

        if(
            e.code==="Space"&&
            !e.repeat
        ){

            beginCharge();
        }
    }
);


addEventListener(
    "keyup",
    e=>{

        if(
            e.code==="Space"
        ){

            endCharge();
        }
    }
);


/* =========================================================
   音效
========================================================= */

let audioContext=null;


function playThunder(){

    try{

        const AC=
            window.AudioContext||
            window.webkitAudioContext;

        if(!AC){

            return;
        }

        if(!audioContext){

            audioContext=
                new AC();
        }

        if(
            audioContext.state===
            "suspended"
        ){

            audioContext.resume();
        }

        const osc=
            audioContext.createOscillator();

        const gain=
            audioContext.createGain();

        osc.type=
            "sawtooth";

        osc.frequency.setValueAtTime(
            180,
            audioContext.currentTime
        );

        osc.frequency.exponentialRampToValueAtTime(
            38,
            audioContext.currentTime+.3
        );

        gain.gain.setValueAtTime(
            .0001,
            audioContext.currentTime
        );

        gain.gain.exponentialRampToValueAtTime(
            .18,
            audioContext.currentTime+.01
        );

        gain.gain.exponentialRampToValueAtTime(
            .0001,
            audioContext.currentTime+.3
        );

        osc.connect(gain);

        gain.connect(
            audioContext.destination
        );

        osc.start();

        osc.stop(
            audioContext.currentTime+.3
        );

    }catch(_){}
}


/* =========================================================
   开始
========================================================= */

document
.getElementById(
    "startButton"
)
.addEventListener(
    "click",
    ()=>{

        started=true;

        document
        .getElementById(
            "startScreen"
        )
        .style.display=
            "none";

        try{

            if(
                audioContext&&
                audioContext.state===
                "suspended"
            ){

                audioContext.resume();
            }

        }catch(_){}
    }
);


/* =========================================================
   Debug面板
========================================================= */

document
.getElementById(
    "debugButton"
)
.addEventListener(
    "click",
    ()=>{

        document
        .getElementById(
            "debugPanel"
        )
        .classList.toggle(
            "show"
        );
    }
);


function bindToggle(
    id,
    key
){

    const button=
        document.getElementById(id);

    button.addEventListener(
        "click",
        ()=>{

            debug[key]=
                !debug[key];

            button.textContent=
                debug[key]
                    ?"ON"
                    :"OFF";

            button.className=
                debug[key]
                    ?"on"
                    :"off";
        }
    );
}


bindToggle(
    "energyDebug",
    "energy"
);

bindToggle(
    "shotEnergyDebug",
    "shotEnergy"
);

bindToggle(
    "blackCloudDebug",
    "blackCloud"
);

bindToggle(
    "targetDebug",
    "target"
);

bindToggle(
    "hintDebug",
    "hint"
);

bindToggle(
    "cloudLevelDebug",
    "cloudLevel"
);

bindToggle(
    "uiDebug",
    "ui"
);

bindToggle(
    "collisionDebug",
    "collision"
);

bindToggle(
    "trajectoryDebug",
    "trajectory"
);

bindToggle(
    "trajectoryDisplayDebug",
    "trajectoryDisplay"
);

bindToggle(
    "shakeDebug",
    "shake"
);


/* =========================================================
   完成
========================================================= */

function finishGame(){

    finished=true;

    document
    .getElementById(
        "finalScore"
    )
    .textContent=
        "最终得分："+score;

    document
    .getElementById(
        "finish"
    )
    .classList.add(
        "show"
    );
}


document
.getElementById(
    "restart"
)
.addEventListener(
    "click",
    ()=>{
        location.reload();
    }
);


/* =========================================================
   初始化
========================================================= */

createClouds();

createUnicorns();

createBackgroundClouds();

resize();


/* =========================================================
   主循环
========================================================= */

let last=
    performance.now();


function loop(now){

    const dt=
        Math.min(
            .05,
            (now-last)/
            1000
        );

    last=now;

    if(
        started&&
        !finished
    ){

        updateWeapon(dt);

        updateArrows(dt);

        updateClouds(dt);

        updateUnicorns(dt);

        updateBackgroundClouds(dt);

        updateWeather(dt);

        updateRainbows(dt);

        updateParticles(dt);

        updateImpacts(dt);

        updateAssist(dt);
    }

    /*
     * 天空。
     */
    drawSky();

    /*
     * 背景云。
     */
    drawBackgroundClouds();

    /*
     * 所有前景对象。
     *
     * UI越高视觉上越远。
     */
    const objects=[];

    for(
        const r of rainbows
    ){

        objects.push({

            type:"rainbow",

            obj:r,

            ui:r.ui
        });
    }

    for(
        const c of clouds
    ){

        objects.push({

            type:"cloud",

            obj:c,

            ui:c.ui
        });
    }

    for(
        const u of unicorns
    ){

        if(
            !u.rescued
        ){

            objects.push({

                type:"unicorn",

                obj:u,

                ui:u.ui
            });
        }
    }

    /*
     * 高UI先绘制，
     * 低UI后绘制。
     *
     * 后绘制的对象会遮挡前面的对象，
     * 因此形成景深。
     */
    objects.sort(
        (a,b)=>
            a.ui-b.ui
    );

    for(
        const o of objects
    ){

        if(
            o.type==="rainbow"
        ){

            drawRainbow(o.obj);

        }else if(
            o.type==="cloud"
        ){

            drawCloud(o.obj);

        }else{

            drawUnicorn(o.obj);
        }
    }

    /*
     * 箭。
     */
    drawArrows();

    /*
     * 雨。
     */
    drawRain();

    /*
     * 粒子。
     */
    drawParticles();

    /*
     * 命中特效。
     */
    drawImpacts();

    /*
     * 弓。
     */
    drawBow();

    /*
     * 准星。
     */
    drawCrosshair();

    /*
     * Debug预测轨迹。
     */
    drawTrajectoryDebug();

    /*
     * UI。
     */
    updateUI();

    requestAnimationFrame(loop);
}


requestAnimationFrame(loop);

</script>

</body>
</html>
```

最终版可能会有所改动

# **感悟和挫折**

## **致谢：**

​		**开发阶段感受到了独立开发者的艰辛，三个臭皮匠顶个诸葛亮。感谢那些在js13k中贡献无数游戏的开发者能让许多玩家得到纯粹的游戏乐趣。**

## **为什么是中文**

​		**我确实尝试将代码中的中文字体改为英文，但是因为发现对代码压缩帮助不大，因此放弃，同时经过edge浏览器测试发现可以右键翻译，同时也希望中文开发者能在js13k中留下一些贡献和足迹。**

## **ai的帮助**

​		**不可否认作为一个非专业人士，通过ai的协助下完成了此次独立开发，同时也丰富了我对这个圈子的了解，这一点我希望我更坦诚一点，一月之期对于专业开发者来讲可能足够，但是能将普通人的想法也能实现在没有ai的帮助下是不可想象的。**

## **艺术创新与技术创新**

### 		在我能力范围内我完成了这部作品，里面可能有一些别的游戏的影子，技术上更谈不上有什么创新，

## 预期和期望

​		**从昨天中午无意中得知举办的消息后，我马不停蹄的进行了开发进程，对于最终的大奖我不抱期望，但也衷心地期望在本次赛事中能出现一些让我眼前一亮的作品，我也尝试在网络上搜寻同样参与的开发者讨论，希望能看到新奇的作品和想法**
