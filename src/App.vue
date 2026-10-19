<template>
  <div class="mx-auto flex w-full max-w-xl flex-col gap-6 px-4 py-12 sm:py-16">
    <header class="flex items-end justify-between">
      <div>
        <h1 class="text-3xl font-bold tracking-tight text-slate-900">
          Tasks
        </h1>
        <p id="summary" class="mt-1 text-sm text-slate-500">
          {{ remaining }} of {{ tasks.length }}
        </p>
      </div>
    </header>

    <div id="task-form"
      class="flex flex-col gap-2 rounded-2xl bg-white p-2 shadow-sm ring-1 ring-slate-200 transition focus-within:ring-2 focus-within:ring-slate-900">
      <input v-model="task" id="task-input" type="text" placeholder="Add a new task…" autocomplete="off"
        class="min-w-0 flex-1 rounded-xl bg-transparent px-3 py-2.5 text-base text-slate-900 placeholder:text-slate-400 focus:outline-none" />
      <button @click="addTask"
        class="rounded-xl bg-slate-900 px-5 py-2.5 text-sm font-medium text-white transition hover:bg-slate-700 active:scale-95 focus:outline-none focus-visible:ring-2 focus-visible:ring-slate-900 focus-visible:ring-offset-2">
        Add
      </button>
      <div class="flex gap-2 mb-4 mt-4">
        <button @click="updateFilter('all')" class="rounded-full px-3 py-1 text-xs font-semibold transition"
          :class="currentFilter === 'all' ? 'bg-slate-900 text-white' : 'bg-slate-100 text-slate-700'">
          All
        </button>
        <button @click="updateFilter('active')"
          class="rounded-full px-3 py-1 text-xs font-semibold hover:bg-slate-200 transition"
          :class="currentFilter === 'active' ? 'bg-slate-900 text-white' : 'bg-slate-100 text-slate-700'">
          Active
        </button>
        <button @click="updateFilter('completed')"
          class="rounded-full px-3 py-1 text-xs font-semibold hover:bg-slate-200 transition"
          :class="currentFilter === 'completed' ? 'bg-slate-900 text-white' : 'bg-slate-100 text-slate-700'">
          Completed
        </button>
      </div>
    </div>

    <ul id="tasks" class="flex flex-col gap-2.5">
      <li v-for="(item, index) in filterTasks" :key="index">
        <div
          class="group flex items-center gap-3 rounded-2xl bg-white px-3 py-3 shadow-sm ring-1 ring-slate-200 transition hover:shadow-md"
          v-if="!item.isEditing">
          <button @click="markDone(index)" v-if="item.done" type="button" aria-label="Mark as not done"
            aria-pressed="true"
            class="flex size-6 shrink-0 items-center justify-center rounded-full bg-emerald-500 text-white transition focus:outline-none focus-visible:ring-2 focus-visible:ring-emerald-500 focus-visible:ring-offset-2">
            <svg viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round"
              stroke-linejoin="round" class="size-3.5">
              <path d="M4.5 10.5l3.5 3.5 7.5-8"></path>
            </svg>
          </button>
          <button v-else @click="markDone(index)" type="button" aria-label="Mark as done" aria-pressed="false"
            class="flex size-6 shrink-0 items-center justify-center rounded-full border-2 border-slate-300 text-white transition hover:border-emerald-500 focus:outline-none focus-visible:ring-2 focus-visible:ring-emerald-500 focus-visible:ring-offset-2"></button>
          <p class="min-w-0 flex-1 break-words text-base"
            :class="item.done ? 'text-slate-400 line-through' : 'text-slate-800'">
            {{ item.text }}
          </p>
          <div class="flex shrink-0 gap-1">
            <button @click="markEditing(index)" type="button"
              class="rounded-lg px-2.5 py-1.5 text-sm font-medium transition focus:outline-none focus-visible:ring-2 focus-visible:ring-slate-400 text-slate-500 hover:bg-slate-100 hover:text-slate-900">
              Edit
            </button>

            <button @click="deleteTask(index)" type="button"
              class="rounded-lg px-2.5 py-1.5 text-sm font-medium transition focus:outline-none focus-visible:ring-2 focus-visible:ring-slate-400 text-red-500 hover:bg-red-50 hover:text-red-600">
              Delete
            </button>
          </div>
        </div>
        <div class="flex items-center gap-2 rounded-2xl bg-white p-2 shadow-sm ring-2 ring-slate-900" v-else>
          <input v-model="item.text" type="text" autocomplete="off" aria-label="Edit task"
            class="min-w-0 flex-1 rounded-xl bg-transparent px-2 py-2 text-base text-slate-900 focus:outline-none" />
          <div class="flex shrink-0 gap-1">
            <button @click="editingDone(index)" type="button"
              class="rounded-lg bg-slate-900 px-3.5 py-1.5 text-sm font-medium text-white transition hover:bg-slate-700 active:scale-95 focus:outline-none focus-visible:ring-2 focus-visible:ring-slate-900 focus-visible:ring-offset-2">
              Done</button><button @click="cancelEditing(index)" type="button"
              class="rounded-lg px-2.5 py-1.5 text-sm font-medium transition focus:outline-none focus-visible:ring-2 focus-visible:ring-slate-400 text-slate-500 hover:bg-slate-100 hover:text-slate-900">
              Cancel
            </button>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  data() {
    return {
      currentFilter: 'all',
      task: '',
      temTask: '',
      tasks: [
        { text: 'Buy milk', done: false, isEditing: false },
        { text: 'Walk the dog', done: true, isEditing: false },
        { text: 'Read a book', done: false, isEditing: false },
      ],
    };
  },
  computed: {
    remaining() {
      return this.tasks.filter((item) => {
        return !item.done;
      }).length;
    },
    filterTasks() {
      if (this.currentFilter === 'active')
        return this.tasks.filter((t) => !t.done);
      if (this.currentFilter === 'completed')
        return this.tasks.filter((t) => t.done);
      return this.tasks;
    },
  },
  methods: {
    updateFilter(filter) {
      this.currentFilter = filter;
    },
    markDone(index) {
      this.tasks[index].done = !this.tasks[index].done;
    },
    addTask() {
      if (this.task.trim() === '') {
        return;
      }
      this.tasks.push({
        text: this.task,
        done: false,
      });
      this.task = '';
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    markEditing(index) {
      this.tasks[index].isEditing = !this.tasks[index].isEditing;
      this.temTask = this.tasks[index].text;
    },
    editingDone(index) {
      this.tasks[index].isEditing = !this.tasks[index].isEditing;
    },
    cancelEditing(index) {
      this.tasks[index].isEditing = false;
      this.tasks[index].text = this.temTask;
    },
  },
}
</script>