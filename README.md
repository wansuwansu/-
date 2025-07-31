<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>완수 ♥ 성지 완성데이트</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body class="bg-gray-50 min-h-screen">
  <header class="bg-white shadow p-6 text-center text-3xl font-bold text-pink-600">
    완수 ♥ 성지 완성데이트
  </header>

  <main class="max-w-6xl mx-auto p-6">
    <!-- 메인 지역 목록 -->
    <section>
      <h2 class="text-2xl font-semibold text-gray-700 mb-4">지역 선택</h2>
      <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
        <button class="bg-white border hover:bg-pink-100 rounded-lg p-4 shadow">서울</button>
        <button class="bg-white border hover:bg-pink-100 rounded-lg p-4 shadow">경기</button>
        <button class="bg-white border hover:bg-pink-100 rounded-lg p-4 shadow">강원</button>
        <button class="bg-white border hover:bg-pink-100 rounded-lg p-4 shadow">부산</button>
        <!-- 더 많은 지역 -->
      </div>
    </section>

    <!-- 세부 지역 UI -->
    <section class="mt-10">
      <h3 class="text-xl font-semibold text-gray-700 mb-3">세부 지역 선택</h3>
      <div class="flex flex-wrap gap-3">
        <button class="bg-gray-200 hover:bg-pink-300 text-gray-800 px-4 py-2 rounded-full">강남역</button>
        <button class="bg-gray-200 hover:bg-pink-300 text-gray-800 px-4 py-2 rounded-full">홍대입구</button>
        <button class="bg-gray-200 hover:bg-pink-300 text-gray-800 px-4 py-2 rounded-full">도산공원</button>
        <!-- 더 많은 세부 지역 -->
      </div>
    </section>

    <!-- 여행기록 작성 및 수정 -->
    <section class="mt-10">
      <h3 class="text-xl font-semibold text-gray-700 mb-3">여행 기록 작성</h3>
      <form id="recordForm" class="bg-white shadow-md rounded-lg p-6 space-y-4">
        <input type="hidden" id="editIndex" value="" />
        <input type="text" id="title" placeholder="제목" class="w-full border px-4 py-2 rounded" required />
        <textarea id="content" rows="4" placeholder="내용을 입력하세요..." class="w-full border px-4 py-2 rounded" required></textarea>
        <input type="file" id="imageUpload" accept="image/*" class="block" />
        <button type="submit" class="bg-pink-500 hover:bg-pink-600 text-white font-semibold px-6 py-2 rounded">
          저장하기
        </button>
      </form>
    </section>

    <!-- 여행기록 목록 -->
    <section class="mt-10">
      <h3 class="text-xl font-semibold text-gray-700 mb-3">여행 기록 목록</h3>
      <div id="recordList" class="grid gap-6">
        <!-- 기록이 여기 렌더링됨 -->
      </div>
    </section>
  </main>

  <script>
    let records = [];

    function renderRecords() {
      const list = document.getElementById("recordList");
      list.innerHTML = "";
      records.forEach((record, index) => {
        const recordEl = document.createElement("div");
        recordEl.className = "bg-white p-5 rounded-lg shadow relative";

        if (record.image) {
          const img = document.createElement("img");
          img.src = record.image;
          img.alt = "여행 사진";
          img.className = "mb-3 rounded max-h-60 w-full object-cover";
          recordEl.appendChild(img);
        }

        const title = document.createElement("h4");
        title.className = "text-lg font-bold text-pink-600 mb-1";
        title.innerText = record.title;
        recordEl.appendChild(title);

        const content = document.createElement("p");
        content.className = "text-gray-700";
        content.innerText = record.content;
        recordEl.appendChild(content);

        const buttonWrap = document.createElement("div");
        buttonWrap.className = "mt-4 space-x-2";

        const editBtn = document.createElement("button");
        editBtn.className = "bg-yellow-400 hover:bg-yellow-500 px-3 py-1 rounded text-white";
        editBtn.innerText = "수정";
        editBtn.onclick = () => editRecord(index);

        const deleteBtn = document.createElement("button");
        deleteBtn.className = "bg-red-500 hover:bg-red-600 px-3 py-1 rounded text-white";
        deleteBtn.innerText = "삭제";
        deleteBtn.onclick = () => deleteRecord(index);

        buttonWrap.appendChild(editBtn);
        buttonWrap.appendChild(deleteBtn);
        recordEl.appendChild(buttonWrap);

        list.appendChild(recordEl);
      });
    }

    function editRecord(index) {
      const record = records[index];
      document.getElementById("title").value = record.title;
      document.getElementById("content").value = record.content;
      document.getElementById("editIndex").value = index;
    }

    function deleteRecord(index) {
      if (confirm("정말 삭제하시겠습니까?")) {
        records.splice(index, 1);
        renderRecords();
      }
    }

    document.getElementById("recordForm").addEventListener("submit", (e) => {
      e.preventDefault();
      const title = document.getElementById("title").value;
      const content = document.getElementById("content").value;
      const imageInput = document.getElementById("imageUpload");
      const editIndex = document.getElementById("editIndex").value;

      const reader = new FileReader();
      reader.onloadend = function () {
        const image = imageInput.files[0] ? reader.result : null;

        if (editIndex === "") {
          records.push({ title, content, image });
        } else {
          records[editIndex] = { title, content, image: image || records[editIndex].image };
        }

        document.getElementById("recordForm").reset();
        document.getElementById("editIndex").value = "";
        renderRecords();
      };

      if (imageInput.files[0]) {
        reader.readAsDataURL(imageInput.files[0]);
      } else {
        reader.onloadend();
      }
    });

    renderRecords();
  </script>
</body>
</html>
