/* vim: set softtabstop=2 shiftwidth=2 expandtab : */
<template>
<div>
  <h1>Map information</h1>
  Map center latitude: <input type="number" v-model="center.lat" number>
  <br>
  Map center longitude: <input type="number" v-model="center.lng" number>
  <br>
  Map zoom: <input type="number" v-model="zoom" number>
  <br>
  Map type: <select id="" name="" v-model="mapType">
    <option value="roadmap">roadmap</option>
    <option value="hybrid">hybrid</option>
    <option value="satellite">satellite</option>
    <option value="terrain">terrain</option>
  </select>
  <br>
  <button @click="addMarker"> Add a new Marker</button>
  <h1>Markers</h1>
  <table>
    <tr>
      <th>lat</th>
      <th>lng</th>
      <th>opacity</th>
      <th>enabled</th>
      <th>draggable</th>
      <th>clicked</th>
      <th>right clicked</th>
      <th>Delete me</th>
    </tr>
    <tr v-for="m in markers">
      <td>
        <input type="number" v-model="m.position.lat" number>
      </td>
      <td>
        <input type="number" v-model="m.position.lng" number>
      </td>
      <td>
        <input type="number" v-model="m.opacity" number>
      </td>
      <td>
        <input type="checkbox" v-model="m.enabled" number>
      </td>
      <td>
        <input type="checkbox" v-model="m.draggable" number>
      </td>
      <td>{{m.clicked}}</td>
      <td>{{m.rightClicked}}</td>
      <td><button @click="markers.splice($index, 1)">Delete me </button></td>
    </tr>
  </table>
  <map
    :center.sync="center"
    :zoom.sync="zoom"
    :map-type-id.sync="mapType"
    >
    <marker
    v-if="m.enabled"
    :position.sync="m.position"
    :opacity="m.opacity"
    :draggable.sync="m.draggable"
    @click="m.clicked++"
    @rightclick="m.rightClicked++"
    v-for="m in markers"
    ></marker>
  </map>

</div>
</template>

<script>

import {load, Marker, Map} from 'vue-google-maps'

load('AIzaSyBzlLYISGjL_ovJwAehh6ydhB56fCCpPQw');
console.log(load);


export default {
  data: function data() {
    return {
      center: { lat: 48.8538302, lng: 2.2982161 },
      zoom: 7,
      mapType: 'terrain',
      markers: []
    };
  },

  methods: {
    addMarker: function addMarker() {
      this.markers.push({
        position: { lat: 48.8538302, lng: 2.2982161 },
        opacity: 1,
        draggable: true,
        enabled: true,
        clicked: 0,
        rightClicked: 0
      });
    }
  },
  components: {
    Map,
    Marker
  }
};
</script>

 <style>
   map {
     width:100%;
     height: 600px;
     display: block;
   }
 </style>
