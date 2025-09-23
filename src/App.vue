<script setup lang="ts">
import axios from 'axios'
import 'gridjs/dist/theme/mermaid.css'
import { Cell, createElement, Grid, h } from 'gridjs'
import { ref, onMounted, watchEffect, effect } from 'vue'
import myModal from './components/v1/vifs/myModal.vue'

var reservations = new Array;
var hotels = new Array;

watchEffect(() => {
    fetchHotels();
    fetchData();
  

})


function fetchData() {
  axios.get('https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1/reservations').then(function (response) {
    reservations = response.data;
    console.log(reservations);
    printList(reservations);
  })
  
}

function fetchHotels() {
  axios.get('https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1/hotels').then(function (response) {
    hotels = response.data;
   
  })
  
}



function getst(st: any) {
  if (st === 1) {
    return 'Approved'
  }
  if (st === 2) {
    return 'Pending'
  }
  if (st === 3) {
    return 'Canceled'
  }
}

function gethotid(hotid: number) {
  for (var i = 0; i < hotels.length; i++) {
    if (hotels[i].id === hotid.toString()) {
      return hotels[i].name
    }
  }
}


function editB(row: any){
  var mod = document.getElementById("myModal");
  mod.style.display = "block";
  document.getElementById("name").value = row[1].data;
  document.getElementById("surname").value = row[2].data;
  document.getElementById("sd").value = row[3].data;
  document.getElementById("ed").value = row[4].data;
  document.getElementById("fee").value = row[5].data;
  if (row[6].data === 'Approved'){
    document.getElementById("appr").checked = true;
  } else if (row[6].data === 'pending') {
    document.getElementById("pend").checked = true;
  } else {document.getElementById("cacl").checked = true;}

  document.getElementById("hotid").value = row[7].data;
  

}





function closeB(data: Array<object>) {
  
}

function printList(dataAr: any) {
 
  new Grid({
    columns: [
      { id: 'createdAt', name: 'Created At' },
      { id: 'name', name: 'Name' },
      { id: 'surname', name: 'Surname' },
      { id: 'start_date', name: 'Start Date' },
      { id: 'end_date', name: 'End Date' },
      { id: 'total_fee', name: 'Total Fee' },
      { id: 'status', name: 'Status' },
      { id: 'hotel_id', name: 'Hotel Name' },
      {
        id: 'edit',
        name: 'Actions',
        formatter: (cell, row) => {
          return h(
            'button',
            {
              className: 'py-2 mb-4 px-4 border rounded-md text-white bg-blue-600',
              onClick: () => editB(row.cells),
            },
            'Edit'
          )
        },
      },
      {
        id: 'close',
        name: 'aa',
        formatter: (cell, row) => {
          return h(
            'button',
            {
              className: 'py-2 mb-4 px-4 border rounded-md text-white bg-blue-600',
              onClick: () => closeB(row.cells),
            },
            'X'
          )
        },
      },
    ],

    data: () =>
      dataAr.map((dataAr:any) => [
        dataAr.createdAt,
        dataAr.name,
        dataAr.surname,
        dataAr.start_date,
        dataAr.end_date,
        dataAr.total_fee,
        getst(dataAr.status),
        gethotid(dataAr.hotel_id),
      ]),
      search: true,
      pagination: true,
      sort: true
  }).render(document.getElementById("datalist"))

  
}
</script>

<template>
  <my-modal id = "myModal"></my-modal>
  
  <table id = "datalist" class="grid">

  </table>


</template>

<style></style>
