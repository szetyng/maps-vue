<template>
  <v-container fluid>
    <v-row>
      <v-btn @click="toggle776"> Show S776 </v-btn>
      <v-btn @click="toggle777"> Show S777 </v-btn>
    </v-row>
    <v-row>
      <v-col cols="12" md="6">
        <div id="map" class="map">
          <v-card id="popup">
            <v-card-title id="pop-title" class="pop-title">This is title</v-card-title>
            <v-card-text>
              <span id="pop-text">This is text</span>
            </v-card-text>
            <v-card-actions>
              <v-btn id="pop-btn">Hello</v-btn>
            </v-card-actions>
          </v-card>
        </div>
      </v-col>
      <v-col>
        <v-card>
          <v-card-title> I am a card </v-card-title>
          <v-card-text>{{ cardText }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile as TileLayer, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource } from "ol/source";
import { fromLonLat, toLonLat } from "ol/proj";
import { Circle as CircleStyle, Fill, Stroke, Style, Text } from "ol/style";
import GeoJSON from "ol/format/GeoJSON";
import Overlay from 'ol/Overlay';
import {toStringHDMS} from 'ol/coordinate';
import Select from 'ol/interaction/Select';
import {altKeyOnly, click, pointerMove} from 'ol/events/condition';

import s776Rings from "../assets/s776.json";
import s777Rings from "../assets/s777.json";


export default {
  data() {
    return {
      mymap: null,
      s776geojson: s776Rings,
      s776Show: false,
      s776Layer: null,
      s777geojson: s777Rings,
      s777Show: false,
      s777Layer: null,
      cardText: "CRY"
    }
  },

  methods: {
    createGeojsonLayer(geojson, markerColor) {
      let styles = {
        Point: new Style({
          image: new CircleStyle({
            radius: 5,
            fill: new Fill({color: markerColor}),
            stroke: new Stroke({color: markerColor, width: 1})
          })
        })
      };

      let styleFunction = function (feature) {
        return styles[feature.getGeometry().getType()];
      };

      let source = new VectorSource({
        features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(geojson),
      });

      let layer = new VectorLayer({
        source: source,
        style: styleFunction
      });

      return layer
    },

    toggle776() {
      this.s776Show = !this.s776Show

      if (this.s776Show && this.s776Layer == null) {
        this.s776Layer = this.createGeojsonLayer(this.s776geojson, "red");
        this.mymap.addLayer(this.s776Layer);
      } else if (this.s776Show && this.s776Layer != null) {
        this.mymap.addLayer(this.s776Layer);
      } else {
        this.mymap.removeLayer(this.s776Layer);
      }
    },
    toggle777() {
      this.s777Show = !this.s777Show

      if (this.s777Show && this.s777Layer == null) {
        this.s777Layer = this.createGeojsonLayer(this.s777geojson, "blue");
        this.mymap.addLayer(this.s777Layer);
      } else if (this.s777Show && this.s777Layer != null) {
        this.mymap.addLayer(this.s777Layer);
      } else {
        this.mymap.removeLayer(this.s777Layer);
      }
    },
    hello() {
      console.log('whaddup')
    },

    initOverlay() {
      let popup = new Overlay({
        element: document.getElementById('popup'),
      });
      this.mymap.addOverlay(popup);

      let element = popup.getElement();
      let select = new Select();

      if (select !== null) {
        this.mymap.addInteraction(select);
        select.on("select", function (e) {
          let coords = e.selected[0].getGeometry().getCoordinates();
          var hdms = toStringHDMS(toLonLat(coords));
          popup.setPosition(coords);
          document.getElementById("pop-title").innerHTML = "This is title"
          document.getElementById("pop-text").innerHTML = 
            `This is ${e.target.getFeatures().item(0).getProperties().name}`
          let btn = document.getElementById("pop-btn");

          btn.onclick = function hehe() {
            console.log('hehe')
            this.hello();
          }
          
        });
      }
    }
  },

  mounted() {
    const map = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM()
        })
      ],
      view: new View({
        center: fromLonLat([101.7170167, 3.129447222]),
        zoom: 18
      })
    })

    // let popup = new Overlay({
    //   element: document.getElementById('popup'),
    // });
    // map.addOverlay(popup);

    // let element = popup.getElement();
    // let select = new Select();

    // if (select !== null) {
    //   map.addInteraction(select);
    //   select.on("select", function (e) {
    //     let coords = e.selected[0].getGeometry().getCoordinates();
    //     var hdms = toStringHDMS(toLonLat(coords));
    //     popup.setPosition(coords);
    //     document.getElementById("pop-title").innerHTML = "This is title"
    //     document.getElementById("pop-text").innerHTML = 
    //       `This is ${e.target.getFeatures().item(0).getProperties().name}`
    //     let btn = document.getElementById("pop-btn");

    //     btn.onclick = function hi() {
    //       console.log('YESS')
    //     }
        
    //   });
    // }





    this.mymap = map;
    this.toggle776();
    this.initOverlay();
  }
}
</script>


<style scoped>
.map {
  height: 600px;
}
</style>