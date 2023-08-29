<script setup>
import Tile from './components/Tile.vue';
import {ref, watchEffect} from 'vue'

watchEffect(async ()=>{
  const url = "http://127.0.0.1:8080/print"
  fetch(url).then((res)=>{
  return res.json()
  })
  .then(json=>{
    console.log(json)
  })
  .catch(err=>{console.error(err)})
})

const from= ref("")
const to= ref("")
const pieces=[{symbol:"♔",id:"a1"}, {symbol:"♗",id:"a2"}, {symbol:"♟︎",id:"a3"},]
function fromFun(id){
console.log(id)
from.value = id
}
function toFun(id){
    console.log(id)
    to.value=id

}
</script>

<template>  
     <p>from: {{ from }}</p>
     <p>to: {{ to }}</p>
<Tile v-for="ele in pieces" :idValue="ele.id" :key="ele.id" @from="fromFun" @to="toFun">{{ ele.symbol }}</Tile>

<!--
  <Tile @from="fromFun" @to="toFun">♗</Tile>
  <Tile @from="fromFun" @to="toFun">♟︎</Tile>
  <div class="tile">♔</-div>
  <div class="tile">♗</div>
  <div class="tile">♟︎</div>
-->
</template>
<style scoped>

</style>
