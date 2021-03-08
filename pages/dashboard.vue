<template>
  <section class="section">
    <div class="container">
      <div class="content">
        <h2 class="title is-1 has-text-white">Todo</h2>
        <ul class="todo-list">
          <li v-for="todo in uncompleted" :key="todo.id" class="mb-4">
            <Todo
              :id="todo.id"
              :subtasks="todo.subtasks"
              :title="todo.Title"
              :done="todo.Done"
              :description="todo.Description"
              @update="handleUpdate"
            />
          </li>
        </ul>

        <div class="mt-6">
          <form ref="addTodo" class="add-todo-form">
            <h3 class="title has-text-white">Add a new item</h3>
            <div class="mb-2">
              <input
                id="add-title"
                ref="addTitle"
                class="input"
                type="text"
                placeholder="Title"
                required
              />
            </div>
            <div class="mb-2">
              <textarea
                id="add-description"
                ref="addDescription"
                class="textarea"
                name="description"
                cols="30"
                rows="3"
                placeholder="Description... (you can add subtasks later)"
              ></textarea>
              <div class="mt-2 is-flex is-justify-content-flex-end">
                <button class="button is-primary" @click.prevent="addTodo">
                  Add Todo
                </button>
              </div>
            </div>
          </form>
        </div>

        <h2 class="title is-3 has-text-white">Done</h2>
        <ul class="todo-list">
          <li v-for="todo in completed" :key="todo.id" class="mb-4">
            <Todo
              :id="todo.id"
              :subtasks="todo.subtasks"
              :title="todo.Title"
              :done="todo.Done"
              :description="todo.Description"
              @update="handleUpdate"
            />
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  middleware: 'auth',
  async asyncData({ $axios }) {
    const todos = await $axios.get(`${process.env.API_URL}/todos`)
    return { todos }
  },
  computed: {
    ...mapGetters(['loggedInUser']),
    uncompleted() {
      return this.todos.data.filter((todo) => {
        return !todo.Done
      })
    },
    completed() {
      return this.todos.data.filter((todo) => {
        return todo.Done
      })
    },
  },
  methods: {
    addTodo(e) {
      const self = this
      this.$axios
        .post(`${process.env.API_URL}/todos`, {
          Title: self.$refs.addTitle.value,
          Description: self.$refs.addDescription.value,
        })
        .then((response) => {
          // eslint-disable-next-line no-console
          console.log(response)
          this.$nuxt.refresh()
          this.$refs.addTodo.reset()
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.error(error)
        })
    },
    handleUpdate(e) {
      console.log('updating')
      this.$nuxt.refresh()
    },
  },
}
</script>

<style lang="scss" scoped>
@import '~assets/scss/main.scss';

.content .todo-list {
  list-style: none !important;
  margin: 0;
  padding: 0;
}

.add-todo-form {
  .input,
  .textarea {
    background-color: rgba($white, 0.2);
    border-color: transparent;
    color: white;
    &::placeholder {
      color: rgba($white, 0.3);
    }
    &:focus {
      border-color: rgba($white, 0.3);
    }
  }
}
</style>
