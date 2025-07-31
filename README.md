<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>여행 기록 지도</title>
  <style>
    body {
      font-family: 'Helvetica Neue', sans-serif;
      background: url('https://images.unsplash.com/photo-1502920917128-1aa500764b79?auto=format&fit=crop&w=1500&q=80') no-repeat center center fixed;
      background-size: cover;
      margin: 0;
      padding: 0;
      color: #333;
    }
    .container {
      background-color: rgba(255, 255, 255, 0.9);
      max-width: 800px;
      margin: 50px auto;
      padding: 30px;
      border-radius: 16px;
      box-shadow: 0 8px 24px rgba(0,0,0,0.2);
    }
    h1 {
      text-align: center;
      color: #2c3e50;
      margin-bottom: 20px;
    }
    button {
      background-color: #3498db;
      color: white;
      border: none;
      padding: 8px 14px;
      margin: 5px;
      border-radius: 6px;
      cursor: pointer;
    }
    button:hover {
      background-color: #2980b9;
    }
    .region {
      margin-top: 20px;
      padding: 15px;
      background-color: #ecf0f1;
      border-radius: 10px;
    }
    .region-title {
      font-weight: bold;
      cursor: pointer;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .subregions, .travel-entry {
      margin-left: 15px;
      margin-top: 10px;
    }
    .travel-entry textarea {
      width: 100%;
      height: 100px;
      margin-top: 5px;
    }
    .delete-button {
      float: right;
      background-color: #e74c3c;
    }
    img.uploaded {
      max-width: 100px;
      margin-top: 5px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>여행 지도 기록</h1>

    <div>
      <input type="text" id="mainRegionInput" placeholder="메인 지역 추가 (예: 서울)" />
      <button onclick="addMainRegion()">메인 지역 추가</button>
    </div>

    <div id="regions"></div>
  </div>

  <script>
    let data = JSON.parse(localStorage.getItem('travelData')) || {};

    function saveData() {
      localStorage.setItem('travelData', JSON.stringify(data));
    }

    function addMainRegion() {
      const input = document.getElementById('mainRegionInput');
      const region = input.value.trim();
      if (region && !data[region]) {
        data[region] = {};
        saveData();
        renderRegions();
        input.value = '';
      }
    }

    function addSubRegion(mainRegion) {
      const subRegion = prompt(`${mainRegion}의 세부 지역을 입력하세요:`);
      if (subRegion && !data[mainRegion][subRegion]) {
        data[mainRegion][subRegion] = { text: '', image: '' };
        saveData();
        renderRegions();
      }
    }

    function deleteMainRegion(region) {
      delete data[region];
      saveData();
      renderRegions();
    }

    function deleteSubRegion(main, sub) {
      delete data[main][sub];
      saveData();
      renderRegions();
    }

    function updateText(main, sub, value) {
      data[main][sub].text = value;
      saveData();
    }

    function handleImageUpload(main, sub, file) {
      const reader = new FileReader();
      reader.onload = function (e) {
        data[main][sub].image = e.target.result;
        saveData();
        renderRegions();
      };
      reader.readAsDataURL(file);
    }

    function renderRegions() {
      const container = document.getElementById('regions');
      container.innerHTML = '';
      for (let main in data) {
        const regionDiv = document.createElement('div');
        regionDiv.className = 'region';

        const titleDiv = document.createElement('div');
        titleDiv.className = 'region-title';
        titleDiv.innerHTML = `
          <span>${main}</span>
          <span>
            <button onclick="addSubRegion('${main}')">세부 지역 추가</button>
            <button class="delete-button" onclick="deleteMainRegion('${main}')">삭제</button>
          </span>`;

        const subContainer = document.createElement('div');
        subContainer.className = 'subregions';

        for (let sub in data[main]) {
          const subDiv = document.createElement('div');
          subDiv.innerHTML = `
            <strong>${sub}</strong>
            <button class="delete-button" onclick="deleteSubRegion('${main}', '${sub}')">삭제</button>
            <div class="travel-entry">
              <textarea placeholder="여행 기록 입력..." oninput="updateText('${main}', '${sub}', this.value)">${data[main][sub].text}</textarea>
              <input type="file" accept="image/*" onchange="handleImageUpload('${main}', '${sub}', this.files[0])" />
              ${data[main][sub].image ? `<br><img src="${data[main][sub].image}" class="uploaded" onclick="window.open('${data[main][sub].image}', '_blank')" />` : ''}
            </div>
          `;
          subContainer.appendChild(subDiv);
        }

        regionDiv.appendChild(titleDiv);
        regionDiv.appendChild(subContainer);
        container.appendChild(regionDiv);
      }
    }

    renderRegions();
  </script>
</body>
</html>
