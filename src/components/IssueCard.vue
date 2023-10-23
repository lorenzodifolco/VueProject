<template>
  <div :id="id" class="issue-card" :class="className" @click="issueClick(issue)" @dragstart="drag">
    <div class="issue-card-title">{{ issue.title }}</div>
  </div>
</template>

<script>
export default {
  props: {
    issue: {
      type: Object,
      required: true
    },
    issueClick: {
      type: Function,
      required: true,
    }
  },
  computed: {
    className() {
      return this.issue.type.toLowerCase();
    },
    id() {
      return this.issue.id;
    }
  },
  methods: {
    drag() {
      this.$emit('drag', this.issue);
    }
  }
}
</script>

<style>
.issue-card {
  margin-left: 5px;
  margin-right: 5px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
  padding: 10px;
  cursor: pointer;
  border-left: 10px solid transparent;
}

.issue-card:hover {
  background-color: #eee;
}

.issue-card-title {
  font-size: 18px;
  font-family: Arial, Helvetica, sans-serif;
}

.task {
  border-color: lightgreen;
}

.bug {
  border-color: lightcoral;
}

.user_story {
  border-color: lightblue;
}

.issue-card.drag {
  opacity: 0.3%;
  cursor: move;
}
#todo_list.drag-over .issue-card {
  cursor: pointer;
}
</style>
