<template>
  <div>
    <table align="right">
      <tr>
        <td align="right">
          <span class="content01"> Search option</span>
        </td>
        <td>Date</td>
        <td align="center">
          <input type="date" name="startDate" @input="startDate = $event.target.value" v-bind:value="startDate">
          ~
          <input type="date" name="endDate" @input="endDate = $event.target.value" v-bind:value="endDate">
        </td>

        <td colspan="8">
          <label for="factnm">사업장</label>
          <select v-model="selectedFact">
            <option value="">-- option --</option>
            <option v-for="fact in factList" :value="fact.factnm" :key="fact.factnm">{{ fact.factnm }}</option>
          </select>
        </td>
        <td align="rigth">
          <label for="managernm">담당자</label>
          <select v-model="selectedManager">
            <option value="">-- option --</option>
            <option v-for="request in mList" :value="request.managernm" :key="request.managernm">{{ request.managernm }}</option>
          </select>
        </td>
        <td>
          <button @click="searchData">검색</button>
        </td>
      </tr>
    </table>
 
    <table width="100%" color="#aaa" cellpadding="0" cellspacing="0" border="1" style="border-color:#eee">
      <tr bgcolor="#333" align="center" height="30" style="color:#ccc">
        <th style="width: 100px; min-width: 100px;">요청번호</th>
        <th style="width: 100px; min-width: 100px;">사업장</th>
        <th style="width: 200px; min-width: 200px;">업무구분</th>
        <th style="width: 100px; min-width: 100px;">요청자</th>
        <th style="width: 100px; min-width: 100px;">요청일</th>
        <th style="width: 600px; min-width: 600px;">요청내역</th>
        <th style="width: 100px; min-width: 100px;">담당자</th>
        <th style="width: 600px; min-width: 600px;">처리결과</th>
        <th style="width: 100px; min-width: 100px;">처리완료일</th>
        <!-- <th style="width: 100px; min-width: 100px;">처리기간</th> -->
        <th style="width: 100px; min-width: 100px;">작업시간</th>

      </tr>
      <tr v-for="(row, index) in requestList" :key="index" @dblclick="editRow(index)">
      <td v-if="!row.isEditing">{{ row.requestno }}</td>
      <td v-else>{{ row.requestno }}</td>
      
      <td v-if="!row.isEditing">{{ row.factnm }}</td>
      <td v-else>
      <select v-model="row.selectedFactnm">
        <option value="">--선택--</option>
        <option v-for="fact in factList" :key="fact.factnm" :value="fact.factnm">{{ fact.factnm }}</option>
        </select>  
      </td>
      <td v-if="!row.isEditing">{{ row.requestgr }}</td>
      <td v-else><input type="text" v-model="row.requestgr"></td>
      <td v-if="!row.isEditing">{{ row.requesternm }}</td>
      <td v-else><input type="text" v-model="row.requesternm"></td>
      <td v-if="!row.isEditing">{{ row.requestdate }}</td>
      <td v-else><input type="date" v-model="row.requestdate"></td>
      <td v-if="!row.isEditing">{{ row.requestcomment }}</td>
      <td v-else><textarea v-model="row.requestcomment" rows="5" cols="100"></textarea></td>
      <td v-if="!row.isEditing">{{ row.managernm }}</td>
      <td v-else>
      <select v-model="row.selectedManagernm">
        <option value="">--선택--</option>
        <option v-for="request in mList" :value="request.managernm" :key="request.managernm">{{ request.managernm }}</option>
        </select>  
      </td>
      <td v-if="!row.isEditing">{{ row.resultstat }}</td>
      <td v-else><textarea v-model="row.resultstat" rows="5" cols="100"></textarea></td>
      <td v-if="!row.isEditing">{{ row.enddate }}</td>
      <td v-else><input type="date" v-model="row.enddate"></td>
      <!-- <td v-if="!row.isEditing">{{ row.processingtime }}</td>
      <td v-else><input type="text" v-model="row.processingtime"></td> -->
      <td v-if="!row.isEditing">{{ row.note }}</td>
      <td v-else><input type="text" v-model="row.note"></td>
      </tr>
    </table>  

    <table>
      <tr>
        <td>
          <button @click="addRow">추가</button>
        </td>
        <td>
          <button @click="insertRow">저장</button>
        </td>
        <td>
          <button @click="delRow">삭제</button>
        </td>
      </tr>
    </table>
    
  </div>
</template>



<script>
import axios from 'axios';

export default {
name: 'RequestsData',
components: {},
data() {
  return {
    requestList: [],
    addRequestList: [],
    factList: [],
    selectedFact: '',
    selectedManager: '',
    mList:[],
    startDate: this.startDate,
    endDate: this.endDate      
  };
},
created() {
  this.getData();
},
methods: {
  getData() {
axios
  .get('http://localhost:8085/requests')
  .then((response) => {
    this.requestList = response.data.request_list;
    this.factList = response.data.f_list;
    this.mList = response.data.m_list; 
    this.requestList = response.data.request_list;
  })
  .catch((error) => {
    console.error(error);
  });
},
  searchData() {
    // console.log("selectedFact: ", this.selectedFact);
    // console.log("selectedManager: ", this.selectedManager);
    // console.log("startDate: ", this.startDate);
    // console.log("endDate: ", this.endDate);

  axios
  .get('http://localhost:8085/requests', {
    params: {
      factnm: this.selectedFact,
      managernm: this.selectedManager,
      sdate: this.startDate,
      edate: this.endDate
    },
  })
  .then((response) => {
    this.requestList = response.data.request_list;
  })
  .catch((error) => {
    console.error(error);
  });
},

  addRow() {  

  const newRow = {
  requestno: '입력',
  factnm: '',
  requestgr: '',
  requesternm: '',
  requestdate: '',
  requestcomment: '',
  managernm: '',
  resultstat: '',
  enddate: '',
  processingtime: '',
  note: '',
  isEditing: true, // 추가
  
};
this.requestList.push(newRow);
},

editRow(index) {
  // 현재 편집중인 row가 없으면, 선택한 row를 편집중인 row로 지정
  if (!this.currentEditingRow) {
    this.requestList[index].isEditing = true;
    this.currentEditingRow = this.requestList[index];
  } else {
    // 다른 row를 편집중인 row로 지정할 때, 이전에 편집중이었던 row는 저장하지 않고 취소
    const prevEditingRow = this.currentEditingRow;
    this.cancelRow(prevEditingRow);
    this.requestList[index].isEditing = true;
    this.currentEditingRow = this.requestList[index];
  }
},

insertRow() {  
  // 새로운 데이터만 모아서 객체로 만들기
  const newRequestList = this.requestList.filter(row => row.isEditing);
  const requestData = {
    requestList: newRequestList.map(row => ({
      requestno: row.requestno,
      factnm: row.selectedFactnm,
      requestgr: row.requestgr,
      requesternm: row.requesternm,
      requestdate: row.requestdate,
      requestcomment: row.requestcomment,
      managernm: row.selectedManagernm,
      resultstat: row.resultstat,
      enddate: row.enddate,
      processingtime: row.processingtime,
      note: row.note,
    }))
  };
  console.log(requestData); // 확인을 위해 requestData 출력
  // 새로운 데이터만 서버로 보내기
  axios.post('http://localhost:8085/update', requestData)
  .then(() => {
    alert('업데이트 성공');
    this.getData();
    })
    .catch((error) => {
      console.error(error);
    });
}
}
};
</script>