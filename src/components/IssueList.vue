<template>
  <div v-if="search" class="search-bar">
    <form @submit.prevent="searchIssues">
      <input v-model="searchText" type="text" placeholder="Search by title"/>
      <select v-model="issueType">
        <option value="">Filter by type</option>
        <option value="TASK">Task</option>
        <option value="BUG">Bug</option>
        <option value="USER_STORY">User Story</option>
      </select>
      <button type="submit">Search</button>
    </form>
  </div>
  <div class="container">
    <div class="row">
      <div class="issue-list-container container-fluid">
        <div id="todo_list" class="issue-list-column" @dragover="dragOver" @drop="drop" @dragleave="dragLeave">
          <h3>TODO</h3>
          <div class="new-issue">
            <form @submit.prevent="createIssue">
              <label for="title">Title</label>
              <input type="text" id="title" placeholder="Issue title" v-model="title" required>
              <button type="submit">Create issue</button>
              <button type="reset">Cancel</button>
            </form>
          </div>
          <IssueCard draggable="true"
                     @dragend="dragEnd"
                     @dragstart="drag" v-for="issue in todoIssues" :key="issue.id"
                     :issue="issue"
                     :issueClick="handleIssueClick"/>
        </div>
        <div id="in_progress_list" class="issue-list-column" @dragover="dragOver" @drop="drop" @dragleave="dragLeave">
          <h3>IN_PROGRESS</h3>
          <IssueCard draggable="true" @dragstart="drag"
                     @dragend="dragEnd"
                     v-for="issue in inProgressIssues" :key="issue.id"
                     :issue="issue"
                     :issueClick="handleIssueClick"/>
        </div>
        <div id="done_list" class="issue-list-column" @dragover="dragOver" @drop="drop" @dragleave="dragLeave">
          <h3>DONE</h3>
          <IssueCard draggable="true"
                     @dragend="dragEnd"
                     @dragstart="drag" v-for="issue in doneIssues" :key="issue.id"
                     :issue="issue"
                     :issueClick="handleIssueClick"/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>


import axios from 'axios';
import IssueCard from './IssueCard.vue';

export default {
  components: {
    IssueCard
  },
  props: {
    selectedBoard: {
      type: Object,
      required: false,
    },
    localStorageId: {
      type: Number,
      required: false,
    },
    search: {
      type: Boolean,
      required: true
    },
    url: {
      type: String,
      required: false
    },
    handleIssueClick: {
      type: Function,
      required: true,
    }
  },

  data() {
    return {
      issuesActive: [],
      issues: [],
      apiKey: 'peluso',
      searchText: '',
      issueType: '',
      draggedIssue: '',
      title: ''
    };
  },
  created() {
    this.getIssues();
  },
  watch: {
    selectedBoard() {
      this.draggedIssue = null;
      this.searchText = '';
      this.issueType = '';
      this.getIssues();
    },
    searchText() {
      this.searchIssues();
    },
    issueType() {
      this.searchIssues();
    }
  },
  computed: {
    todoIssues() {
      return this.issuesActive.filter(issue => issue.status === 'TODO');
    },
    inProgressIssues() {
      return this.issuesActive.filter(issue => issue.status === 'IN_PROGRESS');
    },
    doneIssues() {
      return this.issuesActive.filter(issue => issue.status === 'DONE');
    }
  },

  methods: {
    dragEnd(event) {
      event.preventDefault();
      console.log('drag end');
      event.target.classList.remove('drag');
    },

    //drag leave event function
    dragLeave(event) {
      event.preventDefault();
      console.log('drag leave');
      event.target.classList.remove('drag-over');
    },

    //drag function
    drag(event) {
      event.dataTransfer.setData("text", event.target.id);
      event.target.classList.add('drag');
    },

    //drop event function
    async drop(event) {
      event.preventDefault();
      console.log('drop');
      if(event.target.children[0].tagName !== 'H3')
        return;
      const status = event.target.children[0].innerHTML;
      console.log(status);
      const id = event.dataTransfer.getData("text");
      await this.updateIssue(status, id);
      await this.getIssues();
      event.target.classList.remove('drag-over');
    },

    //drag over event function
    dragOver(event) {
      event.preventDefault();
      event.target.classList.add('drag-over');
    },


    async getIssues() {
      if (!this.url && this.selectedBoard) {
        this.issues = JSON.parse(localStorage.getItem(this.localStorageId + '/issues'));
        this.issuesActive = this.issues.filter(issue => issue.board === this.selectedBoard.id);
        return;
      }

      if (!this.selectedBoard) {
        return;
      }

      const url = `${this.url}/${this.selectedBoard.id}`;

      const params = {
        apiKey: this.apiKey,
      };

      try {
        const response = await axios.get(url, {params});
        this.issuesActive = response.data;
      } catch (error) {
        console.log(error);
      }
    },

    async searchIssues() {
      const params = {
        apiKey: this.apiKey,
        search: this.searchText,
        issueType: this.issueType,
      };


      if (!this.url && this.selectedBoard) {
        if (this.searchText && this.issueType) {
          this.issuesActive = this.issues.filter(issue => issue.board === this.selectedBoard.id && issue.title.includes(this.searchText) && issue.type === this.issueType);
        } else if (this.issueType) {
          this.issuesActive = this.issues.filter(issue => issue.board === this.selectedBoard.id && issue.type === this.issueType);
        } else if (this.searchText) {
          this.issuesActive = this.issues.filter(issue => issue.board === this.selectedBoard.id && issue.title.includes(this.searchText));
        } else {
          this.issuesActive = this.issues.filter(issue => issue.board === this.selectedBoard.id);
        }
        return;
      }

      if (!this.selectedBoard)
        return;

      const url = `${this.url}/${this.selectedBoard.id}/search`;


      try {
        const response = await axios.get(url, {params});
        this.issuesActive = response.data;
      } catch (error) {
        console.log(error);
      }
      this.selectedIssue = null;
    },

    async createIssue() {
      if (!this.url) {
        const issue = {
          id: this.issues.length + 1,
          title: this.title,
          type: 'TASK',
          status: 'TODO',
          board: this.selectedBoard.id,
        };
        this.issues.push(issue);
        localStorage.setItem(this.localStorageId + '/issues', JSON.stringify(this.issues));
        this.$emit('issue-created');
        this.title = '';
      } else {
        const url = `${this.url}/${this.selectedBoard.id}/issues`;
        const data = {
          title: this.title,
        };
        const config = {
          headers: {
            'Content-Type': 'application/json',
          },
          params: {
            apiKey: this.apiKey,
          },
        };

        try {
          const response = await axios.post(url, data, config);
          console.log(response.data);
        } catch (error) {
          console.log(error);
        }
        this.title = '';
        this.$emit('issue-created');
      }
      await this.getIssues();
    },

    async updateIssue(status, id) {
      console.log(this.draggedIssue);
      const apiKey = this.apiKey;

      console.log(id + " update ")

      if (!this.url) {
        for (let i = 0; i < this.issues.length; i++) {
          if (this.issues[i].id === parseInt(id)) {
            this.issues[i].status = status;
          }
        }
        localStorage.setItem(this.localStorageId + '/issues', JSON.stringify(this.issues));
      } else {
        await axios.put(
            `https://supsi-ticket.cloudns.in/supsi-agile-boards/bff/issues/${id}/status?apiKey=${apiKey}`,
            {status: status,});
      }


    }
  },
};
</script>


<style>
.search-bar {
  width: 100%;
  padding: 5px;
  border-bottom: 2px solid #ccc;
}

.search-bar input {
  padding: 10px 10px;
  border: 2px solid #ccc;
  border-radius: 20px;
  font-size: 14px;
  width: 75%;
  margin: 10px 10px 10px 10px;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
}

.search-bar button {
  background-color: black;
  color: #fff;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 14px;
  border: none;
  border-radius: 20px;
  padding: 10px 20px;
  cursor: pointer;
}

.search-bar button:hover {
  background-color: #0069d9;
}

.search-bar select {
  border: 1px solid #ccc;
  border-radius: 20px;
  padding: 10px;
  margin-right: 10px;
  margin-left: 40px;
  font-size: 14px;
  font-weight: bold;
  font-family: Arial, Helvetica, sans-serif;
}

.search-bar select:focus {
  outline: none;
  box-shadow: 0 0 3px rgba(0, 0, 0, 0.3);
}

.issue-list-container {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  min-width: 100%;
}

#done_list.issue-list-column {
  width: 32%;
  max-width: 500px;
  border: 2px solid #7ebe7f;
}

#in_progress_list.issue-list-column {
  width: 32%;
  max-width: 500px;
  border: 2px solid #fdd17f;
}

#todo_list.issue-list-column {
  width: 32%;
  max-width: 500px;
  border: 2px solid #7e7efe;
}

#todo_list.issue-list-column h3 {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 10px;
  color: white;
  background-color: #7e7efe;
  padding: 10px;
}

#in_progress_list.issue-list-column h3 {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 10px;
  color: white;
  background-color: #fdd17f;
  padding: 10px;
}

#done_list.issue-list-column h3 {
  font-family: Arial, Helvetica, sans-serif;
  font-size: 20px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 10px;
  color: white;
  background-color: #7ebe7f;
  padding: 10px;
}

.issue-list-column.drag-over {
  border: 2px dashed #3c3f41;
}

#done_list.drag-over {
  background-color: green;
  opacity: 40%;
  content-visibility: hidden;
}

#in_progress_list.drag-over {
  background-color: orange;
  opacity: 40%;
  content-visibility: hidden;
}

#todo_list.drag-over {
  opacity: 40%;
  content-visibility: hidden;
  background-color: blue;

}

.new-issue {
  margin: 10px 5px 10px 5px;
  border: #ccc 2px solid;
  border-radius: 5px;
  padding: 10px;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
  font-size: 18px;
}

.new-issue button {
  background-color: #f1f1f1;
  color: #000;
  border: 1px solid lightgrey;
  border-radius: 4px;
  padding: 2px 20px;
  cursor: pointer;
  margin: 3px;
  font-family: Arial, Helvetica, sans-serif;
  font-weight: bold;
}

.new-issue button:hover {
  background-color: #e3e3e3;
}

.new-issue input {
  flex: 1;
  padding: 2px 20px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin: 5px;
}

.new-issue label {
  margin: 3px;
}

.row {
  width: 100%;
}

</style>