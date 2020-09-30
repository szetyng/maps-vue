<template>
  <div>
    <p>Center is at {{ center }} and the zoom is: {{ zoom }}</p>
    <v-btn @click="s776Show = !s776Show">
      Show S776
    </v-btn>
    <v-btn @click="s777Show = !s777Show">
      Show S777
    </v-btn>
    <vl-map :load-tiles-while-animating="true" :load-tiles-while-interacting="true" style="height: 600px">
      <vl-view :zoom.sync="zoom" :center.sync="center" :rotation.sync="rotation"></vl-view>

      <vl-layer-tile>
        <vl-source-osm></vl-source-osm>
      </vl-layer-tile>

      <vl-interaction-select :features.sync="selectedFeatures" :condition="featureSelect">
        <template slot-scope="select">
          <!-- <vl-style-box>
            <vl-style-stroke color="#423e9e" :width="7"></vl-style-stroke>
            <vl-style-fill :color="[254, 178, 76, 0.7]"></vl-style-fill>
            <vl-style-circle :radius="5">
              <vl-style-stroke color="#423e9e" :width="7"></vl-style-stroke>
              <vl-style-fill :color="[254, 178, 76, 0.7]"></vl-style-fill>
            </vl-style-circle>
          </vl-style-box>

          <vl-style-box :z-index="1">
            <vl-style-stroke color="#d43f45" :width="2"></vl-style-stroke>
            <vl-style-circle :radius="5">
              <vl-style-stroke color="#d43f45" :width="2"></vl-style-stroke>
            </vl-style-circle>
          </vl-style-box> -->

          <vl-overlay class="feature-popup" v-for="feature in select.features" :key="feature.id" :id="feature.id"
                      :position="pointOnSurface(feature.geometry)" :auto-pan="true" :auto-pan-animation="{ duration: 300 }">
              <section class="card">
                Feature: {{  feature.properties.name }}
              </section>
          </vl-overlay>

        </template>
      </vl-interaction-select>

      <vl-layer-vector v-if="s776Show">
        <vl-source-vector :features="getTunnel"></vl-source-vector>
        <vl-style-func :factory="pointsStyleFunc" />
        <!-- <vl-style-box>
          <vl-style-circle :radius="5">
            <vl-style-fill color="blue"></vl-style-fill>
            <vl-style-stroke color="blue"></vl-style-stroke>
          </vl-style-circle>
          </vl-style-box> -->
      </vl-layer-vector>
      
      <vl-layer-vector v-if="s777Show">
        <vl-source-vector :features="getTunnel2"></vl-source-vector>
        <vl-style-box>
          <vl-style-circle :radius="5">
            <vl-style-fill color="red"></vl-style-fill>
            <vl-style-stroke color="red"></vl-style-stroke>
          </vl-style-circle>
          </vl-style-box>
      </vl-layer-vector>

      <!-- <vl-feature>
        <vl-geom-multi-point :coordinates="circles"></vl-geom-multi-point>
        <vl-style-box>
          <vl-style-circle :radius="5">
            <vl-style-fill color="red"></vl-style-fill>
            <vl-style-stroke color="red"></vl-style-stroke>
          </vl-style-circle>
          </vl-style-box>
      </vl-feature> -->
    </vl-map>

  </div>
</template>

<script>
import { fromLonLat } from 'ol/proj';
import { Style, Circle, Stroke, Fill } from 'ol/style';
import { pointerMove, singleClick } from 'ol/events/condition';
import { findPointOnSurface } from 'vuelayers/lib/ol-ext'
import s776Rings from '../assets/s776rings.json';
import s777Rings from '../assets/s777rings.json';

export default {
  data() {
    return {
      zoom: 18,
      center: fromLonLat([101.7170167, 3.129447222]),
      circles: [fromLonLat([101.7170167, 3.129447222]), fromLonLat([101.7170222, 3.129455556])],
      // center1: fromLonLat([101.7170167, 3.129447222]),
      // notcenter: fromLonLat([101.7170222, 3.129455556]),
      rotation: 0,
      s776RingLoc: s776Rings,
      s777RingLoc: s777Rings,
      selectedFeatures: [],
      hoveredFeatures: [],
      s776Show: true,
      s777Show: false
    }
  },
  methods: {
    pointOnSurface: findPointOnSurface,
    // maybe your style format is wrong 
    pointsStyleFunc() {
      return feature => {
        let st = new Style({
          image: new Circle({
            radius: 5,
            stroke: new Stroke({
              color: '#FF0000',
              width: 1.25
            }),
            fill: new Fill({
              color: '#FF0000'
            })
          })
        })


        // let st = new Style({
        //   radius: 5,
        //   fillColor: '#FF0000',
        //   color: '#FF0000',
        //   weight: 1,
        //   opacity: 1,
        //   fillOpacity: 1
        // })

        return [st]
      }
    },
  },
  computed: {

    getTunnel() {
      // todo: retrieve geojson of this instead of calculating it on the fly everytime
      let tunnel = this.s776RingLoc.map(ringObj => {
        let featureObj = {
          type: "Feature",
          geometry: {
            type: "Point",
            coordinates: fromLonLat([ringObj.long, ringObj.lat])
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
            coordinates: fromLonLat([ringObj.long, ringObj.lat])
          },
          properties: {
            name: `R${ringObj.Ring}`

          }
        }
        return featureObj
      });

      return tunnel
    },

    featureHover() {
      return pointerMove
    },

    featureSelect() {
      return singleClick
    }
  
  }
}
</script>


<style scoped>

</style>