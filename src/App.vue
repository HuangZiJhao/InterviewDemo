<script setup>
import { useStore } from 'vuex';
import {ref, onMounted} from 'vue';
import asideMenu from './components/asideMenu.vue';
import lightbox from './components/lightbox.vue';
import maskMap from './components/maskMap.vue';

const store = useStore();
const fetchLocation = () => store.dispatch('fetchLocations');
const fetchPharmacies = () => store.dispatch('fetchPharmacies');
const map =ref(null);
const openPopup=(id)=>{
  map.value.triggerPopup(id);
}


onMounted(() => {
fetchLocation();
fetchPharmacies();
});

</script>

<template>
  <asideMenu @triggerMarkerPopup="openPopup" ref="menu"></asideMenu>

  <maskMap ref="map"></maskMap>


  <lightbox></lightbox>
</template>

<style lang="scss" src="./assets/style.scss"></style>
