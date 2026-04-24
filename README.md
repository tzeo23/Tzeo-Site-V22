!DOCTYPE html>
<html lang="el">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Τζεο Live Wall</title>

<style>
body{
margin:0;
font-family:system-ui;
background:#0b1220;
color:white;
display:flex;
justify-content:center;
}

.container{
width:100%;
max-width:750px;
padding:20px;
}

h1{
text-align:center;
}

input,textarea,button{
width:100%;
margin-top:10px;
padding:12px;
border-radius:10px;
border:none;
}

input,textarea{
background:#111827;
color:white;
}

textarea{
height:80px;
resize:none;
}

button{
background:#3b82f6;
color:white;
cursor:pointer;
}

.post{
background:#111827;
padding:12px;
border-radius:12px;
margin-top:10px;
}

img{
width:100%;
border-radius:10px;
margin-top:10px;
}

small{
color:#94a3b8;
display:block;
margin-top:5px;
}
</style>
</head>

<body>

<div class="container">

<h1>🔥 Τζεο Live Wall</h1>

<input id="name" placeholder="Όνομα">
<textarea id="text" placeholder="Γράψε κάτι..."></textarea>
<input id="img" placeholder="Image URL (προαιρετικό)">
<button onclick="send()">Δημοσίευση</button>

<div id="feed"></div>

</div>

<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
import {
getFirestore,
collection,
addDoc,
onSnapshot,
orderBy,
query,
serverTimestamp
} from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

const firebaseConfig = {
apiKey: "YOUR_KEY",
authDomain: "YOUR_DOMAIN",
projectId: "YOUR_ID"
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

/* REAL TIME FEED */
const q = query(collection(db,"posts"),orderBy("time","desc"));

onSnapshot(q,(snap)=>{
let feed = document.getElementById("feed");
feed.innerHTML="";

snap.forEach(doc=>{
let d = doc.data();

feed.innerHTML += `
<div class="post">
<b>${d.name}</b>
<div>${d.text}</div>
${d.img ? `<img src="${d.img}">` : ""}
<small>${new Date(d.time?.seconds*1000).toLocaleString()}</small>
</div>
`;
});
});

/* SEND POST */
window.send = async function(){

let name = document.getElementById("name").value || "Ανώνυμος";
let text = document.getElementById("text").value;
let img = document.getElementById("img").value;

if(!text && !img) return;

await addDoc(collection(db,"posts"),{
name,
text,
img,
time:serverTimestamp()
});

document.getElementById("text").value="";
document.getElementById("img").value="";
}

</script>

</body>
</html>
