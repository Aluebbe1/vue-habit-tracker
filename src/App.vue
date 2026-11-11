<script setup>
import { computed, ref } from 'vue'

const habits = ref([
  { id: 1, name: 'Drink a glass of water', completed: true },
  { id: 2, name: 'Read for 20 minutes', completed: true },
  { id: 3, name: 'Go for a walk', completed: false },
  { id: 4, name: 'Write in my journal', completed: false },
])
const newHabit = ref('')
let nextId = habits.value.length + 1

const completedCount = computed(
  () => habits.value.filter((habit) => habit.completed).length,
)
const completionPercentage = computed(() =>
  habits.value.length
    ? Math.round((completedCount.value / habits.value.length) * 100)
    : 0,
)
const today = new Intl.DateTimeFormat('en', {
  weekday: 'long',
  month: 'long',
  day: 'numeric',
}).format(new Date())

function addHabit() {
  const name = newHabit.value.trim()
  if (!name) return

  habits.value.push({ id: nextId++, name, completed: false })
  newHabit.value = ''
}

function deleteHabit(id) {
  habits.value = habits.value.filter((habit) => habit.id !== id)
}
</script>

<template>
  <main class="page-shell">
    <div class="tracker">
      <header class="topbar">
        <a class="brand" href="#" aria-label="Daymark home">
          <span class="brand-mark" aria-hidden="true">
            <svg viewBox="0 0 24 24" fill="none">
              <path
                d="M5 12.5 9.2 17 19 7"
                stroke="currentColor"
                stroke-width="2.6"
                stroke-linecap="round"
                stroke-linejoin="round"
              />
            </svg>
          </span>
          <span>daymark</span>
        </a>
        <span class="date-label">{{ today }}</span>
      </header>

      <section class="intro" aria-labelledby="page-title">
        <p class="eyebrow">YOUR DAILY RESET</p>
        <h1 id="page-title">Small steps, <span>every day.</span></h1>
        <p class="intro-copy">A little consistency goes a long way. Keep showing up.</p>
      </section>

      <section class="progress-card" aria-label="Today's progress">
        <div class="progress-topline">
          <div>
            <p class="progress-label">TODAY'S PROGRESS</p>
            <p class="progress-count">
              <strong>{{ completedCount }}</strong> of {{ habits.length }} habits complete
            </p>
          </div>
          <span class="progress-percent">{{ completionPercentage }}%</span>
        </div>
        <div
          class="progress-track"
          role="progressbar"
          :aria-valuenow="completionPercentage"
          aria-valuemin="0"
          aria-valuemax="100"
          :aria-label="`${completionPercentage}% of today's habits complete`"
        >
          <div class="progress-fill" :style="{ width: `${completionPercentage}%` }"></div>
        </div>
        <p class="progress-message">
          {{ completionPercentage === 100 && habits.length ? 'Amazing work — you did it!' : 'You’re building something good.' }}
        </p>
      </section>

      <section class="habits-section" aria-labelledby="habits-heading">
        <div class="section-heading">
          <div>
            <h2 id="habits-heading">Your habits</h2>
            <p class="section-subtitle">Make today count, one check at a time.</p>
          </div>
          <span class="habit-count">{{ habits.length }} {{ habits.length === 1 ? 'habit' : 'habits' }}</span>
        </div>

        <ul v-if="habits.length" class="habit-list">
          <li v-for="habit in habits" :key="habit.id" class="habit-row" :class="{ completed: habit.completed }">
            <label class="habit-label">
              <input v-model="habit.completed" class="habit-checkbox" type="checkbox" />
              <span class="custom-checkbox" aria-hidden="true">
                <svg viewBox="0 0 16 16" fill="none">
                  <path
                    d="m3.5 8.2 3 3 6-6.4"
                    stroke="currentColor"
                    stroke-width="1.8"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  />
                </svg>
              </span>
              <span class="habit-name">{{ habit.name }}</span>
            </label>
            <button
              class="delete-button"
              type="button"
              :aria-label="`Delete ${habit.name}`"
              @click="deleteHabit(habit.id)"
            >
              <svg viewBox="0 0 20 20" fill="none" aria-hidden="true">
                <path
                  d="M4.5 6h11m-9.5 0 .6 10h6.8L14 6m-5.5 0V4.5h3V6m-3 3v4m3-4v4"
                  stroke="currentColor"
                  stroke-width="1.4"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                />
              </svg>
            </button>
          </li>
        </ul>
        <div v-else class="empty-state">
          <span class="empty-icon" aria-hidden="true">✦</span>
          <p>No habits on your list yet.</p>
          <span>Add one below to get started.</span>
        </div>

        <form class="add-form" @submit.prevent="addHabit">
          <label class="sr-only" for="new-habit">Add a habit</label>
          <input
            id="new-habit"
            v-model="newHabit"
            type="text"
            maxlength="80"
            placeholder="Add a new habit..."
            autocomplete="off"
          />
          <button type="submit" :disabled="!newHabit.trim()">
            <svg viewBox="0 0 20 20" fill="none" aria-hidden="true">
              <path
                d="M10 4v12m-6-6h12"
                stroke="currentColor"
                stroke-width="1.8"
                stroke-linecap="round"
              />
            </svg>
            Add habit
          </button>
        </form>
      </section>

      <footer class="footer-note">
        <span class="footer-sparkle" aria-hidden="true">✳</span>
        Progress, not perfection.
      </footer>
    </div>
  </main>
</template>

<style scoped>
:global(*) {
  box-sizing: border-box;
}

:global(body) {
  min-width: 320px;
  min-height: 100vh;
  margin: 0;
  background: #f7f8f6;
}

:global(#app) {
  width: 100%;
  max-width: none;
  min-height: 100vh;
  border: 0;
  text-align: left;
}

.page-shell {
  min-height: 100vh;
  padding: 0 24px;
  color: #242a25;
  font-family:
    Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
    sans-serif;
  background:
    radial-gradient(ellipse at 50% 0%, rgba(223, 236, 218, 0.6), transparent 40%),
    #f7f8f6;
}

.tracker {
  width: min(100%, 620px);
  margin: 0 auto;
  padding: 30px 0 26px;
}

.topbar,
.brand,
.progress-topline,
.section-heading,
.habit-row,
.habit-label,
.add-form,
.footer-note {
  display: flex;
  align-items: center;
}

.topbar,
.progress-topline,
.section-heading,
.habit-row {
  justify-content: space-between;
}

.brand {
  gap: 9px;
  color: #29372d;
  font-size: 17px;
  font-weight: 750;
  letter-spacing: -0.5px;
  text-decoration: none;
}

.brand-mark {
  display: grid;
  width: 30px;
  height: 30px;
  place-items: center;
  color: #fff;
  background: #527b58;
  border-radius: 10px;
}

.brand-mark svg {
  width: 19px;
  height: 19px;
}

.date-label {
  color: #7c857d;
  font-size: 13px;
  font-weight: 550;
}

.intro {
  margin: 62px 0 28px;
}

.eyebrow,
.progress-label {
  margin: 0;
  color: #718475;
  font-size: 10px;
  font-weight: 750;
  letter-spacing: 1.5px;
}

h1 {
  margin: 11px 0 8px;
  color: #29342c;
  font-size: clamp(30px, 6vw, 42px);
  font-weight: 720;
  letter-spacing: -1.9px;
  line-height: 1.12;
}

h1 span {
  color: #64806a;
}

.intro-copy,
.section-subtitle {
  margin: 0;
  color: #89918a;
  font-size: 14px;
  line-height: 1.6;
}

.progress-card {
  padding: 23px 24px 19px;
  background: #fff;
  border: 1px solid #e9ede8;
  border-radius: 16px;
  box-shadow: 0 8px 26px rgba(43, 62, 46, 0.045);
}

.progress-count {
  margin: 6px 0 0;
  color: #8a928b;
  font-size: 12px;
}

.progress-count strong {
  color: #344638;
  font-size: 14px;
}

.progress-percent {
  color: #527b58;
  font-size: 25px;
  font-weight: 720;
  letter-spacing: -1px;
}

.progress-track {
  height: 8px;
  margin-top: 20px;
  overflow: hidden;
  background: #edf1ec;
  border-radius: 999px;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #6d9471, #8eaf83);
  border-radius: inherit;
  transition: width 240ms ease;
}

.progress-message {
  margin: 11px 0 0;
  color: #9aa19b;
  font-size: 11px;
}

.habits-section {
  margin-top: 35px;
}

.section-heading {
  margin-bottom: 15px;
}

h2 {
  margin: 0 0 4px;
  color: #303a32;
  font-size: 19px;
  font-weight: 700;
  letter-spacing: -0.45px;
}

.section-subtitle {
  font-size: 12px;
}

.habit-count {
  padding: 6px 10px;
  color: #718475;
  background: #eaf0e8;
  border-radius: 999px;
  font-size: 11px;
  font-weight: 650;
}

.habit-list {
  margin: 0;
  padding: 0;
  overflow: hidden;
  list-style: none;
  background: #fff;
  border: 1px solid #e9ede8;
  border-radius: 14px;
}

.habit-row {
  min-height: 61px;
  padding: 0 15px 0 17px;
  border-bottom: 1px solid #f0f2ef;
  transition: background 160ms ease;
}

.habit-row:last-child {
  border-bottom: 0;
}

.habit-row:hover {
  background: #fbfcfa;
}

.habit-label {
  position: relative;
  flex: 1;
  gap: 12px;
  min-width: 0;
  cursor: pointer;
}

.habit-checkbox {
  position: absolute;
  width: 1px;
  height: 1px;
  opacity: 0;
}

.custom-checkbox {
  display: grid;
  width: 20px;
  height: 20px;
  flex: 0 0 auto;
  place-items: center;
  color: transparent;
  background: #fff;
  border: 1.5px solid #d8dfd8;
  border-radius: 7px;
  transition:
    background 150ms ease,
    border-color 150ms ease,
    color 150ms ease;
}

.custom-checkbox svg {
  width: 14px;
  height: 14px;
}

.habit-checkbox:checked + .custom-checkbox {
  color: #fff;
  background: #64866a;
  border-color: #64866a;
}

.habit-checkbox:focus-visible + .custom-checkbox {
  outline: 3px solid rgba(100, 134, 106, 0.25);
  outline-offset: 2px;
}

.habit-name {
  overflow: hidden;
  color: #424a43;
  font-size: 13px;
  font-weight: 550;
  text-overflow: ellipsis;
  white-space: nowrap;
  transition: color 150ms ease;
}

.completed .habit-name {
  color: #a1a8a1;
  text-decoration: line-through;
  text-decoration-color: #c3cbc3;
}

.delete-button {
  display: grid;
  width: 34px;
  height: 34px;
  flex: 0 0 auto;
  place-items: center;
  color: #a3aaa4;
  background: transparent;
  border: 0;
  border-radius: 8px;
  cursor: pointer;
  opacity: 0;
  transition:
    color 150ms ease,
    background 150ms ease,
    opacity 150ms ease;
}

.habit-row:hover .delete-button,
.delete-button:focus-visible {
  opacity: 1;
}

.delete-button:hover {
  color: #b75c55;
  background: #fbefed;
}

.delete-button svg {
  width: 19px;
  height: 19px;
}

.empty-state {
  display: flex;
  min-height: 142px;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #969e97;
  background: #fff;
  border: 1px dashed #dce4dc;
  border-radius: 14px;
}

.empty-icon {
  margin-bottom: 7px;
  color: #7b9b7e;
  font-size: 21px;
}

.empty-state p {
  margin: 0;
  color: #59645b;
  font-size: 13px;
  font-weight: 650;
}

.empty-state span:last-child {
  margin-top: 4px;
  font-size: 11px;
}

.add-form {
  gap: 9px;
  margin-top: 13px;
}

.add-form input {
  width: 100%;
  min-width: 0;
  height: 45px;
  padding: 0 14px;
  color: #39423b;
  background: #fff;
  border: 1px solid #e6ebe5;
  border-radius: 10px;
  outline: none;
  font: inherit;
  font-size: 12px;
  transition:
    border-color 150ms ease,
    box-shadow 150ms ease;
}

.add-form input::placeholder {
  color: #a3aaa4;
}

.add-form input:focus {
  border-color: #8eaa8e;
  box-shadow: 0 0 0 3px rgba(100, 134, 106, 0.12);
}

.add-form button {
  display: inline-flex;
  height: 45px;
  flex: 0 0 auto;
  align-items: center;
  justify-content: center;
  gap: 7px;
  padding: 0 15px;
  color: #fff;
  background: #527b58;
  border: 0;
  border-radius: 10px;
  cursor: pointer;
  font: inherit;
  font-size: 12px;
  font-weight: 650;
  transition:
    background 150ms ease,
    transform 150ms ease;
}

.add-form button:hover:not(:disabled) {
  background: #426a49;
}

.add-form button:active:not(:disabled) {
  transform: translateY(1px);
}

.add-form button:disabled {
  cursor: not-allowed;
  opacity: 0.52;
}

.add-form button svg {
  width: 17px;
  height: 17px;
}

.footer-note {
  justify-content: center;
  gap: 7px;
  margin-top: 30px;
  color: #a0a8a0;
  font-size: 11px;
}

.footer-sparkle {
  color: #88a288;
  font-size: 13px;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

@media (max-width: 540px) {
  .page-shell {
    padding: 0 18px;
  }

  .tracker {
    padding-top: 22px;
  }

  .intro {
    margin-top: 48px;
  }

  .progress-card {
    padding: 20px 18px 17px;
  }

  .habit-row {
    min-height: 58px;
  }

  .delete-button {
    opacity: 1;
  }
}

@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
  }
}
</style>
