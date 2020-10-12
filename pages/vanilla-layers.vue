<template>
  <div>
    <div id="map" class="map"></div>
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
    };
  },
  methods: {
    draw776() {
      let source776 = new VectorSource({
        features: new GeoJSON().readFeatures(this.s776Tunnel),
      });

      let layer776 = new VectorLayer({
        source: source776,
      });

      this.mymap.addLayer(layer776);
    },
  },

  mounted() {
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
    //this.mymap = map;

    let source776 = new VectorSource({
      //format: new GeoJSON(),
      features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(
        this.s776Tunnel
      ),
    });

    let layer776 = new VectorLayer({
      source: source776,
      style: styleFunction,
    });

    // map.addLayer(layer776);
    // map.render();

    const map = new Map({
      target: "map",
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
        layer776,
      ],
      view: new View({
        center: fromLonLat([101.7170167, 3.129447222]),
        zoom: 18,
      }),
    });
  },
};
</script>


<style scoped>
.map {
  height: 600px;
}
</style>