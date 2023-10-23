<template>
  <div class="board-card" :class="{ 'active': active }" @click="selectBoard" >
    <div class="board-card-title">{{ board.name }}</div>
  </div>
</template>

<script>
export default {
  props: {
    board: {
      type: Object,
      required: true
    },
    active: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  methods: {
    selectBoard() {
      this.$emit('select-board', this.board);
    }
  }
}
</script>

<style>
.board-card {
  padding: 10px;
  border-top: 1px solid #ccc;
  border-bottom: 1px solid #ccc;
  cursor: pointer;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
}

.board-card:hover {
  background-color: #848484;
}

.active {
  background-color: #cccccc;
}

.board-card-title {
  font-size: 18px;
  margin-bottom: 15px;
  margin-top: 10px;
  text-align: center;
}
</style>