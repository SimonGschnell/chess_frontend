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
const pieces = [
  { symbol: "♔", id: "a1" },
  { symbol: "♗", id: "a2" },
  { symbol: "♟︎", id: "a3" },
];
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
  fetch(url)
    .then((res) => {
      return res.json();
    })
    .then((json) => {
      console.log(json);
      //player_turn.value = json.player_turn
      board.value = json;
    })
    .catch((err) => {
      console.error(err);
    });
}

async function updateBoard(from, to) {
  const url = `http://127.0.0.1:8080/move/${from}/${to}`;
  fetch(url)
    .then((res) => res.json())
    .then((json) => {
      console.log(json);
      if (json == "success") {
        getBoard();
      }
    })
    .catch((err) => {
      console.error(err);
    });
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
        @click="console.log('to be implemented')"
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
}
</style>
