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
            <input type="text" class="form-control" name="supply_nm" id="supply_nm" maxlength="100">
          </td>
        </tr>
        <tr>
          <th scope="row">공급처코드<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="supply_cd"
            id="supply_cd" maxlength="20">
          </td>
          <th scope="row">연락처<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="tel"
            id="tel" maxlength="20">
          </td>
        </tr>
        <tr>
          <th scope="row">담당자명<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="supply_mng_nm"
            id="supply_mng_nm" maxlength="50">
          </td>
          <th scope="row">담당자 연락처<span class="font_red">*</span></th>
          <td>
            <input type="text" class="form-control" name="mng_tel"
            id="mng_tel" maxlength="20">
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
            <select id="warehouse_nm" name="warehouse_nm" class="form-control" @change="change($event)">
              <option v-for="item in selectList" 
              :key="item.cd" 
              :value="item.cd">
                {{item.name}}
              </option>
            </select>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="btn_areaC mt30">
      <button @click="save()" class="btn btn-primary" id="saveBtn" 
      name="saveBtn">
        저장
      </button>
      <button @click="cancel()" class="btn btn-primary mx-2" id="cancelBtn" name="cancelBtn">취소</button>

    </div>

  </dl>
</template>

<script>
import {closeModal} from 'jenesius-vue-modal';
export default {
  props: {
    title: String
  }, 
  data: function() {
    return {
      selectList:[],
      supply_nm: '',
      supply_cd:'',
      tel:'',
      supply_mng_nm:'',
      mng_tel:'',
      email:'',
      warehouse_cd:'',
      warehouse_nm:'',

    }
  },
  mounted: function() {
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
      if(confirm("해당 공급처를 저장하시겠습니까?")) {
        let param = new URLSearchParams();


      }
    },

    cancel: function() {
      closeModal(this);
    },

    change: function(event) {
      console.log(event.target.value);
      this.warehouse_cd = event.target.value;
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