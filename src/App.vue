<script setup lang="ts">
import 'gridjs/dist/theme/mermaid.css'
import { ref, onMounted, watchEffect, effect } from 'vue'
import myModal from './components/v1/vifs/myModal.vue'
import axios from 'axios'

var reservations = ref([])
var hotels = ref([])
var upTri = "	&#9650";
var downTri = "&#9660";
var swCol = new Number;

watchEffect(() => {
  axios.defaults.baseURL = 'https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1'
})

onMounted(() => {
  var nameKey = 'name'
  var surKey = 'surname'
  var sdKey = 'start_date'
  var edKey = 'end_date'
  var staKey = 'status'
  var hotKey = 'hotel_id'
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

function dataFromModal(data: object) {
  data.name = document.getElementById('name').value
  data.surname = document.getElementById('surname').value
  data.start_date = document.getElementById('start_date').value
  data.end_date = document.getElementById('end_date').value
  data.total_fee = document.getElementById('total_fee').value
  if (document.getElementById('appr').checked) {
    data.status = 1
  } else if (document.getElementById('pend').checked) {
    data.status = 2
  } else {
    data.status = 3
  }
  console.log(document.getElementById("hotid").value)
  for (var i = 0; i < hotels.value.length; i++ ){
    console.log(hotels.value[i])
    if (hotels.value[i].name === document.getElementById("hotid").value){
      
      var x = i + 1
      data.hotel_id = x;
    }
  }
  return data
}

function updateList(data: object) {
  var mod = document.getElementById('myModal')
  mod.style.display = 'none'
  data = dataFromModal(data)
  axios({
    method: 'put',
    url: '/reservations/' + data.id,
    data: data,
  }).then(function () {
    fetchData()
  })
  console.log(data)
  console.log('exit from edit')
}

function newRes() {
  var data = new Object()
  data = dataFromModal(data)
  var mod = document.getElementById('myModal')
  mod.style.display = 'none'
  axios({
    method: 'post',
    url: '/reservations',
    data: data,
  }).then(function () {
    fetchData()
  })
  console.log(data)
  console.log('exit from newres')
}

function sortKey(int: number) {
  
  var keys = Object.keys(reservations.value[0])
  var key = keys[int]

  reservations.value.sort(function (a, b) {
    if (a[key] < b[key]) {
      return -1
    }
    if (a[key] > b[key]) {
      return 1
    }
    return 0
  })
  
  if (swCol === int){
    swCol = -1 * int
    reservations.value.reverse();
  } else {
    swCol = int
  }

  
}

function sortFee(key: number){
  reservations.value.sort(function (a, b) {
    if (parseInt(a.total_fee) < parseInt(b.total_fee)) {
      return -1
    }
    if (parseInt(a.total_fee) > parseInt(b.total_fee)) {
      return 1
    }
    return 0
  })

  if (swCol === key){
    swCol = -1 * key
    reservations.value.reverse();
  } else {
    swCol = key
    
  }
}

function search(){
  var txt = document.getElementById("searchbar").value
  console.log(txt)
  var newArr = new Array
  for (var i = 0; i < reservations.value.length; i++){
    if(Object.values(reservations.value[i].name).includes(txt)){
      newArr.push(reservations.value[i])
    } else {
      reservations.value[i].search = 1;
    }
  }
  
}
</script>

<template>
  <my-modal id="myModal" class="myModal"></my-modal>

  <input id="searchbar" placeholder="Search..." @change="search()"></input>  
  <table>
    <tr>
      <th @click="sortKey(1)">Name <span v-if="swCol === 1">&#9660</span><span v-else-if="swCol === -1">&#9650</span></th>
      <th @click="sortKey(2)">Surname <span v-if="swCol === 2">&#9660</span><span v-else-if="swCol === (-2)">&#9650</span></th>
      <th @click="sortKey(3)">Start Date <span v-if="swCol === 3">&#9660</span><span v-else-if="swCol === (-3)">&#9650</span></th>
      <th @click="sortKey(4)">End Date <span v-if="swCol === 4">&#9660</span><span v-else-if="swCol === (-4)">&#9650</span></th>
      <th @click="sortFee(5)">Total Fee <span v-if="swCol === 5">&#9660</span><span v-else-if="swCol === -5">&#9650</span></th>
      <th @click="sortKey(6)">Status <span v-if="swCol === 6">&#9660</span><span v-else-if="swCol === (-6)">&#9650</span></th>
      <th @click="sortKey(7)">Hotel <span v-if="swCol === 7">&#9660</span><span v-else-if="swCol === (-7)">&#9650</span></th>
      <th colspan="2" @click="emptyModal()">ADD</th>
    </tr>

    <tr v-for="item in reservations">
      
      <td>{{ item.name }}</td>
      <td>{{ item.surname }}</td>
      <td>{{ item.start_date }}</td>
      <td>{{ item.end_date }}</td>
      <td>{{ item.total_fee }}</td>
      <td v-if="item.status === 1" style="background-color: rgb(143,185,53);">{{ getst(item.status) }}</td>
      <td v-else-if="item.status === 2" style="background-color: rgb(230,226,46);">{{ getst(item.status) }}</td>
      <td v-else="item.status === 2" style="background-color: rgb(230,71,71);">{{ getst(item.status) }}</td>
      <td>{{ gethotid(item.hotel_id) }}</td>
      <td @click="editB(item.id)">Edit</td>
      <td @click="closeB(item.id)">X</td>
      
    </tr>
  </table>
</template>

<style></style>
