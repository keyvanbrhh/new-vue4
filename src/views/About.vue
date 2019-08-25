<template>
  <div class="about">
    <mapirMap
      :markerLatLng="[
        [32.654639, 51.667099],
        [32.64728, 51.676097],
        [32.641167, 51.674952]
      ]"
      :markerName="['marker1', 'marker2']"
    >
    </mapirMap>
  </div>
</template>
<script>
import mapirMap from "mapir-vue";

export default {
  name: "about",
  components: {
    mapirMap
  }
};
</script>
