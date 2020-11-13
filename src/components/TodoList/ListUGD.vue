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
            <v-btn color="warning" dark @click="history = true">
                ToDo Selesai
            </v-btn>
            <v-btn color="success" dark @click="dialog = true">
                Tambah
            </v-btn>
        </v-card-title>

        <v-data-table :headers="headers" :items="todos" :search="search">
            <template v-slot:[`item.priority`]="{ item }">
                    <td>
                        <v-card v-if="item.priority == 'Penting'" style="border-color: lightcoral; color: lightcoral; width: fit-content;" outlined>
                            {{ item.priority }}
                        </v-card>
                        <v-card v-else-if="item.priority == 'Biasa'" style="border-color: lightblue; color: lightblue; width: fit-content;" outlined>
                            {{ item.priority }}
                        </v-card>
                        <v-card v-else outlined style="border-color: lightgreen; color: lightgreen; width: fit-content;">
                            {{ item.priority }}
                        </v-card>
                    </td>
                </template>
                
            <template v-slot:[`item.actions`]="{ item }">
                <v-icon small class="pencil mr-2" @click="editItem(item)">  {{ icons.mdiPencil }}</v-icon>
                <v-icon small class="bin mr-2" @click="deleteItem(item)">   {{ icons.mdiDelete }}</v-icon>
            </template>
        </v-data-table>

    </v-card>
    
    <v-dialog v-model="history" >
        

        <v-card>
            <h3 class="text-h3 font-weight-medium mb-5">Finished ToDo List</h3>
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
            </v-card-title>

            <v-data-table :headers="headers2" :items="ftodos" :search="search">
                <template v-slot:[`item.priority`]="{ item }">
                        <td>
                            <v-card v-if="item.priority == 'Penting'" style="border-color: lightcoral; color: lightcoral; width: fit-content;" outlined>
                                {{ item.priority }}
                            </v-card>
                            <v-card v-else-if="item.priority == 'Biasa'" style="border-color: lightblue; color: lightblue; width: fit-content;" outlined>
                                {{ item.priority }}
                            </v-card>
                            <v-card v-else outlined style="border-color: lightgreen; color: lightgreen; width: fit-content;">
                                {{ item.priority }}
                            </v-card>
                        </td>
                    </template>
                    
            </v-data-table>

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

        <v-btn v-if="edit==true" color="blue darken-1" text @click="update(formTodo)">
            Update
        </v-btn>

        <v-btn v-else color="blue darken-1" text @click="save">
            Save
        </v-btn>
    </v-card-actions>    
 </v-card>
 </v-dialog>
    <v-dialog v-model="confirmDel" persistent max-width="400px">
        <v-card>
            <v-card-title>
                <span class="headline">
                    Hapus data?
                </span>
            </v-card-title>

            <v-card-actions>
                <v-spacer></v-spacer>
                        
                    <v-btn color="green darken-1" text @click="cancel">
                        Tidak
                    </v-btn>

                    <v-btn color="red darken-1" text @click="confirm">
                        Ya
                    </v-btn>

            </v-card-actions>

        </v-card>
    </v-dialog>
 </v-main>
</template>
<script>
import {
    mdiPencil,
    mdiDelete,
} from '@mdi/js'

export default {
    name: "List",
data() {
 return {
    search: null,
    dialog: false,
    history: false,
    edit: false,
    temp: null,
    confirmDel: false,
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
    headers2: [
        {
            text: "Task",
            align: "start",
            sortable: true,
            value: "task",
        },
        { text: "Priority", value: "priority" },
        { text: "Note", value: "note" },
      ],
    ftodos: [
        
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
     icons: {
                mdiPencil,
                mdiDelete,
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
        this.edit= false;
        this.confirmDel=false;
     },
    resetForm() {
        this.formTodo = {
        task: null,
        priority: null,
        note: null,
        };
     },
    deleteItem(item){
        this.confirmDel=true;
        this.temp=item;
     },
    confirm(){
        this.ftodos.push(this.temp);
        this.todos.splice(this.todos.indexOf(this.temp), 1);
        this.temp=null;
        this.confirmDel=false;
    },
    editItem(item){
        this.dialog=true;
        this.edit=true;
        this.formTodo= {
            task: item.task,
            priority: item.priority,
            note: item.note,
        };
        this.temp=item;
     },
    update(formTodo){
        this.temp.task = formTodo.task;
        this.temp.priority = formTodo.priority;
        this.temp.note = formTodo.note;
        this.cancel();
     },
    
 },
};
</script>
<style scoped>
    .icnote{color: plum !important;}
    .pencil{color: lightblue !important;}
    .bin{color: lightcoral !important;}
</style>