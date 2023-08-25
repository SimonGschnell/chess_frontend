<script setup>
import { ref, onMounted } from "vue";

const emit = defineEmits(["from", "to"]);

const props = defineProps({
  idValue: String,
});

const tileClasses = ref([""])

function dragEvent(e) {
  console.log(e);
}

function dropFun(e) {
  e.preventDefault();
  emit("to", e.target.id);
  tileClasses.value.pop();

}

function dragoverFun(e) {
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
  
}

function dragstartFun(e) {
  
  emit("from", e.target.id);
}
</script>

<template>
  <!-- this component is responsible to represent a tile on a chess board @dragend="dragEvent"-->
  <div class="tile">
    <span
    :class="tileClasses"
    :id="idValue"
      @drop="dropFun"
      @dragstart="dragstartFun"
      @dragend="dragendFun"
      @dragover="dragoverFun"
      @dragleave="dragleaveFun"
      draggable="true"
      ><slot></slot
    ></span>
    
  </div>
</template>

<style scoped>
.tile {
  height: 100px;
  width: 100px;
  border: solid 1px black;
  font-size: 4em;
  text-align: center;
}

.dragover {
  border: solid 1px red;
  background-color: lightcoral;
}
</style>
