<template>
  <div class="row">
    <div class="board-list">
      <BoardList @select-board="selectBoard" :visible="visible" :url="url" :local-storage-id="localStorageId"/>
    </div>
    <div class="issue-list">
      <IssueList :selected-board="selectedBoard" :search="search" :url="url" :handleIssueClick="issueClick" :local-storage-id="localStorageId"/>
    </div>
  </div>
</template>

<script>
import BoardList from './BoardList.vue';
import IssueList from './IssueList.vue';

export default {
  components: {
    BoardList,
    IssueList,
  },

  props: {
    url: {
      type: String,
      required: false,
    },
    search: {
      type: Boolean,
      required: true,
    },
    visible: {
      type: Boolean,
    },
    issues: {
      type: Array,
      required: false,
    },
    boards: {
      type: Array,
      required: false,
    },
    localStorageId: {
      type: Number,
      required: false,
    },
    issueClick: {
      type: Function,
      required: true,
    },
  },
  created() {
    if (this.url)
      return;
    if (localStorage.getItem(this.localStorageId + "/boards") === null || localStorage.getItem(this.localStorageId + "/boards") === "[]") {
      localStorage.setItem(this.localStorageId + "/boards", JSON.stringify(this.boards));
    }
    if (localStorage.getItem(this.localStorageId + "/issues") === null || localStorage.getItem(this.localStorageId + "/issues") === "[]") {
      localStorage.setItem(this.localStorageId + "/issues", JSON.stringify(this.issues));
    }
  },
  data() {
    return {
      selectedBoard: null,
    }
  },
  methods: {
    selectBoard(board) {
      this.selectedBoard = board;
    }
  }
}
</script>


<style>

* {
  box-sizing: border-box;
  margin: 0;
}

.row {
  display: flex;
}

.board-list {
  min-width: 15%;
}

.issue-list {
  width: 100%;
}
</style>