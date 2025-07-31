<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>여행 지도 기록</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background: #fdfcf9;
      margin: 0;
      padding: 0;
    }
    header {
      background-color: #6fcf97;
      padding: 10px;
      text-align: center;
      color: white;
      font-size: 24px;
      font-weight: bold;
    }
    .container {
      display: flex;
      height: calc(100vh - 50px);
    }
    .main-region-list {
      flex: 0 0 200px;
      background: #f0f4f3;
      padding: 10px;
      display: flex;
      flex-direction: column;
      gap: 10px;
      overflow-y: auto;
      height: 100%;
    }
    .main-region {
      background: #d4ede1;
      padding: 10px;
      border-radius: 10px;
      cursor: pointer;
      text-align: center;
    }
    .content {
      flex-grow: 1;
      padding: 20px;
      overflow-y: auto;
    }
    .top-bar {
      display: flex;
      justify-content: flex-end;
    }
    .top-bar button {
      margin-left: 10px;
      background-color: #eb5757;
      color: white;
      border: none;
      padding: 6px 10px;
      border-radius: 6px;
      cursor: pointer;
    }
    .sub-region-list {
      margin-top: 20px;
      display: flex;
      flex-direction: column;
      gap: 10px;
    }
    .sub-region {
      background: #f9e79f;
      padding: 8px 12px;
      border-radius: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .record-section {
      margin-top: 20px;
    }
    .record {
      background: #fff;
      border: 1px solid #ccc;
      padding: 10px;
      margin-bottom: 10px;
      border-radius: 10px;
      transition: background 0.2s;
      cursor: pointer;
    }
    .record:hover {
      background: #f0f0f0;
    }
    .record-content {
      margin-top: 10px;
    }
    .record img {
      max-width: 100%;
      border-radius: 10px;
      cursor: pointer;
      margin-top: 10px;
    }
    .add-section {
      margin: 10px 0;
    }
    .add-section input,
    .add-section textarea {
      width: 100%;
      margin-top: 5px;
      padding: 8px;
      border: 1px solid #ccc;
      border-radius: 6px;
    }
    .add-section button {
      margin-top: 10px;
      background-color: #6fcf97;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 6px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <header>나의 여행 지도</header>
  <div class="container">
    <div class="main-region-list" id="mainRegionList"></div>
    <div class="content">
      <div class="top-bar">
        <button onclick="deleteItem()">삭제</button>
      </div>
      <div class="add-section">
        <input type="text" id="mainRegionInput" placeholder="메인 지역 추가" />
        <button onclick="addMainRegion()">메인 지역 추가</button>
      </div>
      <div id="subRegionContainer"></div>
    </div>
  </div>

  <script>
    let data = JSON.parse(localStorage.getItem("travelData")) || {};
    let currentMain = null;
    let currentSub = null;

    function saveData() {
      localStorage.setItem("travelData", JSON.stringify(data));
    }

    function renderMainRegions() {
      const list = document.getElementById("mainRegionList");
      list.innerHTML = "";
      Object.keys(data).forEach((region) => {
        const div = document.createElement("div");
        div.className = "main-region";
        div.innerText = region;
        div.onclick = () => {
          currentMain = region;
          currentSub = null;
          renderSubRegions();
        };
        list.appendChild(div);
      });
    }

    function renderSubRegions() {
      const container = document.getElementById("subRegionContainer");
      container.innerHTML = `
        <h2>${currentMain}</h2>
        <div class="add-section">
          <input type="text" id="subRegionInput" placeholder="세부 지역 추가" />
          <button onclick="addSubRegion()">세부 지역 추가</button>
        </div>
        <div class="sub-region-list" id="subRegionList"></div>
      `;
      const list = document.getElementById("subRegionList");
      Object.keys(data[currentMain] || {}).forEach((sub) => {
        const div = document.createElement("div");
        div.className = "sub-region";
        div.innerHTML = `
          <span onclick="selectSubRegion('${sub}')">${sub}</span>
          <button onclick="deleteSubRegion('${sub}')" style="background:#eb5757;color:white;border:none;border-radius:4px;padding:2px 6px;cursor:pointer;">삭제</button>
        `;
        list.appendChild(div);
      });
    }

    function selectSubRegion(name) {
      currentSub = name;
      renderRecords();
    }

    function deleteSubRegion(name) {
      if (confirm(`세부 지역 "${name}"을 삭제할까요?`)) {
        delete data[currentMain][name];
        currentSub = null;
        saveData();
        renderSubRegions();
      }
    }

    function renderRecords() {
      const container = document.getElementById("subRegionContainer");
      container.innerHTML += `
        <div class="record-section">
          <h3>${currentSub}의 여행 기록</h3>
          <button onclick="showRecordForm()">글쓰기</button>
          <div id="recordList"></div>
        </div>
      `;
      const list = document.getElementById("recordList");
      list.innerHTML = "";

      (data[currentMain][currentSub] || []).forEach((item, index) => {
        const wrapper = document.createElement("div");
        wrapper.className = "record";
        wrapper.innerHTML = `
          <div onclick="toggleRecordContent(this)">
            <strong>기록 ${index + 1}</strong> - ${item.text.slice(0, 20)}...
          </div>
          <div class="record-content" style="display:none;">
            <p>${item.text}</p>
            ${item.image ? `<img src="${item.image}" onclick="window.open('${item.image}','_blank')" />` : ""}
            <button onclick="deleteRecord(${index})" style="margin-top:10px;background:#eb5757;color:white;border:none;border-radius:4px;padding:4px 8px;cursor:pointer;">삭제</button>
          </div>
        `;
        list.appendChild(wrapper);
      });
    }

    function toggleRecordContent(el) {
      const content = el.nextElementSibling;
      content.style.display = content.style.display === "none" ? "block" : "none";
    }

    function deleteRecord(index) {
      if (confirm("이 기록을 삭제할까요?")) {
        data[currentMain][currentSub].splice(index, 1);
        saveData();
        renderSubRegions();
      }
    }

    function showRecordForm() {
      const container = document.getElementById("recordList");
      container.innerHTML = `
        <div class="add-section">
          <textarea id="recordText" placeholder="여행 내용을 입력하세요"></textarea>
          <input type="file" id="recordImage" accept="image/*" />
          <button onclick="saveRecord()">저장</button>
        </div>
      ` + container.innerHTML;
    }

    function saveRecord() {
      const text = document.getElementById("recordText").value;
      const file = document.getElementById("recordImage").files[0];
      const newRecord = { text, image: null };

      if (file) {
        const reader = new FileReader();
        reader.onloadend = () => {
          newRecord.image = reader.result;
          pushAndSaveRecord(newRecord);
        };
        reader.readAsDataURL(file);
      } else {
        pushAndSaveRecord(newRecord);
      }
    }

    function pushAndSaveRecord(record) {
      data[currentMain][currentSub] = data[currentMain][currentSub] || [];
      data[currentMain][currentSub].push(record);
      saveData();
      alert("저장 완료!");
      renderSubRegions();
    }

    function addMainRegion() {
      const input = document.getElementById("mainRegionInput");
      const name = input.value.trim();
      if (name && !data[name]) {
        data[name] = {};
        saveData();
        renderMainRegions();
        input.value = "";
      }
    }

    function addSubRegion() {
      const input = document.getElementById("subRegionInput");
      const name = input.value.trim();
      if (name && !data[currentMain][name]) {
        data[currentMain][name] = [];
        saveData();
        renderSubRegions();
      }
    }

    function deleteItem() {
      if (currentSub && currentMain) {
        if (confirm(`"${currentSub}" 세부 지역을 삭제할까요?`)) {
          delete data[currentMain][currentSub];
          currentSub = null;
        }
      } else if (currentMain) {
        if (confirm(`"${currentMain}" 메인 지역을 삭제할까요?`)) {
          delete data[currentMain];
          currentMain = null;
        }
      }
      saveData();
      document.getElementById("subRegionContainer").innerHTML = "";
      renderMainRegions();
    }

    renderMainRegions();
  </script>
</body>
</html>
