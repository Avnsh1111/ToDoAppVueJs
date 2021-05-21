<template>
  <div class="container">
    <div class="today">{{randomMsg}}</div>
    <div class="row">
      <div class="col-md-4 col-md-offset-4 col-xs-6 col-xs-offset-3">
        <div class="add-control">
          <div class="form-group has-feedback">
            <input type="text" class="form-control" v-model="text" @keyup.enter="addItem" placeholder="✍️ Add item..."/>
            <i class="fa fa-plus form-control-feedback add-btn" @click="addItem" title="Add item"></i>
          </div>
        </div>
        <p class="err text-danger text-center" style="font-size: 13px;" v-if="error"><i class="fa fa-warning"></i> Oops! Please, enter name item</p>
        <p class="no-items text-muted text-center hidden"><i class="fa fa-ban"></i></p>
        <ul class="todo-list">
            <li v-for="task in tasks" v-bind:key="task.id" v-bind:class="{ danger :  checkedItems.includes(task.id ), animated : checkedItems.includes(task.id ), flipInX :checkedItems.includes(task.id )  }" style="position: relative; left: 0px; top: 0px;">
                <div class="checkbox">
                    <span class="close"><i class="fa fa-pencil" @click="editTask(task.id)"></i><i class="fa fa-times" @click="deleteSingle(task.id)"></i></span>
                    <label><span class="checkbox-mask"></span><input type="checkbox" v-model="checkedItems" v-bind:value="task.id" class="chec">{{task.text}}</label>
                </div>
            </li>
        </ul>
        <button class="form-control deleteButton" v-if="checkedItems.length > 0" @click="deleteSelected"><i class="fa fa-trash"></i>  Delete selected</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
    name: 'ToDoApp',
    data () {
        return {
            randomMsg : '',
            randomWordArray : [
                "Oh my, it's ",
                "Whoop, it's ",
                "Happy ",
                "Seems it's ",
                "Awesome, it's ",
                "Have a nice ",
                "Happy fabulous ",
                "Enjoy your "
            ],
            weekDays : [
                 "Sunday 🖖",
                 "Monday 💪😀",
                 "Tuesday 😜",
                 "Wednesday 😌☕️",
                 "Thursday 🤗",
                 "Friday 🍻",
                 "Saturday 😴",
            ],
            text : '',
            tasks : [],
            error : false,
            checkedItems : [],
            editId : null
        }
    },
    created : function () {
        var d = new Date();
        var day = this.weekDays[d.getDay()];
        this.randomMsg = this.randomWordArray[Math.floor(Math.random()*this.randomWordArray.length)]+" "+day;
        this.tasks = localStorage.getItem('tasks') == null ? [] : JSON.parse(localStorage.getItem('tasks'));
    },
    methods : {
        generateID: function() {
            var randLetter = String.fromCharCode(65 + Math.floor(Math.random() * 26));
            return randLetter + Date.now();
        },
        addItem : function () {
            var text = this.text;
            if(text == '') {
                this.error = true;
                return false;
            } else {
                this.error = false;
            }
            var id = id ? id : this.generateID();

            var newTask = {
                id : id,
                text : text
            };

            if(this.editId != null) {
                let keyOfTaskToUpdate = this.tasks.map(function(task) {return task.id; }).indexOf(this.editId);
                this.tasks[keyOfTaskToUpdate].text = text;
                this.editId = null;
            } else {
                this.tasks.push(newTask);
            }

            this.updateLocalStorage(this.tasks);
            localStorage.setItem('tasks',JSON.stringify(this.tasks));
            this.text = "";

        },
        editTask : function (id) {
            this.editId = id;
            let keyOfTaskToEdit = this.tasks.map(function(task) {return task.id; }).indexOf(id);
            let task = this.tasks[keyOfTaskToEdit];
            this.text = task.text;
        },
        deleteSelected : function () {

            let checkedItems = this.checkedItems;
            let tasks = this.tasks.filter(function(task) {
                return !checkedItems.includes(task.id)
            });

            this.tasks = tasks;
            this.updateLocalStorage(this.tasks);
            this.checkedItems = [];

        },
        deleteSingle : function (id) {
            let keyOfTaskToDelete = this.tasks.map(function(task) {return task.id; }).indexOf(id);
            this.tasks.splice(keyOfTaskToDelete, 1);
            this.updateLocalStorage(this.tasks);
        },
        updateLocalStorage : function (tasks) {
            localStorage.setItem('tasks',JSON.stringify(tasks));
        },

    },

}
</script>
