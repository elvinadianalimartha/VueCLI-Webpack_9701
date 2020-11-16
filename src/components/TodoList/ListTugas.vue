<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List</h3>
    
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

                <v-select
                    v-model="sortBy"
                    :items="['Penting', 'Tidak penting']"
                    @change="sortItems(sortBy)"
                    label="Urutkan"
                    dense
                    outlined
                    required
                    class="mt-7"
                ></v-select>

                <v-spacer></v-spacer>

                <v-btn color="success" dark @click="dialog = true">
                    Tambah
                </v-btn>
            </v-card-title>
            <v-data-table 
                :headers="headers" 
                :items="todos" 
                :search="search" 
                show-expand 
                item-key="task">
                <template v-slot:expanded-item="{ headers, item }">
                    <td :colspan="headers.length">
                        <b>Note: </b> <br>{{ item.note }}<br>
                    </td>
                </template>
                <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-chip small label outlined :color="setColor(item.priority)">{{ item.priority }}</v-chip>
                    </td>
                </template>
                <template v-slot:[`item.actions`]="{ item }">
                    <v-btn
                        icon
                        color="blue"
                        class="mr-2"
                        @click="editItem(item); indexTodo = todos.indexOf(item)">
                        <v-icon>mdi-pencil</v-icon>
                    </v-btn>
                    <v-btn 
                        icon
                        color="red" 
                        @click="deleteItem(item)">
                        <v-icon>mdi-delete</v-icon>
                    </v-btn>
                </template>
                <template v-slot:[`item.checkbox`]="{ item }">
                    <v-checkbox
                        color="primary"
                        @change="chooseItem(item)"
                    ></v-checkbox>
                </template>
            </v-data-table>
        </v-card>

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

        <v-dialog v-model="dialogEdit" persistent max-width="600px">
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
                    <v-btn color="blue darken-1" text @click="cancelEdit">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="saveEdit">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="400px">
            <v-card>
                <v-card-title>
                    <span class="headline">Yakin ingin menghapus?</span>
                </v-card-title>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green darken-1" text @click="cancelDelete">
                        Tidak
                    </v-btn>
                    <v-btn color="red darken-1" text @click="saveDelete">
                        Ya
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-card class="mt-5" v-if = "selectedItems.length > 0" >
            <v-card-title>
                Delete multiple:  
            </v-card-title> 
            <v-card-subtitle>
                <v-list-item v-for="(item, index) in selectedItems" :key="index">
                    <v-list-item-content>
                        <li v-text = "item.task"></li>
                    </v-list-item-content>
                </v-list-item>
                <v-btn
                    depressed
                    color="error"
                    class="mt-3"
                    @click="deleteSelectedItem">
                    hapus semua
                </v-btn>  
            </v-card-subtitle>
        </v-card>
    </v-main>
</template>

<script>
export default {
    name: "List",
    data() {
        return {
            sortBy: null,

            search: null,
            dialog: false,
            dialogEdit: false,
            dialogDelete: false,
            indexTodo: null,
            itemToDelete: null,

            selectedItems: [],

            headers: [
                { text: '', value: 'data-table-expand'},
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Actions", value: "actions" },
                { text: '', value: "checkbox"}
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
                    note: "bersama teman2",
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
        };
    },
    methods: {
        save() {
            this.todos.push(this.formTodo);
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
            this.dialogEdit = true;
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
        },
        saveEdit() { 
            this.todos[this.indexTodo].task = this.formTodo.task;
            this.todos[this.indexTodo].priority = this.formTodo.priority;
            this.todos[this.indexTodo].note = this.formTodo.note;
            this.resetForm();
            this.dialogEdit = false;                
        },
        cancelEdit() {
            this.resetForm();
            this.dialogEdit = false;
        },

        deleteItem(item) {
            this.dialogDelete = true;
            this.itemToDelete = item;
        },
        saveDelete() {
            this.todos.splice(this.todos.indexOf(this.itemToDelete), 1);
            this.dialogDelete = false;
        },
        cancelDelete() {
            this.dialogDelete = false;
        },
        setColor(priority){
            if(priority == "Penting"){
                return 'red';
            } else if(priority == 'Biasa'){
                return 'blue';
            } else if(priority == 'Tidak penting') {
                return 'green';
            }
        },
        chooseItem(item) {
            if (this.selectedItems.includes(item)) {
                this.selectedItems.splice(this.selectedItems.indexOf(item), 1);
            } else {
                this.selectedItems.push(item);
            }
        },
        deleteSelectedItem() {
            for(var i = 0; i < this.selectedItems.length; i++){
                this.todos.splice(this.todos.indexOf(this.selectedItems[i]), 1);  
            }
            this.selectedItems = [];
        },
        sortItems(sortBy) {  
            function sort(a, b) {
                if (sortBy == "Penting") {
                    if(a.priority == "Penting" < b.priority == "Biasa")
                        return -1;
                    if(a.priority == "Penting" < b.priority == "Tidak penting")
                        return -1;
                    if(a.priority == "Biasa" > b.priority == "Penting")
                        return 1;
                    if(a.priority == "Tidak penting" > b.priority == "Penting")
                        return 1;
                    if(a.priority == "Tidak penting" > b.priority == "Biasa")
                        return 1;
                    return 0;
                } else if (sortBy == "Tidak penting") {   
                    if(a.priority == "Penting" > b.priority == "Biasa")
                        return 1;
                    if(a.priority == "Penting" > b.priority == "Tidak penting")
                        return 1;
                    if(a.priority == "Biasa" > b.priority == "Tidak penting")
                        return 1;
                    if(a.priority == "Tidak penting" < b.priority == "Penting")
                        return -1;
                    if(a.priority == "Tidak penting" < b.priority == "Biasa")
                        return -1;
                    return 0;
                }     
            }
        }
    },
};
</script>