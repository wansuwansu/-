<!DOCTYPE html>
<html lang="ko">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>완수 ♥ 성지 완성데이트</title>
  <style>
    body {
      margin: 0;
      font-family: 'Arial', sans-serif;
      background: #f4f4f8;
      color: #333;
    }

    header {
      background-color: #FFB6C1;
      padding: 1rem;
      text-align: center;
      font-size: 1.8rem;
      font-weight: bold;
      color: #fff;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    main {
      padding: 2rem;
    }

    .region-container {
      display: flex;
      overflow-x: auto;
      gap: 1rem;
      padding-bottom: 1rem;
    }

    .region {
      background-color: #fff;
      border-radius: 12px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
      padding: 1rem;
      min-width: 200px;
      cursor: pointer;
      transition: transform 0.2s;
    }

    .region:hover {
      transform: translateY(-5px);
    }

    .subregions {
      margin-top: 1rem;
    }

    .subregion {
      margin: 0.5rem 0;
      cursor: pointer;
      color: #007BFF;
      text-decoration: underline;
    }

    .travel-log-form,
    .travel-logs {
      margin-top: 2rem;
    }

    textarea {
      width: 100%;
      height: 100px;
      padding: 0.75rem;
      margin-bottom: 1rem;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 1rem;
    }

    button {
      background-color: #FF69B4;
      color: white;
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
    }

    .log-item {
      background: #fff;
      border-radius: 12px;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
      padding: 1rem;
      margin-bottom: 1rem;
      position: relative;
    }

    .log-item button {
      margin-right: 0.5rem;
    }

    .edit-area {
      width: 100%;
      height: 80px;
      margin-top: 0.5rem;
    }
  </style>
</head>

<body>
  <header>완수 ♥ 성지 완성데이트</header>
  <main>
    <div class="region-container" id="regions"></div>
    <div class="subregions" id="subregions"></div>
    <div class="travel-log-form" id="log-form" style="display: none;">
      <h3>여행 기록 작성</h3>
      <textarea id="log-text" placeholder="여행 내용을 입력하세요"></textarea>
      <button onclick="addLog()">기록 저장</button>
    </div>
    <div class="travel-logs" id="travel-logs"></div>
  </main>

  <script>
    const regions = {
      '서울': ['강남', '홍대', '종로'],
      '부산': ['해운대', '광안리', '남포동'],
      '제주': ['성산', '애월', '한림']
    };

    let selectedSubregion = '';
    let logs = JSON.parse(localStorage.getItem('travelLogs')) || {};

    function renderRegions() {
      const container = document.getElementById('regions');
      container.innerHTML = '';
      for (const main in regions) {
        const div = document.createElement('div');
        div.className = 'region';
        div.textContent = main;
        div.onclick = () => renderSubregions(main);
        container.appendChild(div);
      }
    }

    function renderSubregions(main) {
      const container = document.getElementById('subregions');
      container.innerHTML = '';
      regions[main].forEach(sub => {
        const div = document.createElement('div');
        div.className = 'subregion';
        div.textContent = sub;
        div.onclick = () => {
          selectedSubregion = sub;
          document.getElementById('log-form').style.display = 'block';
          renderLogs();
        };
        container.appendChild(div);
      });
    }

    function addLog() {
      const text = document.getElementById('log-text').value;
      if (!text) return;
      if (!logs[selectedSubregion]) logs[selectedSubregion] = [];
      logs[selectedSubregion].push(text);
      localStorage.setItem('travelLogs', JSON.stringify(logs));
      document.getElementById('log-text').value = '';
      renderLogs();
    }

    function renderLogs() {
      const container = document.getElementById('travel-logs');
      container.innerHTML = '';
      const entries = logs[selectedSubregion] || [];
      entries.forEach((entry, index) => {
        const div = document.createElement('div');
        div.className = 'log-item';

        const p = document.createElement('p');
        p.textContent = entry;
        div.appendChild(p);

        const editBtn = document.createElement('button');
        editBtn.textContent = '수정';
        editBtn.onclick = () => editLog(index);
        div.appendChild(editBtn);

        const deleteBtn = document.createElement('button');
        deleteBtn.textContent = '삭제';
        deleteBtn.onclick = () => deleteLog(index);
        div.appendChild(deleteBtn);

        container.appendChild(div);
      });
    }

    function deleteLog(index) {
      logs[selectedSubregion].splice(index, 1);
      localStorage.setItem('travelLogs', JSON.stringify(logs));
      renderLogs();
    }

    function editLog(index) {
      const newText = prompt('수정할 내용을 입력하세요', logs[selectedSubregion][index]);
      if (newText !== null) {
        logs[selectedSubregion][index] = newText;
        localStorage.setItem('travelLogs', JSON.stringify(logs));
        renderLogs();
      }
    }

    renderRegions();
  </script>
</body>

</html>
