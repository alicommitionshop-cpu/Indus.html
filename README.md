<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Video App</title>
  <style>
    body{margin:0;font-family:sans-serif;background:#000;color:#fff}
    header{padding:10px;text-align:center;background:#111}
    video{width:100%;max-height:90vh}
    .card{margin-bottom:20px}
    input,button{width:100%;padding:10px;margin:5px 0}
  </style>script.js
</head>
<body>

<header>
  <h2>Short Video App</h2>
</header>

<div style="padding:10px">
  <input type="file" id="videoFile" accept="video/*">
  <button onclick="uploadVideo()">Upload</button>
</div>

<div id="feed"></div>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-firestore-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.23.0/firebase-storage-compat.js"></script>

<script>
  const firebaseConfig = {
    apiKey: "PASTE_HERE",
    authDomain: "PASTE_HERE",
    projectId: "PASTE_HERE",
    storageBucket: "PASTE_HERE",
    appId: "PASTE_HERE"
  };

  firebase.initializeApp(firebaseConfig);

  const auth = firebase.auth();
  const db = firebase.firestore();
  const storage = firebase.storage();

  auth.signInAnonymously();

  function uploadVideo(){
    const file = document.getElementById("videoFile").files[0];
    if(!file){alert("Select video");return;}

    const ref = storage.ref("videos/"+Date.now()+file.name);
    ref.put(file).then(snap=>{
      snap.ref.getDownloadURL().then(url=>{
        db.collection("videos").add({
          url:url,
          time:Date.now()
        });
      });
    });
  }

  db.collection("videos").orderBy("time","desc")
  .onSnapshot(snap=>{
    const feed = document.getElementById("feed");
    feed.innerHTML="";
    snap.forEach(doc=>{
      feed.innerHTML += `
        <div class="card">
          <video src="${doc.data().url}" controls loop></video>
        </div>
      `;
    });
  });
</script>

</body>
</html>
