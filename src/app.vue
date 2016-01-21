/* vim: set softtabstop=2 shiftwidth=2 expandtab : */
<template>
<div>
  <h1>Map information</h1>
  Map center latitude: <input type="number" v-model="center.lat" number>
  <br>
  Map center longitude: <input type="number" v-model="center.lng" number>
  <br>
  Map bounds: {{mapBounds | json}}
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
  Map style: <select id="" name="" v-model="mapStyle">
    <option value="red">red</option>
    <option value="green">green</option>
    <option value="normal">normal</option>
  </select>
  <br>
  <button @click="addMarker"> Add a new Marker</button> (or right click on the map :) )
  <h1>Clusters</h1>
  enabled: <input type="checkbox" v-model="clustering" number>
  </br>
  Grid size: <input type="number" v-model="gridSize" number>
  <br>
  <h1>Polyline</h1>
  Editable: <input type="checkbox" number v-model="pleditable">
  <button @click="resetPlPath">Reset path</button>
  <br>
  Visible: <input type="checkbox" number v-model="plvisible">
  <br>
  <h1>Circle</h1>
  Visible: <input type="checkbox" number v-model="displayCircle"><br>
  {{circleBounds | json}}
  <br>
  <h1>Rectangle</h1>
  Visible: <input type="checkbox" number v-model="displayRectangle"><br>
  {{rectangleBounds | json}}
  <br>
  <h1> Standalone infoWindow </h1>
  modal 1 : <input type="checkbox" number v-model="ifw"><br>
  modal 2: <input type="checkbox" number v-model="ifw2"> <input type="text" v-model="ifw2text">
  <h1>Markers</h1>
  Display only markers with even ID (to test filters) <input type="checkbox" number v-model="markersEven"><br>
  <table>
    <tr>
      <th>lat</th>
      <th>lng</th>
      <th>opacity</th>
      <th>enabled</th>
      <th>draggable</th>
      <th>clicked</th>
      <th>right clicked</th>
      <th>Drag-ended</th>
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
      <td>{{m.dragended}}</td>
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
    :options="mapOptions"
    :bounds.sync="mapBounds"
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
    @g-dragend="m.dragended++"
    v-for="m in markers | markerRemover"
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
      @g-dragend="m.dragended++"
      v-for="m in markers | markerRemover"
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

    <polyline v-if="plvisible" :path.sync="plPath" :editable="pleditable" :draggable="true" :options="{geodesic:true, strokeColor:'#FF0000'}">
    </polyline>
    <circle v-if="displayCircle" :bounds.sync="circleBounds" :center.sync="center" :radius.sync="100000" :options="{editable: true}"></circle>
    <rectangle v-if="displayRectangle" :bounds.sync="rectangleBounds"  :options="{editable: true}"></rectangle>
  </map>

</div>
</template>

<script>

import {load, Marker, Map, Cluster, InfoWindow, Polyline, Rectangle, Circle} from 'vue-google-maps'

load('AIzaSyBzlLYISGjL_ovJwAehh6ydhB56fCCpPQw');

export default {
  data: function data() {
    return {
      center: { lat: 48.8538302, lng: 2.2982161 },
      mapBounds: {},
      clustering: true,
      zoom: 7,
      gridSize: 50,
      mapType: 'terrain',
      markers: [],
      markersEven: false,
      drag: 0,
      mapClickedCount: 0,
      ifw: true,
      ifw2: false,
      ifw2text: 'You can also use the content prop to set your modal',
      mapStyle: 'green',
      circleBounds: {},
      displayCircle: false,
      displayRectangle: false,
      rectangleBounds: {
        north: 33.685,
        south: 50.671,
        east: -70.234,
        west: -116.251
      },
      originalPlPath: [
        {lat: 37.772, lng: -122.214},
        {lat: 21.291, lng: -157.821},
        {lat: -18.142, lng: 178.431},
        {lat: -27.467, lng: 153.027}
      ],
      plPath: [
        {lat: 37.772, lng: -122.214},
        {lat: 21.291, lng: -157.821},
        {lat: -18.142, lng: 178.431},
        {lat: -27.467, lng: 153.027}
      ],
      pleditable: true,
      plvisible: false
    };
  },

  filters: {
    markerRemover (markers) {
      if (this.markersEven) {
        const result = [];
        for (var i = 0 ; i < markers.length; i+=2) {
          result.push(markers[i]);
        }
        return result;
      } else {
        return markers
      }
    }
  },

  computed: {
    mapOptions () {
      switch(this.mapStyle) {
        case 'normal':
          return {styles: []};
          break;
        case 'red':
          return {
            styles: [
              {
                stylers: [
                  {hue: '#890000'},
                  {visibility: 'simplified'},
                  {gamma: 0.5},
                  {weight: 0.5}
                ]
              },
              {
                elementType: 'labels',
                stylers: [{visibility: 'off'}]
              },
              {
                featureType: 'water',
                stylers: [{color: '#890000'}]
              }
            ]
          }
          break;
        default:
          return {
            styles: [
              {
                stylers: [
                  {hue: '#899999'},
                  {visibility: 'on'},
                  {gamma: 0.5},
                  {weight: 0.5}
                ]
              },
              {
                featureType: 'road',
                stylers: [
                  {visibility: 'off'}
                ]
              },
              {
                featureType: 'transit.line',
                stylers: [
                  {color: '#FF0000'}
                ]
              },
              {
                featureType: 'poi',
                elementType: 'labels.icon',
                stylers: [
                  {visibility: 'on'},
                  {weight: 10}
                ]
              },
              {
                featureType: 'water',
                stylers: [
                  { color: '#8900FF' },
                  { weight:  9999900000},
                ]
              }
            ]
          };
      }
    }
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
        dragended: 0,
        ifw: true,
        ifw2text: "This text is bad please change me :( "
      });
      return this.markers[this.markers.length - 1];
    },
    resetPlPath () {
      this.plPath = this.originalPlPath;
    }
  },
  components: {
    Map,
    Marker,
    Cluster,
    InfoWindow,
    Polyline,
    Rectangle,
    Circle
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
