<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>여행지도 기록</title>
  <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Noto Sans KR', sans-serif;
      background: #f9f7f1;
      margin: 0;
      padding: 20px;
    }
    h1 {
      color: #4a3f35;
      text-align: center;
    }
    .container {
      max-width: 800px;
      margin: 0 auto;
      background: #ffffff;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    }
    .region-list, .subregion-list {
      list-style: none;
      padding: 0;
    }
    .region-item, .subregion-item {
      margin: 8px 0;
      padding: 10px;
      background-color: #f2ede3;
      border-radius: 8px;
      cursor: pointer;
      transition: background-color 0.3s;
    }
    .region-item:hover, .subregion-item:hover {
      background-color: #e8dac4;
    }
    .controls {
      display: flex;
      gap: 10px;
      margin: 20px 0;
      justify-content: center;
    }
    .record, .image-container {
      margin-top: 20px;
    }
    button {
      background-color: #c38b6e;
      color: white;
      border: none;
      padding: 8px 16px;
      border-radius: 6px;
      cursor: pointer;
    }
    button:hover {
      background-color: #a3745f;
    }
    .delete-button {
      position: absolute;
      top: 20px;
      right: 20px;
    }
    .image-preview {
      max-width: 100px;
      margin-top: 10px;
      cursor: pointer;
    }
    .popup-image {
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      max-width: 90%;
      max-height: 90%;
      z-index: 999;
      border: 4px solid #fff;
      box-shadow: 0 0 20px rgba(0,0,0,0.5);
    }
    .overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.5);
      z-index: 998;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>여행지도 기록</h1>
    <div class="controls">
      <input id="mainRegionInput" placeholder="메인 지역 입력" />
      <button onclick="addMainRegion()">메인 지역 추가</button>
    </div>
    <ul class="region-list" id="regionList"></ul>
    <div id="subRegionSection" style="display:none">
      <h2 id="selectedRegionTitle"></h2>
      <div class="controls">
        <input id="subRegionInput" placeholder="세부 지역 입력" />
        <button onclick="addSubRegion()">세부 지역 추가</button>
      </div>
      <ul class="subregion-list" id="subRegionList"></ul>
    </div>
    <div id="recordSection" style="display:none">
      <h3 id="selectedSubRegionTitle"></h3>
      <textarea id="textRecord" placeholder="여행 기록 작성..." rows="5" style="width:100%"></textarea>
      <input type="file" id="imageInput" />
      <div class="image-container" id="imageContainer"></div>
    </div>
    <button class="delete-button" id="deleteButton" style="display:none" onclick="handleDelete()">삭제</button>
  </div>

  <script>
    let data = JSON.parse(localStorage.getItem('travelData') || '{}');
    let currentMain = null;
    let currentSub = null;
    let deleteTarget = null;

    function saveData() {
      localStorage.setItem('travelData', JSON.stringify(data));
    }

    function addMainRegion() {
      const val = document.getElementById('mainRegionInput').value.trim();
      if (!val || data[val]) return;
      data[val] = {};
      saveData();
      renderMainRegions();
      document.getElementById('mainRegionInput').value = '';
    }

    function renderMainRegions() {
      const list = document.getElementById('regionList');
      list.innerHTML = '';
      Object.keys(data).forEach(region => {
        const li = document.createElement('li');
        li.className = 'region-item';
        li.innerText = region;
        li.onclick = () => selectMainRegion(region);
        list.appendChild(li);
      });
    }

    function selectMainRegion(region) {
      currentMain = region;
      currentSub = null;
      deleteTarget = region;
      document.getElementById('selectedRegionTitle').innerText = region;
      document.getElementById('subRegionSection').style.display = 'block';
      document.getElementById('recordSection').style.display = 'none';
      document.getElementById('deleteButton').style.display = 'block';
      renderSubRegions();
    }

    function addSubRegion() {
      const val = document.getElementById('subRegionInput').value.trim();
      if (!val || data[currentMain][val]) return;
      data[currentMain][val] = { text: '', image: '' };
      saveData();
      renderSubRegions();
      document.getElementById('subRegionInput').value = '';
    }

    function renderSubRegions() {
      const list = document.getElementById('subRegionList');
      list.innerHTML = '';
      Object.keys(data[currentMain]).forEach(sub => {
        const li = document.createElement('li');
        li.className = 'subregion-item';
        li.innerText = sub;
        li.onclick = () => selectSubRegion(sub);
        list.appendChild(li);
      });
    }

    function selectSubRegion(sub) {
      currentSub = sub;
      deleteTarget = sub;
      const info = data[currentMain][sub];
      document.getElementById('selectedSubRegionTitle').innerText = `${currentMain} > ${sub}`;
      document.getElementById('textRecord').value = info.text || '';
      document.getElementById('imageContainer').innerHTML = info.image ? `<img src="${info.image}" class="image-preview" onclick="showImage('${info.image}')" />` : '';
      document.getElementById('recordSection').style.display = 'block';
      document.getElementById('deleteButton').style.display = 'block';
    }

    function handleDelete() {
      if (!currentMain) return;
      if (currentSub) {
        delete data[currentMain][currentSub];
        currentSub = null;
        document.getElementById('recordSection').style.display = 'none';
        renderSubRegions();
      } else {
        delete data[currentMain];
        currentMain = null;
        document.getElementById('subRegionSection').style.display = 'none';
        document.getElementById('recordSection').style.display = 'none';
        renderMainRegions();
      }
      document.getElementById('deleteButton').style.display = 'none';
      saveData();
    }

    document.getElementById('textRecord').addEventListener('input', () => {
      if (currentMain && currentSub) {
        data[currentMain][currentSub].text = document.getElementById('textRecord').value;
        saveData();
      }
    });

    document.getElementById('imageInput').addEventListener('change', function () {
      const file = this.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function (e) {
        if (currentMain && currentSub) {
          data[currentMain][currentSub].image = e.target.result;
          document.getElementById('imageContainer').innerHTML = `<img src="${e.target.result}" class="image-preview" onclick="showImage('${e.target.result}')" />`;
          saveData();
        }
      };
      reader.readAsDataURL(file);
    });

    function showImage(src) {
      const overlay = document.createElement('div');
      overlay.className = 'overlay';
      overlay.onclick = () => {
        document.body.removeChild(overlay);
        document.body.removeChild(popup);
      };

      const popup = document.createElement('img');
      popup.className = 'popup-image';
      popup.src = src;

      document.body.appendChild(overlay);
      document.body.appendChild(popup);
    }

    renderMainRegions();
  </script>
</body>
</html>
