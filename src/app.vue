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
  Dragged {{drag}} times
  <br>
  Left clicked {{mapClickedCount}} times
  <br>
  Map type: <select id="" name="" v-model="mapType">
    <option value="roadmap">roadmap</option>
    <option value="hybrid">hybrid</option>
    <option value="satellite">satellite</option>
    <option value="terrain">terrain</option>
  </select>
  <br>
  <button @click="addMarker"> Add a new Marker</button> (or right click on the map :) )
  <h1>Clusters</h1>
  enabled: <input type="checkbox" v-model="clustering" number>
  </br>
  Grid size: <input type="number" v-model="gridSize" number>
  <br>

  <h1> Standalone infoWindow </h1>
  modal 1 : <input type="checkbox" number v-model="ifw"><br>
  modal 2: <input type="checkbox" number v-model="ifw2"> <input type="text" v-model="ifw2text">
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
      <th>Open info window</th>
      <th>infoWIndow text</th>
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
      <td>
        <input type="checkbox" v-model="m.ifw" number>
      </td>
      <td>
        <input type="text" v-model="m.ifw2text">
      </td>
      <td><button @click="markers.splice($index, 1)">Delete me </button></td>
    </tr>
  </table>
  <map
    :center.sync="center"
    :zoom.sync="zoom"
    :map-type-id.sync="mapType"
    @g-rightclick="mapRclicked"
    @g-drag="drag++"
    @g-click="mapClickedCount++"
    >
    <cluster
      :grid-size="gridSize"
      v-if="clustering"
    >
      <marker
        v-if="m.enabled"
        :position.sync="m.position"
        :opacity="m.opacity"
        :draggable.sync="m.draggable"
        @g-click="m.clicked++"
        @g-rightclick="m.rightClicked++"
        v-for="m in markers"
      >
        <info-window
          :opened.sync="m.ifw"
          :content="m.ifw2text"
        ></info-window>
      </marker>
    </cluster>
    <div v-if="!clustering">
      <marker
        v-if="m.enabled"
        :position.sync="m.position"
        :opacity="m.opacity"
        :draggable.sync="m.draggable"
        @g-click="m.clicked++"
        @g-rightclick="m.rightClicked++"
        v-for="m in markers"
      >
        <info-window
          :opened.sync="m.ifw"
          :content="m.ifw2text"
        ></info-window>
      </marker>
    </div>

    <info-window
      :position="center"
      :opened.sync="ifw"
    >
      To show you the bindings are working I will stay on the center of the screen whatever you do :) 
      <br/>
      To show you that even my content is bound to vue here is the number of time you clicked on the map 
      <b>{{mapClickedCount}}</b>
    </info-window>

    <info-window
      :position="center"
      :opened.sync="ifw2"
      :content="ifw2text"
    ></info-window>
  </map>

</div>
</template>

<script>

import {load, Marker, Map, Cluster, InfoWindow} from 'vue-google-maps'

load('AIzaSyBzlLYISGjL_ovJwAehh6ydhB56fCCpPQw');

export default {
  data: function data() {
    return {
      center: { lat: 48.8538302, lng: 2.2982161 },
      clustering: true,
      zoom: 7,
      gridSize: 50,
      mapType: 'terrain',
      markers: [],
      drag: 0,
      mapClickedCount: 0,
      ifw: true,
      ifw2: false,
      ifw2text: 'You can also use the content prop to set your modal'

    };
  },

  methods: {
    mapClicked (mouseArgs) {
      console.log('map clicked', mouseArgs);
    },
    mapRclicked (mouseArgs) {
      const createdMarker = this.addMarker();
      createdMarker.position.lat = mouseArgs.latLng.lat();
      createdMarker.position.lng = mouseArgs.latLng.lng();
    },
    addMarker: function addMarker() {
      this.markers.push({
        position: { lat: 48.8538302, lng: 2.2982161 },
        opacity: 1,
        draggable: true,
        enabled: true,
        clicked: 0,
        rightClicked: 0,
        ifw: true,
        ifw2text: "This text is bad please change me :( "
      });
      return this.markers[this.markers.length - 1];
    }
  },
  components: {
    Map,
    Marker,
    Cluster,
    InfoWindow
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
