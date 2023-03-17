<template>
    <div>
      <table>
        <tr>
          <td align="right">
            <span class="content01"> Search option</span>
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
              <option v-for="request in requestList" :value="request.managernm" :key="request.managernm">{{ request.managernm }}</option>
            </select>
          </td>
          <td>
            <button @click="search">검색</button>
          </td>
        </tr>
      </table>
    </div>
</template>



<script>
import axios from 'axios';

export default {
  data() {
    return {
      requestList: [],
      factList: [],
      selectedManager: '',
      selectedFact: ''
    };
  },
  mounted() {
    this.getData();
  },
  methods: {
    getData() {
    
      axios.get('http://localhost:8085/requests')
  .then(response => {
    this.requestList = response.data.request_list;
    this.factList = response.data.fact_list;
    // 추출한 requestList를 이용한 로직 작성
  })
  .catch(error => {
    console.error(error);
  });
},
search() {
      // 선택된 사업장과 담당자 이름 가져오기
      const factnm = this.selectedFact;
      const managernm = this.selectedManager;

      // AJAX 요청 보내기
      axios.get('http://localhost:8085/requests', {      
        params: {
          factnm,
          managernm
        }
      })
      .then(response => {
        // 서버로부터 받은 데이터 처리하기
        const data = response.data;
        this.requestList = data.request_list;
        this.factList = data.fact_list;
      })
      .catch(error => {
        console.error(error);
      });
    }
  }
};
</script>