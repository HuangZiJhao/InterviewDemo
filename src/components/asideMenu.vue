<template>
    <div class="aside-menu">
        <label>
            縣市:<select v-model="currCity">
                <option v-for="c in cityList" :key="c">{{ c }}</option>
            </select>
        </label>

        <label>
            行政區:<select v-model="currDistrict">
                <option v-for="d in districtList" :key="d.id">{{ d.name }}</option>
            </select>
        </label>
        <div class="wraps">
            <label>
                <i class="fas fa-search-loaction"></i>關鍵字搜尋:
                <input type="text" placeholder="請輸入關鍵字" v-model="keywords">
            </label>
        </div>
        <ul class="store-lists">
            <li class="store-info wraps" v-for="s in filteredStore" :key="s.id" @click="$emit('triggerMarkerPopup',s.id)">
                <h1 v-html="keywordsHightLight(s.name)"></h1>

                <div class="mask-info">
                    <i class=".fas fa-head-side-mask"></i>
                    <span>大人口罩:{{s.mask_adult}}個</span>
                </div>

                <div class="mask-info">
                    <i class=".fas fa-baby"></i>
                    <span>兒童口罩:{{s.mask_child}}個</span>
                </div>
                
                <div class="mask-info">
                    最後更新時間:{{s.updated}}
                </div>

                <button class="btn-store-detail" @click="OpenInfoBox(s.id)">
                    <i class="fas fa-info-circle"></i>
                    看詳細資訊
                </button>

            </li>

        </ul>
    </div>
</template>

<script setup>
import { useStore } from 'vuex';
import { computed, watch } from 'vue';
const store = useStore();
const currCity = computed({
    get(){
        return store.state.currCity;
    },
    set(value){
        store.commit('setcurrCity',value);
}});
const currDistrict = computed(
    {
        get(){
            return store.state.currDistrict;
        },
        set(value){
            store.commit('setcurrDistrict',value);
        }
    }
)
const keywords = computed(
    {
        get(){
            return store.state.keywords;
        },
        set(value){
            store.commit('setKeywords',value)
        }
    }
)
const cityList = computed(()=>store.getters.cityList);
const districtList = computed(()=>store.getters.districtList);
const filteredStore = computed(()=>store.getters.filteredStore);

const keywordsHightLight = (val) =>{
    return val.replace(keywords.value,`<span class="highlight">${keywords.value}</span>`);
};

const showModal = computed(
    {
        get(){
            return store.state.showModal;
        },
        set(value){
            store.commit('setShowModal',value);
        }
    }
);

const infoBoxSid = computed(
    {
        get(){
            return store.state.infoBoxSid
        },
        set(value){
            return store.commit('setInfoBoxSid',value)
        }
    }
)

const OpenInfoBox=(sid)=>{
    showModal.value =true;
    infoBoxSid.value = sid;
}

watch(districtList,
    (newValue,oldValue) =>{
        currDistrict.value =newValue[0].name;
    }
);

const stores = computed(()=>store.state.stores);

</script>