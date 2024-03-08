<template>
  <div id="supplierInfo">
    <p class="Location">
      <a href="/dashboard/home" class="btn_set home"></a>
      <span class="btn_nav bold">기준정보</span>
      <span class="btn_nav bold">공급처관리</span>
      <a href="../scm/supplierInfo.vue" class="btn_set refresh">새로고침</a>
    </p>
    <p class="conTitle">
      <span>공급처 정보</span>
      <span>
        <table
          style="border: 1px #50bcdf;"
          width="100%"
          cellpadding="5"
          cellspacing="0"
          border="1"
          align="left"
        >
          <tr style="border: 0px; border-color: blue">
            <td
              width="50"
              height="25"
              style="font-size: 100%; text-align: left"
            >
              <div id="searchArea" class="d-flex justify-content">
                <select
                  id="searchKey"
                  class="form-control"
                  style="width: 100px"
                  v-model="searchKey"
                >
                  <option value="all">전체</option>
                  <option value="supply_nm">공급처명</option>
                  <option value="supply_mng_nm">담당자명</option>
                </select>

                <input
                  type="text"
                  style="width: 350px"
                  class="form-control"
                  v-model="keyword"
                />

                <span class="fr">
                  <a
                    @click="searchSupplierList"
                    class="btn btn-primary mx-2"
                    id="search_button"
                    name="btn"
                  > 
                    <span>검 색</span>  
                  </a>
                  <a
                    class="btn btn-primary mx-2"
                    @click="saveSupplier()"
                    name="modal"
                  >
                    <span>신규등록</span>
                  </a>
                </span>
              </div>
            </td>
          </tr>
        </table>
      </span>
    </p>

    <div class="divNoticeList">
      <table class="col">
        <caption>
          caption
        </caption>
        <colgroup>
            <col width="10%">
            <col width="10%">
            <col width="17%">
            <col width="10%">
            <col width="17%">
            <col width="16%">
            <col width="10%">
            <col width="10%">
        </colgroup>

        <thead>
          <tr>
            <th scope="col">공급처코드</th>
            <th scope="col">공급처명</th>
            <th scope="col">연락처</th>
            <th scope="col">담당자명</th>
            <th scope="col">담당자 연락처</th>
            <th scope="col">이메일</th>
            <th scope="col">창고명</th>
            <th scope="col">비고</th>
          </tr>
        </thead>
        <tbody>
          <template v-if=" supplierInfoItems.length == 0">
            <tr>
              <td colspan="8">일치하는 검색 결과가 없습니다</td>
            </tr>
          </template>
          <template v-else>
            <tr v-for="item in supplierInfoItems" :key="item.supply_cd">
                <td>{{ item.supply_cd }}</td>
                <td style="cursor:pointer"><a @click="productDetail(item.supply_cd)" class="color1"
                  >{{ item.supply_nm }}</a></td>
                <td>{{ item.tel }}</td>
                <td>{{ item.supply_mng_nm }}</td>
                <td>{{ item.mng_tel }}</td>	
                <td>{{ item.email }}</td>	
                <td>{{ item.warehouse_nm }}</td>	
                <td><button @click="updateSupplier(item.supply_cd)">수정</button></td>	
            </tr>
          </template>
        </tbody>
      </table>
    </div>

    <div id="noticePagination">
      <paginate
        class="justify-content-center"
        v-model="currentPage"
        :page-count="totalPage"
        :page-range="5"
        :margin-pages="0"
        :click-handler="clickCallback"
        :prev-text="'<'"
        :next-text="'>'"
        :container-class="'pagination'"
        :page-class="'page-item'"
        :first-last-button=true
        :first-button-text="'<<'"
        :last-button-text="'>>'"
      >
      </paginate>
    </div>
  </div>

  <p class="conTitle mt50">
    <span>공급 제품정보 <span id="subTitle"></span> </span>
  </p>

  <div class="productList">
    <table class="col">
        <caption>
          caption
        </caption>
        <colgroup>
            <col width="15%">
            <col width="15%">
            <col width="23%">
            <col width="15%">
            <col width="15%">
        </colgroup>

        <thead>
          <tr>
            <th scope="col">공급처명</th>
            <th scope="col">제품코드</th>
            <th scope="col">제품명</th>
            <th scope="col">품목명</th>
            <th scope="col">장비구매액(원)/EA</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="totalCnt == 0">
            <tr>
              <td colspan="8">일치하는 검색 결과가 없습니다</td>
            </tr>
          </template>
          <template v-else>
            <tr v-for="item in productListInfo" :key="item.product_cd">
                <td>{{ item.supply_nm }}</td>
                <td>{{ item.product_cd }}</td>
                <td>{{ item.prod_nm }}</td>
                <td>{{ item.l_ct_nm }}</td>
                <td>{{ item.purchase_price }}</td>	
            </tr>
          </template>
        </tbody>
      </table>
  </div>

</template>

<script>
  import { openModal } from "jenesius-vue-modal";
  import Paginate from "vuejs-paginate-next";
  import supplierModal from "@/components/scm/supplierModal.vue"

export default {
  data: function() {
    return {
      supplierInfoItems: [],
      productListInfo: [],
      option: '',
      searchKey: 'all',
      keyword: '',
      currentPage: 1,
      pageSize: 5,
      totalPage: 1,
      totalCount: 0,
      action: '',
      supplyCode:'',
    };
  },
  components: {
    paginate: Paginate
  },
  mounted() {
    this.searchSupplierInfo();
  },
  methods: {
    searchSupplierInfo: function(pageNumber) {
      if(!pageNumber) {
        this.currentPage = 1;
      } else {
        this.currentPage = pageNumber;
      }
      this.searchSupplierList();
    },

    searchSupplierList: function() {
      let params = new URLSearchParams();
      params.append("currentPage", this.currentPage);
      params.append("pageSize", this.pageSize);
      params.append("oname", this.searchKey);
      params.append("sname", this.keyword);

      this.axios
        .post("/scm/listSupplier.do", params)
        .then((response) => {
          console.log(response.data);
          this.totalCount = response.data.totalSupplier;
          this.supplierInfoItems = response.data.listSupplierModel;
          this.totalPage = this.page();
        })
        .catch((error) => {
          alert("에러! API 요청에 오류가 있습니다." + error);
        });
    },

    productDetail: function(supply_cd) {
      let params = new URLSearchParams();
      params.append("supply_cd", supply_cd);

      this.axios
        .post("/scm/listSupplierProduct.do", params)
        .then((response) => {
          console.log(response.data);
          this.productListInfo = response.data.listSupplierProductModel;
        })
        .catch((error) => {
          alert('에러! API 요청에 오류가 있습니다.' + error);
        });
    },

    saveSupplier: async function() {
      const modal = await openModal(supplierModal, {
        title: "공급처 관리 - 등록",
        action_value: "I"
      })
    },

    updateSupplier: async function(supply_cd) {
      const modal = await openModal(supplierModal, {
        title: "공급처 관리 - 수정",
        action_value : "U",
        supply_code : supply_cd
      })
    },

    
    clickCallback: function(pageNumber) {
      console.log(pageNumber);
      this.currentPage = pageNumber;

      this.searchSupplierList();
    },

    page : function() {
      let totalCount = this.totalCount;
      let pageSize = this.pageSize;
      let pageIndex = totalCount % pageSize;
      let result = parseInt(totalCount / pageSize);

      if(pageIndex == 0) {
        return result;
      } else {
        result = result + 1;
        return result;
      }
    }
  }
}
</script>

<style>
  #searchArea {
    margin-top: 25px;
    margin-bottom: 25px;
  }
  #searchArea > * {
    height: auto;
  }

  #groupTitle {
    text-decoration: underline;
    font-weight: bold;
    cursor: pointer;
  }
</style>