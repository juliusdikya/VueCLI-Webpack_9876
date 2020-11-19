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

    <v-btn color="success" dark @click="dialogfinish = true" style="margin-right:15px;"> To Do Selesai </v-btn>
    <v-btn color="success" dark @click="dialog = true"> Tambah </v-btn>
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
            <v-btn small class="mr-2" @click="editItem(item)"> edit </v-btn>
        <v-btn small @click="deleteItem(item)"> delete </v-btn>
        <input type="checkbox" 
                style="margin-left: 20px; margin-right:-33px" @change="checklist(item)" v-model="item.delete">
        </template>
    </v-data-table>
</v-card>

<v-dialog v-model="dialog" persistent max-width="600px">
    <v-card>
       <v-card-title>
            <span class="headline" v-if="adding==true" >Form Add</span>
            <span class="headline" v-else>Form Edit</span>
            </v-card-title>
        <v-card-text>
            <v-container>
                <v-text-field v-model="formTodo.task" label="Task" required></v-text-field>
                <v-select
                    v-model="formTodo.priority" :items="[ 'Penting' , 'Biasa' , 'Tidak penting' ]" label="Priority" required>
                </v-select>
                <v-textarea v-model="formTodo.note" label="Note" required></v-textarea>
            </v-container>
        </v-card-text>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue" text @click="save">Save</v-btn>
            <v-btn color="blue" text @click="cancel">Cancel</v-btn>
        </v-card-actions>
    </v-card>
</v-dialog>

<v-dialog v-model="dialogfinish" persistent max-width="850px">
    <v-card>
        <v-card-title> <span class="headline">To do Selesai </span> </v-card-title>
        <v-card-text>
        <v-container>
        <v-data-table :headers="headersclear" :items="todoclear">
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
        </v-data-table>
        </v-container>
        </v-card-text>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue" text @click="dialogfinish = false">Close</v-btn>
        </v-card-actions>
    </v-card>
</v-dialog>

<v-dialog v-model="deletedialog" persistent max-width="400px">
    <v-card>
        <v-card-title>
            <span class="headline font-weight-bold">Yakin hapus?</span>
        </v-card-title>
        <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="green" text @click="cancelDelete">Tidak</v-btn>
            <v-btn color="red" text @click="confirmDelete">Ya</v-btn>
        </v-card-actions>
    </v-card>
</v-dialog>


<!-- <v-card>
    <v-card-title> Coba delete </v-card-title>
    <v-card-text>
        <ul v-for=" (todos, x) in selected " :key="x">
        </ul>
    </v-card-text>
    <v-card-actions>
        <v-btn>Hapus</v-btn>
    </v-card-actions>
</v-card> -->


<v-card>
    <v-card-title> Delete Multiple </v-card-title>
    <v-card-text align="start">
        <ul v-for=" (todos, temp) in selected " :key="temp">
            <li>{{todos.task}}</li>
        </ul>
    </v-card-text>
    <v-card-actions>
        <v-btn color="red" class="white--text" @click="deleteAll">Hapus</v-btn>
    </v-card-actions>
</v-card>

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
        dialogfinish: false,
        adding:true,
        editCount: -1,
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
            { 
                text: "Priority", value: "priority" 
            },
            { 
                text: "Note", value: "note" 
            },
            { 
                text: "Actions", value: "actions" 
            },
        ],
        headersclear: [
            {
                text: "Task",
                align: "start",
                sortable: true,
                value: "task",
            },
            { 
                text: "Priority", value: "priority" 
            },
            { 
                text: "Note", value: "note" 
            },
            //tdk ada action
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
            task: null, priority: null, note: null,
            },
        todoclear: [],
        selected: [],
		};
	},
	methods: {
		save() {
            if(this.editCount > -1){
                this.todos.splice(
                    this.editCount, 
                    1, this.formTodo);
                 this.editCount = -1;
            }     
            else {
                this.todos.push(this.formTodo);
                this.editCount = -1; 
            }                               
            this.resetForm();
            this.dialog = false;	
			
		},
		cancel() {
            this.adding = true;
			this.resetForm();
            this.dialog = false;
            this.editCount = -1;
		},
		resetForm() {
			this.formTodo = {
                task: null, 
                priority: null, 
                note: null,
			};
        },
        
        //edit
		editItem(item) {
            this.adding = false;
			this.editCount = this.todos.indexOf(item);

			this.formTodo = { task: item.task, priority: item.priority, note: item.note,
            };
            
			this.dialog = true;
        },
        
        //delete

        confirmDelete() {
			this.todoclear.push(this.todos[this.editCount]);
			this.todos.splice(this.editItem, 1);
			this.deletedialog = false;
			this.editCount = -1;
        },

		deleteItem(item) {
            this.deletedialog = true;
			this.editCount = this.todos.indexOf(item);
        },
        
		cancelDelete() {
			this.deletedialog = false;
			this.editCount = -1;
        },
        
	    //checklist 

        checklist(item){
            if(this.selected.includes(item)) {
                this.selected.splice(this.selected.indexOf(item), 1);
            } else {
                this.selected.push(item);
            }
        },

        //delete checked list
        deleteAll(){
            this.todos = this.todos.filter(del=>!this.selected.includes(del)); this.selected = [];
        },
        
	},
};
</script>
