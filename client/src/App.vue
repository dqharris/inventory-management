<template>
  <div class="app-shell">
    <aside class="sidebar" :class="{ collapsed: sidebarCollapsed }" :aria-expanded="!sidebarCollapsed">
      <div class="sidebar-logo">
        <div class="logo-mark"></div>
        <div class="logo-text">
          <span class="logo-name">{{ t('nav.companyName') }}</span>
          <span class="logo-sub">{{ t('nav.subtitle') }}</span>
        </div>
      </div>

      <button class="sidebar-toggle" @click="toggleSidebar" :title="sidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'">
        <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
          <!-- chevrons-left icon when expanded, chevrons-right when collapsed -->
          <template v-if="!sidebarCollapsed">
            <polyline points="11 17 6 12 11 7"/><polyline points="18 17 13 12 18 7"/>
          </template>
          <template v-else>
            <polyline points="13 17 18 12 13 7"/><polyline points="6 17 11 12 6 7"/>
          </template>
        </svg>
      </button>

      <nav class="sidebar-nav">
        <router-link to="/" class="nav-item" :class="{ active: $route.path === '/' }" :title="sidebarCollapsed ? t('nav.overview') : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <rect x="3" y="3" width="7" height="7"/><rect x="14" y="3" width="7" height="7"/><rect x="3" y="14" width="7" height="7"/><rect x="14" y="14" width="7" height="7"/>
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <router-link to="/inventory" class="nav-item" :class="{ active: $route.path === '/inventory' }" :title="sidebarCollapsed ? t('nav.inventory') : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"/><polyline points="3.27 6.96 12 12.01 20.73 6.96"/><line x1="12" y1="22.08" x2="12" y2="12"/>
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link to="/orders" class="nav-item" :class="{ active: $route.path === '/orders' }" :title="sidebarCollapsed ? t('nav.orders') : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <path d="M16 4h2a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2H6a2 2 0 0 1-2-2V6a2 2 0 0 1 2-2h2"/><rect x="8" y="2" width="8" height="4" rx="1" ry="1"/><line x1="9" y1="12" x2="15" y2="12"/><line x1="9" y1="16" x2="15" y2="16"/>
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <router-link to="/spending" class="nav-item" :class="{ active: $route.path === '/spending' }" :title="sidebarCollapsed ? t('nav.finance') : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <rect x="1" y="4" width="22" height="16" rx="2" ry="2"/><line x1="1" y1="10" x2="23" y2="10"/>
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <div class="nav-section-label">ANALYZE</div>

        <router-link to="/demand" class="nav-item" :class="{ active: $route.path === '/demand' }" :title="sidebarCollapsed ? t('nav.demandForecast') : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/>
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link to="/reports" class="nav-item" :class="{ active: $route.path === '/reports' }" :title="sidebarCollapsed ? 'Reports' : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/>
          </svg>
          <span>Reports</span>
        </router-link>

        <router-link to="/restocking" class="nav-item" :class="{ active: $route.path === '/restocking' }" :title="sidebarCollapsed ? 'Restocking' : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="23 4 23 10 17 10"/><polyline points="1 20 1 14 7 14"/><path d="M3.51 9a9 9 0 0 1 14.85-3.36L23 10M1 14l4.64 4.36A9 9 0 0 0 20.49 15"/>
          </svg>
          <span>Restocking</span>
        </router-link>
      </nav>

      <div class="sidebar-bottom">
        <LanguageSwitcher />
        <button class="sidebar-tasks-btn" @click="showTasks = true" :title="sidebarCollapsed ? 'Tasks' : ''">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round">
            <polyline points="9 11 12 14 22 4"/><path d="M21 12v7a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h11"/>
          </svg>
          <span>Tasks</span>
        </button>
        <div class="sidebar-user" @click="showProfileDetails = true" :title="sidebarCollapsed ? 'John Doe — Operations Manager' : ''">
          <div class="user-avatar">JD</div>
          <div class="user-info">
            <span class="user-name">John Doe</span>
            <span class="user-role">Operations Manager</span>
          </div>
        </div>
      </div>
    </aside>

    <div class="content-area" :class="{ 'sidebar-collapsed': sidebarCollapsed }">
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
import { ref, onMounted, onUnmounted, computed } from 'vue'
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

    const sidebarCollapsed = ref(false)

    const toggleSidebar = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value
    }

    // Auto-collapse sidebar on screens narrower than 1024px
    const handleResize = () => {
      sidebarCollapsed.value = window.innerWidth < 1024
    }
    const apiTasks = ref([])

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

    onMounted(() => {
      handleResize() // Set initial state based on current viewport
      window.addEventListener('resize', handleResize)
      loadTasks()
    })

    // Clean up resize listener when the component is destroyed
    onUnmounted(() => {
      window.removeEventListener('resize', handleResize)
    })

    return {
      t,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask,
      sidebarCollapsed,
      toggleSidebar
    }
  }
}
</script>

<style>
/* vue-saas-redesign skill applied */
:root {
  --sidebar-width: 240px;
  --sidebar-bg: #0f172a;
  --sidebar-border: rgba(255, 255, 255, 0.06);
  --sidebar-text: #94a3b8;
  --sidebar-text-hover: #f1f5f9;
  --sidebar-text-active: #ffffff;
  --sidebar-hover-bg: rgba(255, 255, 255, 0.06);
  --sidebar-active-bg: rgba(99, 102, 241, 0.15);
  --accent: #6366f1;
  --accent-hover: #818cf8;
  --content-bg: #f1f5f9;
  --card-bg: #ffffff;
  --border: #e2e8f0;
  --text-primary: #0f172a;
  --text-secondary: #64748b;
  --radius: 10px;
  --shadow-sm: 0 1px 3px rgba(0, 0, 0, 0.06), 0 1px 2px rgba(0, 0, 0, 0.04);
  --shadow-md: 0 4px 16px rgba(0, 0, 0, 0.08);
}

*, *::before, *::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: var(--content-bg);
  color: var(--text-primary);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  font-size: 14px;
  line-height: 1.5;
}

/* ── Shell layout ── */
.app-shell {
  display: flex;
  min-height: 100vh;
}

/* ── Sidebar ── */
.sidebar {
  width: var(--sidebar-width);
  min-width: var(--sidebar-width);
  background: var(--sidebar-bg);
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  height: 100vh;
  overflow-y: auto;
  z-index: 100;
  /* Subtle right border */
  border-right: 1px solid rgba(255, 255, 255, 0.05);
}

/* Logo */
.sidebar-logo {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 20px 16px 18px;
  border-bottom: 1px solid var(--sidebar-border);
}

.logo-mark {
  width: 30px;
  height: 30px;
  background: var(--accent);
  border-radius: 8px;
  flex-shrink: 0;
  /* Subtle inner glow */
  box-shadow: 0 0 0 1px rgba(255, 255, 255, 0.1) inset;
}

.logo-text {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.logo-name {
  font-size: 13px;
  font-weight: 700;
  color: #f8fafc;
  letter-spacing: -0.01em;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.logo-sub {
  font-size: 10px;
  color: #475569;
  font-weight: 500;
  letter-spacing: 0.01em;
  margin-top: 1px;
}

/* Nav */
.sidebar-nav {
  padding: 12px 10px;
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.nav-section-label {
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 0.08em;
  color: #334155;
  padding: 12px 8px 4px;
  text-transform: uppercase;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 9px 10px;
  border-radius: 7px;
  color: var(--sidebar-text);
  text-decoration: none;
  font-size: 13.5px;
  font-weight: 500;
  transition: background 0.15s ease, color 0.15s ease;
  /* Left accent border placeholder — invisible by default */
  border-left: 2px solid transparent;
}

.nav-item svg {
  width: 16px;
  height: 16px;
  flex-shrink: 0;
  opacity: 0.7;
  transition: opacity 0.15s ease;
}

.nav-item:hover {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-hover);
}

.nav-item:hover svg {
  opacity: 1;
}

/* Active nav item: indigo tint background + left accent border + white text */
.nav-item.active {
  background: var(--sidebar-active-bg);
  color: var(--sidebar-text-active);
  border-left-color: var(--accent);
}

.nav-item.active svg {
  opacity: 1;
}

/* Bottom section */
.sidebar-bottom {
  padding: 12px 10px 16px;
  border-top: 1px solid var(--sidebar-border);
  display: flex;
  flex-direction: column;
  gap: 4px;
}

/* Tasks button */
.sidebar-tasks-btn {
  display: flex;
  align-items: center;
  gap: 10px;
  width: 100%;
  padding: 9px 10px;
  border-radius: 7px;
  background: transparent;
  border: none;
  color: var(--sidebar-text);
  font-size: 13.5px;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.15s ease, color 0.15s ease;
  text-align: left;
}

.sidebar-tasks-btn svg {
  width: 16px;
  height: 16px;
  flex-shrink: 0;
  opacity: 0.7;
}

.sidebar-tasks-btn:hover {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-hover);
}

/* User profile row */
.sidebar-user {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 10px;
  border-radius: 7px;
  cursor: pointer;
  transition: background 0.15s ease;
  margin-top: 4px;
}

.sidebar-user:hover {
  background: var(--sidebar-hover-bg);
}

.user-avatar {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  background: var(--accent);
  color: white;
  font-size: 11px;
  font-weight: 700;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  letter-spacing: 0.03em;
}

.user-info {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.user-name {
  font-size: 12.5px;
  font-weight: 600;
  color: #e2e8f0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.user-role {
  font-size: 11px;
  color: #475569;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* ── Content area ── */
.content-area {
  margin-left: var(--sidebar-width);
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
}

/* ── Main content ── */
.main-content {
  flex: 1;
  padding: 28px 32px;
  max-width: 1400px;
  width: 100%;
}

/* ── Global component styles ── */
.page-header {
  margin-bottom: 24px;
}

.page-header h2 {
  font-size: 1.625rem;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.025em;
  margin-bottom: 4px;
}

.page-header p {
  color: var(--text-secondary);
  font-size: 0.875rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap: 16px;
  margin-bottom: 20px;
}

.stat-card {
  background: var(--card-bg);
  padding: 20px;
  border-radius: var(--radius);
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
  transition: box-shadow 0.2s ease, border-color 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: var(--shadow-md);
}

.stat-label {
  color: var(--text-secondary);
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  margin-bottom: 10px;
}

.stat-value {
  font-size: 2rem;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.025em;
  line-height: 1.1;
}

.stat-card.warning .stat-value { color: #ea580c; }
.stat-card.success .stat-value { color: #059669; }
.stat-card.danger  .stat-value { color: #dc2626; }
.stat-card.info    .stat-value { color: var(--accent); }

.card {
  background: var(--card-bg);
  border-radius: var(--radius);
  padding: 20px 24px;
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
  margin-bottom: 20px;
  transition: box-shadow 0.2s ease;
}

.card:hover {
  box-shadow: var(--shadow-md);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
  padding-bottom: 14px;
  border-bottom: 1px solid var(--border);
}

.card-title {
  font-size: 15px;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.01em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f8fafc;
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}

th {
  text-align: left;
  padding: 10px 12px;
  font-weight: 700;
  color: var(--text-secondary);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.06em;
}

td {
  padding: 10px 12px;
  border-top: 1px solid #f1f5f9;
  color: #334155;
  font-size: 13.5px;
}

tbody tr {
  transition: background-color 0.12s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

/* Badges */
.badge {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 5px;
  font-size: 11px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

.badge.success   { background: #d1fae5; color: #065f46; }
.badge.warning   { background: #fed7aa; color: #92400e; }
.badge.danger    { background: #fecaca; color: #991b1b; }
.badge.info      { background: #e0e7ff; color: #3730a3; }
.badge.increasing { background: #d1fae5; color: #065f46; }
.badge.decreasing { background: #fecaca; color: #991b1b; }
.badge.stable    { background: #e0e7ff; color: #3730a3; }
.badge.high      { background: #fecaca; color: #991b1b; }
.badge.medium    { background: #fed7aa; color: #92400e; }
.badge.low       { background: #dbeafe; color: #1e40af; }

.loading {
  text-align: center;
  padding: 3rem;
  color: var(--text-secondary);
  font-size: 0.9rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem 1.25rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.875rem;
}

/* ── Collapsible sidebar ── */

/* Smooth width transition on the sidebar itself */
.sidebar {
  transition: width 0.22s ease;
}

/* Collapsed state: icon-only, 60px wide */
.sidebar.collapsed {
  width: 60px;
  min-width: 60px;
}

/* Content area shifts left when sidebar collapses */
.content-area {
  transition: margin-left 0.22s ease;
}
.content-area.sidebar-collapsed {
  margin-left: 60px;
}

/* Toggle button */
.sidebar-toggle {
  display: flex;
  align-items: center;
  justify-content: flex-end;  /* right-aligned when expanded */
  padding: 6px 10px 2px;
  background: transparent;
  border: none;
  cursor: pointer;
  width: 100%;
}

.sidebar-toggle svg {
  width: 15px;
  height: 15px;
  color: #334155;
  transition: color 0.15s ease;
}

.sidebar-toggle:hover svg {
  color: #94a3b8;
}

/* When collapsed: center the toggle icon */
.sidebar.collapsed .sidebar-toggle {
  justify-content: center;
  padding: 6px 0 2px;
}

/* Hide text labels and logo subtitle when collapsed */
.sidebar.collapsed .nav-item span,
.sidebar.collapsed .sidebar-tasks-btn span,
.sidebar.collapsed .logo-sub,
.sidebar.collapsed .logo-name,
.sidebar.collapsed .nav-section-label,
.sidebar.collapsed .user-info {
  display: none;
}

/* Center icons in nav items when collapsed */
.sidebar.collapsed .nav-item {
  justify-content: center;
  padding: 9px 0;
  border-left: 2px solid transparent;  /* keep border space */
}

.sidebar.collapsed .nav-item.active {
  border-left-color: var(--accent);
}

/* Center SVG icons at full opacity when collapsed */
.sidebar.collapsed .nav-item svg,
.sidebar.collapsed .sidebar-tasks-btn svg {
  opacity: 0.75;
  width: 18px;  /* slightly larger when labels are hidden */
  height: 18px;
}

/* Center tasks button when collapsed */
.sidebar.collapsed .sidebar-tasks-btn {
  justify-content: center;
  padding: 9px 0;
}

/* Center user avatar when collapsed */
.sidebar.collapsed .sidebar-user {
  justify-content: center;
  padding: 8px 0;
}

/* Logo area: show only the mark when collapsed */
.sidebar.collapsed .sidebar-logo {
  justify-content: center;
  padding: 18px 0;
}

/* Prevent text overflow during transition */
.sidebar .nav-item span,
.sidebar .sidebar-tasks-btn span,
.sidebar .logo-name,
.sidebar .logo-sub,
.sidebar .nav-section-label,
.sidebar .user-info {
  overflow: hidden;
  white-space: nowrap;
  transition: opacity 0.15s ease;
}

/* Fade out text slightly before it gets clipped during collapse */
.sidebar.collapsed .nav-item span,
.sidebar.collapsed .sidebar-tasks-btn span {
  opacity: 0;
}

/* ── Language switcher inside sidebar ──
   Override the component's default white-button styles to match the dark sidebar. */
.sidebar .language-switcher {
  width: 100%;
}

.sidebar .language-button {
  width: 100%;
  background: transparent;
  border: none;
  color: var(--sidebar-text);
  padding: 9px 10px;
  border-radius: 7px;
  font-size: 13.5px;
  font-weight: 500;
  justify-content: flex-start;
}

.sidebar .language-button:hover {
  background: var(--sidebar-hover-bg);
  color: var(--sidebar-text-hover);
  border-color: transparent;
}

/* Tint the globe icon and chevron to match sidebar text */
.sidebar .globe-icon,
.sidebar .chevron {
  color: currentColor;
  opacity: 0.7;
}

/* Open dropdown above and to the right so it doesn't clip into the sidebar edge */
.sidebar .dropdown-menu {
  left: calc(100% + 8px);
  right: auto;
  top: 0;
}

/* Collapsed: hide label and chevron, center the globe icon */
.sidebar.collapsed .language-button {
  justify-content: center;
  padding: 9px 0;
}

.sidebar.collapsed .language-label,
.sidebar.collapsed .chevron {
  display: none;
}

.sidebar.collapsed .globe-icon {
  width: 18px;
  height: 18px;
  opacity: 0.75;
}

/* Collapsed dropdown still opens to the right */
.sidebar.collapsed .dropdown-menu {
  left: calc(100% + 8px);
  top: 0;
}
</style>
