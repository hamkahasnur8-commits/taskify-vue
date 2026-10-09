<script setup>
import Navbar from './Navbar.vue'
import TaskCard from './TaskCard.vue'

const props = defineProps({
  tasks: { type: Array, required: true },
  filteredTasks: { type: Array, required: true },
  activeFilter: { type: String, required: true },
  filterCounts: { type: Object, required: true },
  stats: { type: Object, required: true },
  searchQuery: { type: String, required: true },
})

const emit = defineEmits(['change-filter', 'new-task', 'search', 'edit-task', 'toggle-task', 'remove-task'])
const monthLabel = new Intl.DateTimeFormat('id-ID', { month: 'long', year: 'numeric' }).format(new Date())
const filters = [
  { id: 'all', label: 'Semua tugas' },
  { id: 'in-progress', label: 'Sedang dikerjakan' },
  { id: 'completed', label: 'Selesai' },
  { id: 'priority', label: 'Prioritas' },
]
</script>

<template>
  <div class="dashboard">
    <Navbar
      @new-task="emit('new-task')"
    />

    <main id="dashboard" class="dashboard-main">
      <section class="workspace-strip" aria-label="Status ruang kerja">
        <div class="workspace-person">
          <span class="workspace-status">Fokusmu, tetap terjaga</span>
        </div>
        <div class="workspace-quick-actions">
          <span class="focus-indicator"><i></i> Sedang fokus</span>
        </div>
      </section>

      <section class="hero-section" aria-labelledby="dashboard-title">
        <div class="hero-overline"><span>Ruang kerja pribadi / {{ monthLabel }}</span><span>Sisakan ruang untuk hal penting</span></div>
        <h1 id="dashboard-title">Fokus pada<br class="mobile-break" /> yang berarti<span>.</span></h1>
        <div class="hero-bottom">
          <p>Pikiran jernih berawal dari daftar yang terarah.<br />Satu langkah, satu waktu.</p>
          <div class="stat-grid" id="analytics">
            <div class="stat-item"><span class="stat-label">Total tugas</span><strong>{{ props.stats.total }}</strong><span class="stat-foot">Di ruang kerjamu</span></div>
            <div class="stat-item"><span class="stat-label">Jatuh tempo hari ini</span><strong>{{ props.stats.dueToday }}</strong><span class="stat-foot">Fokus satu per satu</span></div>
            <div class="stat-item"><span class="stat-label">Selesai</span><strong>{{ props.stats.completionRate }}<small>%</small></strong><span class="stat-foot">Persentase tugas selesai</span></div>
          </div>
        </div>
      </section>

      <section class="task-section" aria-labelledby="task-section-title">
        <div class="section-heading">
          <div>
            <span class="section-eyebrow">Tugas, tertata dengan cermat</span>
            <h2 id="task-section-title">Papan tugas <span>{{ props.filteredTasks.length.toString().padStart(2, '0') }}</span></h2>
          </div>
          <label class="search-field">
            <svg viewBox="0 0 20 20" aria-hidden="true"><circle cx="8.8" cy="8.8" r="5.8" /><path d="m13.2 13.2 4 4" /></svg>
            <input
              :value="props.searchQuery"
              type="search"
              placeholder="Cari tugas"
              aria-label="Cari tugas"
              @input="emit('search', $event.target.value)"
            />
          </label>
        </div>

        <nav class="board-filters" aria-label="Filter tugas">
          <button
            v-for="filter in filters"
            :key="filter.id"
            class="board-filter"
            :class="{ 'is-active': props.activeFilter === filter.id }"
            type="button"
            :aria-pressed="props.activeFilter === filter.id"
            @click="emit('change-filter', filter.id)"
          >
            <span>{{ filter.label }}</span>
            <span class="board-filter-count">{{ props.filterCounts[filter.id] }}</span>
          </button>
        </nav>

        <TransitionGroup v-if="props.filteredTasks.length" name="task-list" tag="div" class="task-grid">
          <TaskCard
            v-for="(task, index) in props.filteredTasks"
            :key="task.id"
            :task="task"
            :index="index"
            @toggle="emit('toggle-task', $event)"
            @edit="emit('edit-task', $event)"
            @remove="emit('remove-task', $event)"
          />
        </TransitionGroup>

        <div v-else class="empty-state">
          <span class="empty-mark">—</span>
          <h3>Belum ada tugas di tampilan ini.</h3>
          <p>Buat ruang untuk ide baru, atau coba filter lain.</p>
          <button type="button" @click="emit('new-task')">Buat tugas <span aria-hidden="true">↗</span></button>
        </div>
      </section>

      <footer class="dashboard-footer">
        <a href="#dashboard" class="footer-brand">TASKIFY®</a>
        <span>Lebih sedikit gangguan. Lebih bermakna.</span>
        <span>Untuk langkahmu berikutnya.</span>
      </footer>
    </main>
  </div>
</template>

<style scoped>
.dashboard {
  min-height: 100vh;
}

.dashboard-main {
  width: min(100% - 80px, 1540px);
  margin: 0 auto;
}

.workspace-strip {
  display: flex;
  min-height: 66px;
  align-items: center;
  justify-content: space-between;
  border-bottom: 1px solid var(--line);
}

.workspace-person,
.workspace-quick-actions {
  display: flex;
  align-items: center;
}

.focus-indicator i {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--sage);
}

.workspace-status {
  color: var(--muted);
  font-size: 9px;
}

.workspace-quick-actions {
  gap: 17px;
}

.focus-indicator {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  color: var(--muted);
  font-size: 9px;
}

.hero-section {
  padding-top: 28px;
}

.hero-overline,
.hero-bottom {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
}

.hero-overline {
  color: var(--muted);
  font-size: 8px;
  font-weight: 550;
  letter-spacing: 0.11em;
  text-transform: uppercase;
}

.hero-overline span:last-child {
  text-align: right;
}

.hero-section h1 {
  margin: 14px -0.045em 12px -0.055em;
  font-family: var(--display);
  font-size: 148px;
  font-weight: 400;
  letter-spacing: -0.025em;
  line-height: 0.72;
  text-align: justify;
  text-transform: uppercase;
  white-space: nowrap;
}

.hero-section h1 span {
  color: var(--clay);
}

.mobile-break {
  display: none;
}

.hero-bottom {
  min-height: 127px;
  align-items: center;
  padding: 22px 0 23px;
}

.hero-bottom > p {
  color: var(--muted);
  font-size: 10px;
  line-height: 1.6;
}

.stat-grid {
  display: flex;
  align-items: stretch;
  gap: 0;
}

.stat-item {
  display: grid;
  min-width: 121px;
  gap: 3px;
  padding: 0 19px;
  border-left: 1px solid var(--line);
}

.stat-item:last-child {
  padding-right: 0;
}

.stat-label,
.stat-foot {
  color: var(--muted);
  font-size: 8px;
}

.stat-label {
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.stat-item strong {
  font-family: var(--display);
  font-size: 31px;
  font-weight: 400;
  line-height: 0.95;
}

.stat-item:first-child strong {
  color: var(--oxblood);
}

.stat-item:nth-child(2) strong {
  color: var(--red);
}

.stat-item:nth-child(3) strong {
  color: var(--sage);
}

.stat-item strong small {
  margin-left: 1px;
  font-family: var(--body);
  font-size: 12px;
  font-weight: 500;
}

.task-section {
  padding: 19px 0 35px;
  border-top: 1px solid var(--line);
}

.section-heading {
  display: flex;
  align-items: flex-end;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 16px;
}

.section-eyebrow {
  color: var(--clay);
  font-size: 8px;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.section-heading h2 {
  margin: 4px 0 0;
  font-family: var(--display);
  font-size: 27px;
  font-weight: 400;
  line-height: 1;
  text-transform: uppercase;
}

.section-heading h2 span {
  position: relative;
  top: -8px;
  margin-left: 3px;
  color: var(--sage);
  font-family: var(--body);
  font-size: 9px;
}

.board-filters {
  display: flex;
  align-items: stretch;
  gap: 24px;
  margin: -3px 0 15px;
  overflow-x: auto;
  border-bottom: 1px solid var(--line);
  scrollbar-width: none;
}

.board-filters::-webkit-scrollbar {
  display: none;
}

.board-filter {
  position: relative;
  display: inline-flex;
  flex: 0 0 auto;
  min-height: 38px;
  align-items: center;
  gap: 7px;
  padding: 0;
  border: 0;
  color: rgba(59, 32, 40, 0.63);
  background: transparent;
  font-size: 9px;
  font-weight: 550;
  text-transform: uppercase;
  transition: color 160ms ease;
}

.board-filter::after {
  position: absolute;
  right: 0;
  bottom: -1px;
  left: 0;
  height: 2px;
  background: var(--red);
  content: '';
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 180ms ease;
}

.board-filter:hover,
.board-filter.is-active {
  color: var(--oxblood);
}

.board-filter.is-active::after {
  transform: scaleX(1);
}

.board-filter-count {
  color: #947d7a;
  font-size: 8px;
  font-variant-numeric: tabular-nums;
}

.search-field {
  display: flex;
  width: 190px;
  height: 34px;
  align-items: center;
  gap: 8px;
  padding: 0 9px;
  border-bottom: 1px solid rgba(13, 13, 13, 0.28);
}

.search-field svg {
  width: 15px;
  height: 15px;
  fill: none;
  stroke: var(--sage);
  stroke-linecap: round;
  stroke-width: 1.3;
}

.search-field input {
  min-width: 0;
  flex: 1;
  padding: 0;
  border: 0;
  outline: 0;
  color: var(--ink);
  background: transparent;
  font: inherit;
  font-size: 9px;
}

.search-field input::placeholder {
  color: #90998f;
}

.search-field > span {
  color: #90998f;
  font-size: 8px;
  white-space: nowrap;
}

.task-grid {
  position: relative;
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  align-items: start;
  gap: 13px;
}

.task-list-enter-active,
.task-list-leave-active,
.task-list-move {
  transition: opacity 220ms ease, transform 220ms ease;
}

.task-list-enter-from,
.task-list-leave-to {
  opacity: 0;
  transform: translateY(12px);
}

.task-list-leave-active {
  position: absolute;
}

.empty-state {
  display: grid;
  min-height: 210px;
  justify-items: center;
  align-content: center;
  border: 1px dashed rgba(13, 13, 13, 0.2);
  text-align: center;
}

.empty-mark {
  color: var(--clay);
  font-family: var(--display);
  font-size: 32px;
}

.empty-state h3 {
  margin: 4px 0;
  font-family: var(--display);
  font-size: 23px;
  font-weight: 400;
  text-transform: uppercase;
}

.empty-state p {
  margin: 0 0 13px;
  color: var(--muted);
  font-size: 10px;
}

.empty-state button {
  padding: 8px 11px;
  border: 1px solid var(--oxblood);
  color: var(--oxblood);
  background: transparent;
  font-size: 9px;
}

.dashboard-footer {
  display: flex;
  min-height: 57px;
  align-items: center;
  justify-content: space-between;
  border-top: 1px solid var(--line);
  color: #817c74;
  font-size: 8px;
}

.footer-brand {
  color: var(--oxblood);
  font-family: var(--display);
  font-size: 17px;
  letter-spacing: 0.04em;
}

@media (min-width: 1550px) {
  .hero-section h1 {
    font-size: 164px;
  }
}

@media (max-width: 1180px) {
  .dashboard-main {
    width: calc(100% - 48px);
  }

  .hero-section h1 {
    font-size: 112px;
  }

  .hero-section .mobile-break {
    display: initial;
  }

  .task-grid {
    grid-template-columns: repeat(3, minmax(0, 1fr));
  }
}

@media (max-width: 760px) {
  .dashboard-main {
    width: calc(100% - 32px);
  }

  .workspace-strip {
    min-height: 58px;
  }

  .focus-indicator {
    display: none;
  }

  .workspace-quick-actions {
    gap: 0;
  }

  .hero-section {
    padding-top: 23px;
  }

  .hero-overline {
    font-size: 7px;
  }

  .hero-section h1 {
    margin: 18px -0.025em 14px -0.035em;
    font-size: 76px;
    line-height: 0.79;
    text-align: left;
    white-space: normal;
  }

  .mobile-break {
    display: initial;
  }

  .hero-bottom {
    min-height: 0;
    align-items: flex-start;
    flex-direction: column;
    gap: 21px;
    padding: 7px 0 21px;
  }

  .hero-bottom > p {
    font-size: 9px;
  }

  .stat-grid {
    width: 100%;
  }

  .stat-item {
    min-width: 0;
    flex: 1;
    padding: 0 10px;
  }

  .stat-item:first-child {
    padding-left: 0;
  }

  .stat-item strong {
    font-size: 28px;
  }

  .stat-foot {
    font-size: 7px;
  }

  .task-section {
    padding-top: 15px;
  }

  .section-heading {
    align-items: flex-start;
    flex-direction: column;
    gap: 9px;
  }

  .search-field {
    width: 100%;
    height: 32px;
  }

  .board-filters {
    gap: 19px;
  }

  .task-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 10px;
  }

  .dashboard-footer {
    min-height: 52px;
  }

  .dashboard-footer > span:last-child {
    display: none;
  }
}

@media (max-width: 520px) {
  .hero-section h1 {
    font-size: 62px;
  }

  .task-grid {
    grid-template-columns: 1fr;
  }
}

@media (prefers-reduced-motion: reduce) {
  .task-list-enter-active,
  .task-list-leave-active,
  .task-list-move {
    transition-duration: 0.01ms;
  }
}
</style>
