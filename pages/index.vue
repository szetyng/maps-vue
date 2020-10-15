<template>
  <div>
    <v-row>
      <v-col cols="12" md="6">
        <MapOl 
          :tunnelGeojson776="tunnelGeojson776" 
          :tunnelGeojson777="tunnelGeojson777"  
          @expandFeature="sidebarInfo = $event"
        />
      </v-col>
      <v-col>
        <v-card>
          <v-card-title>
            Click on buttons to change this text
          </v-card-title>
          <v-card-text>
            {{ sidebarInfo }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>


<script>
import MapOL from "../components/MapOL"

import s776Rings from "../assets/s776.json";
import s777Rings from "../assets/s777.json";

export default {
  name: "OpenLayersHome",
  components: {
    MapOL
  },
  data() {
    return {
      mymap: null,
      tunnelGeojson776: s776Rings,
      tunnelGeojson777: s777Rings,
      sidebarInfo: "Nothing is selected"
    }
  }
};
</script>


<style scoped>

</style>