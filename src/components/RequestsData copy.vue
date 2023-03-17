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
          <th style="width: 100px; min-width: 100px;">처리기간</th>
          <th style="width: 100px; min-width: 100px;">작업시간</th>
        </tr>
        <tr height="30" v-for="request in requestList" :key="request.requestno">
          <td align="center">{{ request.requestno}}</td>
          <td align="center">{{ request.factnm}}</td>
          <td align="center">{{ request.requestgr}}</td>
          <td align="center">{{ request.requesternm }}</td>
          <td align="center">{{ request.requestdate }}</td>
          <td align="center">{{ request.requestcomment }}</td>
          <td align="center">{{ request.managernm }}</td>
          <td align="center">{{ request.resultstat }}</td>
          <td align="center">{{ request.enddate }}</td>
          <td align="center">{{ request.processingtime }}</td>
          <td align="center">{{ request.note }}</td>
          
        </tr>
      </table>  

      <table>
        <tr>
          <td>
            <button @click="addRow">추가</button>
          </td>
          <td>
            <button @click="editRow">수정</button>
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
      console.log("selectedFact: ", this.selectedFact);
      console.log("selectedManager: ", this.selectedManager);
      console.log("startDate: ", this.startDate);
      console.log("endDate: ", this.endDate);

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
    // 새로운 데이터 객체 생성
    const newRow = {
    requestno: this.requestList.length + 1,
    factnm: "",
    requestgr: "",
    requesternm: "",
    requestdate: "",
    requestcomment: "",
    managernm: "",
    resultstat: "",
    enddate: "",
    processingtime: "",
    note: "",
    };
    
    // 데이터 배열에 추가
    this.requestList.push(newRow);
  }
  },
};

</script>