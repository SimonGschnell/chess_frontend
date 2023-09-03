<script setup>
import Tile from "./Tile.vue";
import { ref, watchEffect, onBeforeMount } from "vue";

const player_turn = ref(null);
const board = ref(null);

onBeforeMount(async () => {
  await getBoard();
});

const from = ref("");
const to = ref("");

function fromFun(id) {
  console.log(id);
  from.value = id;
}
function toFun(id) {
  console.log(id);
  to.value = id;
}

async function getBoard() {
  const url = "http://127.0.0.1:8080/board";
  try {
    let res = await fetch(url);
    let json = await res.json();
    console.log(json);
    board.value = json;
  } catch (err) {
    console.log(err);
  }
}

async function updateBoard(from, to) {
  const url = `http://127.0.0.1:8080/move/${from}/${to}`;
  try {
    let res = await fetch(url);
    let json = await res.json();
    console.log(json);
    if (json == "success") {
      getBoard();
    }
  } catch (err) {
    console.error(err);
  }
}

async function resetBoard(){
  const url = "http://127.0.0.1:8080/reset";
  try {
    let res = await fetch(url);
    let json = await res.json();
    if (json == "success") {
      getBoard();
    }
  } catch (err) {
    console.error(err);
  }
}
</script>

<template>
  <div style="display: flex; flex-direction: column">
    <p>from: {{ from }}</p>
    <p>to: {{ to }}</p>
    <div>
      <button
        style="background-color: lightgreen"
        class="btn"
        @click="from && to ? updateBoard(from, to) : null"
      >
        send</button
      ><button
        style="background-color: lightcoral"
        class="btn"
        @click="resetBoard"
      >
        reset
      </button>
    </div>
  </div>
  <br />
  <h3>{{ player_turn }}</h3>
  <div style="display: flex" v-for="row in board">
    <Tile
      v-for="ele in row"
      :ele_symbol="ele.symbol"
      :field_color="ele.field_color"
      :piece_color="ele.piece_color"
      :idValue="ele.col + ele.row"
      :key="ele.col + ele.row"
      @from="fromFun"
      @to="toFun"
      >{{ ele.symbol }}</Tile
    >
  </div>
</template>
<style scoped>
.btn {
  width: 50px;
  height: 50px;
  border: 1px solid black;
  margin-right: 1em;
  cursor: pointer;
}
</style>
