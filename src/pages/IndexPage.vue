<template>
  <q-page>
    <div class="column" >
      <q-scroll-area
        :style="`height: ${ this.$q.screen.height - 180 }px; max-width: 100%;`"
        v-if="todos.length > 0">
        <q-list class="q-ma-sm">
          <template
            v-for="(todo, i) in todos"
            :key="i">
            <q-item
              clickable
              @click="changeStatus(i, true)"
              v-ripple
              :class="`q-ma-sm ${todo.isChecked ? 'done bg-blue-1' : ''}`"
              style="border: 1px solid #aeaeae; border-radius: 5px">
              <q-item-section avatar>
                <q-checkbox v-model="todo.isChecked" @update:model-value="changeStatus(i, false)" class="no-pointer-events"/>
              </q-item-section>
              <q-item-section>
                <q-item-label>{{ todo.label }}</q-item-label>
              </q-item-section>
              <q-item-section side v-if="todo.isChecked">
                <q-btn color="negative" @click="onDelete(i)" icon="mdi-delete" no-caps flat />
              </q-item-section>
            </q-item>
          </template>
        </q-list>
      </q-scroll-area>
      <div class="flex flex-center items-center" :style="`height: ${this.$q.screen.height - 180}px`" v-else>
        <div class="column items-center">
          <q-icon name="mdi-check" :size="`${this.$q.screen.xs ? '5em' :'10em'}`" color="primary"/>
          <span :class="`${this.$q.screen.xs ? 'text-h6' :'text-h5'} text-grey-7`">No Todo For Now</span>
        </div>
      </div>
      <q-page-sticky position="bottom-right" :offset="[20, 20]">
        <q-btn @click="prompt = !prompt" fab icon="add" color="primary" />
      </q-page-sticky>
    </div>

    <q-dialog v-model="prompt" persistent>
        <q-card style="min-width: 350px">
          <q-card-section>
            <div class="text-h6">Add Todo</div>
          </q-card-section>
          <q-card-section class="q-pt-none">
            <q-form @submit="onSubmit">
              <div class="column">
                <q-input
                  outlined
                  v-model="todo"
                  autofocus
                  placeholder="Label Todo"
                  :rules="[
                    val => val.length > 0 || 'Please Add Label'
                  ]"/>
              </div>
              <div class="row justify-end q-gutter-sm">
                <q-btn color="primary" flat no-caps label="Cancel" v-close-popup />
                <q-btn color="primary" no-caps label="Add" type="submit" />
              </div>
            </q-form>
          </q-card-section>
        </q-card>
    </q-dialog>
  </q-page>
</template>

<script>
export default {
  data () {
    return {
      todo: '',
      prompt: false,
      todos: [
        {
          id: 0,
          isChecked: false,
          label: 'ini contoh todo'
        }
      ]
    }
  },
  created () {
    this.getTodo()
  },
  methods: {
    getTodo () {
      const data = this.$q.localStorage.getItem('todo')
      if (!data) {
        this.setTodo()
        this.getTodo()
      } else {
        this.todos = data
      }
    },
    setTodo () {
      this.$q.localStorage.set('todo', this.todos)
    },
    changeStatus (index, isChange) {
      this.todos[index].isChecked = isChange ? !this.todos[index].isChecked : isChange
    },
    onSubmit () {
      this.todo = ''
      this.prompt = !this.prompt
      this.$q.notify({
        message: 'Berhasil Menambahkan',
        color: 'primary'
      })
      this.setTodo()
    },
    async onDelete (index) {
      const oldTodo = this.todos
      this.$q.dialog({
        title: 'Confirm',
        message: 'Are you sure you want to delete ?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        oldTodo.splice(index, 1)
        this.$q.notify({
          message: 'Berhasil Menghapus',
          color: 'primary'
        })
      })
      this.todos = oldTodo
      this.setTodo()
    }
  }
}
</script>
