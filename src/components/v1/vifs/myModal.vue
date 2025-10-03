<script setup lang="ts">

import axios from 'axios'
var hotels = new Array;
axios.get('https://682c1d4dd29df7a95be587f9.mockapi.io/api/v1/hotels').
then(function (response) {
    hotels = response.data;
    var hots = document.getElementById("hotid");
    for (var i = 0; i < hotels.length; i++){
        var opt = document.createElement("option");
        opt.append(hotels[i].name)
        hots.appendChild(opt)
    }
})


</script>

<template>
    <div id = "myModal" class="myModal">
        
            <div class="modal-content">
                <label for = "name" >Name:</label>
                <input type="text" id="name" placeholder="Name"></input>
                <label for = "surname">Surname:</label>
                <input type="text" id="surname" placeholder="Surname"></input>
                <label for="start_date">Start Date:</label>
                <input type="date" id="start_date"></input>
                <label for="end_date" required>End Date:</label>
                <input type="date" id="end_date"></input>
                <label for="total_fee"> Total Fee:</label>
                <input type="text" id="total_fee" placeholder="Amount"></input>
                <label>Status:</label>
                <input type="radio" id="appr" name = "stt" >Approved</input><br>
                <input type="radio" id="pend" name = "stt">Pending</input><br>
                <input type="radio" id="cacl" name = "stt">Cancelled</input><br>
                <label>Hotel Name:</label>
                <select id="hotid">
                <option value="">Select an Hotel...</option>
                </select>
                <button id="closeModal" class="closeModal">Save</button>
            </div>
        
    </div>
</template>
<style>
    .myModal {
        display: none;
        background-color: rgba(0,0,0, 0.8);
        width: 100%;
        height: 100%;
        position: fixed; 
        z-index: 1;
        color: black;
    }

    .modal-content{
        text-align: left;
        max-width: 300px;
        min-width: 20%;
        margin: auto;
        margin-top: 6%;
        background-color: rgb(255, 202, 26);
        padding: 10px;
        
    }

    input {
        margin: 10px;
        background-color: rgb(255, 238, 140);
        color: black;
    }

    label {
        display: block;
        margin: 5px;
        margin-bottom: 0px;
    }

    button {
        display:block;
        margin: 10px;
        
    }

    select{
        margin: 5px;
        background-color: rgb(255, 238, 140);
        color: black;
    }
    
    .radio{
        background-color: rgb(255, 238, 140);
        color: black;
    }
    
</style>