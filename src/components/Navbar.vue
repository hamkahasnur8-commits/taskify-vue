<script setup>
const emit = defineEmits(['new-task'])
</script>

<template>
  <header class="site-header">
    <div class="topbar">
      <a class="brand-mark" href="#dashboard" aria-label="Beranda Taskify">TASKIFY<span>®</span></a>

      <div class="topbar-actions">
        <button class="new-task-button" type="button" @click="emit('new-task')">
          <span>Tugas baru</span><span class="new-task-plus" aria-hidden="true">+</span>
        </button>
        <a class="topbar-link analytics-link" href="#analytics">Statistik</a>
        <button class="profile-button" type="button" aria-label="Profil">
          <span class="profile-avatar" aria-hidden="true">
            <svg viewBox="0 0 20 20"><circle cx="10" cy="6.5" r="3" /><path d="M4.5 17c.3-3.1 2.3-4.8 5.5-4.8s5.2 1.7 5.5 4.8" /></svg>
          </span>
        </button>
      </div>
    </div>

  </header>
</template>

<style scoped>
.site-header {
  border-bottom: 1px solid rgba(242, 229, 224, 0.17);
  color: var(--paper);
  background: var(--oxblood);
}

.topbar {
  position: relative;
  display: grid;
  min-height: 76px;
  grid-template-columns: minmax(0, 1fr) auto minmax(0, 1fr);
  align-items: center;
  gap: 20px;
  width: min(100% - 80px, 1540px);
  margin: 0 auto;
}

.topbar-actions {
  display: flex;
  align-items: center;
}

.brand-mark {
  grid-column: 2;
  justify-self: center;
  color: #fff7ef;
  font-family: var(--display);
  font-size: 29px;
  line-height: 1;
  letter-spacing: 0.055em;
  white-space: nowrap;
}

.brand-mark span {
  position: relative;
  top: -10px;
  margin-left: 2px;
  font-family: var(--body);
  font-size: 8px;
  letter-spacing: 0;
}

.topbar-actions {
  grid-column: 3;
  justify-content: flex-end;
  gap: clamp(12px, 1.5vw, 24px);
}

.new-task-button {
  display: inline-flex;
  min-height: 36px;
  align-items: center;
  gap: 13px;
  padding: 0 12px;
  border: 1px solid #f0d9d2;
  color: var(--oxblood);
  background: #f0d9d2;
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  transition: color 180ms ease, background 180ms ease;
}

.new-task-button:hover {
  color: #fff7ef;
  background: transparent;
}

.new-task-plus {
  font-size: 15px;
  font-weight: 400;
  line-height: 1;
}

.topbar-link {
  color: rgba(242, 229, 224, 0.7);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.07em;
  text-transform: uppercase;
  transition: color 160ms ease;
}

.topbar-link:hover {
  color: #fff7ef;
}

.profile-button {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 0;
  border: 0;
  color: #fff7ef;
  background: transparent;
}

.profile-avatar {
  display: grid;
  width: 29px;
  height: 29px;
  place-items: center;
  border-radius: 50%;
  color: #fff7ef;
  background: var(--red);
  font-size: 8px;
  font-weight: 600;
}

.profile-avatar svg {
  width: 16px;
  height: 16px;
  fill: none;
  stroke: currentColor;
  stroke-linecap: round;
  stroke-width: 1.25;
}

@media (max-width: 1050px) {
  .topbar {
    width: calc(100% - 48px);
  }

  .topbar {
    grid-template-columns: 1fr auto;
    min-height: 69px;
  }

  .brand-mark {
    grid-column: 1;
    grid-row: 1;
    justify-self: start;
  }

  .topbar-actions {
    grid-column: 2;
    grid-row: 1;
  }
}

@media (max-width: 600px) {
  .topbar {
    width: calc(100% - 32px);
  }

  .topbar {
    min-height: 64px;
  }

  .topbar-actions {
    gap: 11px;
  }

  .analytics-link {
    display: none;
  }

  .new-task-button {
    min-height: 34px;
    gap: 8px;
    padding: 0 10px;
    font-size: 8px;
  }

}
</style>
