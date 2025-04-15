<template>
  <div class="custom-card">
    <div class="custom-card-body">
      <div class="btn-group">
        <button type="button" class="btn btn-primary" @click="addData">Add</button>
        <button type="button" class="btn btn-success" @click="saveData">Save</button>
        <button type="button" class="btn btn-danger" @click="getData">Update</button>
      </div>
      <div ref="tableWrapper" class="table-wrapper">
        <table class="custom-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Birthday</th>
              <th>Salary</th>
              <th>Address</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(person, index) in salaryDetailList" :key="index">
              <td><input type="text" v-model="person.Name" /></td>
              <td><input type="date" v-model="person.DateOfBirth" /></td>
              <td><input type="range" v-model="person.Salary" min="0" max="100000" /></td>
              <td><input type="text" v-model="person.Address" /></td>
            </tr>
          </tbody>
        </table>
      </div>

    </div>
  </div>

</template>
<script lang="ts" setup>
import { onBeforeUnmount, onMounted, ref, nextTick } from 'vue';
import axios from 'axios';

interface Person {
  Name: string;
  DateOfBirth: string;
  Salary: number;
  Address: string;
}

const salaryDetailList = ref<Person[]>([]);
const tableWrapper = ref<HTMLElement | null>(null);

const getData = () => {
  axios.get('http://nexifytw.mynetgear.com:45000/api/Record/GetRecords')
    .then(response => {
      if (response.data.Success) {
        const salaryList = response.data.Data.map((person) => {
          return {
            Name: person.Name,
            DateOfBirth: person.DateOfBirth.split('T')[0],
            Salary: person.Salary,
            Address: person.Address
          };
        })

        const existingNames = new Set(salaryDetailList.value.map(p => p.Name));
        const newPeople = salaryList.filter(p => !existingNames.has(p.Name));

        salaryDetailList.value.push(...newPeople);
      }
    })
    .catch(error => {
      console.error('取得資料失敗：', error);
    });
}

const addData = () => {
  salaryDetailList.value.push({
    Name: '',
    DateOfBirth: new Date().toISOString().split('T')[0],
    Salary: 0,
    Address: ''
  })
};

const saveData = () => {
  axios.post('http://nexifytw.mynetgear.com:45000/api/Record/SaveRecords',
    salaryDetailList.value,
    {
      headers: {
        'Content-Type': 'application/json',
      },
    }
  )
    .then(response => {
      if (response.data.Success) {
        console.log('存取資料成功');
        getData();
      }
    })
    .catch(error => {
      console.error('存取資料失敗：', error);
    });
};

const setWidth = () => {
  if (tableWrapper.value) {
    const screenWidth = window.innerWidth;
    tableWrapper.value.style.width = `${screenWidth - 120}px`;
  }
};

onMounted(() => {
  nextTick(() => {
    setWidth();
  });
  window.addEventListener('resize', setWidth);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', setWidth);
})
</script>