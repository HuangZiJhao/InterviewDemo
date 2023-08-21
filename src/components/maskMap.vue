<template>
    <div class="mask-map" id="mask-map"></div>
</template>

<script setup>
import L from 'leaflet'
import { reactive,onMounted, computed,watch } from 'vue';
import { useStore } from 'vuex';

const store = useStore();

var map = {};
const markers = [];

const currDistrictInfo = computed(()=>{
    return store.getters.currDistrictInfo;
});
const filteredStore = computed(()=>{
    return store.getters.filteredStore;
})
const addMarker = (item)=>{
    const ICON = {
        iconUrl:'https://raw.githubusercontent.com/pointhi/leaflet-color-markers/master/img/marker-icon-2x-violet.png',
        shadowUrl:'https://cdnjs.cloudflare.com/ajax/libs/leaflet/0.7.7/images/marker-shadow.png',
        iconSize:[25,41],
        iconAnchor:[12,41],
        popupAnchor:[1,-34],
        shadowSize:[41,41]
    };

    const marker = L.marker([item.longitude,item.latitude],ICON)
    .addTo(map)
    .bindPopup(`<h2 class="popup-name">${item.name}</h2>`);

    marker.markerId = item.id;
    marker.lng = item.longitude;
    marker.lat = item.latitude;

    markers.push(marker);
};

const clearMarker=()=>{
    map.eachLayer((layer)=>{
        if(layer instanceof L.Marker){
            map.removeLayer(layer);
        }
    })

    markers.length = 0;
};

const triggerPopup = (markerId)=>{
    const marker = markers.find((d)=>{d.markerId === markerId});

    map.flyTo(new L.LatLng(marker.lng, marker.lat),15);
    marker.openPopup();
}

watch(currDistrictInfo,(dist)=>{
    map.panTo(new L.LatLng(dist.latitude,dist.longitude));
});

watch(filteredStore,(store)=>{

    clearMarker();

    store.forEach(element => {
        addMarker(element);
    });
});

onMounted(()=>{
    map = L.map('mask-map',{
        center:[25.03,121.55],
        zoom:14
    });


    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: '© <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a>         contributors'
    }).addTo(map);
});

</script>