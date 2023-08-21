<template>
<Transition name="modal">
    <div class="modal-mask" v-show="showModal">
        <div class="modal-wrapper" @click="close" >
            <div class="modal-container">
              <div class="modal-body" v-if="currStore">
                <h1 class="store-name">{{currStore.Name}}</h1>
                <hr>
                <h2 class="title">營業時間</h2>
                <table>
                  <thead>
                    <tr>
                      <th></th>
                      <th>ㄧ</th>
                      <th>二</th>
                      <th>三</th>
                      <th>四</th>
                      <th>五</th>
                      <th>六</th>
                      <th>日</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <th>早上</th>
                      <td v-for="(s,idx) in ServicePeriods[0]" :key="idx">{{ s }}</td>
                    </tr>
                    <tr>
                      <th>中午</th>
                      <td v-for="(s,idx) in ServicePeriods[1]" :key="idx">{{ s }}</td>
                    </tr>
                    <tr>
                      <th>晚上</th>
                      <td v-for="(s,idx) in ServicePeriods[2]" :key="idx">{{ s }}</td>
                    </tr>
                  </tbody>
                </table>

                <h2 class="title">地址 {{currStore.address}}</h2>
                <h2 class="title">電話 {{currStore.phone}}</h2>
                <h2 class="title">備註 {{currStore.custom_note}}</h2>
              </div>
            </div>
        </div>

    </div>
</Transition>

</template>

<script setup>
import { computed ,watch } from 'vue';
import { useStore } from 'vuex';

const store= useStore();
const showModal = computed({
    get() {
        return store.state.showModal;
    },
    set (value){
        store.commit('setShowModal',value);
    }
});

const close =()=>{
    showModal.value = false;
}

const currStore=computed({
  get(){
    console.log(store.state.stores.filter((d)=>d.id=== infoBoxSid.value)[0]);
    return store.state.stores.filter((d)=>d.id=== infoBoxSid.value)[0];
  }
});

const infoBoxSid = computed(() => store.state.infoBoxSid)

watch(infoBoxSid, () => {
  // 當 infoBoxSid 的值變更時，手動觸發 currStore 的重新計算
  // currStore.value = store.state.stores.find((d) => d.id === infoBoxSid.value);
});

const ServicePeriods = computed(
  {
    get(){
      let currStoreValue = currStore.value; // 取得 currStore 的實際值
      let servicePeriods = currStoreValue?.['service_periods'] || '';
      servicePeriods = servicePeriods.replace(/n/g,'0').replace(/y/g,'x');

      return servicePeriods
      ?[servicePeriods.slice(0,7).split(''),
        servicePeriods.slice(7,14).split(''),
        servicePeriods.slice(14,21).split('')]
      :servicePeriods;
    }
  })


</script>

<style scoped lang="scss">
.modal-mask {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .65);
  display: table;
  transition: opacity .3s ease;
}
.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
.modal-container {
  width: 520px;
  margin: 0px auto;
  padding: 10px 30px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .3);
  transition: all .3s ease;
  font-family: Helvetica, Arial, sans-serif;
}
.modal-body {
  color: #42b983;
  margin: 20px 0;
}
</style>

<style>
.modal-enter {
  opacity: 0;
}
.modal-leave-active {
  opacity: 0;
}
.modal-enter .modal-container,
.modal-leave-active .modal-container {
  transform: scale(1.1);
}
</style>

<style lang="scss" scoped>
.store-name {
  font-size: 1.6rem;
  font-weight: bold;
  line-height: 1.5;
}
.title{
  font-weight: 500;
  margin-bottom: .5rem;
  line-height: 1.7;
}
table {
  border-spacing: 0;
  border-radius: 3px;
  width: 100%;
  margin-bottom: 1rem;
}
th{
  background-color: #42b983;
  color: #fff;
}
td, th{
  padding: .3em;
  text-align: center;
  line-height: 1.5rem;
}
</style>