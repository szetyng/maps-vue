<template>
  <v-container fluid>
    <v-row>
      <v-btn @click="toggle776"> Show S776 </v-btn>
      <v-btn @click="toggle777"> Show S777 </v-btn>
    </v-row>
    <v-row>
      <v-col cols="12" md="6">
        <div id="mapid"></div>
      </v-col>

      <v-col class="12" md="6">
        <div id="graphid">
          <v-card>
            <v-card-title> I am a card </v-card-title>
          </v-card>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>


<script>
import L, { geoJSON } from "leaflet";
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
      popup: null,
      info: null
    };
  },

  methods: {
    createGeojsonLayer(geojson, markerColor) {
      let geojsonMarkerOptions = {
        radius: 5,
        fillColor: markerColor,
        color: markerColor,
        weight: 1,
        opacity: 1,
        fillOpacity: 1,
      };
      var info = this.info;
      function ptl(feature, latlng) {
        let marker = L.circleMarker(latlng, geojsonMarkerOptions);

        return marker
      }

      function eachFeature(feature, layer) {
        let popupContent = `<b>${feature.properties.name}</b><br>I am a popup. <a href="plot-leaflet.com">Gooooo</a>`
        
        info.update(layer.feature.properties);
        
        layer.bindPopup(popupContent);
      }

      let geojsonLayer = new L.geoJSON(geojson, {
        pointToLayer: ptl,
        onEachFeature: eachFeature,
      });

      return geojsonLayer;
    },

    toggle776() {
      this.s776Show = !this.s776Show;

      if (this.s776Show && this.s776Layer == null) {
        this.s776Layer = this.createGeojsonLayer(this.s776geojson, "red");
        this.s776Layer.addTo(this.mymap);
      } else if (this.s776Show && this.s776Layer != null) {
        this.s776Layer.addTo(this.mymap)
      } else {
        this.mymap.removeLayer(this.s776Layer);
      }
    },
    toggle777() {
      this.s777Show = !this.s777Show;

      if (this.s777Show && this.s777Layer == null) {
        this.s777Layer = this.createGeojsonLayer(this.s777geojson, "blue");
        this.s777Layer.addTo(this.mymap);
      } else if (this.s777Show && this.s777Layer != null) {
        this.s777Layer.addTo(this.mymap)
      } else {
        this.mymap.removeLayer(this.s777Layer);
      }
    },
    
  },

  mounted() {
    let mymap = L.map("mapid").setView([3.129447222, 101.7170167], 18);
    this.mymap = mymap;

    L.tileLayer("http://{s}.tile.osm.org/{z}/{x}/{y}.png", {
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      maxZoom: 28,
      maxNativeZoom: 18,
    }).addTo(mymap);

    let info = L.control();
    info.onAdd = function(map) {
      this._div = L.DomUtil.create('div', 'info');
      this.update();
      return this._div
    }

    info.update = function(props) {
      console.log('am i not being called?')
      this._div.innerHTML = "<h4>Hello</h4>" + (props ? props.name : "HEHEHE")
    };

    info.addTo(mymap);

    this.info = info;

    //this.toggle776();
  },
};
</script>


<style scoped>
#mapid {
  height: 600px;
}

#graphid {
  height: 600px;
}

.info {
    padding: 6px 8px;
    font: 14px/16px Arial, Helvetica, sans-serif;
    background: white;
    background: rgba(255,255,255,0.8);
    box-shadow: 0 0 15px rgba(0,0,0,0.2);
    border-radius: 5px;
}
.info h4 {
    margin: 0 0 5px;
    color: #777;
}
</style>