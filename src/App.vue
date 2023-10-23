<template>
    <MyBoard
            :boards="hardCodedBoards"
            :issues="hardCodedIssues"
            :issueClick="handleIssueClick"
            :visible="false"
            :search="false"
            :localStorageId="3"/>
    <MyBoard
            :boards="hardCodedBoards"
            :issues="hardCodedIssues"
            :issueClick="handleIssueClick"
            :visible="true"
            :search="true"
            :localStorageId="1"/>
    <MyBoard
            :boards="hardCodedBoards"
            :issues="hardCodedIssues"
            :issueClick="handleIssueClickAlert"
            :visible="true"
            :search="false"
            :localStorageId="2"/>
    <MyBoard
            :url="boardUrl"
            :search="true"
            :visible="true"
            :issueClick="handleIssueClick"/>
    <IssueModal v-if="modal" :issue="issue" @close="closeModal"/>
</template>

<script>
import MyBoard from "@/components/MyBoard.vue";
import IssueModal from "@/components/IssueModal.vue";

export default {
    components: {
        MyBoard,
        IssueModal
    },

    data() {
        return {
            modal: false,
            issue: null,
            boardUrl: 'https://supsi-ticket.cloudns.in/supsi-agile-boards/bff/boards',
            hardCodedBoards: [
                {id: 1, name: "Board 1"},
                {id: 2, name: "Board 2"}
            ],
            hardCodedIssues: [
                {
                    id: 1,
                    title: "titolo",
                    type: "BUG",
                    status: "TODO",
                    board: 1
                }, {
                    id: 2,
                    title: "titolo222",
                    type: "BUG",
                    status: "DONE",
                    board: 1
                }
            ]
        };
    },
    methods: {
        handleIssueClick(issue) {
            this.issue = issue;
            this.modal = true;
        },
        closeModal() {
            this.modal = false;
        },
        handleIssueClickAlert(issue) {
            alert(issue.title)
        }
    }
}
</script>
