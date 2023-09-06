<script setup>
import Tile from "./Tile.vue";
import { ref, watchEffect, onBeforeMount } from "vue";

const from = ref("");
const to = ref("");
const player_turn = ref(null);
const board = ref(null);
const check = ref("");
const error_message = ref("");

const available_moves = ref([]);

onBeforeMount(async () => {
  await getBoard();
});

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
    error_message.value = err;
  }
}

async function updateBoard() {
  const url_move = `http://127.0.0.1:8080/move/${from.value}/${to.value}`;
  const url_checkmate = `http://127.0.0.1:8080/board/checkmate`;
  try {
    let res = await fetch(url_move);
    let json = await res.json();
    error_message.value = json;

    let res_checkmate = await fetch(url_checkmate);
    let json_checkmate = await res_checkmate.json();
    console.log(json_checkmate);

    if (json_checkmate.is_checkmate){
      resetBoard()
    }else{
      getBoard()
    }
  } catch (err) {
    error_message.value = err;
  } finally {
    from.value="";
    to.value="";
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
    error_message.value = err;
  }
}

async function show_moves(position){
  const url = `http://127.0.0.1:8080/move/show/${position}`;
  console.log(url)
  try {
    let res = await fetch(url);
    let json = await res.json();
    available_moves.value = json;
  } catch (err) {
    console.log(err);
    error_message.value = err;
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
        @click="from && to ? updateBoard() : null"
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
      @click="show_moves(ele.col+ele.row)"
      :ele_symbol="ele.symbol"
      :field_color="ele.field_color"
      :piece_color="ele.piece_color"
      :idValue="ele.col + ele.row"
      :key="ele.col + ele.row"
      :class="{from_to_highlight:ele.col + ele.row==from || ele.col + ele.row==to,available_move:available_moves.includes(ele.col + ele.row)}"
      :style="ele.col+ele.row ==check? 'background-color:red':''"
      @from="fromFun"
      @to="toFun"
      >{{ele.symbol }}</Tile>
      
  </div>
  <p style="color: red;">{{ check?"CHECK":"" }}</p>
  <p :style="error_message=='success'?'color: green;':'color: red;'">{{ error_message }}</p>
</template>
<style scoped>
.btn {
  width: 50px;
  height: 50px;
  border: 1px solid black;
  margin-right: 1em;
  cursor: pointer;
}
.from_to_highlight{
  border: solid 1px red;
  background-color: lightcoral ;
}

.available_move{
  position: relative;
}
.available_move::before {
            content: ""; 
            position: absolute; 
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            width: 40px;
            height: 40px;
            opacity: 0.5;
            border: solid 1px salmon;
            background-color: lightsalmon ;
            border-radius: 50%; 
        }
</style>
