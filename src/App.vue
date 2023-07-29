<template>
<div class="home">
  <el-calendar v-model="value" />

</div>
<tinymceVue/>
</template>

<script setup lang='ts'>
import tinymce from 'tinymce'
import {ref,reactive} from 'vue'
import tinymceVue from './components/tinymce.vue'
const value = ref(new Date())

console.log(value);


</script>
<style lang='less' scoped>
.home{
  width:500px;
  height: 500px;
  margin: 0 auto
}
.el-calendar-table .el-calender-day {
  height:37px;
  padding:0;
  line-height: 37px;
  font-size: 20px;
  color:red;
}
</style>