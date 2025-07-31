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
    .subregion, .saved-subregion { margin-top: 15px; border: 1px solid #ccc; padding: 10px; border-radius: 5px; }
    .subregion h4, .saved-subregion h4 { margin: 0 0 10px; }
    .subregion img, .saved-subregion img { max-width: 100%; height: auto; margin-top: 10px; border-radius: 5px; }
    .add-subregion-btn, .add-image-btn, .save-btn, .delete-btn { margin-top: 10px; padding: 5px 10px; background: #2196F3; color: white; border: none; border-radius: 4px; cursor: pointer; }
    .add-subregion-btn:hover, .add-image-btn:hover, .save-btn:hover, .delete-btn:hover { background: #1976D2; }
    .delete-btn { background: #f44336; }
    .delete-btn:hover { background: #d32f2f; }
    input[type="file"] { margin-top: 10px; }
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
        <h4 contenteditable="true">${title}</h4>
        <textarea placeholder="여행기록 작성..." rows="4" style="width:100%;"></textarea>
        <input type="file" accept="image/*" onchange="previewImage(this)">
        <div class="image-list"></div>
        <button class="save-btn" onclick="saveSubregion(this)">저장하기</button>
        <button class="delete-btn" onclick="this.parentElement.remove()">삭제</button>
      `;
      subregions.appendChild(subDiv);
    }

    function previewImage(input) {
      const file = input.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(e) {
          const img = document.createElement('img');
          img.src = e.target.result;
          input.nextElementSibling.appendChild(img);
        };
        reader.readAsDataURL(file);
      }
    }

    function saveSubregion(button) {
      const container = button.parentElement;
      const title = container.querySelector('h4').innerText;
      const text = container.querySelector('textarea').value;
      const imageList = container.querySelector('.image-list');

      const newDiv = document.createElement('div');
      newDiv.className = 'saved-subregion';
      newDiv.innerHTML = `
        <h4>${title}</h4>
        <p>${text}</p>
        <div class="image-list">${imageList.innerHTML}</div>
        <button class="delete-btn" onclick="this.parentElement.remove()">삭제</button>
      `;

      container.remove(); // 입력 폼 삭제
      document.getElementById('subregions').appendChild(newDiv); // 목록에 추가
    }
  </script>
</body>
</html>
