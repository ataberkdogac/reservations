<script setup lang="ts">
import { ref } from 'vue'
import { senddata } from '../../../App.vue';
import axios from 'axios'
var hotels = new Array;

axios.get('https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1/hotels').
then(function (response) {
hotels = response.data;
var hots = document.getElementById("hotid");
for (var i = 0; i < hotels.length; i++){
    var opt = document.createElement("option");
    opt.append(hotels[i].name)
    hots?.appendChild(opt)
}
})



</script>

<template>
    <div id = "myModal" class="myModal">
        <span class="close-button">
            <div class="modal-content">
                <label for = "name">Name:</label>
                <input type="text" id="name"></input>
                <label for = "surname">Surname:</label>
                <input type="text" id="surname"></input>
                <label for="sd">Start Date:</label>
                <input type="date" id="sd"></input>
                <label for="ed">End Date:</label>
                <input type="date" id="ed"></input>
                <label for="fee"> Total Fee:</label>
                <input type="text" id="fee"></input>
                <label>Status:</label>
                <input type="radio" id="appr" name = "stt" >Approved</input><br>
                <input type="radio" id="pend" name = "stt">Pending</input><br>
                <input type="radio" id="cacl" name = "stt">Cancelled</input><br>
                Hotel Name:
                <select id="hotid">

                </select>
            
            </div>
        </span>
    </div>
</template>
<style>
    .myModal {
        display: none;
        z-index: 1;
        background-color: rgba(0, 0, 0, 0.6);
        position: absolute;
        width: 100%;
        height: 100%;
    }

    .myModal div{
        text-align: left;
        display: block;
        width: 40%;
        margin: auto;
        margin-top: 10%;
        border: solid 2px black;
        border-radius: 10px;
        background-color: black;
        padding: 10px;
        
    }

    input {
       
        
        margin: 10px;
        
    }
    label {
        display: block;
    }
    
    
</style>