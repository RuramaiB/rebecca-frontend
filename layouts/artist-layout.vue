<template>
  <div class="min-h-screen bg-gray-950">
    <!-- NAVBAR -->
    <nav class="fixed top-0 inset-x-0 h-16 bg-gray-900 border-b border-gray-800 z-50">
      <div class="h-full px-6 flex items-center justify-between">
        <div class="flex items-center space-x-4">
          <button
            @click="sidebarOpen = !sidebarOpen"
            class="p-2 rounded-lg hover:bg-gray-800"
          >
            <svg class="w-5 h-5 text-gray-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M4 6h16M4 12h16M4 18h16" />
            </svg>
          </button>

          <div>
            <h1 class="text-xl font-bold text-white">
              MTax<span class="text-red-500">Studio</span>
            </h1>
            <p class="text-xs text-gray-400">Zimbabwe Musician Tax System</p>
          </div>
        </div>

        <div class="flex items-center space-x-6">
          <div class="text-right">
            <p class="text-sm font-medium text-white">J-Doe</p>
            <p class="text-xs text-gray-400">Artist</p>
          </div>
          <div class="w-8 h-8 rounded-full bg-gray-700 flex items-center justify-center text-gray-300">
            JD
          </div>
        </div>
      </div>
    </nav>

    <!-- SIDEBAR -->
    <aside
      :class="[
        'fixed top-16 left-0 h-[calc(100vh-4rem)] bg-gray-900 border-r border-gray-800 transition-all duration-300 z-40',
        sidebarOpen ? 'w-64' : 'w-0 overflow-hidden'
      ]"
    >
      <div class="p-4 space-y-1">
        <NuxtLink
          v-for="item in navItems"
          :key="item.path"
          :to="item.path"
          :class="[
            'flex items-center space-x-3 px-4 py-3 rounded-lg text-sm font-medium transition-colors',
            isActive(item.path)
              ? 'bg-red-600/20 text-red-400 border-l-2 border-red-500'
              : 'text-gray-400 hover:text-white hover:bg-gray-800'
          ]"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" :d="item.icon" />
          </svg>
          <span>{{ item.label }}</span>
        </NuxtLink>

        <!-- LOGOUT -->
        <div class="pt-6 mt-6 border-t border-gray-800">
          <NuxtLink
            to="/"
            class="flex items-center space-x-3 px-4 py-3 text-sm text-gray-400 hover:text-white hover:bg-gray-800 rounded-lg"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M17 16l4-4m0 0l-4-4m4 4H9" />
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M13 4H6a2 2 0 00-2 2v12a2 2 0 002 2h7" />
            </svg>
            <span>Logout</span>
          </NuxtLink>
        </div>
      </div>
    </aside>

    <!-- MAIN CONTENT -->
    <main
      :class="[
        'pt-16 transition-all duration-300 min-h-screen',
        sidebarOpen ? 'ml-64' : 'ml-0'
      ]"
    >
      <div class="max-w-7xl mx-auto p-8">
        <slot />
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRoute } from 'vue-router'

const sidebarOpen = ref(true)
const route = useRoute()

const navItems = [
    { id: 'dashboard', path: '/artist/', label: 'Dashboard', icon: 'M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6' },
    { id: 'tax-records', path: '/artist/taxes', label: 'Tax Records', icon: 'M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z' },
    { id: 'videos', path: '/artist/videos', label: 'My Videos', icon: 'M15 10l4.553-2.069A1 1 0 0121 8.871v6.258a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z' },
    { id: 'compliance', path: '/artist/compliance', label: 'Compliance', icon: 'M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z' },
    { id: 'payments', path: '/artist/payments', label: 'Payments', icon: 'M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z' },
    { id: 'profile', path: '/artist/profile', label: 'Settings', icon: 'M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z' },
]

const isActive = (path) => {
  // Dashboard should ONLY be active on exact match
  if (path === '/artist/') {
    return route.path === '/artist/' || route.path === '/artist'
  }

  // Other pages can use prefix matching
  return route.path.startsWith(path)
}

</script>
<style></style>