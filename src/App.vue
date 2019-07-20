<template>
  <div id="app">
    <!-- テンプレートはじまり -->
    
    <label v-for="(label, index) in options" v-bind:key="index">
      <input type="radio" v-model="current" v-bind:value="label.value">
      {{ label.label }}
    </label>

    <table>
      <thead>
        <tr>
          <th class="id">ID</th>
          <th class="comment">Comment</th>
          <th class="state">State</th>
          <th class="button">-</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="todo in computedTodos" v-bind:key="todo.id">
          <th>{{ todo.id }}</th>
          <td>{{ todo.comment }}</td>
          <td class="state">
            <button v-on:click="doChangeState(todo)">{{ labels[todo.state] }}</button>
          </td>
          <td class="button">
            <button v-on:click="doRemove(todo)">削除</button>
          </td>
        </tr>
      </tbody>
    </table>

    <h2>新しいTODOの追加</h2>
    <form class="add-form" v-on:submit.prevent="doAdd">
      コメント <input type="text" ref="comment">
      <button type="submit">追加</button>
    </form>

    <!-- テンプレートおわり -->
  </div>
</template>

<script>
// https://jp.vuejs.org/v2/examples/todomvc.html
var STORAGE_KEY = 'todos-vuejs-neko'
var todoStorage = {
  fetch: function () {
    var todos = JSON.parse(
      localStorage.getItem(STORAGE_KEY) || '[]'
    )
    todos.forEach(function (todo, index) {
      todo.id = index
    })
    todoStorage.uid = todos.length
    return todos
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
  }
}

export default {
  data() {
    return{
      todos: [],
      options: [
        { value: -1, label: 'すべて' },
        { value: 0, label: '作業中' },
        { value: 1, label: '完了' }
      ],
      current: -1
    }
  },
  methods: {
    doAdd: function (event, value) {
      const comment = this.$refs.comment
      if (!comment.value.length) {
        return
      }
      this.todos.push({
        id: todoStorage.uid++,
        comment: comment.value,
        state: 0
      })
      comment.value = null
    },
    doChangeState: function (todo) {
      todo.state = todo.state ? 0 : 1
    },
    doRemove: function (todo) {
      const index = this.todos.indexOf(todo)
      this.todos.splice(index, 1)
    }
  },
  computed: {
    computedTodos: function(){
      return this.todos.filter(function(el){
        return this.current < 0 ? true: this.current === el.state
      }, this)
    },
    labels() {
      return this.options.reduce(function(a, b){
        return Object.assign(a, { [b.value]: b.label })
      }, {})
    }
  },
  watch: {
    todos: {
      handler: function (todos) {
        todoStorage.save(todos)
      },
      deep: true
    }
  },
  created() {
    this.todos = todoStorage.fetch()
  },
  render: h => h(App)
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

/* 追加 */
/*
  ちょっと雑なので、クラスなど追加していい感じに調整してください
*/

* {
  box-sizing: border-box;
}
#app {
  max-width: 640px;
  margin: 0 auto;
}
table {
  width: 100%;
  border-collapse: collapse;
}
thead th {
  border-bottom: 2px solid #0099e4; /*#d31c4a */
  color: #0099e4;
}
th,
th {
  padding: 0 8px;
  line-height: 40px;
}
thead th.id {
  width: 50px;
}
thead th.state {
  width: 100px;
}
thead th.button {
  width: 60px;
}
tbody td.button, tbody td.state {
  text-align: center;
}
tbody tr td,
tbody tr th {
  border-bottom: 1px solid #ccc;
  transition: all 0.4s;
}
tbody tr.done td,
tbody tr.done th {
  background: #f8f8f8;
  color: #bbb;
}
tbody tr:hover td,
tbody tr:hover th {
  background: #f4fbff;
}
button {
  border: none;
  border-radius: 20px;
  line-height: 24px;
  padding: 0 8px;
  background: #0099e4;
  color: #fff;
  cursor: pointer;
}
</style>
