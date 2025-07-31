<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>완수 ♥ 성지 완성데이트 - 최소 예시</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2"></script>
  <style>
    body { font-family: sans-serif; margin: 20px; }
    select, input, textarea, button { display: block; width: 300px; margin: 8px 0; padding: 8px; }
    .image-preview { max-width: 300px; margin-top: 10px; }
  </style>
</head>
<body>

<div id="app">
  <h3>메인 지역 선택</h3>
  <select v-model="selectedRegion">
    <option disabled value="">지역을 선택하세요</option>
    <option v-for="region in regions" :key="region.name" :value="region.name">{{ region.name }}</option>
  </select>
  <input v-model="newRegion" placeholder="새 지역 이름 입력" />
  <button @click="addRegion">지역 추가</button>

  <div v-if="selectedRegion">
    <h3>세부 지역 선택</h3>
    <select v-model="selectedSubregion">
      <option disabled value="">세부 지역을 선택하세요</option>
      <option v-for="sub in getSubregions" :key="sub" :value="sub">{{ sub }}</option>
    </select>
    <input v-model="newSubregion" placeholder="새 세부 지역 이름 입력" />
    <button @click="addSubregion">세부 지역 추가</button>
  </div>

  <div v-if="selectedSubregion">
    <h3>여행기록 작성</h3>
    <input v-model="recordTitle" placeholder="제목 입력" />
    <textarea v-model="recordContent" placeholder="내용 입력"></textarea>
    <input type="file" @change="handleImageUpload" ref="fileInput" />
    <img v-if="imageDataUrl" :src="imageDataUrl" class="image-preview" />
    <button @click="submitRecord">{{ editingIndex !== null ? '수정 완료' : '기록 추가' }}</button>
  </div>

  <div v-if="selectedSubregion">
    <h3>여행기록 목록</h3>
    <div v-for="(record, index) in getRecords" :key="index" style="border: 1px solid #ccc; padding: 10px; margin-top: 8px;">
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
      regions: [{ name: '서울', subregions: ['강남', '홍대'] }],
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
        if(this.newRegion && !this.regions.some(r => r.name === this.newRegion)) {
          this.regions.push({name: this.newRegion, subregions: []});
          this.selectedRegion = this.newRegion;
          this.newRegion = '';
          this.selectedSubregion = '';
        }
      },
      addSubregion() {
        const region = this.regions.find(r => r.name === this.selectedRegion);
        if(region && this.newSubregion && !region.subregions.includes(this.newSubregion)) {
          region.subregions.push(this.newSubregion);
          this.selectedSubregion = this.newSubregion;
          this.newSubregion = '';
        }
      },
      submitRecord() {
        if(!this.recordTitle.trim()) { alert('제목 입력하세요'); return; }
        if(!this.recordContent.trim()) { alert('내용 입력하세요'); return; }
        const key = this.selectedRegion + '_' + this.selectedSubregion;
        if(!this.records[key]) this.records[key] = [];
        const newRecord = { title: this.recordTitle, content: this.recordContent, image: this.imageDataUrl };
        if(this.editingIndex !== null) {
          this.records[key].splice(this.editingIndex,1,newRecord);
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
        this.records[key].splice(index,1);
        if(this.editingIndex === index) this.editingIndex = null;
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
        if(file) {
          const reader = new FileReader();
          reader.onload = e => this.imageDataUrl = e.target.result;
          reader.readAsDataURL(file);
        }
        this.$refs.fileInput.value = '';
      }
    }
  })
</script>

</body>
</html>
