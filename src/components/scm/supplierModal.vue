<template>
  <dl id="supplerModalWrap">
    <dd class="content"></dd>
    <table id="supplierInfo">
      <caption>
        caption
      </caption>
      <colgroup>
        <col width="120px" />
        <col width="200px" />
        <col width="120px" />
        <col width="200px" />
      </colgroup>

      <tbody>
        <tr>
          <td colspan="4" class="text-center">
            <div class="my-4">
              <strong style="font-size: 30px">{{ title }}</strong>
            </div>
          </td>
        </tr>
        <tr>
          <th scope="row">공급처명 <span class="font_red">*</span></th>
          <td colspan="3">
            <input type="text" class="form-control" name="supply_nm" id="supply_nm" maxlength="100" v-model="supply_nm">
          </td>
        </tr>
        <tr>
          <th scope="row">공급처코드<span class="font_red">*</span></th>
          <td>
            <input :readonly="action == 'U'" type="text" class="form-control" name="supply_cd"
            id="supply_cd" maxlength="20" v-model="supply_cd">
          </td>
          <th scope="row">연락처<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="tel"
            id="tel" maxlength="20" v-model="tel">
          </td>
        </tr>
        <tr>
          <th scope="row">담당자명<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="supply_mng_nm"
            id="supply_mng_nm" maxlength="50" v-model="supply_mng_nm">
          </td>
          <th scope="row">담당자 연락처<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="mng_tel"
            id="mng_tel" maxlength="20" v-model="mng_tel">
          </td>
        </tr>
        <tr>
          <th scope="row">이메일<span class="font_red">*</span></th>
          <td colspan=3>
            <input type="text" class="form-control"
						name="email" id="email" v-model="email" maxlength="100" />
          </td>
        </tr>
        <tr>
          <th scope="row">창고코드<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="warehouse_cd"
            id="warehouse_cd" v-model="warehouse_cd">
          </td>
          <th scope="row">창고명</th>
          <td>
            <select id="warehouse_nm" name="warehouse_nm" 
            class="form-control" 
            v-model="warehouse_nm" 
            @change="change($event)">
              <option v-for="item in selectList" 
              :key="item.cd" 
              :value="item.name"
              :id="item.cd">
                {{item.name}}
              </option>
            </select>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="btn_areaC mt30">
      <button v-if="action == 'I'" @click="save()" class="btn btn-primary" id="saveBtn" 
      name="saveBtn">
        저장
      </button>

      <button v-if="action == 'U'" @click="save()" class="btn btn-primary mx-2">수정</button>

      <button v-if="action == 'U'" @click="deleteSupplier" class="btn btn-danger mx-2">삭제</button>
      <button @click="cancel()" class="btn btn-primary mx-2" id="cancelBtn" name="cancelBtn">취소</button>

    </div>

  </dl>
</template>

<script>
import {closeModal} from 'jenesius-vue-modal';
export default {
  props: {
    title: String,
    supply_code: Number,
    action_value: String
  }, 
  data: function() {
    return {
      selectList:[],
      supply_nm: '',
      supply_cd: this.supply_code,
      tel:'',
      supply_mng_nm:'',
      mng_tel:'',
      email:'',
      warehouse_cd:'',
      warehouse_nm:'',
      action: this.action_value
    }
  },
  mounted: function() {

    let vm = this;

    if(vm.supply_cd == null || vm.supply_cd == "") {
      vm.supply_nm = '';
      vm.tel = '';
      vm.supply_mng_nm = '';
      vm.mng_tel = '';
      vm.email = '';
      vm.warehouse_cd = ''
      vm.warehouse_nm = ''
    } else {
      let supplierDetailParams = new URLSearchParams();
      supplierDetailParams.append("supply_cd", vm.supply_cd);

      this.axios
        .post("/scm/selectSupplier.do", supplierDetailParams)
        .then(function(response) {
          vm.supply_nm = response.data.supplierInfoModel.supply_nm;
          vm.supply_cd = response.data.supplierInfoModel.supply_cd;
          vm.tel = response.data.supplierInfoModel.tel;
          vm.supply_mng_nm = response.data.supplierInfoModel.supply_mng_nm;
          vm.mng_tel = response.data.supplierInfoModel.mng_tel;
          vm.email = response.data.supplierInfoModel.email;
          vm.warehouse_cd = response.data.supplierInfoModel.warehouse_cd;
          vm.warehouse_nm = response.data.supplierInfoModel.warehouse_nm;
        })
        .catch(error => {
          alert("에러! API 요청에 오류가 있습니다." + error);
        })
    }

    let params = new URLSearchParams();

    params.append("comtype", "wh");

    this.axios
      .post("/system/selectComCombo.do", params)
      .then(response => {
        console.log(response.data.list);
        this.selectList = response.data.list;
      })
      .catch(error => {
        alert("에러! API 요청에 오류가 있습니다." + error)
      })
  },
  methods: {
    save: function() {
      let comfirmText = '';
      let vm = this;
      if(this.action == "I") {
        comfirmText = "해당 공급처를 저장하시겠습니까?"
      } else if(this.action == 'U') {
        comfirmText = "해당 공급처를 수정하시겠습니까?"
      }

      if(confirm(comfirmText)) {
        let params = new URLSearchParams();
        params.append("action", this.action);
        params.append("supply_nm", this.supply_nm);
        params.append("supply_cd", this.supply_cd);
        params.append("tel", this.tel);
        params.append("supply_mng_nm", this.supply_mng_nm);
        params.append("mng_tel", this.mng_tel);
        params.append("email", this.email);
        params.append("warehouse_cd", this.warehouse_cd);
        params.append("warehouse_nm", this.warehouse_nm);

        this.axios
          .post("/scm/saveSupplier.do", params)
          .then(function(response) {
            console.log(response.data);
            if(vm.action == 'I') {
              alert("공급처 등록이 완료되었습니다.")
            } else if(vm.action == 'U') {
              alert("공급처 수정이 완료되었습니다.")
            }
            closeModal(this);
            window.location.reload();
          }) 
          .catch(function(error) {
            alert("에러! API 요청에 오류가 있습니다." + error);
          })
        }
    },

    deleteSupplier : function() {
      let vm = this;
      vm.action = "D";
      if(confirm("해당 공급처를 삭제하시겠습니까?")) {
        let params = new URLSearchParams();
        params.append("action", this.action);
        params.append("supply_cd", this.supply_cd);
        params.append("warehouse_cd", this.warehouse_cd);

        this.axios
          .post("/scm/saveSupplier.do", params)
          .then(function(response) {
            console.log(response.data);
            closeModal(this);
            window.location.reload();
          })
          .catch(function(error) {
            alert("에러! API 요청에 오류가 있습니다." + error);
          }) 
      }
    },

    cancel: function() {
      closeModal(this);
    },

    change: function(event){
      let vm = this
      console.log(event.target);

      for(let i = 0; i < vm.selectList.length; i++) {
        if(vm.selectList[i].name == event.target.value) {
          vm.warehouse_cd = vm.selectList[i].cd;
        }
      }
    }
  }
}
</script>

<style>
  #supplierInfo {
    border-collapse: separate;
    border-spacing: 20px;
  }
  #supplerModalWrap {
    background-color: #fff;
    padding: 3rem;
    border-radius: 10px;
    border: 2px solid rgb(59, 59, 59);
  }
</style>