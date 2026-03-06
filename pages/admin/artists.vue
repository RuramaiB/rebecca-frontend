<template>
  <NuxtLayout name="admin-layout">
    <div class="space-y-8 p-6">

      <!-- Header -->
      <div class="flex items-center justify-between">
        <div>
          <h2 class="text-2xl font-bold text-white">Artists Management</h2>
          <p class="text-gray-400 text-sm">
            Manage all registered musicians and their tax profiles
          </p>
        </div>
      </div>

      <!-- Search / Filters -->
      <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
        <div class="flex flex-col md:flex-row gap-4 items-center justify-between">

          <div class="w-full md:flex-1 relative">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Search by name, email, or channel..."
              class="w-full bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-white placeholder-gray-500 focus:outline-none focus:ring-1 focus:ring-red-600"
            />
            <svg
              class="absolute right-3 top-3 w-4 h-4 text-gray-500"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
            </svg>
          </div>

          <div class="flex items-center gap-3">
            <select
              v-model="statusFilter"
              class="bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-gray-300"
            >
              <option value="all">All Status</option>
              <option value="compliant">Compliant</option>
              <option value="non-compliant">Non-Compliant</option>
            </select>

            <button
              @click="refreshData"
              class="bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-gray-300 hover:bg-gray-700 transition"
              :disabled="pending"
            >
              <svg
                class="w-4 h-4"
                :class="{ 'animate-spin': pending }"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
              >
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
              </svg>
            </button>
          </div>

        </div>
      </div>

      <!-- Loading State -->
      <div v-if="pending" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center">
        <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-gray-700 border-t-red-600"></div>
        <p class="text-gray-400 mt-4">Loading artists...</p>
      </div>

      <!-- Error State -->
      <div v-else-if="error" class="bg-red-500/10 border border-red-500/50 rounded-xl p-6 text-center">
        <svg class="w-12 h-12 text-red-400 mx-auto mb-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
            d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
        </svg>
        <p class="text-red-400 font-medium">Failed to load artists</p>
        <p class="text-red-300 text-sm mt-1">{{ error.message }}</p>
        <button
          @click="refreshData"
          class="mt-4 bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm transition"
        >
          Try Again
        </button>
      </div>

      <!-- Artists Table -->
      <div v-else class="bg-gray-900 border border-gray-800 rounded-xl overflow-hidden">
        <div class="overflow-x-auto">
          <table class="w-full text-sm">
            <thead class="bg-gray-800/50 text-gray-400 uppercase text-xs">
              <tr>
                <th class="px-6 py-3 text-left">Artist</th>
                <th class="px-6 py-3 text-left">Email</th>
                <th class="px-6 py-3 text-left">Channel</th>
                <!-- <th class="px-6 py-3 text-left">Join Date</th> -->
                <th class="px-6 py-3 text-left">Last Authorized</th>
                <th class="px-6 py-3 text-left">Status</th>
                <th class="px-6 py-3 text-left">Actions</th>
              </tr>
            </thead>

            <tbody class="divide-y divide-gray-800">
              <tr
                v-for="artist in paginatedArtists"
                :key="artist.id"
                class="hover:bg-gray-800/40 transition"
              >
                <td class="px-6 py-4">
                  <div class="flex items-center gap-3">
                    <div class="w-8 h-8 rounded-full bg-gray-700 flex items-center justify-center">
                      <span class="text-xs font-medium text-gray-200">
                        {{ getInitials(artist.name) }}
                      </span>
                    </div>
                    <span class="text-white font-medium">
                      {{ artist.name }}
                    </span>
                  </div>
                </td>

                <td class="px-6 py-4 text-gray-400">
                  {{ artist.email }}
                </td>

                <td class="px-6 py-4">
                  <div class="flex flex-col">
                    <span class="text-white text-xs">{{ artist.youtubeChannelTitle }}</span>
                    <span class="text-gray-500 text-xs truncate max-w-[200px]">{{ artist.youtubeChannelId }}</span>
                  </div>
                </td>


                <td class="px-6 py-4 text-gray-400 text-xs">
                  {{ formatDate(artist.lastSyncedAt || artist.authorizedAt) }}
                </td>

                <td class="px-6 py-4">
                  <span
                    :class="[
                      'px-2.5 py-1 text-xs rounded-full font-medium',
                      artist.taxCompliant
                        ? 'bg-green-500/10 text-green-400'
                        : 'bg-red-500/10 text-red-400'
                    ]"
                  >
                    {{ artist.taxCompliant ? 'COMPLIANT' : 'NON-COMPLIANT' }}
                  </span>
                </td>

                <td class="px-6 py-4">
                  <button 
                    @click="viewArtistDetails(artist)"
                    class="p-2 rounded-lg hover:bg-gray-800 text-gray-400 hover:text-white transition"
                    title="View Details"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                        d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                    </svg>
                  </button>
                </td>
              </tr>

              <tr v-if="filteredArtists.length === 0">
                <td colspan="7" class="text-center py-12">
                  <svg class="w-12 h-12 text-gray-600 mx-auto mb-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                      d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                  <p class="text-gray-500 font-medium">No artists found</p>
                  <p class="text-gray-600 text-sm mt-1">Try adjusting your search or filters</p>
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Pagination -->
        <div v-if="filteredArtists.length > 0" class="bg-gray-800/30 px-6 py-4 border-t border-gray-800 flex items-center justify-between">
          <div class="flex items-center gap-2">
            <p class="text-gray-400 text-sm">
              Showing <span class="text-white font-medium">{{ startItem }}</span> to 
              <span class="text-white font-medium">{{ endItem }}</span> of 
              <span class="text-white font-medium">{{ filteredArtists.length }}</span> artists
            </p>
          </div>

          <div class="flex items-center gap-2">
            <!-- Items per page selector -->
            <select
              v-model="itemsPerPage"
              class="bg-gray-800 border border-gray-700 rounded-lg px-3 py-1.5 text-sm text-gray-300"
            >
              <option :value="5">5 per page</option>
              <option :value="10">10 per page</option>
              <option :value="20">20 per page</option>
              <option :value="50">50 per page</option>
            </select>

            <!-- Pagination buttons -->
            <div class="flex gap-1">
              <button
                @click="currentPage = 1"
                :disabled="currentPage === 1"
                class="px-3 py-1.5 rounded-lg text-sm font-medium transition disabled:opacity-50 disabled:cursor-not-allowed"
                :class="currentPage === 1 ? 'bg-gray-800 text-gray-600' : 'bg-gray-800 text-gray-300 hover:bg-gray-700'"
              >
                First
              </button>

              <button
                @click="currentPage--"
                :disabled="currentPage === 1"
                class="px-3 py-1.5 rounded-lg text-sm font-medium transition disabled:opacity-50 disabled:cursor-not-allowed"
                :class="currentPage === 1 ? 'bg-gray-800 text-gray-600' : 'bg-gray-800 text-gray-300 hover:bg-gray-700'"
              >
                Previous
              </button>

              <div class="flex gap-1">
                <button
                  v-for="page in displayedPages"
                  :key="page"
                  @click="currentPage = page"
                  class="px-3 py-1.5 rounded-lg text-sm font-medium transition"
                  :class="currentPage === page 
                    ? 'bg-red-600 text-white' 
                    : 'bg-gray-800 text-gray-300 hover:bg-gray-700'"
                >
                  {{ page }}
                </button>
              </div>

              <button
                @click="currentPage++"
                :disabled="currentPage === totalPages"
                class="px-3 py-1.5 rounded-lg text-sm font-medium transition disabled:opacity-50 disabled:cursor-not-allowed"
                :class="currentPage === totalPages ? 'bg-gray-800 text-gray-600' : 'bg-gray-800 text-gray-300 hover:bg-gray-700'"
              >
                Next
              </button>

              <button
                @click="currentPage = totalPages"
                :disabled="currentPage === totalPages"
                class="px-3 py-1.5 rounded-lg text-sm font-medium transition disabled:opacity-50 disabled:cursor-not-allowed"
                :class="currentPage === totalPages ? 'bg-gray-800 text-gray-600' : 'bg-gray-800 text-gray-300 hover:bg-gray-700'"
              >
                Last
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>

    <!-- Artist Details Modal -->
    <div
      v-if="selectedArtist"
      class="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center p-4 z-50"
      @click.self="selectedArtist = null"
    >
      <div class="bg-gray-900 border border-gray-800 rounded-xl max-w-3xl w-full max-h-[90vh] overflow-y-auto">
        <!-- Modal Header -->
        <div class="flex items-center justify-between p-6 border-b border-gray-800">
          <div class="flex items-center gap-4">
            <div class="w-12 h-12 rounded-full bg-gray-700 flex items-center justify-center">
              <span class="text-lg font-medium text-gray-200">
                {{ getInitials(selectedArtist.name) }}
              </span>
            </div>
            <div>
              <h3 class="text-xl font-bold text-white">{{ selectedArtist.name }}</h3>
              <p class="text-gray-400 text-sm">{{ selectedArtist.email }}</p>
            </div>
          </div>
          <button
            @click="selectedArtist = null"
            class="p-2 rounded-lg hover:bg-gray-800 text-gray-400 hover:text-white transition"
          >
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <!-- Modal Content -->
        <div class="p-6 space-y-6">
          <!-- Account Information -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Account Information</h4>
            <div class="grid grid-cols-2 gap-4">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Artist ID</p>
                <p class="text-white font-mono text-sm">{{ selectedArtist.id }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Join Date</p>
                <p class="text-white text-sm">{{ formatDate(selectedArtist.createdAt) }}</p>
              </div>
            </div>
          </div>

          <!-- YouTube Information -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">YouTube Channel</h4>
            <div class="space-y-3">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Channel Title</p>
                <p class="text-white font-medium">{{ selectedArtist.youtubeChannelTitle }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Channel ID</p>
                <p class="text-white font-mono text-sm break-all">{{ selectedArtist.youtubeChannelId }}</p>
              </div>
              <div class="grid grid-cols-2 gap-4">
                <div class="bg-gray-800/50 rounded-lg p-4">
                  <p class="text-gray-400 text-xs mb-1">YouTube Authorized</p>
                  <span
                    :class="[
                      'inline-block px-2.5 py-1 text-xs rounded-full font-medium',
                      selectedArtist.youtubeAuthorized
                        ? 'bg-green-500/10 text-green-400'
                        : 'bg-gray-500/10 text-gray-400'
                    ]"
                  >
                    {{ selectedArtist.youtubeAuthorized ? 'YES' : 'NO' }}
                  </span>
                </div>
                <div class="bg-gray-800/50 rounded-lg p-4">
                  <p class="text-gray-400 text-xs mb-1">Analytics Authorized</p>
                  <span
                    :class="[
                      'inline-block px-2.5 py-1 text-xs rounded-full font-medium',
                      selectedArtist.analyticsAuthorized
                        ? 'bg-blue-500/10 text-blue-400'
                        : 'bg-gray-500/10 text-gray-400'
                    ]"
                  >
                    {{ selectedArtist.analyticsAuthorized ? 'YES' : 'NO' }}
                  </span>
                </div>
              </div>
            </div>
          </div>

          <!-- YouTube Metrics -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">YouTube Content</h4>
            <div class="grid grid-cols-2 gap-4">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Total Videos</p>
                <p class="text-white font-bold text-xl">{{ selectedArtist.totalVideos || 0 }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4 font-mono">
                <p class="text-gray-400 text-xs mb-1">Total Shots</p>
                <p class="text-red-400 font-bold text-xl">{{ selectedArtist.totalShots || 0 }}</p>
              </div>
            </div>
          </div>

          <!-- Authorization Timestamps -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Authorization History</h4>
            <div class="grid grid-cols-2 gap-4">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Authorized At</p>
                <p class="text-white text-sm">{{ formatDate(selectedArtist.authorizedAt) }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Last Synced At</p>
                <p class="text-white text-sm">{{ formatDate(selectedArtist.lastSyncedAt) }}</p>
              </div>
            </div>
          </div>

          <!-- Tax Compliance -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3 text-red-500">Tax Compliance & DST</h4>
            <div class="space-y-3">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <div class="flex items-center justify-between">
                  <div>
                    <p class="text-gray-400 text-xs mb-1">DST Registration</p>
                    <p class="text-white text-sm">Digital Service Tax Status</p>
                  </div>
                  <span
                    :class="[
                      'px-3 py-1.5 text-sm rounded-full font-medium',
                      selectedArtist.registeredForDigitalTax
                        ? 'bg-blue-500/10 text-blue-400'
                        : 'bg-gray-500/10 text-gray-400'
                    ]"
                  >
                    {{ selectedArtist.registeredForDigitalTax ? 'REGISTERED' : 'PENDING' }}
                  </span>
                </div>
              </div>

              <div class="bg-gray-800/50 rounded-lg p-4">
                <div class="flex items-center justify-between">
                  <div>
                    <p class="text-gray-400 text-xs mb-1">Compliance Status</p>
                    <p class="text-white text-sm">Overall tax standing</p>
                  </div>
                  <span
                    :class="[
                      'px-3 py-1.5 text-sm rounded-full font-medium',
                      selectedArtist.taxCompliant
                        ? 'bg-green-500/10 text-green-400'
                        : 'bg-red-500/10 text-red-400'
                    ]"
                  >
                    {{ selectedArtist.taxCompliant ? 'COMPLIANT' : 'NON-COMPLIANT' }}
                  </span>
                </div>
              </div>

              <div class="bg-gray-800/50 rounded-lg p-4">
                <div class="flex items-center justify-between">
                  <div>
                    <p class="text-gray-400 text-xs mb-1">Compliance Level</p>
                    <p class="text-white text-sm">Historical rating</p>
                  </div>
                  <span :class="getComplianceLevelClass(selectedArtist.complianceLevel)" class="px-3 py-1.5 text-sm rounded-full font-medium">
                    {{ selectedArtist.complianceLevel || 'N/A' }}
                  </span>
                </div>
              </div>
            </div>
          </div>

          <!-- Outstanding Tax -->
          <div v-if="selectedArtist.outstandingTax !== undefined">
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Financial Details</h4>
            <div class="bg-gray-800/50 rounded-lg p-4">
              <p class="text-gray-400 text-xs mb-1">Outstanding Tax Amount</p>
              <p class="text-white text-2xl font-bold">${{ selectedArtist.outstandingTax }}</p>
            </div>
          </div>
        </div>

        <!-- Modal Footer -->
        <div class="p-6 border-t border-gray-800 flex justify-end gap-3">
          <button
            @click="selectedArtist = null"
            class="px-4 py-2 bg-gray-800 hover:bg-gray-700 text-white rounded-lg text-sm font-medium transition"
          >
            Close
          </button>
        </div>
      </div>
    </div>

  </NuxtLayout>
</template>

<script setup>
import { ref, computed } from 'vue'

const searchQuery = ref('')
const statusFilter = ref('all')
const currentPage = ref(1)
const itemsPerPage = ref(10)
const selectedArtist = ref(null)

// Fetch artists from API
const { data: apiData, pending, error, refresh } = await useFetch('http://localhost:8080/api/artists/get-all-artists')

// Extract artists array from API response
const artists = computed(() => apiData.value?.artists || [])

// Filtered artists based on search and status
const filteredArtists = computed(() => {
  return artists.value.filter(a => {
    const matchesSearch =
      a.name?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      a.email?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      a.youtubeChannelTitle?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      a.youtubeChannelId?.toLowerCase().includes(searchQuery.value.toLowerCase())

    const matchesStatus =
      statusFilter.value === 'all' ||
      (statusFilter.value === 'compliant' && !a.taxCompliant) ||
      (statusFilter.value === 'non-compliant' && a.taxCompliant)

    return matchesSearch && matchesStatus
  })
})

// Pagination calculations
const totalPages = computed(() => Math.ceil(filteredArtists.value.length / itemsPerPage.value))

const paginatedArtists = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value
  const end = start + itemsPerPage.value
  return filteredArtists.value.slice(start, end)
})

const startItem = computed(() => {
  return filteredArtists.value.length === 0 ? 0 : (currentPage.value - 1) * itemsPerPage.value + 1
})

const endItem = computed(() => {
  return Math.min(currentPage.value * itemsPerPage.value, filteredArtists.value.length)
})

const displayedPages = computed(() => {
  const pages = []
  const maxPagesToShow = 5
  let startPage = Math.max(1, currentPage.value - Math.floor(maxPagesToShow / 2))
  let endPage = Math.min(totalPages.value, startPage + maxPagesToShow - 1)

  if (endPage - startPage + 1 < maxPagesToShow) {
    startPage = Math.max(1, endPage - maxPagesToShow + 1)
  }

  for (let i = startPage; i <= endPage; i++) {
    pages.push(i)
  }

  return pages
})

// Reset to page 1 when filters change
watch([searchQuery, statusFilter, itemsPerPage], () => {
  currentPage.value = 1
})

// Get initials from name
const getInitials = (name = '') =>
  name
    .split(' ')
    .map(n => n[0])
    .join('')
    .slice(0, 2)
    .toUpperCase()

// Format date
const formatDate = (dateString) => {
  if (!dateString) return 'N/A'
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

// Get compliance level class
const getComplianceLevelClass = (level) => {
  const classes = {
    'EXCELLENT': 'bg-green-500/10 text-green-400',
    'GOOD': 'bg-blue-500/10 text-blue-400',
    'FAIR': 'bg-yellow-500/10 text-yellow-400',
    'POOR': 'bg-orange-500/10 text-orange-400',
    'CRITICAL': 'bg-red-500/10 text-red-400'
  }
  return classes[level] || 'bg-gray-500/10 text-gray-400'
}

// View artist details
const viewArtistDetails = (artist) => {
  selectedArtist.value = artist
}

// Refresh data
const refreshData = () => {
  refresh()
}
</script>