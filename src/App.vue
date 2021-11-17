<template>
  <div class="container">
  
  <header class="row header">
    <div class="logo">LOGO</div>
    <h1>Лабораторная работа №11</h1>
    <div class="mode">
      <img id="mode1" @click="switchMode" src="./assets/moon.svg" alt="">
      <img id="mode2" @click="switchMode" src="./assets/sun.svg" alt="">
    </div>
  </header>
  
    <div class="d-flex flex-column align-items-center">
        <div class="form-group col-12">
          <b-form-input
            v-bind:class="{ dark: darkMode }"
            id="input-2"
            class="p-2 m-1 text-center"
            v-model="newTaskName" 
            required
            placeholder="Название"
            @keyup.enter="add"
          ></b-form-input>
      </div>
        <div class="form-group col-xl">
          <b-form-input
            v-bind:class="{ dark: darkMode }"
            id="input-3"
            class="p-2 m-1 text-center"
            v-model="newTaskText"
            required
            placeholder="Текст"
            @keyup.enter="add"
          ></b-form-input>
        </div>
          <div class="d-flex align-items-center justify-content-center">
          <p class="p-1">Приоритет:</p>
          <div class="p-1">
            <input class="m-1" type="radio" id="one" value="1" v-model="priority">
            <label  for="one">1</label>
          </div>
          <div class="p-1">  
            <input class="m-1" type="radio" id="two" value="2" v-model="priority">
            <label for="two">2</label>
          </div>
          <div class="p-1">  
            <input class="m-1" type="radio" id="three" value="3" v-model="priority">
            <label for="three">3</label>
          </div>
          </div>
        <b-button @click="add" variant="primary" class="ml-3">Добавить</b-button>
    </div>
    <div class="row mt-5">
    
      <div class="col-4">
        <div class="p-2 alert alert-secondary">
          <h2>Задачи {{ arrBackLog.length }}</h2>
          <draggable
            class="list-group kanban-column"
            :list="arrBackLog"
            group="tasks"
          >
            <div
              v-bind:class="{ dark: darkMode, priority1: element.priority === '1', priority2: element.priority === '2', priority3: element.priority === '3'}"
              class="list-group-item"
              v-for="(element, index) in arrBackLog"
              :key="element.name"
            >
              <h3>{{ index + 1 }} {{ element.name }}</h3>
              <p>{{ element.text }}</p>
              <p>{{ element.date }}</p>
              <div class="edit"><img @click="showPopup(index, 'arrBackLog')" src="./assets/edit.svg" alt=""></div>
              
              <div>
                <button @click="move(index, 'arrBackLog', 'arrInProgress')" class="alert-primary">Вправо</button>
              </div>
              
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-4">
        <div class="p-2 alert alert-primary" v-bind:class="{ dark: darkMode }">
          <h2>В процессе {{ arrInProgress.length }}</h2>
          <draggable
            class="list-group kanban-column"
            :list="arrInProgress"
            group="tasks"
          >
            <div
              v-bind:class="{ dark: darkMode, priority1: element.priority === '1', priority2: element.priority === '2', priority3: element.priority === '3' }"
              class="list-group-item"
              v-for="(element, index) in arrInProgress"
              :key="element.name"
            >
              <h3>{{ index + 1}} {{ element.name }}</h3>
              <p>{{ element.text }}</p>
              <p>{{ element.date }}</p>
              <div class="edit"><img @click="showPopup(index, 'arrInProgress')" src="./assets/edit.svg" alt=""></div>
              
              <div>
                <button @click="move(index, 'arrInProgress', 'arrBackLog')" class="alert-secondary">Влево</button>
                <button @click="move(index, 'arrInProgress', 'arrDone')" class="alert-success">Вправо</button>
              </div>
              
            </div>
          </draggable>
        </div>
      </div>

      <div class="col-4">
        <div class="p-2 alert alert-success">
          <h2>Завершено {{ arrDone.length }}</h2>
          <draggable
            class="list-group kanban-column"
            :list="arrDone"
            group="tasks"
          >
            <div
              v-bind:class="{ dark: darkMode, priority1: element.priority === '1', priority2: element.priority === '2', priority3: element.priority === '3' }"
              class="list-group-item"
              v-for="(element, index) in arrDone"
              :key="element.name"
            >
              <h3>{{ index + 1 }} {{ element.name }}</h3>
              <p>{{ element.text }}</p>
              <p>{{ element.date }}</p>
              <div class="edit">
                <img @click="deleteElement(index)" src="./assets/delete.svg" alt="">
                <img @click="showPopup(index, 'arrDone')" src="./assets/edit.svg" alt="">
              </div>
              
              <div>
                <button @click="move(index, 'arrDone', 'arrInProgress')" class="alert-primary">Влево</button>
              </div>
              
            </div>
          </draggable>
        </div>
      </div>
      
    </div>
    
    <div v-bind:class="{ dark: darkMode }" class="shadow">
      <form @submit.prevent="edit" v-bind:class="{ dark: darkMode }" class="form">
        <div @click="closePopup" class="cross">+</div>
        <h3>Редактировать запись</h3>
        <label for="name">Название</label>
        <input v-bind:class="{ dark: darkMode }" v-model="editTaskName" type="text" id="name" placeholder="Название">
        <label for="text">Текст</label>
        <input v-bind:class="{ dark: darkMode }" v-model="editTaskText" type="text" id="text" placeholder="Текст">
        <div>
          <input type="radio" id="one" value="1" v-model="editPrior">
          <label for="one">1</label>
        </div>
        <div>
          <input type="radio" id="two" value="2" v-model="editPrior">
          <label for="two">2</label>
        </div>
        <div>
          <input type="radio" id="three" value="3" v-model="editPrior">
          <label for="three">3</label>
        </div>
        <button>Изменить</button>
      </form>
    </div>
    
    <footer>
      <h2>Соколов Илья 201-321</h2>
    </footer>
    
  </div>
</template>

<script>
//import draggable
import draggable from "vuedraggable";

export default {
  name: "kanban-board",
  components: {
    // импорт компонента
    draggable
  },
  data() {
    return {
      // для новых задач
      newTaskName: "",
      newTaskText: "",
      editTaskName: "",
      editTaskText: "",
      indexOfEdited: 0,
      priority: '3',
      editPrior: '3',
      array: '',
      darkMode: false,
      // 3 массива для разных типов задач
      arrBackLog: [
        // инициализация начальных задач
        { name: "Покупки", text: 'Макароны, сосиски, кетчуп, чай', date: (new Date()).toLocaleString('en-GB'), priority: '1' },
        { name: "Книга", text: 'Прочитать главы 3-4', date: (new Date()).toLocaleString('en-GB'), priority: '2' },
        { name: "Уборка", text: 'Отсортировать шкаф, помыть посуду', date: (new Date()).toLocaleString('en-GB'), priority: '3' },
        { name: "Песик", text: 'Выгулять пса', date: (new Date()).toLocaleString('en-GB'), priority: '2' },
      ],
      arrInProgress: [],
      arrDone: []
    };
  },
  methods: {
    //add new tasks method
    add: function() {
      if (this.newTaskName && this.newTaskText) {
        this.arrBackLog.push({ name: this.newTaskName, text: this.newTaskText, date: (new Date()).toLocaleString('en-GB'), priority: this.priority });
        this.newTaskName = "";
        this.newTaskText = "";
      }
    },
    edit: function() {
      if (this.editTaskName) this[this.array][this.indexOfEdited].name = this.editTaskName;
      if (this.editTaskText) this[this.array][this.indexOfEdited].text = this.editTaskText;
      if (this.editPrior) this[this.array][this.indexOfEdited].priority = this.editPrior;
      this.editTaskName = '';
      this.editTaskText = '';
      document.getElementsByClassName('shadow')[0].classList.remove('active');
    },
    showPopup: function(index, array) {
      document.getElementsByClassName('shadow')[0].classList.add('active');
      this.indexOfEdited = index;
      this.array = array;
    },
    closePopup: function() {
      this.editTaskName = '';
      this.editTaskText = '';
      document.getElementsByClassName('shadow')[0].classList.remove('active');
    },
    deleteElement: function(index) {
      this.arrDone.splice(index, 1);
    },
    switchMode: function() {
      if (!this.darkMode) {
        this.darkMode = true;
        document.getElementById('mode1').style.display = 'none';
        document.getElementById('mode2').style.display = 'inline-block';
      } else {
        this.darkMode = false;
        document.getElementById('mode1').style.display = 'inline-block';
        document.getElementById('mode2').style.display = 'none';
      }
      
      document.getElementsByTagName('body')[0].classList.toggle('dark');
      document.querySelector('.alert.alert-secondary').classList.toggle('dark');
    },
    move: function(index, array, toArray) {
      let el = this[array][index];
      this[toArray].push(el)
      this[array].splice(index, 1);
    },
    
  }
};
</script>

<style>
.kanban-column {
  min-height: 300px;
}
.edit {
    text-align: right;
}
.edit img {
    width: 30px;
    cursor: pointer;
}

.list-group-item {
  cursor: grab;
}
.list-group-item:focus, 
.list-group-item:active {
  cursor: grabbing;
}

.shadow {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto;
  background: rgba(0,0,0,0.3);
  opacity: 0;
  z-index: -1;
  transition: .3s;
}

.shadow.dark {
  background: rgba(255,255,255,0.3);
}

.shadow.active {
  z-index: 1;
  opacity: 1;
}

.form {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  margin: auto;
  max-width: 300px;
  height: 400px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  background: #fff;
  padding: 25px;
}

.cross {
    position: absolute;
    right: 20px;
    top: 0px;
    font-size: 4rem;
    font-weight: 100;
    transform: rotate(45deg);
    cursor: pointer;
}

.header {
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.header img {
    width: 30px;
    cursor: pointer;
}

#mode2 {
  display: none
}

.dark {
  background-color: #333;
  color: #fff;
}

.list-group-item {
  margin: 5px 0;
}

.list-group-item.dark {
  background-color: #222;
  color: #fff;
}

.form.dark {
  background-color: #333;
}

input.dark {
  background: grey;
  color: #fff;
}

input.dark::placeholder {
  color: #fff;
  opacity: 0.9;
}

footer {
    text-align: center;
}

.list-group-item.priority1 {
  color: red;
}
.priority2 {
  color: orange;
}
.priority2.dark {
  color: orange;
}
.priority3.dark {
  color: #fff;
}

.alert.alert-secondary.dark {
  background-color: #444;
  border: none;
  color: #fff;
}
</style>
