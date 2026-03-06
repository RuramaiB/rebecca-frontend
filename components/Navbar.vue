<template>
  <nav class="bg-white border-b border-gray-200 fixed w-full z-10 top-0">
    <div class="px-4 py-3 flex items-center justify-between">
      <div class="flex items-center gap-4">
        <button @click="$emit('toggleSidebar')" class="p-2 hover:bg-gray-100 rounded-lg">
          <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16"/>
          </svg>
        </button>
        <h1 class="text-xl font-semibold text-gray-900">Music Tax Admin</h1>
      </div>
      <div class="flex items-center gap-4">
        <div class="w-9 h-9 bg-red-600 rounded-full flex items-center justify-center text-white font-medium">
          A
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
defineEmits(['toggleSidebar'])
</script>
