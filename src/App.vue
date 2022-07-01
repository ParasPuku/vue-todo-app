<template>
  <div class="container">
    <div class="top-container">
      <header>
        <img
          alt="Vue logo"
          class="logo"
          src="./assets/logo.svg"
          width="125"
          height="125"
        />
        <div class="wrapper">
          <h2>Todo Bucket List</h2>
        </div>
      </header>
    </div>
    <div class="outer-container">
      <div class="inner-top-container">
        <button class="btn">
          TOTAL: <span class="results">{{ `${total}` }}</span>
        </button>
        <button class="btn">
          COMPLETED: <span class="results">{{ `${complete}` }}</span>
        </button>
        <button class="btn">
          PENDING: <span class="results">{{ `${pending}` }}</span>
        </button>
      </div>
      <div class="form-container">
        <p v-show="this.checkEmpty" class="error">
          Please enter your today's task
        </p>
        <form @submit.prevent="addTodo" class="add-form">
          <input
            type="text"
            name="title"
            v-model="title"
            class="input"
            placeholder="Enter your task..."
          />
          <button type="submit" class="submit-btn">ADD</button>
        </form>
      </div>
      <div v-if="this.todos.length > 0" class="list-container">
        <ul>
          <li
            v-for="todo in todos"
            v-bind:key="todo.id"
            v-bind:class="todo.completed ? 'completed' : ''"
          >
            <input
              type="checkbox"
              class="input-btn"
              @click="toggleCompletion(todo.id)"
            />
            <p class="todos-item">{{ todo.title }}</p>
            <div class="edit-delete">
              <span class="edit" @click="(e) => editTodo(todo.id)">Edit</span>
              <span class="delete" @click="deleteTodo(todo.id)">Delete</span>
            </div>
          </li>
        </ul>
      </div>
      <div v-else-if="this.todos.length === 0" class="empty-bucketlist">
        <h3>Your bucket list is currently empty..!!</h3>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      title: "",
      todos: [],
      isEditMode: false,
      editId: null,
      checkEmpty: false,
      total: 0,
      complete: 0,
      pending: 0,
      completed: false,
    };
  },
  methods: {
    generateKey() {
      return `${new Date().getTime()}`;
    },
    addTodo() {
      if (this.title && this.isEditMode) {
        this.todos = this.todos.map((item) => {
          if (item.id === this.editId) {
            return { ...item, title: this.title, completed: false };
          }
          return item;
        });
        this.title = "";
        this.isEditMode = false;
        this.editId = null;
      } else {
        if (this.title.length > 0) {
          let newItem = {
            id: this.generateKey(),
            title: this.title,
          };
          console.log("NewItem >>>", newItem);
          this.todos.unshift(newItem);
          console.log("Todo::", this.todos);
        }

        if (this.title.length > 0) {
          this.total = this.total + 1;
          this.checkEmpty = false;
        } else {
          this.checkEmpty = true;
        }
        this.pending = this.total - this.complete;
        this.title = "";
      }
    },
    deleteTodo(id) {
      console.log("CLicked id >>>", id);
      const filteredItem = this.todos.filter((todo) => todo.id !== id);
      this.todos = filteredItem;
      this.total = this.total - 1;
      this.countCompletetionAndPending();
      this.pending = this.total - this.complete;
    },
    editTodo(id) {
      const editItem = this.todos.find((item) => item.id === id);
      this.editId = id;
      this.isEditMode = true;
      this.title = editItem.title;
    },
    toggleCompletion(id) {
      const updatedCheckedState = this.todos.map((todo) => {
        if (todo.id === id) {
          return { ...todo, completed: !todo.completed };
        }
        return todo;
      });
      this.todos = updatedCheckedState;
      this.countCompletetionAndPending();
    },
    countCompletetionAndPending() {
      this.complete = 0;
      this.todos.map((todo) => {
        if (todo.completed === true) {
          this.complete = this.complete + 1;
        }
        this.pending = this.total - this.complete;
      });
    },
  },
};
</script>

<style>
@import "./assets/base.css";
.wrapper h2 {
  font-size: 40px;
  letter-spacing: 2px;
}
.inner-top-container {
  display: flex;
  justify-content: space-between;
  margin: 40px auto 30px;
}
.inner-top-container .btn {
  font-size: 20px;
  padding: 5px 10px;
  background: #c9bc36;
  border: 2px solid black;
  margin: 0 10px;
}
.add-form input {
  width: 300px;
  padding: 5px 10px;
  font-size: 20px;
  letter-spacing: 2px;
  outline: none;
  border: 2px solid #b1b12c;
  color: #000000;
}
form.add-form {
  display: flex;
  justify-content: center;
  align-items: center;
}
.submit-btn {
  font-size: 20px;
  margin-left: 10px;
  padding: 5px 10px;
  border: 2px solid #9c9c32;
  background: #38ab7f;
  cursor: pointer;
  letter-spacing: 2px;
  color: #ffffff;
}
.form-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 0 auto 40px;
}
.empty-bucketlist {
  text-align: center;
  font-size: 15px;
  letter-spacing: 1px;
  color: #228b22;
}
p.error {
  color: red;
  margin: 10px auto;
}
.list-container li {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
}
span.results {
  background: #000000;
  color: #b9b942;
  padding: 6px 10px;
  position: relative;
  left: 10px;
}
p.todos-item {
  width: 250px;
  text-align: left;
  margin: 5px 20px;
  font-size: 18px;
}
.list-container ul > li {
  list-style: none;
  margin: 10px 0;
  box-shadow: 0px 0px 2px 2px #ccc117;
}
.edit-delete {
  display: contents;
  cursor: pointer;
}
.edit {
  font-size: 18px;
  background: #0000b3;
  color: #dedede;
  padding: 6px 10px;
  border-left: 3px solid #acb438;
}
.delete {
  font-size: 18px;
  background: #850000;
  color: #dedede;
  padding: 6px 10px;
  border-left: 3px solid #acb438;
}
.list-container ul {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  padding: 0;
}
input.input-btn {
  margin-left: 10px;
}
.completed p.todos-item {
  text-decoration: line-through;
}
#app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 0;

  font-weight: normal;
}

header {
  line-height: 1.5;
}

/* .logo {
  display: block;
  margin: 0 auto 2rem;
} */

a,
.green {
  text-decoration: none;
  color: hsla(160, 100%, 37%, 1);
  transition: 0.4s;
}

@media (hover: hover) {
  a:hover {
    background-color: hsla(160, 100%, 37%, 0.2);
  }
}

.list-container ul > li {
  list-style: none;
}

input.input-btn {
  width: 20px;
  height: 20px;
}

@media (min-width: 1024px) {
  body {
    display: flex;
    place-items: center;
  }

  /* #app {
    display: grid;
    grid-template-columns: 1fr 1fr;
    padding: 0 2rem;
  } */

  header {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }

  /* .logo {
    margin: 0 2rem 0 0;
  } */
}
</style>
