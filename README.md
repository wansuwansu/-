<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>한국 여행 지도</title>
  <style>
    body { font-family: sans-serif; padding: 20px; background-color: #f9f9f9; }
    h1 { text-align: center; }
    .region-container { display: flex; flex-wrap: wrap; gap: 10px; margin-top: 20px; }
    .region, .subregion { background-color: #fff; border: 1px solid #ccc; padding: 15px; border-radius: 8px; cursor: pointer; width: 150px; box-shadow: 1px 1px 3px rgba(0,0,0,0.1); }
    .form-group { margin-bottom: 10px; }
    .hidden { display: none; }
    .note, .photo-upload { margin-top: 10px; }
    button { margin-left: 5px; }
  </style>
</head>
<body>
  <h1>한국 여행 지도</h1>

  <div class="form-group">
    <input type="text" id="mainRegionInput" placeholder="메인 지역 추가 (예: 서울)">
    <button onclick="addMainRegion()">+</button>
  </div>

  <div id="mainRegions" class="region-container"></div>

  <div id="subRegionSection" class="hidden">
    <h2 id="selectedRegionTitle"></h2>
    <div class="form-group">
      <input type="text" id="subRegionInput" placeholder="세부 지역 추가 (예: 홍대)">
      <button onclick="addSubRegion()">+</button>
    </div>
    <div id="subRegionContainer" class="region-container"></div>
  </div>

  <div id="recordSection" class="hidden">
    <h3 id="selectedSubRegionTitle"></h3>
    <textarea id="travelNote" class="note" rows="4" cols="50" placeholder="여행기록을 입력하세요..."></textarea><br>
    <input type="file" id="photoUpload" class="photo-upload" accept="image/*" multiple><br>
    <button onclick="saveNote()">기록 저장</button>
    <div id="savedData"></div>
  </div>

  <script>
    const mainRegionsEl = document.getElementById('mainRegions');
    const subRegionSection = document.getElementById('subRegionSection');
    const selectedRegionTitle = document.getElementById('selectedRegionTitle');
    const subRegionInput = document.getElementById('subRegionInput');
    const subRegionContainer = document.getElementById('subRegionContainer');
    const recordSection = document.getElementById('recordSection');
    const selectedSubRegionTitle = document.getElementById('selectedSubRegionTitle');
    const travelNote = document.getElementById('travelNote');
    const photoUpload = document.getElementById('photoUpload');
    const savedData = document.getElementById('savedData');

    let selectedMainRegion = '';
    let selectedSubRegion = '';

    const data = JSON.parse(localStorage.getItem('travelData') || '{}');

    function addMainRegion() {
      const name = document.getElementById('mainRegionInput').value.trim();
      if (!name) return;
      if (!data[name]) data[name] = {};
      localStorage.setItem('travelData', JSON.stringify(data));
      document.getElementById('mainRegionInput').value = '';
      renderMainRegions();
    }

    function renderMainRegions() {
      mainRegionsEl.innerHTML = '';
      for (let region in data) {
        const div = document.createElement('div');
        div.className = 'region';
        div.textContent = region;
        div.onclick = () => selectMainRegion(region);

        const del = document.createElement('button');
        del.textContent = '삭제';
        del.onclick = (e) => {
          e.stopPropagation();
          delete data[region];
          localStorage.setItem('travelData', JSON.stringify(data));
          renderMainRegions();
          subRegionSection.classList.add('hidden');
          recordSection.classList.add('hidden');
        };

        div.appendChild(del);
        mainRegionsEl.appendChild(div);
      }
    }

    function selectMainRegion(region) {
      selectedMainRegion = region;
      selectedRegionTitle.textContent = `${region}의 세부 지역`;
      subRegionSection.classList.remove('hidden');
      recordSection.classList.add('hidden');
      renderSubRegions();
    }

    function addSubRegion() {
      const name = subRegionInput.value.trim();
      if (!name) return;
      if (!data[selectedMainRegion][name]) data[selectedMainRegion][name] = { note: '', photos: [] };
      localStorage.setItem('travelData', JSON.stringify(data));
      subRegionInput.value = '';
      renderSubRegions();
    }

    function renderSubRegions() {
      subRegionContainer.innerHTML = '';
      const subregions = data[selectedMainRegion];
      for (let sub in subregions) {
        const div = document.createElement('div');
        div.className = 'subregion';
        div.textContent = sub;
        div.onclick = () => selectSubRegion(sub);
        subRegionContainer.appendChild(div);
      }
    }

    function selectSubRegion(sub) {
      selectedSubRegion = sub;
      selectedSubRegionTitle.textContent = `${selectedMainRegion} > ${sub}`;
      travelNote.value = data[selectedMainRegion][sub].note;
      recordSection.classList.remove('hidden');
      savedData.innerHTML = '';
    }

    function saveNote() {
      const note = travelNote.value;
      const files = photoUpload.files;
      const photos = [];

      for (let file of files) {
        const reader = new FileReader();
        reader.onload = (e) => {
          photos.push(e.target.result);
          data[selectedMainRegion][selectedSubRegion].photos = photos;
          localStorage.setItem('travelData', JSON.stringify(data));
          renderSavedData();
        };
        reader.readAsDataURL(file);
      }

      data[selectedMainRegion][selectedSubRegion].note = note;
      localStorage.setItem('travelData', JSON.stringify(data));
      renderSavedData();
    }

    function renderSavedData() {
      const item = data[selectedMainRegion][selectedSubRegion];
      savedData.innerHTML = `<p><strong>기록:</strong> ${item.note}</p>`;
      item.photos.forEach(src => {
        const img = document.createElement('img');
        img.src = src;
        img.style.width = '100px';
        img.style.margin = '5px';
        savedData.appendChild(img);
      });
    }

    renderMainRegions();
  </script>
</body>
</html>
