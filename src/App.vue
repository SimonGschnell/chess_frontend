<script setup>
import Tile from './components/Tile.vue';

const pieces=[{symbol:"♔",id:"a1"}, {symbol:"♗",id:"a2"}, {symbol:"♟︎",id:"a3"},]
function fromFun(id){
console.log(id)
}
function toFun(id){
    console.log(id)


}
</script>

<template>  
     
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
