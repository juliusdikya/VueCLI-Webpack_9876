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
            hide-details>
        </v-text-field>
    <v-spacer></v-spacer>

    <v-btn color="success" dark @click="dialogFinished = true" style="margin-right:15px;">
        To Do Selesai
    </v-btn>

    <v-btn color="success" dark @click="dialog = true">
        Tambah
    </v-btn>
    </v-card-title>

    <v-data-table :headers="headers" :items="todos" :search="search">

        <template v-slot:[`item.priority`]="{ item }">
            <td>
                <v-card v-if="item.priority == 'Penting'" style="padding:2px 5px;border-color: coral; color: coral;" outlined>
                    {{ item.priority }}
                </v-card>
                <v-card v-else-if="item.priority == 'Biasa'" style="padding:2px 5px;border-color: blue; color: blue;" outlined>
                    {{ item.priority }}
                </v-card>
                <v-card v-else outlined style="padding:2px 5px; border-color:green; color: green;">
                    {{ item.priority }}
                </v-card>
            </td>
        </template>

        <template v-slot:[`item.actions`]="{ item }">
            <v-btn small class="mr-2" @click="editItem(item)">
                edit
            </v-btn>
        <v-btn small @click="deleteItem(item)">
                delete
        </v-btn>
        </template>
    </v-data-table>
</v-card>

 <v-dialog v-model="dialog" persistent max-width="600px">
    <v-card>
        <v-card-title>
            <span class="headline" 
                v-if="adding == true" >Form Add</span>
            <span class="headline" 
                v-else>Form Edit</span>
            </v-card-title>
    <v-card-text>

 <v-container>
    <v-text-field
        v-model="formTodo.task"
        label="Task"
        required>
    </v-text-field>

 <v-select
    v-model="formTodo.priority"
    :items="[ 'Penting' , 'Biasa' , 'Tidak penting']"
    label="Priority"
    required>
 </v-select>

<v-textarea v-model="formTodo.note" label="Note" required></v-textarea>

</v-container>
    </v-card-text>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="cancel">Cancel</v-btn>
            <v-btn v-if="adding == true" 
                color="blue darken-1" 
                text 
                @click="save">
                Save
            </v-btn>
            <v-btn v-else 
                color="blue darken-1" 
                text 
                @click="edit(formTodo)">
                Save
            </v-btn>
        </v-card-actions>
    </v-card>
</v-dialog>

<v-dialog v-model="deletedialog" persistent max-width="400px">
    <v-card>
        <v-card-title>
            <span class="headline">Yakin hapus?</span>
        </v-card-title>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green darken-1" text @click="confirmdelete">Ya</v-btn>
            <v-btn color="red darken-1" text @click="cancel">Tidak</v-btn>
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
        deletedialog: false,
        notedialog: false,
        searchp: "All Priority",
        adding: true,
        edititem: null,
        filters: {
                search: '',
                priority: '',
            },
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
            this.cancel();
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
            this.edititem = null;
            this.adding = true;
            this.deletedialog = false;
            this.notedialog = false;
        },
        deleteItem(item) {
            this.dialogdel = true;
            this.edititem = item;
        },
        confirmdelete() {
            this.todos.splice(this.todos.indexOf(this.edititem), 1);
            this.deletedialog = false;
        },
        editItem(item) {
            this.adding = false;
            this.formTodo = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            };
            this.dialog = true;
            this.edititem = item;
        },
        closeDelete() {
            this.dialogDelete = false;
            this.$nextTick(() => {
                this.editedItem = Object.assign({}, this.defaultItem);
                this.editedIndex = -1;
            });
        },
        detailItem(item) {
            this.detail = {
                task: item.task,
                priority: item.priority,
                note: item.note,
            }
            this.notedialog = true;
        },
        edit(formTodo) {
            this.edititem.task = formTodo.task;
            this.edititem.priority = formTodo.priority;
            this.edititem.note = formTodo.note;
            this.cancel();
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
    },
};
</script>
