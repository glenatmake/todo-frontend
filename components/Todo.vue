<template>
  <article class="card todo" :class="{ 'is-done': done }">
    <div class="card-content">
      <div class="is-flex is-flex-direction-row is-align-items-center mb-3">
        <h2 class="todo-label mb-0">
          <Checkbox
            :is-checked="done"
            :label-text="title"
            @click="toggleTodo"
          />
        </h2>
      </div>
      <div v-if="description" class="description pl-6 is-size-5">
        {{ description }}
      </div>
      <ul v-if="subtasks" class="subtask-list mt-3">
        <li
          v-for="subtask in subtasks"
          :key="subtask.id"
          class="subtask-item"
          :class="{ 'is-done': subtask.done }"
        >
          <Checkbox
            :is-checked="subtask.done"
            :label-text="subtask.Description"
            @click="toggleSubtask(subtask.id, subtask.done)"
          />
          <button class="subtask-delete" @click="deleteSubtask(subtask)">
            <svg
              width="16"
              height="16"
              viewBox="0 0 18 18"
              fill="none"
              xmlns="http://www.w3.org/2000/svg"
            >
              <path
                fill-rule="evenodd"
                clip-rule="evenodd"
                d="M9 18C13.9706 18 18 13.9706 18 9C18 4.02944 13.9706 0 9 0C4.02944 0 0 4.02944 0 9C0 13.9706 4.02944 18 9 18ZM14 8H4V10H14V8Z"
              />
            </svg>
          </button>
        </li>
      </ul>
      <form
        ref="addSubtask"
        action=""
        class="add-subtask is-flex is-flex-direction-row mt-4"
      >
        <input
          ref="newSubtaskDescription"
          class="input is-flex is-flex-grow-1"
          type="text"
        />
        <button
          class="button is-flex is-flex-shrink-0 ml-2"
          @click.prevent="addSubtask"
        >
          Add Subtask
        </button>
      </form>
    </div>
  </article>
</template>

<script>
export default {
  props: {
    id: {
      type: Number,
      required: true,
    },
    title: {
      type: String,
      required: true,
    },
    description: {
      type: String,
      required: false,
      default: '',
    },
    done: {
      type: Boolean,
      required: false,
    },
    subtasks: {
      type: Array,
      required: false,
      default: () => {
        return []
      },
    },
  },
  methods: {
    toggleTodo(e) {
      this.$axios
        .put(`${process.env.API_URL}/todos/${this.id}`, {
          Done: !e,
        })
        .then((response) => {
          this.$nuxt.refresh()
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.error(error)
        })
    },
    toggleSubtask(subtaskId, isDone) {
      this.$axios
        .put(`${process.env.API_URL}/subtasks/${subtaskId}`, {
          done: !isDone,
        })
        .then((response) => {
          this.$nuxt.refresh()
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.error(error)
        })
    },
    addSubtask(e) {
      const self = this
      self.$axios
        .post(`${process.env.API_URL}/subtasks`, {
          Description: self.$refs.newSubtaskDescription.value,
        })
        .then((response) => {
          if (response.status === 200) {
            let updatedSubtasks = []

            if (self.subtasks.length) {
              updatedSubtasks = self.subtasks.map((st) => {
                return st.id
              })
            }
            updatedSubtasks.push(response.data)

            this.$axios
              .put(`${process.env.API_URL}/todos/${self.id}`, {
                subtasks: updatedSubtasks,
              })
              .then((response) => {
                if (response.status === 200) {
                  this.$refs.addSubtask.reset()
                  this.$emit('update')
                }
              })
          }
        })
        .catch((error) => {
          // eslint-disable-next-line no-console
          console.error(error)
        })
    },
    deleteSubtask(subtask) {
      const self = this
      if (confirm(`Delete subtask?\n${subtask.Description}`)) {
        self.$axios
          .delete(`${process.env.API_URL}/subtasks/${subtask.id}`)
          .then((response) => {
            if (response.status === 200) {
              this.$emit('update')
            }
          })
      }
    },
  },
}
</script>

<style lang="scss" scoped>
@import '~assets/scss/main.scss';

@keyframes appear {
  0% {
    opacity: 0;
  }
}

.todo {
  background-color: rgba(0, 0, 0, 0.25);
  color: $white;
}

.todo-label {
  display: flex;
  align-items: center;
  color: inherit !important;
  font-weight: normal;

  .cb-text {
    color: $white;
  }
}

.subtask-list {
  list-style-type: none !important;
  margin: 1em 0 0 0 !important;
  padding: 0 0 0 0.5em;
  color: rgba($white, 0.8);
}

.todo.is-done {
  opacity: 0.8;
  .todo-label {
    font-size: 1.25rem;
  }
  .description {
    font-size: 1rem;
  }
}

.subtask-item {
  display: flex;
  position: relative;
  padding: 0.2em;
  border-radius: 5em;

  &.is-done {
    text-decoration: line-through;
  }
}

.subtask-delete {
  margin-left: auto;
  background-color: transparent;
  border: 0;
  padding: 0.25em;
  appearance: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  path {
    fill: $grey-lighter;
    transition: fill 0.2s ease;
  }
  &:hover {
    path {
      fill: $danger;
    }
    &::before {
      content: '';
      background: rgba($danger, 0.1);
      display: block;
      position: absolute;
      top: 0;
      left: 0;
      bottom: 0;
      right: 0;
      z-index: 0;
    }
  }
}

.add-subtask {
  .input {
    background: rgba($white, 0.05);
    border-color: transparent;
    color: white;
    &:focus {
      border-color: rgba($white, 0.3);
    }
  }
  .button {
    background: transparent;
    color: white;
    border-color: rgba($white, 0.4);
    opacity: 0.8;
    &:hover {
      opacity: 1;
    }
  }
}
</style>
