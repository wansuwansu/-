<!DOCTYPE html>
<html lang="ko">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>완수 ♥ 성지 완성데이트</title>
    <script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
    <link href="https://fonts.googleapis.com/css2?family=Nanum+Gothic&display=swap" rel="stylesheet">
    <style>
      body {
        font-family: 'Nanum Gothic', sans-serif;
        background-color: #f4f6f9;
        margin: 0;
        padding: 20px;
        color: #333;
      }
      h1 {
        text-align: center;
        color: #d16d7a;
        margin-bottom: 40px;
      }
      .region-selector, .subregion-selector, .record-form, .record-list {
        max-width: 800px;
        margin: 0 auto 30px auto;
        background: white;
        border-radius: 16px;
        padding: 20px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      }
      select, input, textarea, button {
        width: 100%;
        padding: 12px;
        margin: 8px 0;
        border-radius: 8px;
        border: 1px solid #ccc;
        box-sizing: border-box;
      }
      button {
        background-color: #d16d7a;
        color: white;
        font-weight: bold;
        cursor: pointer;
        transition: background 0.3s ease;
      }
      button:hover {
        background-color: #b35564;
      }
      .record-item {
        background: #fafafa;
        border: 1px solid #ddd;
        border-radius: 12px;
        padding: 16px;
        margin-top: 12px;
      }
      .record-item button {
        width: auto;
        margin-right: 10px;
        margin-top: 8px;
      }
      .image-preview {
        max-width: 100%;
        margin-top: 10px;
        border-radius: 10px;
      }
    </style>
  </head>
  <body>
    <div id="app">
      <h1>완수 ♥ 성지 완성데이트</h1>

      <!-- 지역 선택 -->
      <div class="region-selector">
        <h3>메인 지역 선택</h3>
        <select v-model="selectedRegion">
          <option disabled value="">지역을 선택하세요</option>
          <option v-for="region in regions" :key="region.name">{{ region.name }}</option>
        </select>
        <input v-model="newRegion" placeholder="새 지역 이름 입력" />
        <button @click="addRegion">지역 추가</button>
      </div>

      <!-- 세부 지역 선택 -->
      <div class="subregion-selector" v-if="selectedRegion">
        <h3>세부 지역 선택</h3>
        <select v-model="selectedSubregion">
          <option disabled value="">세부 지역을 선택하세요</option>
          <option v-for="sub in getSubregions" :key="sub">{{ sub }}</option>
        </select>
        <input v-model="newSubregion" placeholder="새 세부 지역 이름 입력" />
        <button @click="addSubregion">세부 지역 추가</button>
      </div>

      <!-- 여행기록 작성 -->
      <div class="record-form" v-if="selectedSubregion">
        <h3>여행기록 작성</h3>
        <input v-model="recordTitle" placeholder="제목 입력" />
        <textarea v-model="recordContent" placeholder="내용 입력"></textarea>
        <input type="file" @change="handleImageUpload" />
        <img v-if="imageDataUrl" :src="imageDataUrl" class="image-preview" />
        <button @click="submitRecord">{{ editingIndex !== null ? '수정 완료' : '기록 추가' }}</button>
      </div>

      <!-- 여행기록 목록 -->
      <div class="record-list" v-if="selectedSubregion">
        <h3>여행기록 목록</h3>
        <div v-for="(record, index) in getRecords" :key="index" class="record-item">
          <h4>{{ record.title }}</h4>
          <p>{{ record.content }}</p>
          <img v-if="record.image" :src="record.image" class="image-preview" />
          <button @click="editRecord(index)">수정</button>
          <button @click="deleteRecord(index)">삭제</button>
        </div>
      </div>
    </div>

    <script>
      new Vue({
        el: '#app',
        data: {
          regions: [],
          selectedRegion: '',
          newRegion: '',
          selectedSubregion: '',
          newSubregion: '',
          recordTitle: '',
          recordContent: '',
          records: {},
          imageDataUrl: '',
          editingIndex: null,
        },
        computed: {
          getSubregions() {
            const region = this.regions.find(r => r.name === this.selectedRegion);
            return region ? region.subregions : [];
          },
          getRecords() {
            const key = this.selectedRegion + '_' + this.selectedSubregion;
            return this.records[key] || [];
          }
        },
        methods: {
          addRegion() {
            if (this.newRegion && !this.regions.some(r => r.name === this.newRegion)) {
              this.regions.push({ name: this.newRegion, subregions: [] });
              this.newRegion = '';
            }
          },
          addSubregion() {
            const region = this.regions.find(r => r.name === this.selectedRegion);
            if (region && this.newSubregion && !region.subregions.includes(this.newSubregion)) {
              region.subregions.push(this.newSubregion);
              this.newSubregion = '';
            }
          },
          submitRecord() {
            const key = this.selectedRegion + '_' + this.selectedSubregion;
            if (!this.records[key]) this.records[key] = [];

            const newRecord = {
              title: this.recordTitle,
              content: this.recordContent,
              image: this.imageDataUrl
            };

            if (this.editingIndex !== null) {
              this.records[key].splice(this.editingIndex, 1, newRecord);
              this.editingIndex = null;
            } else {
              this.records[key].push(newRecord);
            }

            this.recordTitle = '';
            this.recordContent = '';
            this.imageDataUrl = '';
          },
          deleteRecord(index) {
            const key = this.selectedRegion + '_' + this.selectedSubregion;
            this.records[key].splice(index, 1);
          },
          editRecord(index) {
            const record = this.getRecords[index];
            this.recordTitle = record.title;
            this.recordContent = record.content;
            this.imageDataUrl = record.image;
            this.editingIndex = index;
          },
          handleImageUpload(event) {
            const file = event.target.files[0];
            if (file) {
              const reader = new FileReader();
              reader.onload = e => {
                this.imageDataUrl = e.target.result;
              };
              reader.readAsDataURL(file);
            }
          }
        }
      });
    </script>
  </body>
</html>
