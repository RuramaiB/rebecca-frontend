<template>
  <NuxtLayout name="admin-layout">
    <div class="min-h-screen bg-gray-950">
      <div class="relative overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-gray-900/50 to-gray-950 z-0"></div>
        <div class="relative z-10 px-8 py-12">
          <div class="max-w-7xl mx-auto">
            <div class="flex items-center justify-between mb-8">
              <div>
                <h1 class="text-3xl font-bold text-white mb-2">Welcome back, Admin</h1>
                <p class="text-gray-400">Monitor and manage musician tax compliance in real-time</p>
              </div>
              <div class="flex items-center space-x-4">
                <div class="text-right">
                  <p class="text-sm text-gray-400">Current Period</p>
                  <p class="text-lg font-semibold text-white">{{ currentPeriod }}</p>
                </div>
                <button
                  @click="refreshAllData"
                  :disabled="isRefreshing"
                  class="bg-red-600 hover:bg-red-700 text-white px-6 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2 disabled:opacity-50"
                >
                  <svg 
                    class="w-4 h-4" 
                    :class="{ 'animate-spin': isRefreshing }"
                    fill="none" 
                    stroke="currentColor" 
                    viewBox="0 0 24 24"
                  >
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                      d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                  </svg>
                  <span>{{ isRefreshing ? 'Refreshing...' : 'Refresh Data' }}</span>
                </button>
              </div>
            </div>

            <!-- Loading State -->
            <div v-if="loadingData" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center mb-8">
              <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-gray-700 border-t-red-600"></div>
              <p class="text-gray-400 mt-4">Loading dashboard data...</p>
            </div>

            <!-- Error State -->
            <div v-else-if="loadingError" class="bg-red-500/10 border border-red-500/50 rounded-xl p-6 text-center mb-8">
              <svg class="w-12 h-12 text-red-400 mx-auto mb-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
              <p class="text-red-400 font-medium">Failed to load dashboard data</p>
              <p class="text-red-300 text-sm mt-1">{{ loadingError }}</p>
              <button
                @click="refreshAllData"
                class="mt-4 bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm transition"
              >
                Try Again
              </button>
            </div>

            <template v-else>
              <!-- Stats Grid -->
              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-8">
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-blue-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-blue-500/10 text-blue-400">
                      Active
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Total Artists</div>
                  <div class="text-2xl font-bold text-white">{{ stats.totalArtists }}</div>
                  <div class="text-xs text-gray-500 mt-2">Registered musicians</div>
                </div>

                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-green-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-green-500/10 text-green-400">
                      Total
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Total Gross Revenue</div>
                  <div class="text-2xl font-bold text-white">${{ formatNumber(stats.totalRevenue) }}</div>
                  <div class="text-xs text-gray-500 mt-2">All-time revenue</div>
                </div>



                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-red-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-red-500/10 text-red-400">
                      Alert
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Non-Compliant Artists</div>
                  <div class="text-2xl font-bold text-red-400">{{ stats.nonCompliant }}</div>
                  <div class="text-xs text-gray-500 mt-2">Require attention</div>
                </div>

                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-purple-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 7h6m0 10v-3m-3 3h.01M9 17h.01M9 14h.01M12 14h.01M15 11h.01M12 11h.01M9 11h.01M7 21h10a2 2 0 002-2V5a2 2 0 00-2-2H7a2 2 0 00-2 2v14a2 2 0 002 2z"></path>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-purple-500/10 text-purple-400">
                      Liability
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Amount Due</div>
                  <div class="text-2xl font-bold text-white">${{ formatNumber(stats.totalTaxDue) }}</div>
                  <div class="text-xs text-gray-500 mt-2">Total aggregate liability</div>
                </div>
              </div>

              <!-- Recent Activity & Charts -->
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                <!-- Compliance Chart -->
                <div class="lg:col-span-2 bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
                  <div class="flex items-center justify-between mb-6">
                    <h3 class="text-lg font-semibold text-white">Compliance Overview</h3>
                    <div class="text-sm text-gray-400">
                      Last updated: {{ formatDate(new Date()) }}
                    </div>
                  </div>
                  <div class="h-64 flex items-center justify-center">
                    <div class="relative">
                      <svg class="w-48 h-48 transform -rotate-90">
                        <!-- Background circle -->
                        <circle
                          cx="96"
                          cy="96"
                          r="80"
                          stroke="#1f2937"
                          stroke-width="16"
                          fill="none"
                        />
                        <!-- Non-compliant base (red) -->
                        <circle
                          cx="96"
                          cy="96"
                          r="80"
                          stroke="#ef4444"
                          stroke-width="16"
                          fill="none"
                          stroke-dasharray="502.6 502.6"
                        />
                        <!-- Compliant arc (green) -->
                        <circle
                          cx="96"
                          cy="96"
                          r="80"
                          stroke="#10b981"
                          stroke-width="16"
                          fill="none"
                          :stroke-dasharray="`${compliancePercentage * 5.026} 502.6`"
                          class="transition-all duration-1000"
                        />
                      </svg>
                      <div class="absolute inset-0 flex items-center justify-center">
                        <div class="text-center">
                          <div class="text-3xl font-bold text-white">{{ compliancePercentage }}%</div>
                          <div class="text-sm text-gray-400">Compliance Rate</div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="flex items-center justify-center space-x-6 mt-6">
                    <div class="flex items-center space-x-2">
                      <div class="w-3 h-3 rounded-full bg-green-500"></div>
                      <span class="text-sm text-gray-400">Compliant: {{ stats.compliant }}</span>
                    </div>
                    <div class="flex items-center space-x-2">
                      <div class="w-3 h-3 rounded-full bg-red-500"></div>
                      <span class="text-sm text-gray-400">Non-Compliant: {{ stats.nonCompliant }}</span>
                    </div>
                  </div>
                </div>

                <!-- Quick Stats -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
                  <h3 class="text-lg font-semibold text-white mb-6">Quick Stats</h3>
                  <div class="space-y-4">
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Total Tax Records</p>
                      <p class="text-2xl font-bold text-white">{{ taxRecordsCount }}</p>
                    </div>
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Outstanding Tax</p>
                      <p class="text-2xl font-bold text-yellow-400">${{ formatNumber(stats.outstandingTax) }}</p>
                    </div>
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Average Tax Rate</p>
                      <p class="text-2xl font-bold text-blue-400">{{ averageTaxRate }}%</p>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Top Artists -->
              <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
                <div class="flex items-center justify-between mb-6">
                  <h3 class="text-lg font-semibold text-white">Top Performing Artists</h3>
                  <NuxtLink to="/admin/artists" class="text-sm text-gray-400 hover:text-white transition-colors">
                    View All
                  </NuxtLink>
                </div>
                
                <div v-if="topArtists.length === 0" class="text-center py-8">
                  <p class="text-gray-500">No artist data available</p>
                </div>
                
                <div v-else class="overflow-x-auto">
                  <table class="w-full">
                    <thead>
                      <tr class="text-left text-sm text-gray-500 border-b border-gray-800">
                        <th class="pb-3 font-medium">Artist</th>
                        <th class="pb-3 font-medium">Revenue</th>
                        <th class="pb-3 font-medium">Tax Due</th>
                        <th class="pb-3 font-medium">Status</th>
                        <th class="pb-3 font-medium">Compliance Level</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="artist in topArtists" :key="artist.artistId" class="border-b border-gray-800/50 last:border-0">
                        <td class="py-4">
                          <div class="flex items-center space-x-3">
                            <div class="w-8 h-8 bg-gray-700 rounded-full flex items-center justify-center">
                              <span class="text-xs font-medium text-gray-300">{{ getInitials(artist.artistName) }}</span>
                            </div>
                            <div>
                              <p class="text-sm font-medium text-white">{{ artist.artistName }}</p>
                              <p class="text-xs text-gray-500">{{ artist.email }}</p>
                            </div>
                          </div>
                        </td>
                        <td class="py-4">
                          <p class="text-sm text-white">${{ formatNumber(artist.totalRevenueToDate) }}</p>
                        </td>
                        <td class="py-4">
                          <p class="text-sm text-white">${{ formatNumber(artist.totalTaxDue) }}</p>
                        </td>
                        <td class="py-4">
                          <span :class="[
                            'px-2 py-1 text-xs font-medium rounded-full',
                            artist.taxCompliant ? 'bg-green-500/10 text-green-400' : 'bg-red-500/10 text-red-400'
                          ]">
                            {{ artist.taxCompliant ? 'Compliant' : 'Non-Compliant' }}
                          </span>
                        </td>
                        <td class="py-4">
                          <span :class="getComplianceLevelClass(artist.complianceLevel)" class="px-2 py-1 text-xs font-medium rounded-full">
                            {{ artist.complianceLevel || 'N/A' }}
                          </span>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>
            </template>
          </div>
        </div>
      </div>
    </div>
  </NuxtLayout>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const loadingData = ref(true)
const loadingError = ref(null)
const isRefreshing = ref(false)

// Fetch compliance data
const { data: complianceData, pending: compliancePending, error: complianceError, refresh: refreshCompliance } = 
  await useFetch('http://localhost:8080/api/compliance/initialize-all-compliance', {
    method: 'POST',
    headers: { 'accept': '*/*' }
  })

// Fetch tax records
const { data: taxRecordsData, pending: taxPending, error: taxError, refresh: refreshTax } = 
  await useFetch('http://localhost:8080/api/tax/get-all-tax-records')

// Fetch artists
const { data: artistsData, pending: artistsPending, error: artistsError, refresh: refreshArtists } = 
  await useFetch('http://localhost:8080/api/artists/get-all-artists')

// Extract data from API responses
const complianceRecords = computed(() => Array.isArray(complianceData.value) ? complianceData.value : [])
const taxRecords = computed(() => taxRecordsData.value?.records || [])
const artists = computed(() => artistsData.value?.artists || [])

// Current period
const currentPeriod = computed(() => {
  const now = new Date()
  return now.toLocaleDateString('en-US', { year: 'numeric', month: 'long' })
})

// Calculate statistics
const stats = computed(() => {
  const totalArtists = complianceRecords.value.length
  const compliant = complianceRecords.value.filter(a => a.taxCompliant).length
  const nonCompliant = totalArtists - compliant
  
  const totalRevenue = complianceRecords.value.reduce((sum, artist) => 
    sum + (artist.totalRevenueToDate || 0), 0
  )
  
  const totalTaxCollected = complianceRecords.value.reduce((sum, artist) => 
    sum + (artist.totalTaxPaid || 0), 0
  )
  
  const outstandingTax = complianceRecords.value.reduce((sum, artist) => 
    sum + (artist.outstandingTax || 0), 0
  )

  return {
    totalArtists,
    compliant,
    nonCompliant,
    totalRevenue,
    totalTaxCollected,
    outstandingTax,
    totalTaxDue: totalTaxCollected + outstandingTax
  }
})

// Compliance percentage
const compliancePercentage = computed(() => {
  if (stats.value.totalArtists === 0) return 0
  return Math.round((stats.value.compliant / stats.value.totalArtists) * 100)
})

// Tax records count
const taxRecordsCount = computed(() => taxRecords.value.length)

// Average tax rate
const averageTaxRate = computed(() => {
  if (stats.value.totalRevenue === 0) return 0
  return ((stats.value.totalTaxCollected / stats.value.totalRevenue) * 100).toFixed(1)
})

// Top artists (sorted by revenue, limit to 5)
const topArtists = computed(() => {
  return [...complianceRecords.value]
    .sort((a, b) => (b.totalRevenueToDate || 0) - (a.totalRevenueToDate || 0))
    .slice(0, 5)
    .map(artist => ({
      ...artist,
      totalTaxDue: (artist.totalTaxPaid || 0) + (artist.outstandingTax || 0)
    }))
})

// Get initials from name
const getInitials = (name) => {
  if (!name) return '??'
  return name.split(' ').map(n => n[0]).join('').toUpperCase().slice(0, 2)
}

// Format number with commas
const formatNumber = (num) => {
  if (!num && num !== 0) return '0'
  return num.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}

// Format date
const formatDate = (date) => {
  return new Date(date).toLocaleString('en-US', {
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

// Refresh all data
const refreshAllData = async () => {
  isRefreshing.value = true
  loadingError.value = null
  
  try {
    await Promise.all([
      refreshCompliance(),
      refreshTax(),
      refreshArtists()
    ])
  } catch (err) {
    loadingError.value = err.message || 'Failed to refresh data'
  } finally {
    isRefreshing.value = false
  }
}

// Check loading state
watchEffect(() => {
  loadingData.value = compliancePending.value || taxPending.value || artistsPending.value
  
  if (complianceError.value || taxError.value || artistsError.value) {
    loadingError.value = complianceError.value?.message || 
                         taxError.value?.message || 
                         artistsError.value?.message || 
                         'Failed to load data'
  }
})
</script>

<style scoped>
/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #1f2937;
}

::-webkit-scrollbar-thumb {
  background: #4b5563;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #6b7280;
}
</style>