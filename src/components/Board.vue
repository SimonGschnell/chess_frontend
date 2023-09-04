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
const check = ref("");

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
  const url_check = "http://127.0.0.1:8080/board/check";
  try {
    //get board data
    let res = await fetch(url);
    let json = await res.json();
    console.log(json);
    board.value = json;

    //get check data
    let check_res = await fetch(url_check);
    let check_json = await check_res.json();
    console.log(check_json);
    if (check_json.pos){
      check.value = check_json.pos.file + check_json.pos.rank.toString();
    }else{
      check.value = "";
    }
    
  } catch (err) {
    console.log(err);
  }
}

async function updateBoard(from, to) {
  const url_move = `http://127.0.0.1:8080/move/${from}/${to}`;
  const url_checkmate = `http://127.0.0.1:8080/board/checkmate`;
  try {
    let res = await fetch(url_move);
    let json = await res.json();
    console.log(json);

    let res_checkmate = await fetch(url_checkmate);
    let json_checkmate = await res_checkmate.json();
    console.log(json_checkmate);

    if (json_checkmate.is_checkmate){
      resetBoard()
    }else{
      getBoard()
    }
  } catch (err) {
    console.error(err);
  }
}

async function resetBoard(){
  const url = "http://127.0.0.1:8080/board/reset";
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
      :style="ele.col+ele.row ==check? 'background-color:red':''"
      @from="fromFun"
      @to="toFun"
      >{{ele.symbol }}</Tile>
      
  </div>
  <p style="color: red;">{{ check?"CHECK":"" }}</p>
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
