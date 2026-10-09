<script setup>
import { computed, onMounted, onUnmounted, reactive, ref } from 'vue'
import Dashboard from './components/Dashboard.vue'

function dateOffset(days) {
  const date = new Date()
  date.setDate(date.getDate() + days)
  return date.toISOString().slice(0, 10)
}

const tasks = ref([
  {
    id: 1,
    title: 'Finalisasi kampanye musim dingin',
    description: 'Satukan cerita musim, pilihan produk, dan catatan akhir kampanye.',
    priority: 'high',
    dueDate: dateOffset(0),
    completed: false,
    art: 'stone',
  },
  {
    id: 2,
    title: 'Tinjau arah visual',
    description: 'Berikan catatan pada konsep terbaru dan referensi gambar.',
    priority: 'medium',
    dueDate: dateOffset(1),
    completed: false,
    art: 'olive',
  },
  {
    id: 3,
    title: 'Kirim proposal revisi',
    description: 'Bagikan ruang lingkup terbaru kepada studio sebelum akhir pekan.',
    priority: 'high',
    dueDate: dateOffset(3),
    completed: false,
    art: 'clay',
  },
  {
    id: 4,
    title: 'Perbarui deskripsi produk',
    description: 'Rapikan deskripsi koleksi dan detail produk.',
    priority: 'low',
    dueDate: dateOffset(-1),
    completed: true,
    art: 'blue',
  },
])

const activeFilter = ref('all')
const searchQuery = ref('')
const isTaskModalOpen = ref(false)
const editingTaskId = ref(null)
const isTaskOptionsOpen = ref(false)
const today = dateOffset(0)
const artStyles = ['stone', 'olive', 'clay', 'blue']
const taskForm = reactive({ title: '', description: '', priority: 'medium', dueDate: '' })

const filteredTasks = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()

  return tasks.value.filter((task) => {
    const matchesFilter = {
      all: true,
      'in-progress': !task.completed,
      completed: task.completed,
      priority: task.priority === 'high',
    }[activeFilter.value]
    const matchesSearch = !query || `${task.title} ${task.description}`.toLowerCase().includes(query)
    return matchesFilter && matchesSearch
  })
})

const filterCounts = computed(() => ({
  all: tasks.value.length,
  'in-progress': tasks.value.filter((task) => !task.completed).length,
  completed: tasks.value.filter((task) => task.completed).length,
  priority: tasks.value.filter((task) => task.priority === 'high').length,
}))

const stats = computed(() => {
  const total = tasks.value.length
  const completed = tasks.value.filter((task) => task.completed).length
  return {
    total,
    dueToday: tasks.value.filter((task) => task.dueDate === today && !task.completed).length,
    completionRate: total ? Math.round((completed / total) * 100) : 0,
  }
})

const modalTitle = computed(() => editingTaskId.value ? 'Ubah tugas' : 'Tugas baru')

function resetForm() {
  taskForm.title = ''
  taskForm.description = ''
  taskForm.priority = 'medium'
  taskForm.dueDate = ''
  isTaskOptionsOpen.value = false
}

function openNewTask() {
  editingTaskId.value = null
  resetForm()
  isTaskModalOpen.value = true
}

function editTask(task) {
  editingTaskId.value = task.id
  taskForm.title = task.title
  taskForm.description = task.description
  taskForm.priority = task.priority
  taskForm.dueDate = task.dueDate || ''
  isTaskOptionsOpen.value = Boolean(task.description || task.dueDate || task.priority !== 'medium')
  isTaskModalOpen.value = true
}

function closeTaskModal() {
  isTaskModalOpen.value = false
  editingTaskId.value = null
  isTaskOptionsOpen.value = false
}

function saveTask() {
  const title = taskForm.title.trim()
  if (!title) return

  const details = {
    title,
    description: taskForm.description.trim(),
    priority: taskForm.priority,
    dueDate: taskForm.dueDate,
  }

  if (editingTaskId.value !== null) {
    const task = tasks.value.find((item) => item.id === editingTaskId.value)
    if (task) Object.assign(task, details)
  } else {
    tasks.value.unshift({
      id: Date.now(),
      ...details,
      completed: false,
      art: artStyles[tasks.value.length % artStyles.length],
    })
  }

  closeTaskModal()
}

function toggleTask(taskId) {
  const task = tasks.value.find((item) => item.id === taskId)
  if (task) task.completed = !task.completed
}

function removeTask(taskId) {
  tasks.value = tasks.value.filter((task) => task.id !== taskId)
}

function closeOnEscape(event) {
  if (event.key === 'Escape') closeTaskModal()
}

onMounted(() => window.addEventListener('keydown', closeOnEscape))
onUnmounted(() => window.removeEventListener('keydown', closeOnEscape))
</script>

<template>
  <Dashboard
    :tasks="tasks"
    :filtered-tasks="filteredTasks"
    :active-filter="activeFilter"
    :filter-counts="filterCounts"
    :stats="stats"
    :search-query="searchQuery"
    @change-filter="activeFilter = $event"
    @new-task="openNewTask"
    @search="searchQuery = $event"
    @edit-task="editTask"
    @toggle-task="toggleTask"
    @remove-task="removeTask"
  />

  <Transition name="modal">
    <div v-if="isTaskModalOpen" class="modal-backdrop" @click.self="closeTaskModal">
      <section class="task-modal" role="dialog" aria-modal="true" aria-labelledby="task-modal-title">
        <button class="modal-close" type="button" aria-label="Tutup formulir tugas" @click="closeTaskModal">
          <svg viewBox="0 0 20 20" aria-hidden="true"><path d="m5 5 10 10M15 5 5 15" /></svg>
        </button>
        <h2 id="task-modal-title">{{ modalTitle }}</h2>

        <form class="task-form" @submit.prevent="saveTask">
          <label class="form-field form-field-wide">
            <span>Task</span>
            <input v-model="taskForm.title" autofocus maxlength="90" placeholder="Tulis nama tugas..." required />
          </label>
          <button
            v-if="editingTaskId !== null"
            class="options-toggle"
            type="button"
            :aria-expanded="isTaskOptionsOpen"
            @click="isTaskOptionsOpen = !isTaskOptionsOpen"
          >
            <span>{{ isTaskOptionsOpen ? 'Sembunyikan opsi' : 'Tambah rincian' }}</span>
            <span class="options-caption">Prioritas · tenggat</span>
            <svg viewBox="0 0 16 16" aria-hidden="true"><path d="m4 6 4 4 4-4" /></svg>
          </button>
          <Transition name="options">
            <div v-if="isTaskOptionsOpen" class="task-options">
              <label class="form-field form-field-wide">
                <span>Deskripsi <small>Opsional</small></span>
                <textarea v-model="taskForm.description" rows="2" maxlength="220" placeholder="Tambahkan konteks..."></textarea>
              </label>
              <label class="form-field">
                <span>Prioritas</span>
                <select v-model="taskForm.priority">
                  <option value="low">Rendah</option>
                  <option value="medium">Sedang</option>
                  <option value="high">Tinggi</option>
                </select>
              </label>
              <label class="form-field">
                <span>Tenggat</span>
                <input v-model="taskForm.dueDate" type="date" />
              </label>
            </div>
          </Transition>
          <div class="form-actions">
            <button class="cancel-button" type="button" @click="closeTaskModal">Batal</button>
            <button class="save-button" type="submit">{{ editingTaskId ? 'Simpan perubahan' : 'Tambah tugas' }} <span aria-hidden="true">↗</span></button>
          </div>
        </form>
      </section>
    </div>
  </Transition>
</template>

<style>
.modal-backdrop {
  position: fixed;
  z-index: 20;
  inset: 0;
  display: grid;
  padding: 20px;
  place-items: center;
  background: rgba(48, 20, 28, 0.54);
  backdrop-filter: blur(8px);
}

.task-modal {
  position: relative;
  width: min(510px, 100%);
  padding: 39px;
  border: 1px solid rgba(255, 238, 230, 0.62);
  background: rgba(244, 231, 225, 0.97);
  box-shadow: 0 25px 85px rgba(13, 13, 13, 0.2);
}

.modal-close {
  position: absolute;
  top: 18px;
  right: 18px;
  display: grid;
  width: 31px;
  height: 31px;
  padding: 0;
  place-items: center;
  border: 1px solid var(--line);
  border-radius: 50%;
  background: transparent;
}

.modal-close svg {
  width: 14px;
  height: 14px;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-width: 1.4;
}

.modal-eyebrow {
  color: var(--clay);
  font-size: 8px;
  letter-spacing: 0.11em;
  text-transform: uppercase;
}

.task-modal h2 {
  max-width: 370px;
  margin: 0;
  font-family: var(--display);
  font-size: 42px;
  font-weight: 400;
  line-height: 0.9;
  text-transform: uppercase;
}

.task-form {
  display: grid;
  gap: 13px;
  margin-top: 22px;
}

.task-options {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 13px 12px;
  overflow: hidden;
}

.options-toggle {
  display: flex;
  min-height: 38px;
  align-items: center;
  gap: 9px;
  padding: 0;
  border: 0;
  color: var(--oxblood);
  background: transparent;
  font-size: 9px;
  font-weight: 600;
  text-align: left;
}

.options-toggle svg {
  width: 14px;
  height: 14px;
  margin-left: auto;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-width: 1.4;
  transition: transform 180ms ease;
}

.options-toggle[aria-expanded='true'] svg {
  transform: rotate(180deg);
}

.options-caption {
  color: #947d7a;
  font-size: 8px;
  font-weight: 400;
}

.options-enter-active,
.options-leave-active {
  transition: opacity 160ms ease, max-height 200ms ease;
  max-height: 220px;
}

.options-enter-from,
.options-leave-to {
  max-height: 0;
  opacity: 0;
}

.form-field {
  display: grid;
  gap: 7px;
  min-width: 0;
}

.form-field-wide {
  grid-column: 1 / -1;
}

.form-field > span {
  color: var(--muted);
  font-size: 8px;
  font-weight: 650;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.form-field small {
  margin-left: 4px;
  color: #969f95;
  font-size: 8px;
  font-weight: 400;
  letter-spacing: 0;
  text-transform: none;
}

.form-field input,
.form-field textarea,
.form-field select {
  width: 100%;
  min-height: 41px;
  padding: 10px 11px;
  border: 1px solid rgba(97, 39, 53, 0.2);
  border-radius: 0;
  outline: 0;
  color: var(--ink);
  background: rgba(255, 243, 238, 0.56);
  font: inherit;
  font-size: 10px;
}

.form-field textarea {
  min-height: 78px;
  resize: vertical;
}

.form-field input:focus,
.form-field textarea:focus,
.form-field select:focus {
  border-color: var(--ink);
}

.form-field input::placeholder,
.form-field textarea::placeholder {
  color: #969f95;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 9px;
  margin-top: 7px;
}

.cancel-button,
.save-button {
  min-height: 38px;
  padding: 0 13px;
  border: 1px solid var(--ink);
  font-size: 9px;
}

.cancel-button {
  background: transparent;
}

.save-button {
  display: inline-flex;
  align-items: center;
  gap: 17px;
  border-color: var(--oxblood);
  color: var(--paper);
  background: var(--oxblood);
}

.modal-enter-active,
.modal-leave-active {
  transition: opacity 180ms ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

@media (max-width: 540px) {
  .task-modal {
    padding: 31px 22px 23px;
  }

  .task-modal h2 {
    max-width: 280px;
    font-size: 41px;
  }

  .task-options {
    grid-template-columns: 1fr 1fr;
  }
}

@media (prefers-reduced-motion: reduce) {
  .modal-enter-active,
  .modal-leave-active,
  .options-enter-active,
  .options-leave-active {
    transition-duration: 0.01ms;
  }
}
</style>
