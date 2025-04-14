<template>
<div class="card custom-card">
  <div class="card-body">
    <div class="d-flex justify-content-between gap">
      <button type="button" class="btn btn-primary">Add</button>
      <button type="button" class="btn btn-success">Save</button>
      <button type="button" class="btn btn-danger" @click="getData">Update</button>

      
    </div>

    <table class="table">
        <thead>
          <tr>
            <th scope="col">Name</th>
            <th scope="col">Birthday</th>
            <th scope="col">Salary</th>
            <th scope="col">Address</th>
          </tr>
        </thead>
        <tbody >
          <tr v-for="(person, index) in salaryDetailList" :key="index">
            <th scope="row">{{ person.Name }}</th>
            <td>{{ person.DateOfBirth }}</td>
            <td>{{ person.Salary }}</td>
            <td>{{ person.Address }}</td>
          </tr>
        </tbody>
      </table>
  </div>
</div>
</template>
<script lang="ts" setup>
import { ref } from 'vue';
import axios from 'axios';
const salaryDetailList  = ref();

const getData = () => {
  axios.get('http://nexifytw.mynetgear.com:45000/api/Record/GetRecords')
  .then(response => {
    console.log('取得資料成功：', response.data);
    if(response.data.Success) {
      salaryDetailList.value = response.data.Data
    }
  })
  .catch(error => {
    console.error('取得資料失敗：', error);
  });
}



</script>
<style lang="scss" scoped>
.custom-card {
  padding: 0;
}

.gap {
  gap: 300px;
}
</style>