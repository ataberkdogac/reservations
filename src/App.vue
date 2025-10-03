<script setup lang="ts">
import 'gridjs/dist/theme/mermaid.css'
import { ref, onMounted, watchEffect, effect } from 'vue'
import myModal from './components/v1/vifs/myModal.vue'

import { reactive } from 'vue'
import axios from 'axios'

var reservations = ref([])
var hotels = ref([])

watchEffect(() => {
  axios.defaults.baseURL = 'https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1'
})

onMounted(() => {
  fetchHotels()
  fetchData()
})

const fetchData = async () => {
  axios.get('/reservations').then(function (response) {
    reservations.value = response.data
  })
}

const fetchHotels = async () => {
  axios.get('/hotels').then(function (response) {
    hotels.value = response.data
  })
}

function getst(st: any) {
  if (st === 1) {
    return 'Approved'
  } else if (st === 2) {
    return 'Pending'
  } else if (st === 3) {
    return 'Canceled'
  } else {
    return 'Undefined'
  }
}

function closeB(id: string) {
  axios.delete('/reservations/' + id).then(function () {
    fetchData()
  })
}

function editB(id: string) {
  axios.get('reservations/' + id).then(function (response) {
    fillModal(response.data)
  })
}

function fillModal(data: any) {
  console.log('in modal to edit')
  var mod = document.getElementById('myModal')
  mod.style.display = 'block'
  document.getElementById('name').value = data.name
  document.getElementById('surname').value = data.surname
  document.getElementById('start_date').value = data.start_date
  document.getElementById('end_date').value = data.end_date
  document.getElementById('total_fee').value = data.total_fee
  if (data.status === 1) {
    document.getElementById('appr').checked = true
  } else if (data.status === 2) {
    document.getElementById('pend').checked = true
  } else {
    document.getElementById('cacl').checked = true
  }

  document.getElementById('hotid').value = gethotid(data.hotel_id)
  var cbut = document.getElementById('closeModal')
  cbut.onclick = function () {
    updateList(data)
  }
}

function emptyModal() {
  var inpList = document.getElementsByTagName('input')
  for (var i = 0; i < inpList.length; i++) {
    inpList[i].value = null
  }
  console.log('in modal for add')
  var mod = document.getElementById('myModal')
  mod.style.display = 'block'
  var cbut = document.getElementById('closeModal')
  cbut.onclick = function () {
    newRes()
  }
}

function gethotid(hotid: number) {
  for (var i = 0; i < hotels.value.length; i++) {
    if (hotels.value[i].id === hotid.toString()) {
      return hotels.value[i].name
    } else if (hotid > hotels.value.length) {
      return 'Undefined'
    }
  }
}

function dataFromModal(data: object){
  data.name = document.getElementById('name').value
  data.surname = document.getElementById('surname').value
  data.start_date = document.getElementById('start_date').value
  data.end_date = document.getElementById('end_date').value
  data.total_fee = document.getElementById('total_fee').value
  if (document.getElementById('appr')) {
    data.status = 1
  } else if (document.getElementById('pend')) {
    data.status = 2
  } else {
    data.status = 3
  }
  return data
}

function updateList(data: object) {
  var mod = document.getElementById('myModal')
  mod.style.display = 'none'
  data = dataFromModal(data);
  console.log('/reservations/' + data.id)
  axios({
  method: 'put',
  url: '/reservations/' + data.id,
  data: data,
}).then(function(){
  fetchData()
})
  console.log(data)
  console.log('exit from edit')
}

function newRes() {
  var data = new Object
  data = dataFromModal(data)
  var mod = document.getElementById('myModal')
  mod.style.display = 'none'
  axios({
  method: 'post',
  url: '/reservations',
  data: data,
}).then(function(){
  fetchData()
})
  console.log(data)
  console.log('exit from newres')
}
</script>

<template>
  <my-modal id="myModal" class="myModal"> </my-modal>

  <table>
    <tr>
      <th>Name</th>
      <th>Surname</th>
      <th>Start Date</th>
      <th>End Date</th>
      <th>Total Fee</th>
      <th>Status</th>
      <th>Hotel</th>
      <th><button id="addBtn" @click="emptyModal()">ADD</button></th>
    </tr>

    <tr v-for="item in reservations">
      <td>{{ item.name }}</td>
      <td>{{ item.surname }}</td>
      <td>{{ item.start_date }}</td>
      <td>{{ item.end_date }}</td>
      <td>{{ item.total_fee }}</td>
      <td>{{ getst(item.status) }}</td>
      <td>{{ gethotid(item.hotel_id) }}</td>
      <td><button @click="editB(item.id)">Edit</button></td>
      <td><button @click="closeB(item.id)">X</button></td>
    </tr>
  </table>
</template>

<style>

</style>
