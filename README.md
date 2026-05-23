<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<script src="https://cdn.jsdelivr.net/npm/qrcode/build/qrcode.min.js"></script>

<style>
body { margin:0; font-family:sans-serif; background:#e9f0fb; }

/* 상단 */
.header {
  display:flex;
  justify-content:space-between;
  padding:15px;
  background:white;
  font-weight:bold;
}

/* 메뉴 */
.menu {
  position:fixed;
  top:0;
  left:-250px;
  width:250px;
  height:100%;
  background:white;
  padding:20px;
  transition:0.3s;
}
.menu.active { left:0; }

/* 카드 */
.card {
  background:white;
  margin:20px;
  padding:20px;
  border-radius:20px;
  box-shadow:0 5px 15px rgba(0,0,0,0.2);
}

/* 상단 정보 정렬 */
.top-box {
  display:flex;
  align-items:center;
}

/* 사진 크게 */
img {
  width:120px;
  height:150px;
  border-radius:10px;
  object-fit:cover;
  margin-right:15px;
}

/* 정보 */
.info {
  text-align:left;
}

.info h2 { margin:5px 0; }
.info p { margin:3px 0; }

/* QR */
.qr {
  text-align:center;
  margin-top:10px;
}

/* 타이머 */
.bar {
  width:100%;
  height:6px;
  background:#ddd;
  border-radius:5px;
  margin-top:10px;
}
.bar-inner {
  height:6px;
  background:#3b82f6;
  border-radius:5px;
  width:100%;
}

/* 하단 버튼 */
.bottom {
  position:fixed;
  bottom:0;
  width:100%;
  background:linear-gradient(to right,#3b82f6,#9333ea);
  color:white;
  text-align:center;
  padding:15px;
  cursor:pointer;
}

.hidden { display:none; }
input { margin:5px; padding:10px; width:80%; }
button { padding:10px; margin:5px; }
</style>
</head>

<body>

<div class="header">
  <div onclick="toggleMenu()">☰</div>
  <div>모바일 신분증</div>
</div>

<div id="menu" class="menu">
  <h3>메뉴</h3>
  <button onclick="openEdit()">정보 수정</button>
</div>

<!-- 메인 -->
<div id="mainPage" class="card">

  <div class="top-box">
    <img id="photo">
    
    <div class="info">
      <h2 id="name"></h2>
      <p id="region"></p>
      <p id="idnum"></p>
    </div>
  </div>

  <div class="qr">
    <canvas id="qrcode"></canvas>
  </div>

  <div class="bar">
    <div id="bar" class="bar-inner"></div>
  </div>
  <p id="timeText">180초</p>

</div>

<!-- 수정 -->
<div id="editPage" class="card hidden">
  <h3>정보 수정</h3>

  <input id="inputName" placeholder="이름"><br>
  <input id="inputRegion" placeholder="지역"><br>
  <input id="inputId" placeholder="주민번호"><br>
  <input type="file" id="inputPhoto"><br>

  <button onclick="save()">저장</button>
  <button onclick="back()">뒤로</button>
</div>

<div class="bottom" onclick="showDetail()">상세정보 표시</div>

<script>
// 메뉴
function toggleMenu(){
  menu.classList.toggle("active");
}

// 화면 이동
function openEdit(){
  mainPage.classList.add("hidden");
  editPage.classList.remove("hidden");
  menu.classList.remove("active");
}

function back(){
  editPage.classList.add("hidden");
  mainPage.classList.remove("hidden");
  load();
}

// 저장
function save(){
  localStorage.setItem("name", inputName.value);
  localStorage.setItem("region", inputRegion.value);
  localStorage.setItem("id", inputId.value);

  const file = inputPhoto.files[0];
  if(file){
    const reader = new FileReader();
    reader.onload = e=>{
      localStorage.setItem("photo", e.target.result);
      back();
    }
    reader.readAsDataURL(file);
  } else back();
}

// 불러오기
function load(){
  name.innerText = localStorage.getItem("name") || "";
  region.innerText = localStorage.getItem("region") || "";
  idnum.innerText = localStorage.getItem("id") || "";
  photo.src = localStorage.getItem("photo") || "";

  const qr = name.innerText + "/" + region.innerText + "/" + idnum.innerText;
  QRCode.toCanvas(qrcode, qr);
}

// 상세정보 (확실히 작동)
function showDetail(){
  alert(
    "이름: " + (localStorage.getItem("name")||"없음") + "\n" +
    "지역: " + (localStorage.getItem("region")||"없음") + "\n" +
    "주민번호: " + (localStorage.getItem("id")||"없음")
  );
}

// 타이머
let time = 180;
setInterval(()=>{
  if(time > 0){
    time--;
    timeText.innerText = time + "초";
    bar.style.width = (time/180*100) + "%";
  } else {
    time = 180;
  }
},1000);

load();
</script>

</body>
</html>