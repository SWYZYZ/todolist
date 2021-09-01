<template>
  <div style="text-align: center">
    <el-transfer
      v-model="rightValue"
      style="text-align: left; display: inline-block"
      filterable
      :titles="['全选', '全选']"
      :button-texts="['未做', '已做']"
      :format="{
        noChecked: '${total}',
        hasChecked: '${checked}/${total}',
      }"
      :data="data"
      @left-check-change="checkfunN"
      @right-check-change="checkfunY"
      @change="handleChange"
    >
      <template #default="{ option }">
        <input
          type="text"
          :id="option.key"
          @input="input"
          :value="option.label"
        />
      </template>
      <template #left-footer>
        <el-button class="transfer-footer" size="small" @click="insertN()"
          >添加未做</el-button
        >
        <el-button class="transfer-footer" size="small" @click="moveN()"
          >已做</el-button
        >
        <el-button class="transfer-footer" size="small" @click="destroyN()"
          >删除所选</el-button
        >
      </template>
      <template #right-footer>
        <el-button class="transfer-footer" size="small" @click="insertY()"
          >添加已做</el-button
        >
        <el-button class="transfer-footer" size="small" @click="moveY()"
          >未做</el-button
        >
        <el-button class="transfer-footer" size="small" @click="destroyY()"
          >删除所选</el-button
        >
      </template>
    </el-transfer>
  </div>
</template>

<script>
let self;
export default {
  data() {
    return {
      data: [],
      rightValue: [],
      selectN: [],
      selectY: [],
    };
  },
  watch: {
    rightValue() {
      self.savedata();
    },
  },
  methods: {
    handleChange(value, direction, movedKeys) {
      console.log(value, direction, movedKeys);
    },
    input(value) {
      console.log(value.target.value.length)
      self.data[Number(value.target.id) - 1].label = value.target.value;
      self.savedata();
    },
    insertN() {
      let keyv = 1;
      let keys = [];
      for (let i = 0; i < self.data.length; i++) {
        keys.push(self.data[i].key);
      }
      for (let i = 0; i < keys.length; i++) {
        if (keys.indexOf(keyv) != -1) {
          keyv = i + 2;
        }
      }
      let da = {
        key: keyv,
        label: "",
        disabled: false,
      };
      self.data.push(da);
      self.savedata();
    },
    insertY() {
      let da = {
        key: self.data.length + 1,
        label: "",
        disabled: false,
      };
      self.data.push(da);
      self.rightValue.push(da.key);
      self.savedata();
    },
    moveN(){
      for(let i of self.selectN){
        self.rightValue.push(i)
      }
      self.savedata();
    },
    moveY(){
      for(let i of self.selectY){
        console.log(1)
        self.rightValue.splice(self.rightValue.indexOf(i),1)
      }
      self.savedata();
    },
    savedata() {
      let da = JSON.stringify(self.data);
      let ny = JSON.stringify(self.rightValue);
      localStorage.setItem("data", da);
      localStorage.setItem("ny", ny);
    },
    checkfunN(value) {
      self.selectN = self.getObjectValues(value);
    },
    checkfunY(value) {
      self.selectY = self.getObjectValues(value);
    },
    destroyN() {
      for (let i of self.selectN) {
        for (let o = 0; o < self.data.length; o++) {
          if (self.data[o].key == i) {
            self.data.splice(o, 1);
          }
        }
      }
      self.savedata();
    },
    destroyY() {
      for (let i of self.selectY) {
        for (let o = 0; o < self.data.length; o++) {
          if (self.data[o].key == i) {
            self.data.splice(o, 1);
          }
        }
        for (let o = 0; o < self.rightValue.length; o++) {
          if (self.rightValue[o] == i) {
            self.rightValue.splice(o, 1);
          }
        }
      }
      self.savedata();
    },
    getObjectValues(object) {
      var values = [];
      for (var property in object) values.push(object[property]);
      return values;
    },
  },
  created() {
    self = this;
  },
  mounted() {
    if (JSON.parse(localStorage.getItem("data"))) {
      self.data = JSON.parse(localStorage.getItem("data"));
    }
    if (JSON.parse(localStorage.getItem("ny"))) {
      self.rightValue = JSON.parse(localStorage.getItem("ny"));
    }
  },
};
</script>
<style>
.transfer-footer {
  margin-left: 20px;
  padding: 6px 5px;
}
.el-transfer-panel {
  width: 50vw !important;
  height: calc(100vh - 40px) !important;
  margin: 0 !important;
  box-sizing: border-box !important;
}
.el-transfer-panel:first-child input[type="text"]{
  color: rgb(34, 0, 6);

}
.el-transfer-panel:last-child input[type="text"]{
  color: rgb(253, 9, 50);

}
.el-transfer-panel input[type="text"]{
  border: 1px solid pink;
  height: 18px;
  padding: 4px;
  border-radius: 4px;
  width: 80%;
}
.el-transfer__buttons{
  display: none !important;
  width: 30vw !important;
  margin: 0 auto !important;
  text-align: center;
  display: inline-block;
  box-sizing: border-box !important;
  font-size: 12px !important;
}
.el-transfer__buttons button{
  width: 20%;
  padding: 10px !important;
  margin: 0 10px;
}
.el-transfer-panel__header{
  height: 40px !important;
}
.el-transfer-panel .el-transfer-panel__footer{
    top: 0 !important;
    left: unset !important;
    right: 0 !important;
    width: auto !important;
    height: 20px !important;
    line-height: 40px;
    margin: 0 auto;
    background: none !important;
}
.el-transfer-panel .el-transfer-panel__footer>*{
  height: 100% !important;
}
.el-transfer-panel__body{
  height: calc(92vh - 120px) !important;

}
.el-transfer-panel__list{
  height: calc(92vh - 120px) !important;
}
</style>