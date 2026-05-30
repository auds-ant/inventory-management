<template>
  <div :class="['app', { collapsed }]">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="brand-inner">
          <div class="brand-mark" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"/>
              <polyline points="3.27 6.96 12 12.01 20.73 6.96"/>
              <line x1="12" y1="22.08" x2="12" y2="12"/>
            </svg>
          </div>
          <div class="brand-text">
            <h1>{{ t('nav.companyName') }}</h1>
            <span class="subtitle">{{ t('nav.subtitle') }}</span>
          </div>
        </div>
        <button
          class="sidebar-toggle"
          @click="toggleCollapsed"
          :aria-expanded="String(!collapsed)"
          aria-label="Toggle sidebar"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="15 18 9 12 15 6"/>
          </svg>
        </button>
      </div>

      <nav class="sidebar-nav">
        <router-link
          to="/"
          :class="{ active: $route.path === '/' }"
          :title="t('nav.overview')"
          :aria-label="t('nav.overview')"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="3" width="7" height="7"/>
            <rect x="14" y="3" width="7" height="7"/>
            <rect x="3" y="14" width="7" height="7"/>
            <rect x="14" y="14" width="7" height="7"/>
          </svg>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>

        <router-link
          to="/inventory"
          :class="{ active: $route.path === '/inventory' }"
          :title="t('nav.inventory')"
          :aria-label="t('nav.inventory')"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"/>
            <polyline points="3.27 6.96 12 12.01 20.73 6.96"/>
            <line x1="12" y1="22.08" x2="12" y2="12"/>
          </svg>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          to="/orders"
          :class="{ active: $route.path === '/orders' }"
          :title="t('nav.orders')"
          :aria-label="t('nav.orders')"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="9" cy="21" r="1"/>
            <circle cx="20" cy="21" r="1"/>
            <path d="M1 1h4l2.68 13.39a2 2 0 0 0 2 1.61h9.72a2 2 0 0 0 2-1.61L23 6H6"/>
          </svg>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          to="/spending"
          :class="{ active: $route.path === '/spending' }"
          :title="t('nav.finance')"
          :aria-label="t('nav.finance')"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="12" y1="20" x2="12" y2="10"/>
            <line x1="18" y1="20" x2="18" y2="4"/>
            <line x1="6" y1="20" x2="6" y2="16"/>
          </svg>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          to="/demand"
          :class="{ active: $route.path === '/demand' }"
          :title="t('nav.demandForecast')"
          :aria-label="t('nav.demandForecast')"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/>
            <polyline points="17 6 23 6 23 12"/>
          </svg>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          to="/reports"
          :class="{ active: $route.path === '/reports' }"
          title="Reports"
          aria-label="Reports"
        >
          <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/>
            <polyline points="14 2 14 8 20 8"/>
            <line x1="16" y1="13" x2="8" y2="13"/>
            <line x1="16" y1="17" x2="8" y2="17"/>
            <polyline points="10 9 9 9 8 9"/>
          </svg>
          <span class="nav-label">Reports</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher :collapsed="collapsed" />
        <ProfileMenu
          :collapsed="collapsed"
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="content-col">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    // Sidebar collapsed state with localStorage persistence
    const collapsed = ref(localStorage.getItem('sidebar-collapsed') === 'true')

    const toggleCollapsed = () => {
      collapsed.value = !collapsed.value
      localStorage.setItem('sidebar-collapsed', String(collapsed.value))
    }

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(loadTasks)

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      collapsed,
      toggleCollapsed
    }
  }
}
</script>

<style>
:root {
  /* Brand */
  --color-primary:#4f46e5; --color-primary-hover:#4338ca; --color-primary-soft:#eef2ff;
  --color-accent:#22c55e;  --color-accent-hover:#16a34a;  --color-accent-soft:#dcfce7;
  /* Surfaces */
  --bg:#eef2f7; --surface:#ffffff; --surface-2:#f1f5f9;
  /* Text */
  --text:#0f172a; --text-muted:#64748b; --text-subtle:#94a3b8;
  /* Lines */
  --border:#e2e8f0; --border-strong:#cbd5e1;
  /* Status */
  --success:#22c55e; --success-soft:#dcfce7; --success-ink:#166534;
  --warning:#d97706; --warning-soft:#fef3c7; --warning-ink:#92400e;
  --danger:#dc2626;  --danger-soft:#fee2e2;  --danger-ink:#991b1b;
  --info:#4f46e5;    --info-soft:#eef2ff;    --info-ink:#3730a3;
  /* Spacing scale (4px base) */
  --space-1:.25rem; --space-2:.5rem; --space-3:.75rem; --space-4:1rem;
  --space-5:1.5rem; --space-6:2rem; --space-8:3rem;
  /* Radius / shadow / layout */
  --radius:10px; --radius-sm:6px; --radius-lg:14px;
  --shadow-sm:0 1px 2px rgba(15,23,42,.06);
  --shadow:0 1px 3px rgba(15,23,42,.08),0 1px 2px rgba(15,23,42,.04);
  --sidebar-w:248px; --sidebar-w-collapsed:68px; --content-max:1280px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--bg);
  color: var(--text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* ── App shell ─────────────────────────────────────────────── */

.app {
  display: grid;
  grid-template-columns: var(--sidebar-w) 1fr;
  min-height: 100vh;
  transition: grid-template-columns 0.2s ease;
}

.app.collapsed {
  grid-template-columns: var(--sidebar-w-collapsed) 1fr;
}

/* ── Sidebar ────────────────────────────────────────────────── */

.sidebar {
  background: var(--surface);
  border-right: 1px solid var(--border);
  position: sticky;
  top: 0;
  height: 100vh;
  display: flex;
  flex-direction: column;
  padding: var(--space-5) var(--space-4);
  overflow-y: auto;
  transition: padding 0.2s ease;
}

/* ── Brand ──────────────────────────────────────────────────── */

.sidebar-brand {
  display: flex;
  align-items: center;
  gap: var(--space-2);
  margin-bottom: var(--space-5);
  padding-bottom: var(--space-5);
  border-bottom: 1px solid var(--border);
}

.brand-inner {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  flex: 1;
  min-width: 0;
}

.brand-mark {
  flex-shrink: 0;
  color: var(--color-primary);
  display: flex;
  align-items: center;
  justify-content: center;
}

.brand-text {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
  min-width: 0;
}

.sidebar-brand h1 {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--text);
  letter-spacing: -0.025em;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-brand .subtitle {
  font-size: 0.75rem;
  color: var(--text-muted);
  font-weight: 400;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-toggle {
  flex-shrink: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 28px;
  height: 28px;
  padding: 0;
  background: none;
  border: 1px solid var(--border);
  border-radius: var(--radius-sm);
  color: var(--text-muted);
  cursor: pointer;
  transition: background 0.15s ease, border-color 0.15s ease, color 0.15s ease;
}

.sidebar-toggle:hover {
  background: var(--surface-2);
  border-color: var(--border-strong);
  color: var(--text);
}

.sidebar-toggle svg {
  transition: transform 0.2s ease;
}

/* ── Nav ────────────────────────────────────────────────────── */

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
  flex: 1;
}

.sidebar-nav a {
  display: flex;
  align-items: center;
  gap: .6rem;
  padding: .6rem .75rem;
  border-radius: var(--radius-sm);
  color: var(--text-muted);
  font-weight: 500;
  font-size: 0.875rem;
  text-decoration: none;
  transition: background 0.15s ease, color 0.15s ease;
}

.sidebar-nav a:hover {
  background: var(--surface-2);
  color: var(--text);
}

.sidebar-nav a.active {
  background: var(--color-primary-soft);
  color: var(--color-primary);
}

.sidebar-nav svg {
  flex-shrink: 0;
}

/* ── Footer ─────────────────────────────────────────────────── */

.sidebar-footer {
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  margin-top: auto;
  padding-top: var(--space-4);
  border-top: 1px solid var(--border);
}

/* ── Collapsed sidebar ──────────────────────────────────────── */

.app.collapsed .sidebar {
  padding: var(--space-5) var(--space-2);
  overflow: visible;
}

.app.collapsed .sidebar-brand {
  justify-content: center;
  gap: var(--space-1);
}

.app.collapsed .brand-inner {
  flex: none;
}

.app.collapsed .brand-text {
  display: none;
}

.app.collapsed .sidebar-toggle svg {
  transform: rotate(180deg);
}

.app.collapsed .sidebar-nav a {
  justify-content: center;
  padding: .6rem 0;
  gap: 0;
}

.app.collapsed .nav-label {
  display: none;
}

.app.collapsed .sidebar-footer {
  align-items: center;
}

/* ── Content column ─────────────────────────────────────────── */

.content-col {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.main-content {
  max-width: var(--content-max);
  width: 100%;
  margin: 0 auto;
  padding: var(--space-5) var(--space-6);
}

/* ── Page header ────────────────────────────────────────────── */

.page-header {
  margin-bottom: var(--space-5);
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: var(--text);
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--text-muted);
  font-size: 0.938rem;
}

/* ── Stats grid ─────────────────────────────────────────────── */

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: var(--space-5);
}

.stat-card {
  background: var(--surface);
  padding: 1.25rem;
  border-radius: var(--radius);
  border: 1px solid var(--border);
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: var(--border-strong);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: var(--text-muted);
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: var(--text);
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: var(--warning);
}

.stat-card.success .stat-value {
  color: var(--color-accent-hover);
}

.stat-card.danger .stat-value {
  color: var(--danger);
}

.stat-card.info .stat-value {
  color: var(--color-primary);
}

/* ── Card ───────────────────────────────────────────────────── */

.card {
  background: var(--surface);
  border-radius: var(--radius);
  padding: var(--space-5);
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid var(--border);
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: var(--text);
  letter-spacing: -0.025em;
}

/* ── Tables ─────────────────────────────────────────────────── */

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--bg);
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: var(--text-muted);
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid var(--surface-2);
  color: var(--text);
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: var(--bg);
}

/* ── Badges ─────────────────────────────────────────────────── */

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: var(--radius-sm);
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: var(--success-soft);
  color: var(--success-ink);
}

.badge.warning {
  background: var(--warning-soft);
  color: var(--warning-ink);
}

.badge.danger {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.info {
  background: var(--info-soft);
  color: var(--info-ink);
}

.badge.increasing {
  background: var(--success-soft);
  color: var(--success-ink);
}

.badge.decreasing {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.stable {
  background: var(--info-soft);
  color: var(--info-ink);
}

.badge.high {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

.badge.medium {
  background: var(--warning-soft);
  color: var(--warning-ink);
}

.badge.low {
  background: var(--info-soft);
  color: var(--info-ink);
}

/* ── Loading / Error ────────────────────────────────────────── */

.loading {
  text-align: center;
  padding: var(--space-8);
  color: var(--text-muted);
  font-size: 0.938rem;
}

.error {
  background: var(--danger-soft);
  border: 1px solid var(--danger-soft);
  color: var(--danger-ink);
  padding: var(--space-4);
  border-radius: var(--radius-sm);
  margin: var(--space-4) 0;
  font-size: 0.938rem;
}

/* ── Responsive: auto icons-only below 1024px ───────────────── */

@media (max-width: 1024px) {
  .app,
  .app.collapsed {
    grid-template-columns: var(--sidebar-w-collapsed) 1fr;
  }

  .sidebar {
    padding: var(--space-5) var(--space-2);
    overflow: visible;
  }

  .sidebar-brand {
    justify-content: center;
    gap: var(--space-1);
  }

  .brand-inner {
    flex: none;
  }

  .brand-text {
    display: none;
  }

  /* Hide toggle — expanding is not available below this breakpoint */
  .sidebar-toggle {
    display: none;
  }

  .sidebar-nav a {
    justify-content: center;
    padding: .6rem 0;
    gap: 0;
  }

  .nav-label {
    display: none;
  }

  .sidebar-footer {
    align-items: center;
  }
}
</style>
