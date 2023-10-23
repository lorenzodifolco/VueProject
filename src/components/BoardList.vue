<template>
  <div class="board-list-container">
    <h1>Boards</h1>
    <ul v-if="visible">
      <BoardCard v-for="board in boards" :key="board.id" :board="board" :active="isActive(board)"  @select-board="selectBoard"/>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';
import BoardCard from "@/components/BoardCard.vue";

export default {
  components: {BoardCard},
  props:{
    visible: {
      type: Boolean,
      required: true
    },
    localStorageId: {
      type: Number,
      required: false
    },
    url: {
      type: String,
      required: false
    }
  },

  data() {
    return {
      boards: [],
      apiKey: 'peluso',
      selectedBoard: null,
    };
  },
  created() {
    this.getBoards();
    if (!this.visible)
      this.$emit('select-board', this.boards[0]);
  },
  methods: {
    async getBoards() {


      if(!this.url){
        this.boards = JSON.parse(localStorage.getItem(this.localStorageId + "/boards"));
        return
      }

      const params = { apiKey: this.apiKey };

      try {
        const response = await axios.get(this.url, { params });
        this.boards = response.data;
      } catch (error) {
        console.log(error);
      }
    },
    selectBoard(board) {
      this.selectedBoard = board;
      this.$emit('select-board', board);
    },
    isActive(board) {
      return this.selectedBoard && this.selectedBoard.id === board.id;
    }
  },
};
</script>

<style>
.board-list-container {
  margin: auto;
  border-right: #ccc 10px solid;
  height: 100%;
}

h1{
  padding: 40px;
  text-align: center;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 40px;
  font-weight: bold;
}

ul {
  list-style-type: none;
  align-content: center;
  padding-left: 0;
}

</style>
