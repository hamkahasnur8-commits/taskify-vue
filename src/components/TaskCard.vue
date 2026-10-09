<script setup>
const props = defineProps({
  task: { type: Object, required: true },
  index: { type: Number, default: 0 },
})

const emit = defineEmits(['toggle', 'edit', 'remove'])

const priorityLabels = {
  low: 'Prioritas rendah',
  medium: 'Prioritas sedang',
  high: 'Prioritas tinggi',
}

function formatDueDate(value) {
  if (!value) return 'Tanpa tenggat'
  const date = new Date(`${value}T12:00:00`)
  return new Intl.DateTimeFormat('id-ID', { day: 'numeric', month: 'short' }).format(date)
}
</script>

<template>
  <article class="task-card" :class="[`art-${props.task.art}`, { 'is-complete': props.task.completed }]">
    <div class="card-art" aria-hidden="true">
      <span class="art-mark">{{ String(props.index + 1).padStart(2, '0') }}</span>
      <span class="art-shape art-shape-one"></span>
      <span class="art-shape art-shape-two"></span>
      <span class="art-caption">Catatan tugas<br />Nomor {{ String(props.index + 1).padStart(2, '0') }}</span>
      <button
        class="status-toggle"
        type="button"
        :aria-label="props.task.completed ? `Tandai ${props.task.title} sedang dikerjakan` : `Selesaikan ${props.task.title}`"
        :aria-pressed="props.task.completed"
        @click="emit('toggle', props.task.id)"
      >
        <svg v-if="props.task.completed" viewBox="0 0 20 20" aria-hidden="true"><path d="m4 10 4 4 8-8" /></svg>
        <span v-else class="status-empty" aria-hidden="true"></span>
      </button>
      <span class="art-overlay"></span>
      <h3 class="card-title">{{ props.task.title }}</h3>
    </div>

    <div class="card-content">
      <p class="card-description">{{ props.task.description || 'Satu langkah kecil yang mendekatkanmu pada tujuan.' }}</p>
      <div class="card-meta">
        <span class="priority-tag" :class="`priority-${props.task.priority}`">
          <i></i>{{ priorityLabels[props.task.priority] }}
        </span>
        <span class="due-date" :class="{ 'due-today': props.task.dueDate === new Date().toISOString().slice(0, 10) && !props.task.completed }">
          <svg viewBox="0 0 16 16" aria-hidden="true"><rect x="2.5" y="3.5" width="11" height="10" rx="1.5" /><path d="M5 2v3M11 2v3M3 6.5h10" /></svg>
          {{ formatDueDate(props.task.dueDate) }}
        </span>
      </div>
      <div class="card-footer">
        <span class="task-status"><i></i>{{ props.task.completed ? 'Selesai' : 'Sedang dikerjakan' }}</span>
        <div class="card-actions">
          <button type="button" :aria-label="`Ubah ${props.task.title}`" @click="emit('edit', props.task)">Ubah</button>
          <button type="button" :aria-label="`Hapus ${props.task.title}`" @click="emit('remove', props.task.id)">
            <svg viewBox="0 0 16 16" aria-hidden="true"><path d="M3 4.5h10M6 4.5V3h4v1.5M5 4.5l.5 9h5l.5-9M6.5 7v4M9.5 7v4" /></svg>
          </button>
        </div>
      </div>
    </div>
  </article>
</template>

<style scoped>
.task-card {
  min-width: 0;
  overflow: hidden;
  border: 1px solid rgba(56, 88, 71, 0.13);
  border-radius: 14px;
  background: var(--surface);
  box-shadow: 0 4px 14px rgba(26, 24, 19, 0.025);
  transition: transform 220ms ease, box-shadow 220ms ease, opacity 180ms ease;
}

.task-card:hover {
  z-index: 1;
  box-shadow: 0 14px 32px rgba(26, 24, 19, 0.1);
  transform: translateY(-4px);
}

.task-card.is-complete .card-title {
  opacity: 0.66;
}

.card-art {
  position: relative;
  height: 220px;
  overflow: hidden;
  color: #fff7ef;
  background: #8e5c5c;
  isolation: isolate;
}

.card-art::before,
.card-art::after {
  position: absolute;
  inset: 0;
  content: '';
  transition: transform 600ms cubic-bezier(0.2, 0.65, 0.25, 1);
}

.card-art::before {
  background:
    radial-gradient(ellipse at 69% 29%, rgba(248, 224, 208, 0.92) 0 9%, transparent 9.5%),
    radial-gradient(ellipse at 70% 36%, rgba(70, 27, 42, 0.78) 0 16%, transparent 16.5%),
    linear-gradient(132deg, #612735 0%, #bf827e 48%, #45232e 100%);
}

.card-art::after {
  inset: 14% 10% 0 45%;
  border: 1px solid rgba(247, 244, 236, 0.52);
  border-bottom: 0;
  border-radius: 48% 48% 0 0;
  background: linear-gradient(145deg, rgba(246, 218, 207, 0.36), rgba(76, 31, 44, 0.52));
  transform: rotate(-13deg);
}

.task-card:hover .card-art::before,
.task-card:hover .card-art::after {
  transform: scale(1.055);
}

.art-olive .card-art::before {
  background:
    radial-gradient(ellipse at 29% 39%, rgba(248, 232, 204, 0.9) 0 8%, transparent 8.5%),
    radial-gradient(ellipse at 30% 46%, rgba(53, 76, 53, 0.72) 0 17%, transparent 17.5%),
    linear-gradient(128deg, #78896c, #d0c5a7 49%, #52664f);
}

.art-olive .card-art::after {
  inset: 9% 42% 0 8%;
  border-radius: 45% 45% 0 0;
  transform: rotate(10deg);
}

.art-clay .card-art::before {
  background:
    radial-gradient(ellipse at 72% 24%, rgba(250, 225, 202, 0.9) 0 9%, transparent 9.5%),
    radial-gradient(ellipse at 72% 34%, rgba(91, 34, 45, 0.75) 0 17%, transparent 17.5%),
    linear-gradient(132deg, #853d46, #d6a395 50%, #aa6058);
}

.art-clay .card-art::after {
  inset: 18% 7% 0 42%;
  border-radius: 52% 52% 0 0;
}

.art-blue .card-art::before {
  background:
    radial-gradient(ellipse at 31% 28%, rgba(235, 239, 224, 0.9) 0 8%, transparent 8.5%),
    radial-gradient(ellipse at 31% 37%, rgba(44, 76, 77, 0.74) 0 16%, transparent 16.5%),
    linear-gradient(135deg, #527476, #becbbd 52%, #698c88);
}

.art-blue .card-art::after {
  inset: 11% 37% 0 5%;
  transform: rotate(9deg);
}

.art-mark,
.art-caption {
  position: absolute;
  z-index: 1;
  font-size: 8px;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.art-mark {
  top: 15px;
  left: 17px;
}

.art-caption {
  top: 15px;
  right: 17px;
  text-align: right;
  line-height: 1.45;
}

.art-shape {
  position: absolute;
  z-index: 1;
  width: 1px;
  height: 48px;
  background: rgba(248, 246, 240, 0.6);
}

.art-shape-one {
  top: 55px;
  left: 17px;
}

.art-shape-two {
  right: 17px;
  bottom: 62px;
  height: 32px;
}

.art-overlay {
  position: absolute;
  z-index: 1;
  inset: 40% 0 0;
  background: linear-gradient(180deg, transparent, rgba(13, 13, 13, 0.58));
}

.card-title {
  position: absolute;
  z-index: 2;
  right: 17px;
  bottom: 16px;
  left: 17px;
  margin: 0;
  font-family: var(--display);
  font-size: 36px;
  font-weight: 400;
  letter-spacing: 0.015em;
  line-height: 0.9;
  text-transform: uppercase;
  transition: opacity 180ms ease;
}

.status-toggle {
  position: absolute;
  z-index: 3;
  top: 12px;
  left: 50%;
  display: grid;
  width: 29px;
  height: 29px;
  padding: 0;
  place-items: center;
  border: 1px solid rgba(255, 247, 239, 0.78);
  border-radius: 50%;
  color: var(--ink);
  background: rgba(255, 247, 239, 0.9);
  transform: translateX(-50%);
  transition: background 160ms ease, color 160ms ease;
}

.status-toggle:hover,
.status-toggle[aria-pressed='true'] {
  color: #fff7ef;
  background: var(--oxblood);
}

.status-toggle svg {
  width: 15px;
  height: 15px;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-width: 1.7;
}

.status-empty {
  width: 7px;
  height: 7px;
  border: 1px solid currentColor;
  border-radius: 50%;
}

.card-content {
  padding: 15px 16px 13px;
}

.card-description {
  min-height: 36px;
  color: var(--muted);
  font-size: 10px;
  line-height: 1.55;
}

.card-meta,
.card-footer,
.card-actions,
.priority-tag,
.due-date,
.task-status {
  display: flex;
  align-items: center;
}

.card-meta {
  justify-content: space-between;
  gap: 8px;
  margin-top: 15px;
}

.priority-tag {
  gap: 6px;
  color: #66625b;
  font-size: 8px;
  font-weight: 600;
  text-transform: uppercase;
}

.priority-tag i,
.task-status i {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: #9a978f;
}

.priority-high {
  color: #a13f49;
}

.priority-high i {
  background: var(--red);
}

.priority-medium i {
  background: var(--gold);
}

.priority-low i {
  background: var(--sage);
}

.due-date {
  gap: 5px;
  color: #716d65;
  font-size: 9px;
  font-variant-numeric: tabular-nums;
}

.due-date svg {
  width: 13px;
  height: 13px;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-width: 1.2;
}

.due-date.due-today {
  color: var(--clay);
}

.card-footer {
  justify-content: space-between;
  margin-top: 13px;
  padding-top: 11px;
  border-top: 1px solid rgba(13, 13, 13, 0.1);
}

.task-status {
  gap: 6px;
  color: #77736c;
  font-size: 8px;
}

.task-status i {
  width: 5px;
  height: 5px;
  background: var(--gold);
}

.is-complete .task-status i {
  background: var(--sage);
}

.card-actions {
  gap: 10px;
}

.card-actions button {
  display: grid;
  min-width: 24px;
  min-height: 24px;
  padding: 0;
  place-items: center;
  border: 0;
  color: #77736c;
  background: transparent;
  font-size: 8px;
  transition: color 150ms ease;
}

.card-actions button:hover {
  color: var(--ink);
}

.card-actions svg {
  width: 14px;
  height: 14px;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-width: 1.2;
}

@media (max-width: 760px) {
  .card-art {
    height: 230px;
  }

  .card-title {
    font-size: 40px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .task-card,
  .card-art::before,
  .card-art::after {
    transition-duration: 0.01ms;
  }
}
</style>
