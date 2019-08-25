<template>
  <div class="home">
    <NewVue4 msg="Welcome to Your Vue.js App" />

    <mapirMap :lat="35" :lng="52" :zoom="6"></mapirMap>
  </div>
</template>

<script>
// @ is an alias to /src

import NewVue4 from "../components/NewVue4";
import mapirMap from "mapir-vue";
export default {
  name: "home",
  components: {
    NewVue4,
    mapirMap
  }
};
</script>
