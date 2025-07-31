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
    .subregion { margin-top: 15px; border: 1px solid #ccc; padding: 10px; border-radius: 5px; }
    .subregion h4 { margin: 0 0 10px; }
    .subregion img { max-width: 100%; height: auto; margin-top: 10px; border-radius: 5px; }
    .add-subregion-btn, .add-image-btn { margin-top: 10px; padding: 5px 10px; background: #2196F3; color: white; border: none; border-radius: 4px; cursor: pointer; }
    .add-subregion-btn:hover, .add-image-btn:hover { background: #1976D2; }
  </style>
</head>
<body>
  <h2 style="padding: 20px;">한국 여행 지도</h2>
  <div id="map">
    <button class="region-button" onclick="showRegion('서울')">서울</button>
    <!-- 다른 지역도 원하면 추가 가능 -->
  </div>
  <div id="details"></div>

  <script>
    const details = document.getElementById('details');

    function showRegion(region) {
      details.innerHTML = `<h3>${region} 상세 여행기록</h3>
        <div id="subregions"></div>
        <button class="add-subregion-btn" onclick="addSubregion()">+ 세부 지역 추가</button>`;
    }

    function addSubregion() {
      const subregions = document.getElementById('subregions');
      const subDiv = document.createElement('div');
      subDiv.className = 'subregion';

      const title = prompt('세부 지역 이름을 입력하세요 (예: 홍대, 강남 등)');
      if (!title) return;

      subDiv.innerHTML = `
        <h4>${title}</h4>
        <textarea placeholder="여행기록 작성..." rows="4" style="width:100%;"></textarea>
        <div class="image-list"></div>
        <button class="add-image-btn" onclick="addImage(this)">+ 이미지 추가</button>
      `;
      subregions.appendChild(subDiv);
    }

    function addImage(button) {
      const container = button.previousElementSibling;
      const imageUrl = prompt('이미지 주소(URL)를 입력하세요');
      if (imageUrl) {
        const img = document.createElement('img');
        img.src = imageUrl;
        container.appendChild(img);
      }
    }
  </script>
</body>
</html>
