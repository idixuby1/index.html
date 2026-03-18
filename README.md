<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Lion Star FC</title>
<style>
body{ margin:0; font-family:Arial,sans-serif; background:#0b6623; color:white; text-align:center;}
header{ background:#111; padding:20px;}
header img{ width:100px; border-radius:50%; }
section{ margin:20px auto; padding:20px; border-radius:12px; width:90%; max-width:900px; color:black; }
#playersSection{ background:#ddeeff; }
#matchesSection{ background:#ddffdd; }
#newsSection{ background:#ffdddd; }
#gallerySection{ background:#ffffdd; }
.pitch{ position:relative; height:500px; background:green; border:4px solid white; border-radius:10px; margin-bottom:20px; }
.player{ position:absolute; width:60px; text-align:center; cursor:grab; }
.player img{ width:60px; height:60px; border-radius:50%; border:2px solid white; }
.player span{ display:block; font-size:12px; background:white; color:black; border-radius:10px; }
button,input,select{padding:10px;margin:5px;border-radius:5px;border:none;cursor:pointer;}
button{ background:#111; color:white;}
.adminOnly{ display:none; }
.deleteBtn{ background:red; font-size:12px; padding:5px; cursor:pointer; }
#adminBtn{ position:fixed; bottom:20px; right:20px; background:#444; color:white; padding:10px 15px; border-radius:50%; font-size:16px; z-index:1000; }
#adminPanel{ background:#eee; color:black; padding:15px; border-radius:12px; display:none; margin-bottom:20px; }
#loginBox{ display:none; background:#111; color:white; padding:15px; border-radius:12px; width:250px; margin:20px auto; }
footer{ background:#111; padding:15px; color:#aaa; }
</style>
</head>
<body>

<header>
<img src="images - 2026-03-03T054620.224.jpeg" alt="Lion Star FC Logo">
<h1>⚽ Lion Star FC</h1>
</header>

<section>
<h2>About Team</h2>
<p>Lion Star FC is a strong football club focused on teamwork and winning mentality.</p>
</section>

<section id="playersSection">
<h2>Players</h2>
<div id="playersList"></div>
</section>

<section id="matchesSection">
<h2>Matches</h2>
<div id="matchesList"></div>
</section>

<section id="newsSection">
<h2>News</h2>
<div id="newsList"></div>
</section>

<section id="gallerySection">
<h2>Gallery</h2>
<div id="galleryList"></div>
</section>

<section>
<h2>Starting XI</h2>
<div class="pitch" id="pitch"></div>
</section>

<!-- LOGIN BOX -->
<div id="loginBox">
<h3>Admin Login</h3>
<input type="text" id="user" placeholder="Username"><br>
<input type="password" id="pass" placeholder="Password"><br>
<button onclick="login()">Login</button>
<button onclick="loginBox.style.display='none'">Cancel</button>
</div>

<!-- ADMIN PANEL -->
<section id="adminPanel">
<h2>Admin Panel</h2>
<button onclick="logout()">Logout</button><br>

<h3>Players</h3>
<input type="text" id="playerName" placeholder="Player Name">
<input type="file" id="playerImg">
<button class="adminOnly" onclick="addPlayer()">Add to Pitch</button>

<h3>Matches</h3>
<input type="text" id="matchText" placeholder="Match Info">
<button class="adminOnly" onclick="addMatch()">Add Match</button>

<h3>News</h3>
<input type="text" id="newsText" placeholder="News Info">
<button class="adminOnly" onclick="addNews()">Add News</button>

<h3>Gallery</h3>
<input type="file" id="galleryImage">
<button class="adminOnly" onclick="addGallery()">Add Image</button>

<h3>Formation</h3>
<select id="formation" onchange="setFormation()">
<option value="433">4-3-3</option>
<option value="442">4-4-2</option>
<option value="352">3-5-2</option>
<option value="4231">4-2-3-1</option>
<option value="343">3-4-3</option>
<option value="532">5-3-2</option>
<option value="451">4-5-1</option>
<option value="541">5-4-1</option>
<option value="4141">4-1-4-1</option>
<option value="343d">3-4-3 Diamond</option>
</select>
<button class="adminOnly" onclick="clearTeam()">Clear Team</button>
</section>

<button id="adminBtn" onclick="toggleLogin()">Admin</button>

<footer>© 2026 Lion Star FC</footer>

<script>
// Variables
let adminPanel = document.getElementById("adminPanel");
let loginBox = document.getElementById("loginBox");
let pitch = document.getElementById("pitch");

// Toggle Login
function toggleLogin(){ loginBox.style.display = loginBox.style.display==="none" ? "block" : "none"; }

// Login
function login(){
    let u = document.getElementById("user").value;
    let p = document.getElementById("pass").value;
    if(u==="admin" && p==="1234"){
        localStorage.setItem("admin","true");
        adminPanel.style.display="block";
        document.querySelectorAll(".adminOnly").forEach(el=> el.style.display="inline-block");
        loginBox.style.display="none";
    }else{ alert("Wrong login!"); }
}

// Auto-login
if(localStorage.getItem("admin")==="true"){
    adminPanel.style.display="block";
    document.querySelectorAll(".adminOnly").forEach(el=> el.style.display="inline-block");
}

// Logout
function logout(){
    localStorage.removeItem("admin");
    location.reload();
}

// FORMATIONS
let formation="433";
const positions={
"433":[{top:450,left:45},{top:350,left:15},{top:350,left:35},{top:350,left:55},{top:350,left:75},{top:250,left:25},{top:250,left:45},{top:250,left:65},{top:100,left:25},{top:80,left:45},{top:100,left:65}]
};

// Add Player
function addPlayer(){
    let team = JSON.parse(localStorage.getItem("team"))||[];
    if(team.length>=11){ alert("Max 11"); return; }
    let file = document.getElementById("playerImg").files[0];
    let reader = new FileReader();
    reader.onload = function(e){
        team.push({name:document.getElementById("playerName").value,img:e.target.result,x:0,y:0});
        localStorage.setItem("team",JSON.stringify(team));
        reloadTeam();
    };
    if(file) reader.readAsDataURL(file);
}

// Reload Pitch
function reloadTeam(){
    pitch.innerHTML="";
    let team = JSON.parse(localStorage.getItem("team"))||[];
    team.forEach((p,i)=>{
        let pos=positions[formation][i]||{top:0,left:0};
        let div=document.createElement("div");
        div.className="player";
        div.style.top=(p.y||pos.top)+"px";
        div.style.left=(p.x||pos.left)+"%";
        div.innerHTML=`<img src="${p.img}"><span>${p.name}</span>`;
        // Dragging
        div.onmousedown = function(e){
            let ox=e.offsetX, oy=e.offsetY;
            function moveHandler(e){
                div.style.left=(e.pageX-pitch.offsetLeft-ox)/pitch.offsetWidth*100+"%";
                div.style.top=(e.pageY-pitch.offsetTop-oy)+"px";
            }
            document.addEventListener("mousemove",moveHandler);
            document.addEventListener("mouseup",()=>{ document.removeEventListener("mousemove",moveHandler); saveTeam(); }, {once:true});
        };
        // Double click to remove
        div.ondblclick=function(){ team.splice(i,1); localStorage.setItem("team",JSON.stringify(team)); reloadTeam(); };
        pitch.appendChild(div);
    });
}

// Save Pitch
function saveTeam(){
    let team=[];
    document.querySelectorAll(".player").forEach(el=>{
        team.push({name:el.querySelector("span").textContent,img:el.querySelector("img").src,x:parseFloat(el.style.left),y:parseFloat(el.style.top)});
    });
    localStorage.setItem("team",JSON.stringify(team));
}

// Clear Team
function clearTeam(){ localStorage.removeItem("team"); reloadTeam(); }

// Add / Delete Lists
function addMatch(){ let data=JSON.parse(localStorage.getItem("matches"))||[]; data.push(document.getElementById("matchText").value); localStorage.setItem("matches",JSON.stringify(data)); loadList("matches","matchesList"); }
function addNews(){ let data=JSON.parse(localStorage.getItem("news"))||[]; data.push(document.getElementById("newsText").value); localStorage.setItem("news",JSON.stringify(data)); loadList("news","newsList"); }
function addGallery(){ let file=document.getElementById("galleryImage").files[0]; if(!file) return; let reader=new FileReader(); reader.onload=function(e){ let data=JSON.parse(localStorage.getItem("gallery"))||[]; data.push(e.target.result); localStorage.setItem("gallery",JSON.stringify(data)); loadGallery(); }; reader.readAsDataURL(file); }
function deleteItem(type,index){ let data=JSON.parse(localStorage.getItem(type))||[]; data.splice(index,1); localStorage.setItem(type,JSON.stringify(data)); loadAll(); }

// Load Lists
function loadList(type,element){ let list=document.getElementById(element); list.innerHTML=""; (JSON.parse(localStorage.getItem(type))||[]).forEach((item,i)=> list.innerHTML+=`<p>${item} <button class="deleteBtn adminOnly" onclick="deleteItem('${type}',${i})">X</button></p>`); }
function loadGallery(){ let list=document.getElementById("galleryList"); list.innerHTML=""; (JSON.parse(localStorage.getItem("gallery"))||[]).forEach((img,i)=> list.innerHTML+=`<img src="${img}" width="100" style="margin:5px;"><button class="deleteBtn adminOnly" onclick
