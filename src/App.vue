<script setup lang="ts">
import axios from 'axios'
import 'gridjs/dist/theme/mermaid.css'
import { Cell, createElement, Grid, h } from 'gridjs'
import { ref, onMounted, watchEffect, effect } from 'vue'
import myModal from './components/v1/vifs/myModal.vue'
import { reactive } from 'vue'

var reservations = ref([])
var hotels = ref([])
var oldData

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

function fillModal(data: object) {
  var mod = document.getElementById('myModal')
  mod.style.display = 'block'

  var inps = document.getElementsByTagName('input')
 
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
}

function gethotid(hotid: number) {
   console.log(hotels.value)
  for (var i = 0; i < hotels.value.length; i++) {
   
    if (hotels.value[i].id === hotid.toString()) {
      return hotels.value[i].name
    } else if (hotid > hotels.value.length) {
      return 'Undefined'
    }
  }
}
</script>

<template>
  <my-modal id="myModal"></my-modal>

  <table>
    <tr>
      <th></th>
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

<style></style>
