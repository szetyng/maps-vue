<template>
  <div ref="map-root" class="map">
    <v-card id="popup">
      <v-card-title>{{ popupTitle }}</v-card-title>
      <v-card-text>{{ popupText }}</v-card-text>
      <v-card-actions>
        <v-btn id="pop-btn" color="primary">Clickety</v-btn>
      </v-card-actions>
    </v-card>
  </div>
</template>


<script>
import "ol/ol.css";
import { Map, View, Overlay } from "ol";
import { Tile as TileLayer, Vector as VectorLayer } from "ol/layer";
import { OSM, Vector as VectorSource } from "ol/source";
import { Circle as CircleStyle, Fill, Stroke, Style, Text } from "ol/style";
import GeoJSON from "ol/format/GeoJSON";
import { fromLonLat, toLonLat } from "ol/proj";
import { toStringHDMS } from "ol/coordinate";
import Select from "ol/interaction/Select";
import { altKeyOnly, click, pointerMove } from "ol/events/condition";

export default {
  name: "MapOL",
  props: {
    tunnelGeojson776: Object,
    tunnelGeojson777: Object,
  },
  data() {
    return {
      mymap: null,
      popupTitle: "Title",
      popupText: "Text"
    };
  },

  methods: {
    createGeojsonLayer(geojson, markerColor) {
      let styles = {
        Point: new Style({
          image: new CircleStyle({
            radius: 5,
            fill: new Fill({ color: markerColor }),
            stroke: new Stroke({ color: markerColor, width: 1 }),
          }),
        }),
      };

      let styleFunction = function (feature) {
        return styles[feature.getGeometry().getType()];
      };

      let source = new VectorSource({
        features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(
          geojson
        ),
      });

      let layer = new VectorLayer({
        source: source,
        style: styleFunction,
      });

      return layer;
    },
  },

  mounted() {
    let ogView = new View({
      center: fromLonLat([101.7170167, 3.129447222]),
      zoom: 18,
    });

    this.mymap = new Map({
      target: this.$refs["map-root"],
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
      ],
      view: ogView,
    });

    let layer776 = this.createGeojsonLayer(this.tunnelGeojson776, "red");
    let layer777 = this.createGeojsonLayer(this.tunnelGeojson777, "blue");

    this.mymap.addLayer(layer776);
    this.mymap.addLayer(layer777);

    // fit to 776 tunnel
    const source776 = layer776.getSource();
    ogView.fit(source776.getExtent());

    // create overlay
    let popup = new Overlay({
      element: document.getElementById('popup'),
      autoPan: true,
      autoPanAnimation: {
        duration: 300,
      },
    });
    
    // create Select interaction
    let select = new Select();

    if (select !== null) {
      this.mymap.addInteraction(select);

      select.on("select", e => {
        if (e.target.getFeatures().getLength() > 0) {
          let propertyName = e.target.getFeatures().item(0).getProperties().name;

          this.popupTitle = "Additional info";
          this.popupText = propertyName;

          let coords = e.selected[0].getGeometry().getCoordinates();
          this.mymap.addOverlay(popup);
          popup.setPosition(coords);

          let btn = document.getElementById("pop-btn");
          btn.onclick = () => {
            this.$emit('expandFeature', propertyName)
          }
        } else {
          console.log('did not select a feature')
          this.mymap.removeOverlay(popup);
        }

      })
    }
  },
};
</script>


<style scoped>
.map {
  height: 600px;
}
</style>