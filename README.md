<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>한국 여행 지도</title>
  <style>
    body { font-family: sans-serif; margin: 0; padding: 0; }
    #map { display: flex; flex-wrap: wrap; gap: 10px; padding: 20px; }
    .region-button { padding: 10px 20px; background: #4CAF50; color: white; border: none; cursor: pointer; border-radius: 5px; }
    .region-button:hover { background: #45a049; }
    #details { padding: 20px; border-top: 1px solid #ddd; }
    .subregion { margin-top: 15px; border: 1px solid #ccc; padding: 10px; border-radius: 5px; position: relative; }
    .subregion h4 { margin: 0 0 10px; }
    .subregion img { max-width: 100%; height: auto; margin-top: 10px; border-radius: 5px; display: block; }
    .add-subregion-btn, .add-image-btn, .save-btn, .add-region-btn { margin-top: 10px; padding: 5px 10px; background: #2196F3; color: white; border: none; border-radius: 4px; cursor: pointer; }
    .add-subregion-btn:hover, .add-image-btn:hover, .save-btn:hover, .add-region-btn:hover { background: #1976D2; }
    .delete-btn { position: absolute; top: 10px; right: 10px; background: red; color: white; padding: 3px 7px; border: none; border-radius: 4px; cursor: pointer; }
    .delete-btn:hover { background: darkred; }
  </style>
</head>
<body>
  <h2 style="padding: 20px;">한국 여행 지도</h2>

  <div id="map">
    <!-- 지역 버튼들이 여기 추가됨 -->
  </div>

  <div style="padding: 0 20px;">
    <input type="text" id="regionInput" placeholder="메인 지역 이름 입력" />
    <button class="add-region-btn" onclick="addRegion()">+ 지역 추가</button>
  </div>

  <div id="details"></div>

  <script>
    const map = document.getElementById('map');
    const details = document.getElementById('details');
    const regionInput = document.getElementById('regionInput');

    let currentRegion = null;
    let regionData = {};

    window.onload = () => {
      const saved = localStorage.getItem('travelData');
      if (saved) {
        regionData = JSON.parse(saved);
        Object.keys(regionData).forEach(region => createRegionButton(region));
      }
    };

    function addRegion() {
      const name = regionInput.value.trim();
      if (!name || regionData[name]) return;
      regionData[name] = [];
      createRegionButton(name);
      regionInput.value = '';
      saveRegion(); // 초기 저장
    }

    function createRegionButton(region) {
      const btn = document.createElement('button');
      btn.className = 'region-button';
      btn.textContent = region;
      btn.onclick = () => showRegion(region);
      map.appendChild(btn);
    }

    function showRegion(region) {
      currentRegion = region;
      details.innerHTML = `<h3>${region} 상세 여행기록</h3>
        <div id="subregions"></div>
        <button class="add-subregion-btn" onclick="addSubregion()">+ 세부 지역 추가</button>
        <button class="save-btn" onclick="saveRegion()">💾 저장하기</button>`;

      const subregions = document.getElementById('subregions');
      if (regionData[region]) {
        regionData[region].forEach(data => {
          const subDiv = createSubregionElement(data);
          subregions.appendChild(subDiv);
        });
      }
    }

    function addSubregion() {
      const title = prompt('세부 지역 이름을 입력하세요 (예: 홍대)');
      if (!title) return;

      const newSubregion = { title: title, content: '', images: [] };
      const subDiv = createSubregionElement(newSubregion);
      document.getElementById('subregions').appendChild(subDiv);
    }

    function createSubregionElement(data) {
      const subDiv = document.createElement('div');
      subDiv.className = 'subregion';

      const titleEl = document.createElement('h4');
      titleEl.textContent = data.title;

      const textarea = document.createElement('textarea');
      textarea.placeholder = '여행기록 작성...';
      textarea.rows = 4;
      textarea.style.width = '100%';
      textarea.value = data.content;

      const imageContainer = document.createElement('div');
      imageContainer.className = 'image-list';
      data.images.forEach(imgData => {
        const img = document.createElement('img');
        img.src = imgData;
        imageContainer.appendChild(img);
      });

      const fileInput = document.createElement('input');
      fileInput.type = 'file';
      fileInput.accept = 'image/*';
      fileInput.style.display = 'none';

      const imageBtn = document.createElement('button');
      imageBtn.className = 'add-image-btn';
      imageBtn.textContent = '+ 이미지 추가';
      imageBtn.onclick = () => fileInput.click();

      fileInput.onchange = () => {
        const file = fileInput.files[0];
        if (file) {
          const reader = new FileReader();
          reader.onload = () => {
            const img = document.createElement('img');
            img.src = reader.result;
            imageContainer.appendChild(img);
          };
          reader.readAsDataURL(file);
        }
      };

      const deleteBtn = document.createElement('button');
      deleteBtn.className = 'delete-btn';
      deleteBtn.textContent = '삭제';
      deleteBtn.onclick = () => subDiv.remove();

      subDiv.appendChild(titleEl);
      subDiv.appendChild(textarea);
      subDiv.appendChild(imageContainer);
      subDiv.appendChild(fileInput);
      subDiv.appendChild(imageBtn);
      subDiv.appendChild(deleteBtn);

      return subDiv;
    }

    function saveRegion() {
      const region = currentRegion;
      const subDivs = document.querySelectorAll('.subregion');
      regionData[region] = [];

      subDivs.forEach(div => {
        const title = div.querySelector('h4').textContent;
        const content = div.querySelector('textarea').value;
        const images = [...div.querySelectorAll('img')].map(img => img.src);
        regionData[region].push({ title, content, images });
      });

      localStorage.setItem('travelData', JSON.stringify(regionData));
      alert('저장 완료!');
    }
  </script>
</body>
</html>
