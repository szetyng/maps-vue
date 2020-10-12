<template>
  <div>
    <v-btn @click="toggle776">
      Show S776
    </v-btn>
    <v-btn @click="toggle777">
      Show S777
    </v-btn>

    Hello {{ selectedFeature }}
    <div id="map" class="map">
      <div id="popup"></div>
    </div>
  </div>
</template>


<script>
import "ol/ol.css";
import { Map, View } from "ol";
import { Tile as TileLayer, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource } from "ol/source";
import { fromLonLat } from "ol/proj";
import { Circle as CircleStyle, Fill, Stroke, Style, Text } from "ol/style";
import GeoJSON from "ol/format/GeoJSON";
import Overlay from 'ol/Overlay';
import Select from 'ol/interaction/Select';
import {altKeyOnly, click, pointerMove} from 'ol/events/condition';

import s776Rings from "../assets/s776.json";
import s777Rings from "../assets/s777.json";

export default {
  data() {
    return {
      s776Tunnel: s776Rings,
      s777Tunnel: s777Rings,
      s776Show: true,
      s777Show: false,
      mymap: null,
      layer776: null,
      layer777: null,
      selectedFeature: null
    };
  },
  methods: {
    draw776() {
      var image = new CircleStyle({
        radius: 5,
        fill: new Fill({ color: "red" }),
        stroke: new Stroke({ color: "red", width: 1 }),
      });

      var styles = {
        Point: new Style({
          image: image,
        }),
      };

      var styleFunction = function (feature) {
        return styles[feature.getGeometry().getType()];
      };

      let source776 = new VectorSource({
        features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(this.s776Tunnel),
      });

      let layer776 = new VectorLayer({
        source: source776,
        style: styleFunction,
      });

      this.layer776 = layer776;
      this.mymap.addLayer(layer776);
    
    },
    draw777() {
      var image = new CircleStyle({
        radius: 5,
        fill: new Fill({ color: "blue" }),
        stroke: new Stroke({ color: "blue", width: 1 }),
      });

      var styles = {
        Point: new Style({
          image: image,
        }),
      };

      var styleFunction = function (feature) {
        return styles[feature.getGeometry().getType()];
      };

      let source777 = new VectorSource({
        features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(this.s777Tunnel),
      });

      let layer777 = new VectorLayer({
        source: source777,
        style: styleFunction,
      });

      this.layer777 = layer777;
      this.mymap.addLayer(layer777);
    },
    toggle776() {
      this.s776Show = !this.s776Show;

      if (this.s776Show) {
        this.mymap.addLayer(this.layer776);
      } else {
        this.mymap.removeLayer(this.layer776);
      }
    },

    toggle777() {
      this.s777Show = !this.s777Show;

      if (this.s777Show) {
        if (this.layer777 == null) {
          this.draw777();
          return
        }
        this.mymap.addLayer(this.layer777);
      } else {
        this.mymap.removeLayer(this.layer777);
      }
    }
  },

  mounted() {

    // map.addLayer(layer776);
    // map.render();

    const map = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM(),
        })
      ],
      view: new View({
        center: fromLonLat([101.7170167, 3.129447222]),
        zoom: 18,
      }),
    });



    this.mymap = map;
    this.draw776();

    
  },
};
</script>


<style scoped>
.map {
  height: 600px;
}
</style>