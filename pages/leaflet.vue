<template>
  <div style="height: 500px; width: 100%">
    <div style="height: 200px overflow: auto;">
      <p>Center is at {{ currentCenter }} and the zoom is: {{ currentZoom }}</p>
    </div>

    <v-btn @click="s776Show = !s776Show">
      Show S776
    </v-btn>
    <v-btn @click="s777Show = !s777Show">
      Show S777
    </v-btn>

    <client-only>
    <l-map
      :zoom="zoom"
      :center="center"
      :options="mapOptions"
      :maxZoom="28"
      style="height: 600px"
      @update:center="centerUpdate"
      @update:zoom="zoomUpdate"
    >
      <l-tile-layer
        :url="url"
        :attribution="attribution"
        :options="{ maxZoom: 28 , maxNativeZoom: 18 }"
      />
      <l-geo-json 
        v-if="s776Show"
        :geojson="getTunnel"
        :options="options"
      ></l-geo-json>
      <l-geo-json 
        v-if="s777Show"
        :geojson="getTunnel2"
        :options="options"
      ></l-geo-json>

    </l-map>
    </client-only>
  </div>
</template>

<script>
// import Logo from '~/components/Logo.vue'
// import VuetifyLogo from '~/components/VuetifyLogo.vue'
import { latLng } from "leaflet";
import { LMap, LTileLayer, LMarker, LPopup, LTooltip, LCircleMarker, LGeoJson } from 'vue2-leaflet';
import s776Rings from '../assets/s776rings.json';
import s777Rings from '../assets/s777rings.json';

export default {
  components: {
    LMap,
    LTileLayer,
    LMarker,
    LPopup,
    LTooltip, 
    LCircleMarker,
    LGeoJson
  },
  data() {
    return {
      zoom: 18,
      center: latLng(3.129447222, 101.7170167),
      url: 'http://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
      attribution:
        '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors',
      currentZoom: 11.5,
      currentCenter: latLng(3.129447222, 101.7170167),
      mapOptions: {
        zoomSnap: 0.5
      },
      s776RingLoc: s776Rings,
      s777RingLoc: s777Rings,
      geojson: null,
      s776Show: true,
      s777Show: false
    };
  },
  methods: {
    zoomUpdate(zoom) {
      this.currentZoom = zoom;
    },
    centerUpdate(center) {
      this.currentCenter = center;
    },
    showLongText() {
      this.showParagraph = !this.showParagraph;
    },
    innerClick() {
      alert("Click!");
    }
  },
  computed: {
    getTunnel() {
      // let tunnel = [
      //   {
      //     type: "Feature",
      //     geometry: {
      //       type: "Point",
      //       coordinates: [101.7170167,  3.129447222]
      //     }
      //   }
      // ]

      // todo: retrieve geojson of this instead of calculating it on the fly everytime
      let tunnel = this.s776RingLoc.map(ringObj => {
        let featureObj = {
          type: "Feature",
          geometry: {
            type: "Point",
            coordinates: [ringObj.long, ringObj.lat]
          },
          properties: {
            name: `R${ringObj.Ring}`

          }
        }
        return featureObj
      });

      return tunnel
    },

    getTunnel2() {
      let tunnel = this.s777RingLoc.map(ringObj => {
        let featureObj = {
          type: "Feature",
          geometry: {
            type: "Point",
            coordinates: [ringObj.long, ringObj.lat]
          },
          properties: {
            name: `R${ringObj.Ring}`

          }
        }
        return featureObj
      });

      return tunnel
    },

    options() {
      var geojsonMarkerOptions = {
        radius: 5,
        fillColor: '#FF0000',
        color: '#FF0000',
        weight: 1,
        opacity: 1,
        fillOpacity: 1
      }

      function onEach(feature, layer) {
        let ringInfo = feature.properties.name;
        ringInfo += "<br>Other stuff that I can add to everyone"

        return layer.bindPopup(ringInfo)
      }



      return {
        pointToLayer: function (feature, latlng) {
          return L.circleMarker(latlng, geojsonMarkerOptions)
        },
        onEachFeature: onEach
      }
    }
  
  }
}
</script>
