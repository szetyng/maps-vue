<template>
  <div>
    <v-btn @click="toggle776">
      Show S776
    </v-btn>
    <v-btn @click="toggle777">
      Show S777
    </v-btn>

    <div id="mapid"></div>
  </div>
</template>


<script>
import L from 'leaflet';
import s776Rings from '../assets/s776.json';
import s777Rings from '../assets/s777.json';

export default {
  data() {
    return {
      s776Tunnel: s776Rings,
      s777Tunnel: s777Rings,
      s776Show: true,
      s777Show: false,
      mymap: null,
      layer776: null,
      layer777: null
    }
  },
  methods: {
    draw776() {
      let geojsonMarkerOptions = {
        radius: 5,
        fillColor: '#FF0000',
        color: '#FF0000',
        weight: 1,
        opacity: 1,
        fillOpacity: 1
      }

      this.layer776 = new L.geoJSON(this.s776Tunnel, {
        pointToLayer: function(feature, latlng) {
          return L.circleMarker(latlng, geojsonMarkerOptions)
        },
        onEachFeature: function(feature, layer) {
          layer.bindPopup(feature.properties.name)
        }
      });

      if (this.s776Show) this.layer776.addTo(this.mymap);

    },

    toggle776() {
      this.s776Show = !this.s776Show;

      if (this.s776Show) {
        this.layer776.addTo(this.mymap);
      } else {
        this.mymap.removeLayer(this.layer776);
      }
    },
    draw777() {
      let geojsonMarkerOptions = {
        radius: 5,
        fillColor: '#FF0000',
        color: '#FF0000',
        weight: 1,
        opacity: 1,
        fillOpacity: 1
      }

      this.layer777 = new L.geoJSON(this.s777Tunnel, {
        pointToLayer: function(feature, latlng) {
          return L.circleMarker(latlng, geojsonMarkerOptions)
        },
        onEachFeature: function(feature, layer) {
          layer.bindPopup(feature.properties.name)
        }
      });

      if (this.s777Show) this.layer777.addTo(this.mymap);

    },

    toggle777() {
      this.s777Show = !this.s777Show;

      if (this.s777Show) {
        if (this.layer777 == null) {
          this.draw777();
          return
        }
        this.layer777.addTo(this.mymap);
      } else {
        this.mymap.removeLayer(this.layer777);
      }
    }
  },

  mounted() {
    let mymap = L.map('mapid').setView([3.129447222, 101.7170167], 18);
    this.mymap = mymap;
    L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
                attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
                maxZoom: 28,
                maxNativeZoom: 18
            }).addTo(mymap);

    this.draw776();
  }
}
</script>


<style scoped>
#mapid { 
  height: 600px; 
}
</style>