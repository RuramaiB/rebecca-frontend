<template>
  <aside :class="[
    'fixed left-0 top-14 h-full bg-white border-r border-gray-200 transition-all duration-300 z-10',
    isOpen ? 'w-64' : 'w-0 overflow-hidden'
  ]">
    <nav class="p-4">
      <a
        v-for="item in menuItems"
        :key="item.id"
        href="#"
        @click.prevent="$emit('selectSection', item.id)"
        :class="[
          'flex items-center gap-3 px-3 py-2 rounded-lg mb-1 transition-colors',
          activeSection === item.id
            ? 'bg-gray-100 text-gray-900 font-medium'
            : 'text-gray-700 hover:bg-gray-50'
        ]"
      >
        <component :is="item.icon" class="w-5 h-5" />
        <span>{{ item.label }}</span>
      </a>
    </nav>
  </aside>
</template>

<script setup>
import { h } from 'vue'

defineProps({
  isOpen: Boolean,
  activeSection: String
})

defineEmits(['selectSection'])

const menuItems = [
  { id: 'dashboard', label: 'Dashboard', icon: () => h('svg', { fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', 'stroke-width': '2', d: 'M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6' })
  ])},
  { id: 'artists', label: 'Artists', icon: () => h('svg', { fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', 'stroke-width': '2', d: 'M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z' })
  ])},
  { id: 'tax-records', label: 'Tax Records', icon: () => h('svg', { fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', 'stroke-width': '2', d: 'M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z' })
  ])},
  { id: 'compliance', label: 'Compliance', icon: () => h('svg', { fill: 'none', stroke: 'currentColor', viewBox: '0 0 24 24' }, [
    h('path', { 'stroke-linecap': 'round', 'stroke-linejoin': 'round', 'stroke-width': '2', d: 'M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z' })
  ])}
]
</script>
