<template>
  <q-page class="relative-position bg-grey-3">
    <div class="row q-pa-sm bg-primary">
      <q-input
        v-model="newTask"
        @keyup.enter="addTask(newTask)"
        class="col"
        square
        filled
        bg-color="white"
        placeholder="Add Task"
        dense>
        <template v-slot:append>
          <q-btn
            @click="addTask"
            round
            dense
            flat
            icon="add" />
        </template>
      </q-input>
    </div>
    <q-scroll-area class="absolute full-height full-width">
     <q-list
      separator
      bordered
      class="bg-white">
      <q-item
        v-for="(task, index) in tasks"
        :key="task.title"
        @click="task.done = !task.done"
        :class="{ 'done bg-blue-1' : task.done}"
        clickable
        v-ripple>
        <q-item-section avatar>
          <q-checkbox
            v-model="task.done"
            class="no-pointer-events"
            color="primary"
          />
        </q-item-section>
        <q-item-section>
        <q-item-label class="q-item-label">{{ task.title }}</q-item-label>
      </q-item-section>
        <q-item-section
          v-if="task.done"
          side>
          <q-btn
            @click.stop="deleteTask(index)"
            flat
            round
            dense
            color="primary"
            icon="delete" />
        </q-item-section>
      </q-item>
    </q-list>
    </q-scroll-area>
    <div
      v-if="!tasks.length"
      class="no-tasks absolute-center">
      <q-icon
        name="check"
        size="100px"
        color="primary"/>
      <div class="text-h5 text-primary text-center">
        No Tasks
      </div>
    </div>
  </q-page>
</template>

<script>
export default {
  data () {
    return {
      newTask: '',
      tasks: [
        /* {
          title: 'Get nuts',
          done: false
        },
        {
          title: 'Eat nuts',
          done: true
        },
        {
          title: 'Poo nuts',
          done: false
        } */
      ]
    }
  },
  mounted () {
    if (localStorage.getItem('tasks')) {
      this.tasks = JSON.parse(localStorage.getItem('tasks'))
    }
  },
  watch: {
    tasks: {
      handler () {
        localStorage.setItem('tasks', JSON.stringify(this.tasks))
      },
      deep: true
    }
  },
  methods: {
    deleteTask (index) {
      this.$q.dialog({
        title: 'Confirm',
        message: 'Sure you want to delete?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        // this.tasks = this.tasks.filter(task => task.index !== index) // works better with ids, but below we use index
        this.tasks.splice(index, 1)
        this.$q.notify({
          message: 'Task Deleted!',
          position: 'bottom',
          timeout: 1500,
          color: 'grey'
        })
      })
    },
    addTask (newTask) {
      // regex to check if newTask contains only white spaces
      if (!this.newTask.replace(/\s/g, '').length) {
        this.newTask = ''
        return
      }
      const newTaskAdd = {
        title: newTask,
        done: false
      }
      this.tasks = [...this.tasks, newTaskAdd]
      this.newTask = ''
    }
  }
}
</script>

<style lang="scss">
  .done{
    .q-item__label {
      text-decoration: line-through;
      color: #bbb;
    }
    //same as above style
    /*.q-item-label {*/
    /*  text-decoration: line-through;*/
    /*  color: #bbb;*/
    /*}*/
  }
  .no-tasks{
    opacity: 0.5;
  }
</style>
