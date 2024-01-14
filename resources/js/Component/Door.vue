<!-- resources/js/components/Door.vue -->
<template>
	<div id="app">
	  <DoorDesign />
	</div>
  </template>
  
  <script>
  import { ref, computed, watch } from 'vue';
  import { SketchPicker } from "vue-color";
  import DoorDesign from "./DoorDesign.vue";

  export default {
  components: {
    DoorDesign,
  },
};
</script>

<style>
/* Add global styling here */
#app {
  text-align: center;
  margin-top: 2em;
}
</style>