<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>완수 ♥ 성지 완성데이트</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f8f9fa;
      margin: 0;
      padding: 20px;
      color: #343a40;
    }
    h1 {
      text-align: center;
      font-size: 2.5em;
      color: #ff6b81;
      margin-bottom: 20px;
    }
    .region-container {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 20px;
      margin-bottom: 40px;
    }
    .region {
      background-color: #ffffff;
      border-radius: 12px;
      padding: 16px 24px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      cursor: pointer;
      transition: transform 0.2s ease;
    }
    .region:hover {
      transform: scale(1.03);
    }
    .subregion-list {
      display: none;
      margin-top: 10px;
    }
    .diary-container {
      background-color: #ffffff;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      padding: 20px;
      max-width: 600px;
      margin: 0 auto;
    }
    .diary-item {
      border-bottom: 1px solid #dee2e6;
      padding: 10px 0;
    }
    .diary-item:last-child {
      border-bottom: none;
    }
    .diary-item button {
      margin-left: 8px;
      background: none;
      border: none;
      cursor: pointer;
      color: #ff6b81;
    }
    textarea {
      width: 100%;
      height: 80px;
      margin-top: 12px;
      padding: 10px;
      border-radius: 6px;
      border: 1px solid #ccc;
      resize: none;
    }
    button {
      margin-top: 10px;
      padding: 10px 20px;
      border: none;
      border-radius: 6px;
      background-color: #ff6b81;
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: background-color 0.2s ease;
    }
    button:hover {
      background-color: #e85a70;
    }
  </style>
</head>
<body>
  <h1>완수 ♥ 성지 완성데이트</h1>

  <div class="region-container">
    <div class="region" onclick="toggleSubregions('seoul')">서울</div>
    <div class="region" onclick="toggleSubregions('busan')">부산</div>
    <div class="region" onclick="toggleSubregions('jeju')">제주</div>
  </div>

  <div id="subregions"></div>
  <div class="diary-container" id="diaryContainer" style="display:none;">
    <h2>여행 기록</h2>
    <div id="diaryList"></div>
    <textarea id="newDiary" placeholder="오늘의 데이트를 기록해보세요"></textarea>
    <button onclick="addDiary()">기록 추가</button>
  </div>

  <script>
    const subregionsData = {
      seoul: ["강남", "홍대", "한강"],
      busan: ["해운대", "광안리", "남포동"],
      jeju: ["성산", "한림", "서귀포"]
    };

    let currentRegion = "";
    let diaryData = {};

    function toggleSubregions(region) {
      currentRegion = region;
      const container = document.getElementById('subregions');
      container.innerHTML = '';
      const subregionList = document.createElement('div');
      subregionList.className = 'subregion-list';

      subregionsData[region].forEach(sub => {
        const btn = document.createElement('button');
        btn.innerText = sub;
        btn.onclick = () => showDiary(sub);
        subregionList.appendChild(btn);
      });

      container.appendChild(subregionList);
      subregionList.style.display = 'block';
    }

    function showDiary(subregion) {
      const diaryContainer = document.getElementById('diaryContainer');
      diaryContainer.style.display = 'block';
      renderDiaryList(subregion);
    }

    function renderDiaryList(subregion) {
      const diaryList = document.getElementById('diaryList');
      diaryList.innerHTML = '';
      const items = diaryData[subregion] || [];
      items.forEach((entry, index) => {
        const div = document.createElement('div');
        div.className = 'diary-item';

        const textArea = document.createElement('textarea');
        textArea.value = entry;
        textArea.onchange = () => updateDiary(subregion, index, textArea.value);

        const deleteBtn = document.createElement('button');
        deleteBtn.innerText = '삭제';
        deleteBtn.onclick = () => deleteDiary(subregion, index);

        div.appendChild(textArea);
        div.appendChild(deleteBtn);
        diaryList.appendChild(div);
      });
    }

    function addDiary() {
      const input = document.getElementById('newDiary');
      const content = input.value.trim();
      if (!content || !currentRegion) return;
      const subregion = document.querySelector('#subregions button:focus')?.innerText;
      if (!subregion) return;

      if (!diaryData[subregion]) diaryData[subregion] = [];
      diaryData[subregion].push(content);
      input.value = '';
      renderDiaryList(subregion);
    }

    function updateDiary(subregion, index, newText) {
      diaryData[subregion][index] = newText;
    }

    function deleteDiary(subregion, index) {
      diaryData[subregion].splice(index, 1);
      renderDiaryList(subregion);
    }
  </script>
</body>
</html>
