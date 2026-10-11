<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>AR Musa Video Chat</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#25D366">
<link rel="manifest" href="manifest.json">
<style>
body{margin:0;background:#000;color:#fff;font-family:sans-serif;text-align:center}
.card{background:#1a1a1a;padding:24px;border-radius:20px;margin:30px auto;max-width:380px}
input{width:90%;padding:14px;border-radius:10px;border:none;margin:10px 0;font-size:16px}
button{padding:14px;border:none;border-radius:10px;background:#25D366;color:#fff;font-weight:bold;width:95%;margin:8px 0;font-size:16px}
#videos{display:none;flex-direction:column;height:100vh}
#local,#remote{flex:1;background:#111;margin:4px;border-radius:12px;overflow:hidden}
</style>
</head>
<body>
<div id="login" class="card">
<h1>AR MUSA</h1><h3>Video Chat</h3>
<input id="name" placeholder="Your name">
<input id="room" placeholder="Room name e.g. family">
<button onclick="createRoom()">Create & Share Link</button>
<button onclick="joinRoom()" style="background:#333">Join Room</button>
<div id="linkBox" style="display:none;margin-top:15px;background:#000;padding:12px;border-radius:8px;word-break:break-all"></div>
</div>
<div id="videos"><div style="display:flex;flex:1"><div id="local"></div><div id="remote"></div></div><button onclick="location.reload()" style="background:red;width:120px;margin:10px auto">End Call</button></div>
<script src="https://download.agora.io/sdk/release/AgoraRTC_N-4.19.0.js"></script>
<script>
const APP_ID="000b1440e67c48fb8bd4ddbf30f111c4";
let client,audio,video;
window.onload=()=>{let r=new URLSearchParams(location.search).get('room');if(r)document.getElementById('room').value=r}
function createRoom(){let r=document.getElementById('room').value||'room_'+Math.random().toString(36).slice(2,6);document.getElementById('room').value=r;let l=location.origin+location.pathname+'?room='+r;let b=document.getElementById('linkBox');b.style.display='block';b.innerHTML=`Share this link:<br><b>${l}</b><br><br><button onclick="navigator.clipboard.writeText('${l}')">Copy Link</button>`;joinRoom()}
async function joinRoom(){let ch=document.getElementById('room').value;if(!ch)return alert('Enter room name');document.getElementById('login').style.display='none';document.getElementById('videos').style.display='flex';client=AgoraRTC.createClient({mode:'rtc',codec:'vp8'});await client.join(APP_ID,ch,null,null);audio=await AgoraRTC.createMicrophoneAudioTrack();video=await AgoraRTC.createCameraVideoTrack();video.play('local');await client.publish([audio,video]);client.on('user-published',async(u,t)=>{await client.subscribe(u,t);if(t=='video')u.videoTrack.play('remote');if(t=='audio')u.audioTrack.play()})}
if('serviceWorker' in navigator)navigator.serviceWorker.register('sw.js');
</script>
</body>
</html>
