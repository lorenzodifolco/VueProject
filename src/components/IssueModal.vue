<template>
  <div class="modal">
    <div class="modal-overlay" @click="close"></div>
    <div class="modal-content" :class="typeName">
      <div class="modal-header" :class="typeName">
        <h2>{{ issue.type }}</h2>
        <button class="modal-close-btn" @click="close">X</button>
      </div>
      <div class="modal-body">
        <div :class="statusName">{{ issue.status }}</div>
        <p class="modal-title">{{ issue.title }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    issue: {
      type: Object,
      required: true
    }
  },
  computed: {
    statusName() {
      return this.issue.status.toLowerCase();
    },
    typeName() {
      return this.issue.type.toLowerCase();
    },
  },
  methods: {
    close() {
      this.$emit("close");
    }
  }
};
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
}

.modal-content {
  position: relative;
  background-color: #ffffff;
  border: 8px solid #000000;
  min-width: 20%;
  max-width: 40%;
}

.bug {
  border-color: lightcoral;
  color: black;
}


.done {
  background-color: #7ebe7f;
  color: white;
  border-radius: 8px;
  align-content: center;
  padding: 2%;
  text-align: center;
  border: 3px solid black;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  margin: 15px 0 15px 0;
  display: inline-block;
  float: right;
}

.in_progress {
  background-color: #faa70e;
  color: white;
  border-radius: 8px;
  align-content: center;
  padding: 2%;
  text-align: center;
  border: 3px solid black;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  margin: 15px 0 15px 0;
  display: inline-block;
  float: right;
}


.todo {
  background-color: #7e7efe;
  color: white;
  align-content: center;
  padding: 2%;
  border: 1px solid black;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  margin: 15px 15px 15px 0;
  display: inline-block;
  float: right;
}

h2 {
  margin: 0;
  color: black;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 40px;
}

.modal-title {
  font-size: 30px;
  font-family: Arial, Helvetica, sans-serif;
  color: black;
}

.task {
  border-color: lightgreen;
  color: black;

}

.user_story {
  border-color: lightblue;
  color: black;
}

.modal-header.task {
  padding: 4%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  border-bottom: 1px solid #ccc;
  background: lightgreen;
}

.modal-header.bug {
  padding: 4%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  border-bottom: 1px solid #ccc;
  background: lightcoral;
}

.modal-header.user_story {
  padding: 4%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 10px;
  border-bottom: 1px solid #ccc;
  background: lightblue;
}

.modal-close-btn {
  color: black;
  border: none;
  background-color: transparent;
  font-size: 40px;
  cursor: pointer;
  font-family: Arial, Helvetica, sans-serif;
}

.modal-body {
  padding: 7% 7% 7% 7%;
}

.task {
  color: lightgreen;
}

</style>
