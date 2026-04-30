<template>
  <NuxtLayout name="artist-layout">
    <div class="min-h-screen bg-gray-950">
      <div class="relative overflow-hidden">
        <div class="absolute inset-0 bg-gradient-to-br from-gray-900/50 to-gray-950 z-0"></div>
        <div class="relative z-10 px-8 py-12">
          <div class="max-w-7xl mx-auto">
            <!-- Header -->
            <div class="flex items-center justify-between mb-8">
              <div>
                <h1 class="text-3xl font-bold text-white mb-2">Welcome back, {{ artistName }}</h1>
                <p class="text-gray-400">Your tax compliance dashboard and financial overview</p>
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
              <p class="text-gray-400 mt-4">Loading your dashboard...</p>
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
              <!-- Compliance Status Banner -->
              <div v-if="complianceData" :class="[
                'rounded-xl p-6 mb-8 border',
                complianceData.taxCompliant 
                  ? 'bg-green-500/10 border-green-500/20' 
                  : 'bg-red-500/10 border-red-500/20'
              ]">
                <div class="flex items-start space-x-4">
                  <div :class="[
                    'p-3 rounded-lg',
                    complianceData.taxCompliant ? 'bg-green-500/20' : 'bg-red-500/20'
                  ]">
                    <svg v-if="complianceData.taxCompliant" class="w-8 h-8 text-green-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                    </svg>
                    <svg v-else class="w-8 h-8 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/>
                    </svg>
                  </div>
                  <div class="flex-1">
                    <h3 :class="[
                      'text-lg font-semibold mb-1',
                      complianceData.taxCompliant ? 'text-green-400' : 'text-red-400'
                    ]">
                      {{ complianceData.taxCompliant ? 'Tax Compliant' : 'Action Required' }}
                    </h3>
                    <p :class="[
                      'text-sm',
                      complianceData.taxCompliant ? 'text-green-300/80' : 'text-red-300/80'
                    ]">
                      {{ complianceData.taxCompliant 
                        ? 'You are up to date with your tax obligations.' 
                        : 'You have outstanding tax obligations that require immediate attention.' 
                      }}
                    </p>
                    <div class="mt-3 flex items-center space-x-4">
                      <span :class="getComplianceLevelClass(complianceData.complianceLevel)" class="px-3 py-1 text-xs font-medium rounded-full">
                        {{ complianceData.complianceLevel || 'N/A' }}
                      </span>
                      <span class="text-xs text-gray-400">
                        Last updated: {{ formatDate(complianceData.lastTaxCalculation) }}
                      </span>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Financial Stats Grid -->
              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                <!-- Total Revenue -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-blue-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-blue-500/10 text-blue-400">
                      Total
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Total Revenue</div>
                  <div class="text-2xl font-bold text-white">${{ formatNumber(complianceData?.totalRevenueToDate || 0) }}</div>
                  <div class="text-xs text-gray-500 mt-2">All-time earnings</div>
                </div>

                <!-- Tax Paid -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-green-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-green-500/10 text-green-400">
                      Paid
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Total Tax Paid</div>
                  <div class="text-2xl font-bold text-white">${{ formatNumber(complianceData?.totalTaxPaid || 0) }}</div>
                  <div class="text-xs text-gray-500 mt-2">Tax contributions</div>
                </div>

                <!-- Outstanding Tax -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-red-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-red-500/10 text-red-400">
                      Due
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Outstanding Tax</div>
                  <div class="text-2xl font-bold text-red-400">${{ formatNumber(complianceData?.outstandingTax || 0) }}</div>
                  <div class="text-xs text-gray-500 mt-2">Amount due</div>
                </div>

                <!-- Total Tax Due (Liability) -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                  <div class="flex items-center justify-between mb-4">
                    <div class="p-2 bg-purple-500/10 rounded-lg">
                      <svg class="w-5 h-5 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 7h6m0 10v-3m-3 3h.01M9 17h.01M9 14h.01M12 14h.01M15 11h.01M12 11h.01M9 11h.01M7 21h10a2 2 0 002-2V5a2 2 0 00-2-2H7a2 2 0 00-2 2v14a2 2 0 002 2z"/>
                      </svg>
                    </div>
                    <span class="text-xs font-medium px-2 py-1 rounded-full bg-purple-500/10 text-purple-400">
                      Liability
                    </span>
                  </div>
                  <div class="text-sm text-gray-400 mb-1">Total Tax Due</div>
                  <div class="text-2xl font-bold text-white">${{ formatNumber(complianceData?.totalTaxDue || 0) }}</div>
                  <div class="text-xs text-gray-500 mt-2">Total tax liability</div>
                </div>
              </div>

              <!-- Charts and Quick Stats -->
              <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                <!-- Compliance Score -->
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
                        <circle cx="96" cy="96" r="80" stroke="#1f2937" stroke-width="16" fill="none"/>
                        <circle 
                          cx="96" cy="96" r="80" 
                          :stroke="complianceData?.taxCompliant ? '#10b981' : '#ef4444'"
                          stroke-width="16" fill="none"
                          :stroke-dasharray="`${complianceScore * 5.026} 502.6`"
                          class="transition-all duration-1000"
                        />
                      </svg>
                      <div class="absolute inset-0 flex items-center justify-center">
                        <div class="text-center">
                          <div class="text-3xl font-bold text-white">{{ complianceScore }}%</div>
                          <div class="text-sm text-gray-400">Compliance Score</div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="flex items-center justify-center space-x-8 mt-6">
                    <div class="flex items-center space-x-2">
                      <div :class="[
                        'w-3 h-3 rounded-full',
                        complianceData?.taxCompliant ? 'bg-green-500' : 'bg-red-500'
                      ]"></div>
                      <span class="text-sm text-gray-400">
                        Status: {{ complianceData?.taxCompliant ? 'Compliant' : 'Non-Compliant' }}
                      </span>
                    </div>
                    <div class="flex items-center space-x-2">
                      <div class="w-3 h-3 rounded-full bg-blue-500"></div>
                      <span class="text-sm text-gray-400">Streak: {{ complianceData?.consecutiveCompliantMonths || 0 }} months</span>
                    </div>
                  </div>
                </div>

                <!-- Quick Metrics -->
                <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
                  <h3 class="text-lg font-semibold text-white mb-6">Quick Metrics</h3>
                  <div class="space-y-4">
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Total Videos</p>
                      <p class="text-2xl font-bold text-white">{{ complianceData?.totalVideosToDate || 0 }}</p>
                    </div>
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Total Shots</p>
                      <p class="text-2xl font-bold text-red-400">{{ complianceData?.totalShotsToDate || 0 }}</p>
                    </div>
                    <div class="bg-gray-800/50 rounded-lg p-4">
                      <p class="text-xs text-gray-400 mb-1">Tax Rate</p>
                      <p class="text-2xl font-bold text-blue-400">{{ taxRate }}%</p>
                    </div>
                  </div>
                </div>
              </div>

              <!-- YouTube Channel Stats -->
              <div v-if="channelData" class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 mb-8">
                <div class="flex items-center justify-between mb-6">
                  <div class="flex items-center space-x-4">
                    <div class="p-3 bg-red-600/10 rounded-xl">
                      <svg class="w-6 h-6 text-red-600" fill="currentColor" viewBox="0 0 24 24">
                        <path d="M23.498 6.186a3.016 3.016 0 0 0-2.122-2.136C19.505 3.545 12 3.545 12 3.545s-7.505 0-9.377.505A3.017 3.017 0 0 0 .502 6.186C0 8.07 0 12 0 12s0 3.93.502 5.814a3.016 3.016 0 0 0 2.122 2.136c1.871.505 9.376.505 9.376.505s7.505 0 9.377-.505a3.015 3.015 0 0 0 2.122-2.136C24 15.93 24 12 24 12s0-3.93-.502-5.814zM9.545 15.568V8.432L15.818 12l-6.273 3.568z"/>
                      </svg>
                    </div>
                    <div>
                      <h3 class="text-lg font-semibold text-white">{{ channelData.title }}</h3>
                      <p class="text-sm text-gray-400">YouTube Channel Overview</p>
                    </div>
                  </div>
                  <div class="flex items-center space-x-6">
                    <div class="text-center">
                      <p class="text-xs text-gray-400 uppercase tracking-wider mb-1">Subscribers</p>
                      <p class="text-lg font-bold text-white">{{ formatCompactNumber(channelData.subscriberCount) }}</p>
                    </div>
                    <div class="text-center">
                      <p class="text-xs text-gray-400 uppercase tracking-wider mb-1">Total Views</p>
                      <p class="text-lg font-bold text-white">{{ formatCompactNumber(channelData.viewCount) }}</p>
                    </div>
                    <div class="text-center">
                      <p class="text-xs text-gray-400 uppercase tracking-wider mb-1">Videos</p>
                      <p class="text-lg font-bold text-white">{{ channelData.videoCount }}</p>
                    </div>
                  </div>
                </div>
                
                <p class="text-sm text-gray-400 line-clamp-2 mb-0">{{ channelData.description }}</p>
              </div>

              <!-- YouTube Videos List -->
              <div v-if="videosData && videosData.length > 0" class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 mb-8">
                <div class="flex items-center justify-between mb-6">
                  <h3 class="text-lg font-semibold text-white">Latest Content</h3>
                  <button @click="fetchYouTubeData" class="text-sm text-red-500 hover:text-red-400 transition-colors">
                    Sync Videos
                  </button>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                  <div v-for="video in videosData.slice(0, 6)" :key="video.videoId" class="group bg-gray-800/30 border border-gray-700/50 rounded-xl overflow-hidden hover:border-red-500/50 transition-all">
                    <div class="relative aspect-video">
                      <img :src="video.thumbnailUrl" :alt="video.title" class="w-full h-full object-cover">
                      <div class="absolute bottom-2 right-2 px-2 py-0.5 bg-black/80 rounded text-[10px] font-bold text-white">
                        {{ video.duration.replace('PT', '').replace('H', ':').replace('M', ':').replace('S', '') }}
                      </div>
                    </div>
                    <div class="p-4">
                      <h4 class="text-sm font-semibold text-white truncate mb-2 group-hover:text-red-400 transition-colors">{{ video.title }}</h4>
                      <div class="flex items-center justify-between">
                        <div class="flex items-center space-x-3 text-[11px] text-gray-400">
                          <span class="flex items-center">
                            <svg class="w-3 h-3 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/>
                              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z"/>
                            </svg>
                            {{ formatCompactNumber(video.viewCount) }}
                          </span>
                          <span class="flex items-center">
                            <svg class="w-3 h-3 mr-1" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14 10h4.704a1 1 0 01.94 1.347l-2.292 6.005A2 2 0 0115.462 19H8.538a2 2 0 01-1.89-1.348L4.356 11.65a1 1 0 01.94-1.347H10V5L14 10z"/>
                            </svg>
                            {{ formatCompactNumber(video.likeCount) }}
                          </span>
                        </div>
                        <span class="text-[10px] text-gray-500">{{ formatDate(video.publishedAt) }}</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Recent Payments -->
              <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
                <div class="flex items-center justify-between mb-6">
                  <h3 class="text-lg font-semibold text-white">Recent Payments</h3>
                  <NuxtLink to="/artist/payments" class="text-sm text-gray-400 hover:text-white transition-colors">
                    View All
                  </NuxtLink>
                </div>
                
                <div v-if="!paymentsData || paymentsData.length === 0" class="text-center py-8">
                  <svg class="w-12 h-12 text-gray-600 mx-auto mb-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"/>
                  </svg>
                  <p class="text-gray-500">No payment records available</p>
                </div>
                
                <div v-else class="overflow-x-auto">
                  <table class="w-full">
                    <thead>
                      <tr class="text-left text-sm text-gray-500 border-b border-gray-800">
                        <th class="pb-3 font-medium">Payment ID</th>
                        <th class="pb-3 font-medium">Amount</th>
                        <th class="pb-3 font-medium">Method</th>
                        <th class="pb-3 font-medium">Status</th>
                        <th class="pb-3 font-medium">Date</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="payment in recentPayments" :key="payment.paymentID" class="border-b border-gray-800/50 last:border-0">
                        <td class="py-4">
                          <div class="flex items-center space-x-2">
                            <span class="text-xs font-mono text-gray-400">{{ payment.paymentID.slice(0, 8) }}...</span>
                          </div>
                        </td>
                        <td class="py-4">
                          <p class="text-sm font-semibold text-white">${{ formatNumber(payment.amount) }}</p>
                        </td>
                        <td class="py-4">
                          <span class="px-2 py-1 text-xs font-medium rounded-full bg-blue-500/10 text-blue-400">
                            {{ payment.method }}
                          </span>
                        </td>
                        <td class="py-4">
                          <span :class="getStatusClass(payment.paymentStatus)">
                            {{ payment.paymentStatus }}
                          </span>
                        </td>
                        <td class="py-4">
                          <div>
                            <p class="text-sm text-white">{{ formatDate(payment.dateOfPayment) }}</p>
                            <p class="text-xs text-gray-500">{{ formatTime(payment.dateOfPayment) }}</p>
                          </div>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>
              </div>

              <!-- Alerts Section -->
              <div v-if="complianceData?.alerts && complianceData.alerts.length > 0" class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6 mt-6">
                <h3 class="text-lg font-semibold text-white mb-4">Active Alerts ({{ complianceData.alerts.length }})</h3>
                <div class="space-y-3">
                  <div v-for="(alert, index) in complianceData.alerts" :key="index" 
                       class="bg-yellow-500/10 border border-yellow-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                      <svg class="w-5 h-5 text-yellow-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"/>
                      </svg>
                      <p class="text-sm text-yellow-400">{{ alert }}</p>
                    </div>
                  </div>
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
import { ref, computed, onMounted, watchEffect } from 'vue'

const loadingData = ref(true)
const loadingError = ref(null)
const isRefreshing = ref(false)

// Get artist info from localStorage
const artistEmail = ref(null)
const artistId = ref(null)
const artistName = ref('Artist')
const channelData = ref(null)
const videosData = ref(null)
const loadingYouTube = ref(false)

onMounted(async () => {
  artistEmail.value = localStorage.getItem('artistEmail')
  artistId.value = localStorage.getItem('artistID')
  artistName.value = localStorage.getItem('artistName') || 'Artist'
  
  if (artistId.value) {
    await fetchYouTubeData()
  }
})

const fetchYouTubeData = async () => {
  if (!artistId.value) return
  loadingYouTube.value = true
  try {
    const channelRes = await $fetch(`http://localhost:8080/api/youtube/channel?artistId=${artistId.value}`)
    channelData.value = channelRes
    
    if (channelRes.channelId) {
      const videosRes = await $fetch(`http://localhost:8080/api/youtube/videos/${channelRes.channelId}?artistId=${artistId.value}`)
      videosData.value = videosRes.videos
    }
  } catch (err) {
    console.error('Failed to fetch YouTube data:', err)
  } finally {
    loadingYouTube.value = false
  }
}

// Fetch compliance data for this artist
const { data: complianceData, pending: compliancePending, error: complianceError, refresh: refreshCompliance } = 
  await useFetch(() => artistId.value ? `http://localhost:8080/api/compliance/${artistId.value}` : null, {
    method: 'GET',
    headers: { 'accept': '*/*' },
    immediate: false
  })

// Fetch payments for this artist
const { data: paymentsData, pending: paymentsPending, error: paymentsError, refresh: refreshPayments } = 
  await useFetch(() => artistId.value ? `http://localhost:8080/api/paynow/get-all-payments-by-artist/${artistId.value}` : null, {
    immediate: false
  })

// Current period
const currentPeriod = computed(() => {
  const now = new Date()
  return now.toLocaleDateString('en-US', { year: 'numeric', month: 'long' })
})

// Compliance score calculation
const complianceScore = computed(() => {
  if (!complianceData.value) return 0
  
  const totalRevenue = complianceData.value.totalRevenueToDate || 0
  const outstandingTax = complianceData.value.outstandingTax || 0
  
  if (totalRevenue === 0) return 100
  
  const paidPercentage = ((totalRevenue - outstandingTax) / totalRevenue) * 100
  return Math.round(Math.max(0, Math.min(100, paidPercentage)))
})

// Tax rate calculation
const taxRate = computed(() => {
  if (!complianceData.value) return 0
  const totalRevenue = complianceData.value.totalRevenueToDate || 0
  const totalTaxPaid = complianceData.value.totalTaxPaid || 0
  
  if (totalRevenue === 0) return 0
  return ((totalTaxPaid / totalRevenue) * 100).toFixed(1)
})

// Recent payments (last 5)
const recentPayments = computed(() => {
  if (!paymentsData.value) return []
  return [...paymentsData.value]
    .sort((a, b) => new Date(b.dateOfPayment) - new Date(a.dateOfPayment))
    .slice(0, 5)
})

// Format number with commas
const formatNumber = (num) => {
  if (!num && num !== 0) return '0'
  return num.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}

// Format date
const formatDate = (date) => {
  if (!date) return 'N/A'
  return new Date(date).toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })
}

// Format time
const formatTime = (date) => {
  if (!date) return ''
  return new Date(date).toLocaleTimeString('en-US', {
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

// Get status class for payments
const getStatusClass = (status) => {
  const base = 'px-2 py-1 text-xs font-medium rounded-full'
  switch (status) {
    case 'COMPLETED':
      return `${base} bg-green-500/10 text-green-400`
    case 'PENDING':
      return `${base} bg-yellow-500/10 text-yellow-400`
    case 'FAILED':
      return `${base} bg-red-500/10 text-red-400`
    default:
      return `${base} bg-gray-500/10 text-gray-400`
  }
}

// Format compact number (e.g. 1.2M)
const formatCompactNumber = (num) => {
  if (!num) return '0'
  return new Intl.NumberFormat('en-US', {
    notation: 'compact',
    maximumFractionDigits: 1
  }).format(num)
}

// Refresh all data
const refreshAllData = async () => {
  isRefreshing.value = true
  loadingError.value = null
  
  try {
    await Promise.all([
      refreshCompliance(),
      refreshPayments()
    ])
  } catch (err) {
    loadingError.value = err.message || 'Failed to refresh data'
  } finally {
    isRefreshing.value = false
  }
}

// Watch for artistId and fetch data
watchEffect(async () => {
  if (artistId.value && !complianceData.value) {
    await Promise.all([
      refreshCompliance(),
      refreshPayments()
    ])
  }
})

// Check loading state
watchEffect(() => {
  loadingData.value = compliancePending.value || paymentsPending.value
  
  if (complianceError.value || paymentsError.value) {
    loadingError.value = complianceError.value?.message || 
                         paymentsError.value?.message || 
                         'Failed to load data'
  }
})
</script>

<style scoped>
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