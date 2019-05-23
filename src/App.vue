<template>
  <div id="app">
  <nav class="navbar">
    <span>
      <img class="logo" src="./assets/images/logo-white.svg" alt="logo">
    </span>

    <ul class="navitems">
      <li>{{user}}</li>
      <li @click="theme">
        <span v-if="!nightmode">
          <img class="moonicon" src="./assets/icons/moon.svg" alt="dark mode">
        </span>
        <span v-else>
          <img class="sunicon" src="./assets/icons/sun.svg" alt="light mode">
        </span>
      </li>
    </ul>
  </nav>

  <div class="entername" v-if="user==''">
    <label for="user">Enter your name</label>
    <input type="text" v-model="userinput" v-on:keyup.enter="setname">
  </div>

  <div v-else class="todosgrid">
    <p> Hi {{user}}! What are your ToDos for today? <span area-label="memo">📝</span>
    </p>
    <div class="inputbox">
      <input type="text" v-model="todoInput" placeholder="e.G. water the plants" v-on:keyup.enter="addTodo">
      <span><img class="entericon" src="./assets/icons/enter.svg" @click="addTodo" alt="add"></span>
    </div>

    <div>
      <h3>Open Tasks ({{pending.length}})</h3>
      <p v-if="pending.length==0">Let's chill for now!<span area-label="Beer Mugs">🍻</span><span area-label="sofa">🛋️</span></p>
      <ul v-if="pending.length>0">
        <li class="taskli" v-for="todo in pending" :key="todo.id" v-bind:class="{'isdone' :todo.status}">
          <img class="checkicon not-check" src="./assets/icons/check.svg" @click="checked(todo)">
          <span>
          {{todo.title}} 
        </span>
          <img class="closeicon" src="./assets/icons/close.svg" @click="remove(todo)">
        </li>
      </ul>
    </div>

    <div>
      <h3>Finished Tasks ({{done.length}})</h3>
      <p v-if="done.length==0">Let's get something done. <span area-label="winking face">😉</span></p>
      <ul v-if="done.length>0">
        <li class="taskli" v-for="item in done" :key="item.id" v-bind:class="{'isdone' :item.status}">
          <img class="checkicon" src="./assets/icons/check.svg" @click="checked(item)">
          <span>
          {{item.title}} 
        </span>
          <img class="closeicon" src="./assets/icons/close.svg" @click="remove(item)">
        </li>
      </ul>
    </div>
  </div>
</div>
</template>

<script>

export default {
  name: "todoapp",
  data:
  function(){
  return({
    userInput: "",
    user: "",
    todoInput: "",
    checkid: 0,
    nightmode: false,
    todos: [
      { id: 0, title: "make a new pen", status: false },
      { id: 1, title: "enjoy the day", status: false }
    ]
  })},
  methods: {
    setname() {
      this.user = this.userinput;
    },
    checked(todo) {
      console.log(todo.id);
      this.todos[todo.id].status = !this.todos[todo.id].status;
    },
    addTodo() {
      if (this.todoInput !== "") {
        this.todos.push({
          id: this.todos.length,
          title: this.todoInput,
          status: false
        });
      }
      this.todoInput = "";
    },
    remove(todo) {
      this.todos.splice(this.todos.indexOf(todo), 1);
    },
    theme() {
      let b = document.querySelector("body");
      b.classList.toggle("nightmode");
      this.nightmode = !this.nightmode;
    }
  },
  computed: {
    pending() {
      return this.todos.filter(function(item) {
        return !item.status;
      });
    },
    done() {
      return this.todos.filter(function(item) {
        return item.status;
      });
    }
  },
  watch: {
    todoList: {
      handler: function(todos) {
        localStorage.setItem('todos', JSON.stringify(todos));
      },
      deep: true
    }
  }
};
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
@font-face{
  font-family: "gt walsheim light";
  src: url(./assets/fonts/gt-walsheim-light/gt-walsheim-light-webfont.woff);
}
body {
  font-family: "gt walsheim light", monospace;
}
.logo{
  height: 1.6em;
  filter: invert(100%);
}
.nightmode {
  background: #000;
  color: #eee;

  .logo{
    filter:none;
  }
  .taskli{
  background: #1c1c1c;
    .checkicon,.closeicon{
        filter:invert(80%);
    }
  }
  .todosgrid>div>h3,.inputbox{
    border-color: #1c1c1c;
  }
}

.entername {
  font-size: 1.6em;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);

  input {
    border-radius: 0.3em;
    border: 1px solid lightsalmon;
  }
}

#app {
  display: grid;
  grid-template-columns: 1fr 1fr minmax(330px, 540px) 1fr 1fr;
  input {
    display: block;
    padding: 0.6em 0.3em;
    font-size: 1em;
  }
}

h3{
  font-weight: 100;
  padding: 6px 0;
  border-bottom: 1px solid lightsalmon;
}

div ul {
  margin: 0;
  padding: 0;
  list-style: none;
  li {
    width: 100%;
  }
}


nav {
  grid-column: 2/5;
  margin:1em 0 3em 0;
  display: flex;
  justify-content: space-between;
  cursor: pointer;

  ul {
    margin: 0;
    font-size: 1.5em;
    list-style: none;
    display: flex;

    li {
      display: inline;
      
      :not(first-of-type){
        margin-left: 0.8em;
      }
    }
  }
}

.taskli {
  padding: 1em;
  display: flex;
  justify-content: space-between;
  font-size: 1.2em;
  background: #eee;
  margin-bottom: 10px;

  & :nth-child(2) {
    width: 70%;
  }
}

.todosgrid {
  grid-column: 3;

  & > p {
    font-size: calc(1em + 8px);
  }
}

.todosgrid > div > p {
  font-size: 1.5em;
}

.inputbox {
  padding: 0.2em 0.4em;
  border-radius: 0.3em;
  background: white;
  border: 1px solid lightsalmon;
  display: flex;
  justify-content: space-between;
  input {
    width: 100%;
  }
  input,
  span {
    border-radius: 0.3em;
    border: 0;
    align-self: center;
    margin: 0 0.2em;
  }
}

.isdone {
  text-decoration: line-through;
}
.moonicon{
  height:1em;
}
.sunicon{
  height:1.2em;
  filter: invert(100%);
}
.entericon{
  height: 0.8em;
  margin-left: 1px solid gray;
}
.checkicon,.closeicon{
  height: 0.8em;
  align-self: center;
}
.not-check{
  height: 1em;
  width:1em;
  background:black;
}
</style>