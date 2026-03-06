<template>
  <NuxtLayout name="admin-layout">
    <div class="min-h-screen bg-gray-950">
      <div class="space-y-6 pt-6">
        <div class="flex items-center justify-between">
          <div>
            <h2 class="text-2xl font-bold text-white mb-2">Payment Records</h2>
            <p class="text-gray-400">View and manage all payment transactions</p>
          </div>
          <div class="flex items-center space-x-3">
            <button
              @click="refreshData"
              class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2"
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
              <span>Refresh</span>
            </button>
            <button
              @click="exportToCSV"
              class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2"
              :disabled="pending || payments.length === 0"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path>
              </svg>
              <span>Export CSV</span>
            </button>
          </div>
        </div>

        <!-- Summary Stats -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
          <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
            <div class="flex items-center justify-between">
              <div>
                <p class="text-sm text-gray-400 mb-1">Total Payments</p>
                <p class="text-2xl font-bold text-white">{{ payments.length }}</p>
              </div>
              <div class="w-12 h-12 bg-blue-500/10 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"/>
                </svg>
              </div>
            </div>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
            <div class="flex items-center justify-between">
              <div>
                <p class="text-sm text-gray-400 mb-1">Total Amount</p>
                <p class="text-2xl font-bold text-white">${{ formatNumber(totalAmount) }}</p>
              </div>
              <div class="w-12 h-12 bg-green-500/10 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                </svg>
              </div>
            </div>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
            <div class="flex items-center justify-between">
              <div>
                <p class="text-sm text-gray-400 mb-1">Pending</p>
                <p class="text-2xl font-bold text-yellow-400">{{ pendingCount }}</p>
              </div>
              <div class="w-12 h-12 bg-yellow-500/10 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-yellow-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"/>
                </svg>
              </div>
            </div>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
            <div class="flex items-center justify-between">
              <div>
                <p class="text-sm text-gray-400 mb-1">Completed</p>
                <p class="text-2xl font-bold text-green-400">{{ completedCount }}</p>
              </div>
              <div class="w-12 h-12 bg-green-500/10 rounded-lg flex items-center justify-center">
                <svg class="w-6 h-6 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                </svg>
              </div>
            </div>
          </div>
        </div>

        <!-- Filters -->
        <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
          <div class="flex flex-col md:flex-row gap-4 items-center justify-between">
            <div class="w-full md:flex-1 relative">
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Search by payment ID, email, or phone..."
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
                <option value="PENDING">Pending</option>
                <option value="ECOCASH">EcoCash</option>
              </select>

              <select
                v-model="itemsPerPage"
                class="bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-gray-300"
              >
                <option :value="10">10 per page</option>
                <option :value="20">20 per page</option>
                <option :value="50">50 per page</option>
                <option :value="100">100 per page</option>
              </select>
            </div>
          </div>
        </div>

        <!-- Loading State -->
        <div v-if="pending" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center">
          <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-gray-700 border-t-red-600"></div>
          <p class="text-gray-400 mt-4">Loading payments...</p>
        </div>

        <!-- Error State -->
        <div v-else-if="error" class="bg-red-500/10 border border-red-500/50 rounded-xl p-6 text-center">
          <svg class="w-12 h-12 text-red-400 mx-auto mb-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          <p class="text-red-400 font-medium">Failed to load payments</p>
          <p class="text-red-300 text-sm mt-1">{{ error.message }}</p>
          <button
            @click="refreshData"
            class="mt-4 bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm transition"
          >
            Try Again
          </button>
        </div>

        <!-- Empty State -->
        <div v-else-if="filteredPayments.length === 0" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center">
          <svg class="w-16 h-16 text-gray-600 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
              d="M17 9V7a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2m2 4h10a2 2 0 002-2v-6a2 2 0 00-2-2H9a2 2 0 00-2 2v6a2 2 0 002 2zm7-5a2 2 0 11-4 0 2 2 0 014 0z" />
          </svg>
          <p class="text-gray-400 font-medium">No payments found</p>
          <p class="text-gray-500 text-sm mt-1">
            {{ searchQuery || statusFilter !== 'all' ? 'Try adjusting your filters' : 'Payments will appear here once they are created' }}
          </p>
        </div>

        <!-- Payments Table -->
        <div v-else class="bg-gray-900 border border-gray-800 rounded-xl overflow-hidden">
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead class="bg-gray-800/50">
                <tr>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Payment ID
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Artist
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Contact
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Amount
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Method
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Status
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Date
                  </th>
                  <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                    Actions
                  </th>
                </tr>
              </thead>
              <tbody class="divide-y divide-gray-800">
                <tr v-for="payment in paginatedPayments" :key="payment.paymentID"
                  class="hover:bg-gray-800/30 transition-colors">
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span class="text-xs font-mono text-gray-400">{{ payment.paymentID.slice(0, 8) }}...</span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <div class="flex items-center space-x-3">
                      <div class="w-8 h-8 bg-gray-700 rounded-full flex items-center justify-center">
                        <span class="text-xs font-medium text-gray-300">{{ getInitials(payment.email) }}</span>
                      </div>
                      <span class="text-sm font-medium text-white">{{ payment.artistId.slice(0, 12) }}...</span>
                    </div>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <div class="flex flex-col">
                      <span class="text-sm text-gray-300">{{ payment.email }}</span>
                      <span class="text-xs text-gray-500">{{ payment.phoneNumber }}</span>
                    </div>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span class="text-sm font-medium text-white">${{ formatNumber(payment.amount) }}</span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span :class="getMethodBadgeClass(payment.method)" 
                          class="px-2 py-1 text-xs font-medium rounded-full">
                      {{ payment.method }}
                    </span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <span :class="getStatusBadgeClass(payment.paymentStatus)" 
                          class="px-2 py-1 text-xs font-medium rounded-full">
                      {{ payment.paymentStatus }}
                    </span>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <div class="flex flex-col">
                      <span class="text-sm text-gray-300">{{ formatDate(payment.dateOfPayment) }}</span>
                      <span class="text-xs text-gray-500">{{ formatTime(payment.timeOfPayment) }}</span>
                    </div>
                  </td>
                  <td class="px-6 py-4 whitespace-nowrap">
                    <button
                      @click="viewPaymentDetails(payment)"
                      class="text-red-400 hover:text-red-300 text-sm font-medium hover:underline transition-colors">
                      View Details
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Pagination -->
          <div v-if="filteredPayments.length > 0" class="bg-gray-800/30 px-6 py-4 border-t border-gray-800 flex items-center justify-between">
            <div class="flex items-center gap-2">
              <p class="text-gray-400 text-sm">
                Showing <span class="text-white font-medium">{{ startItem }}</span> to 
                <span class="text-white font-medium">{{ endItem }}</span> of 
                <span class="text-white font-medium">{{ filteredPayments.length }}</span> payments
              </p>
            </div>

            <div class="flex items-center gap-2">
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

    <!-- Payment Details Modal -->
    <div
      v-if="selectedPayment"
      class="fixed inset-0 bg-black/50 backdrop-blur-sm flex items-center justify-center p-4 z-50"
      @click.self="selectedPayment = null"
    >
      <div class="bg-gray-900 border border-gray-800 rounded-xl max-w-2xl w-full max-h-[90vh] overflow-y-auto">
        <!-- Modal Header -->
        <div class="flex items-center justify-between p-6 border-b border-gray-800">
          <div>
            <h3 class="text-xl font-bold text-white">Payment Details</h3>
            <p class="text-gray-400 text-sm mt-1">Payment ID: {{ selectedPayment.paymentID }}</p>
          </div>
          <button
            @click="selectedPayment = null"
            class="p-2 rounded-lg hover:bg-gray-800 text-gray-400 hover:text-white transition"
          >
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>

        <!-- Modal Content -->
        <div class="p-6 space-y-6">
          <!-- Payment Status -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Payment Status</h4>
            <div class="bg-gray-800/50 rounded-lg p-4 flex items-center justify-between">
              <div>
                <p class="text-gray-400 text-xs mb-1">Current Status</p>
                <span :class="getStatusBadgeClass(selectedPayment.paymentStatus)" 
                      class="px-3 py-1.5 text-sm font-medium rounded-full">
                  {{ selectedPayment.paymentStatus }}
                </span>
              </div>
              <div class="text-right">
                <p class="text-gray-400 text-xs mb-1">Payment Method</p>
                <span :class="getMethodBadgeClass(selectedPayment.method)" 
                      class="px-3 py-1.5 text-sm font-medium rounded-full">
                  {{ selectedPayment.method }}
                </span>
              </div>
            </div>
          </div>

          <!-- Artist Information -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Artist Information</h4>
            <div class="space-y-3">
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Artist ID</p>
                <p class="text-white font-mono text-sm break-all">{{ selectedPayment.artistId }}</p>
              </div>
              <div class="grid grid-cols-2 gap-4">
                <div class="bg-gray-800/50 rounded-lg p-4">
                  <p class="text-gray-400 text-xs mb-1">Email</p>
                  <p class="text-white text-sm">{{ selectedPayment.email }}</p>
                </div>
                <div class="bg-gray-800/50 rounded-lg p-4">
                  <p class="text-gray-400 text-xs mb-1">Phone Number</p>
                  <p class="text-white text-sm">{{ selectedPayment.phoneNumber }}</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Payment Details -->
          <div>
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-3">Payment Information</h4>
            <div class="grid grid-cols-2 gap-4">
              <div class="bg-gray-800/50 rounded-lg p-4 col-span-2">
                <p class="text-gray-400 text-xs mb-1">Amount</p>
                <p class="text-green-400 text-3xl font-bold">${{ formatNumber(selectedPayment.amount) }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Date</p>
                <p class="text-white text-sm">{{ formatDate(selectedPayment.dateOfPayment) }}</p>
              </div>
              <div class="bg-gray-800/50 rounded-lg p-4">
                <p class="text-gray-400 text-xs mb-1">Time</p>
                <p class="text-white text-sm">{{ formatTime(selectedPayment.timeOfPayment) }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Modal Footer -->
        <div class="p-6 border-t border-gray-800 flex justify-end gap-3">
          <button
            @click="selectedPayment = null"
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
const selectedPayment = ref(null)

// Fetch payments from API
const { data: apiData, pending, error, refresh } = await useFetch('http://localhost:8080/api/paynow/getAllPayments')

// Extract payments from API response (it's an array directly)
const payments = computed(() => Array.isArray(apiData.value) ? apiData.value : [])

// Calculate statistics
const totalAmount = computed(() => {
  return payments.value.reduce((sum, payment) => sum + (payment.amount || 0), 0)
})

const pendingCount = computed(() => {
  return payments.value.filter(p => p.paymentStatus === 'PENDING').length
})

const completedCount = computed(() => {
  return payments.value.filter(p => p.paymentStatus !== 'PENDING').length
})

// Filter payments based on search and status
const filteredPayments = computed(() => {
  return payments.value.filter(payment => {
    const matchesSearch = 
      payment.paymentID?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      payment.email?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      payment.phoneNumber?.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      payment.artistId?.toLowerCase().includes(searchQuery.value.toLowerCase())

    const matchesStatus = 
      statusFilter.value === 'all' || 
      payment.paymentStatus === statusFilter.value ||
      payment.method === statusFilter.value

    return matchesSearch && matchesStatus
  })
})

// Pagination calculations
const totalPages = computed(() => Math.ceil(filteredPayments.value.length / itemsPerPage.value))

const paginatedPayments = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value
  const end = start + itemsPerPage.value
  return filteredPayments.value.slice(start, end)
})

const startItem = computed(() => {
  return filteredPayments.value.length === 0 ? 0 : (currentPage.value - 1) * itemsPerPage.value + 1
})

const endItem = computed(() => {
  return Math.min(currentPage.value * itemsPerPage.value, filteredPayments.value.length)
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

// Get initials from email
const getInitials = (email) => {
  if (!email) return '??'
  return email.slice(0, 2).toUpperCase()
}

// Format number with commas
const formatNumber = (num) => {
  if (!num && num !== 0) return '0.00'
  return parseFloat(num).toLocaleString('en-US', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  })
}

// Format date
const formatDate = (dateString) => {
  if (!dateString) return 'N/A'
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'short',
    day: 'numeric'
  })
}

// Format time
const formatTime = (timeString) => {
  if (!timeString) return 'N/A'
  return timeString
}

// Get status badge class
const getStatusBadgeClass = (status) => {
  const classes = {
    'PENDING': 'bg-yellow-500/10 text-yellow-400',
    'COMPLETED': 'bg-green-500/10 text-green-400',
    'FAILED': 'bg-red-500/10 text-red-400'
  }
  return classes[status] || 'bg-gray-500/10 text-gray-400'
}

// Get method badge class
const getMethodBadgeClass = (method) => {
  const classes = {
    'ECOCASH': 'bg-blue-500/10 text-blue-400',
    'CARD': 'bg-purple-500/10 text-purple-400',
    'BANK': 'bg-indigo-500/10 text-indigo-400'
  }
  return classes[method] || 'bg-gray-500/10 text-gray-400'
}

// View payment details
const viewPaymentDetails = (payment) => {
  selectedPayment.value = payment
}

// Refresh data
const refreshData = () => {
  refresh()
}

// Export to CSV
const exportToCSV = () => {
  if (payments.value.length === 0) return

  const headers = [
    'Payment ID',
    'Artist ID',
    'Email',
    'Phone Number',
    'Amount',
    'Method',
    'Status',
    'Date',
    'Time'
  ]
  
  const rows = payments.value.map(payment => [
    payment.paymentID,
    payment.artistId,
    payment.email,
    payment.phoneNumber,
    payment.amount,
    payment.method,
    payment.paymentStatus,
    formatDate(payment.dateOfPayment),
    formatTime(payment.timeOfPayment)
  ])

  let csvContent = headers.join(',') + '\n'
  rows.forEach(row => {
    csvContent += row.map(cell => `"${cell}"`).join(',') + '\n'
  })

  const blob = new Blob([csvContent], { type: 'text/csv' })
  const url = window.URL.createObjectURL(blob)
  const link = document.createElement('a')
  link.href = url
  link.download = `payments-${new Date().toISOString().split('T')[0]}.csv`
  link.click()
  window.URL.revokeObjectURL(url)
}
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

/* Modal animations */
.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}
</style>