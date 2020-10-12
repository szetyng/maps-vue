<template>
  <div>
    <div
      id="map"
      class="map"
      style="
        width: 100%;
        height: 600px;
        border: 2px solid black;
        background-color: white;
      "
    ></div>
    <div id="popup">Hello</div>
    <form class="form-inline">
      <label>Action type &nbsp;</label>
      <select id="type" class="form-control">
        <option value="click" selected>Click</option>
        <option value="singleclick">Single-click</option>
        <option value="pointermove">Hover</option>
        <option value="altclick">Alt+Click</option>
        <option value="none">None</option>
      </select>
      <span id="status">&nbsp;0 selected features</span>
    </form>
  </div>
</template>


<script>
import "ol/ol.css";
import GeoJSON from "ol/format/GeoJSON";
import Map from "ol/Map";
import OSM from "ol/source/OSM";
import Select from "ol/interaction/Select";
import VectorSource from "ol/source/Vector";
import View from "ol/View";
import Overlay from 'ol/Overlay';
import {fromLonLat, toLonLat} from 'ol/proj';
import {toStringHDMS} from 'ol/coordinate';
import { Tile as TileLayer, Vector as VectorLayer } from "ol/layer";
import { altKeyOnly, click, pointerMove } from "ol/events/condition";
import s776Rings from "../assets/s776.json";

export default {
  data() {
    return {
      s776Tunnel: s776Rings,
      selectedFeature: null
    };
  },

  mounted() {
    var raster = new TileLayer({
      source: new OSM(),
    });

    var vector = new VectorLayer({
      source: new VectorSource({
        features: new GeoJSON({ featureProjection: "EPSG:3857" }).readFeatures(this.s776Tunnel),
      }),
    });

    var map = new Map({
      layers: [raster, vector],
      target: "map",
      view: new View({
        center: fromLonLat([101.7170167, 3.129447222]),
        zoom: 18,
      }),
    });
    var pos = fromLonLat([16.3725, 48.208889]);
    var popup = new Overlay({
      element: document.getElementById('popup'),
    });
    map.addOverlay(popup);



    var select = null; // ref to currently selected interaction

    // select interaction working on "singleclick"
    var selectSingleClick = new Select();

    // select interaction working on "click"
    var selectClick = new Select({
      condition: click,
    });

    // select interaction working on "pointermove"
    var selectPointerMove = new Select({
      condition: pointerMove,
    });

    var selectAltClick = new Select({
      condition: function (mapBrowserEvent) {
        return click(mapBrowserEvent) && altKeyOnly(mapBrowserEvent);
      },
    });

    var selectElement = document.getElementById("type");

    var changeInteraction = function () {
      if (select !== null) {
        map.removeInteraction(select);
      }
      var value = selectElement.value;
      if (value == "singleclick") {
        select = selectSingleClick;
      } else if (value == "click") {
        select = selectClick;
      } else if (value == "pointermove") {
        select = selectPointerMove;
      } else if (value == "altclick") {
        select = selectAltClick;
      } else {
        select = null;
      }
      if (select !== null) {
        map.addInteraction(select);
        select.on("select", function (e) {
          let coords = e.selected[0].getGeometry().getCoordinates();
          var hdms = toStringHDMS(toLonLat(coords));
          popup.setPosition(coords);
          document.getElementById("popup").innerHTML = `This is ${e.target.getFeatures().item(0).getProperties().name}`


          document.getElementById("status").innerHTML =
            "&nbsp;" +
            e.target.getFeatures().getLength() +
            " selected features (last operation selected " +
            e.selected.length +
            " and deselected " +
            e.deselected.length +
            " features) " +
            e.target.getFeatures().item(0).getProperties().name + 
            hdms;
        });
      }
    };


    // map.on('click', function (evt) {
    //   var element = popup.getElement();
    //   var coordinate = evt.coordinate;
    //   var hdms = toStringHDMS(toLonLat(coordinate));

    //   $(element).popover('dispose');
    //   popup.setPosition(coordinate);
    //   $(element).popover({
    //     container: element,
    //     placement: 'top',
    //     animation: false,
    //     html: true,
    //     content: '<p>The location you clicked was:</p><code>' + hdms + '</code>',
    //   });
    //   $(element).popover('show');
    // });




    /**
     * onchange callback on the select element.
     */
    selectElement.onchange = changeInteraction;
    changeInteraction();
  },
};
</script>


<style scoped>
</style>