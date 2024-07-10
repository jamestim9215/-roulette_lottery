<script setup>
import { ref } from 'vue'
import Circle from './components/circle.vue'

const prizeData = ref([
  {
    name: '一獎',
  },
  {
    name: '二獎',
  },
  {
    name: '三獎',
  },
])

const deletePrize = (index) => {
  prizeData.value.splice(index, 1);
}

const addPrize = () => {
  prizeData.value.push({
    name: ''
  })
}

</script>

<template>
  
  <Circle :prize-data="prizeData"/>
  <div class="table-div">
    <table>
      <tr>
        <th>#</th>
        <th>獎項</th>
        <th></th>
      </tr>
      <tr v-for="(item, index) in prizeData" :key="index">
        <td>{{ index+1 }}</td>
        <td>
          <input type="text" v-model="item.name">
        </td>
        <td>
          <button @click="deletePrize(index)">刪除</button>
        </td>
      </tr>
      <tr>
        <td colspan="3">
          <button @click="addPrize">新增獎項</button>
        </td>
      </tr>
    </table>
  </div>
</template>

<style lang="scss" scoped>
  .table-div {
    position: relative;
    margin: 0 auto;
    width: 100%;
    max-width: 400px;
    height: 100%;
    background: #f5f5f5;
    margin-bottom: 15px;
    table{
      width: 100%;
      border-collapse: collapse;
      th, td {
        border: 1px solid #ddd;
        padding: 8px;
        text-align: center;
      }
    }
    input{
      width: calc(100% - 16px);
      padding: 8px;
      border: none;
      background: #fff;
      &:focus{
        outline: none;
      }
    }
    button{
      padding: 8px;
      border: none;
      background: orange;
      color: #fff;
      cursor: pointer;
      &:focus{
        outline: none;
      }
    }
  }
</style>
