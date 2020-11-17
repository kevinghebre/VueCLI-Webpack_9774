<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">UGD To Do List</h3>
        <v-card>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="purple" dark @click="dialogDone = true">
                    Todo Selesai
                </v-btn>
                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            <v-dialog v-model="dialogDone" persistent max-width="600px">
                <v-card>
                    <v-card-title>
                        <span class="headline">Finish Todo</span>
                        <v-btn class="red" dark @click="dialogDone = false">
                            Cancel
                        </v-btn>
                    </v-card-title>
                    <v-card-actions>
                        <v-spacer></v-spacer>
                        <template>
                            <v-data-table
                                :headers="headersDone"
                                :items="todosDone"
                                :search="search"
                            >
                                <template v-slot:[`item.priority`]="{ item }">
                                    <v-chip
                                        :color="getColor(item.priority)"
                                        dark
                                        outlined
                                        label
                                    >
                                        {{ item.priority }}
                                    </v-chip>
                                </template>
                            </v-data-table>
                        </template>
                    </v-card-actions>
                </v-card>
            </v-dialog>
            <v-data-table :headers="headers" :items="todos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                    <v-chip
                        :color="getColor(item.priority)"
                        dark
                        outlined
                        label
                    >
                        {{ item.priority }}
                    </v-chip>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-icon color="blue" @click="editItem(item)">
                        mdi-pencil
                    </v-icon>
                    <v-icon color="red" @click="deleteItem(item)">
                        mdi-delete
                    </v-icon>
                </template>
            </v-data-table>
        </v-card>

        <v-dialog v-model="dialogDelete" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Delete Todo</span>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="closeDelete">
                        Nooo
                    </v-btn>
                    <v-btn
                        color="blue darken-1"
                        text
                        @click="deleteItemConfirm"
                    >
                        Yeahhh
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>
                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>
                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>

<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            editedIndex: -1,
            dialogDelete: false,
            dialogDone: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
            ],
            headersDone: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                },
            ],
            formTodo: {
                task: null,
                priority: null,
                note: null,
            },
            todosDone: [],
        };
    },
    methods: {
        save() {
            if (this.editedIndex > -1) {
                this.todos.splice(this.editedIndex, 1, this.formTodo);
            } else {
                this.todos.push(this.formTodo);
            }
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem(item) {
            this.editedIndex = this.todos.indexOf(item);
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
            this.dialog = true;
        },
        deleteItem(item) {
            this.editedIndex = this.todos.indexOf(item);
            this.dialogDelete = true;
        },
        deleteItemConfirm() {
            this.todosDone.push(this.todos[this.editedIndex]);
            this.todos.splice(this.editedIndex, 1);
            this.dialogDelete = false;
            this.editedItem = -1;
        },
        closeDelete() {
            this.dialogDelete = false;
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem);
                this.editedIndex = -1;
            });
        },
        getColor(priority) {
            if (priority == "Penting") return "red";
            else if (priority == "Biasa") return "blue";
            else return "green";
        },
    },
};
</script>
