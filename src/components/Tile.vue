<script setup>
import { ref, onMounted } from "vue";

const emit = defineEmits(["from", "to"]);

const props = defineProps({
  idValue: String,
  field_color: String,
  ele_symbol:String,
  piece_color:String,
});

const tileClasses = ref([""]);
const gettigDragged = ref(false);

function dragEvent(e) {
  console.log(e);
}

function dropFun(e) {
  e.preventDefault();
  emit("to", e.target.id);
  tileClasses.value.pop();
}

function dragoverFun(e) {
  gettigDragged.value=false
  e.preventDefault();
  if (!tileClasses.value.includes("dragover")) {
    tileClasses.value.push("dragover");
  }
}
function dragleaveFun(e) {
  e.preventDefault();
  tileClasses.value.pop();
}
function dragendFun(e) {
  e.preventDefault();
  tileClasses.value.pop();
  gettigDragged.value=false
}

function dragstartFun(e) {
  emit("from", e.target.id);
  gettigDragged.value=true
}
</script>

<template>
  <!-- this component is responsible to represent a tile on a chess board @dragend="dragEvent"-->

  <div
    :class="{
      gettigDragged:gettigDragged,
      dragover: tileClasses.includes('dragover'),
      tile: true,
      black_tile: field_color == 'BLACK',
      white_piece: piece_color == 'WHITE',
    }"
    
    @drop="dropFun" :draggable="ele_symbol.length>0" @dragstart="dragstartFun"
    @dragend="dragendFun" :id="idValue"
    @dragover="dragoverFun"
    @dragleave="dragleaveFun"
    style="cursor: grab;"
  >
 
    <slot></slot>
  </div>
</template>

<style scoped>
.tile {
  height: 50px;
  width: 50px;
  border: solid 1px black;
  font-size: 2em;
  text-align: center;
  display: inline-block;
}
.black_tile {
  background-color: grey;
  
}

.white_piece {
  color :rgb(192, 192, 192);
}

.dragover {
  border: solid 1px red;
  background-color: lightcoral !important;
}

.gettigDragged {
  border : solid 1px black !important;
  background-color: lightgreen!important;
}
</style>
