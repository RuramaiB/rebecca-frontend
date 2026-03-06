<template>
    <NuxtLayout name="artist-layout">
        <div class="space-y-6 pt-6">
            
            <!-- Header -->
            <div class="flex items-center justify-between">
                <div>
                    <h2 class="text-2xl font-bold text-white mb-2">My Compliance Details</h2>
                    <p class="text-gray-400">View your tax compliance status and information</p>
                </div>
                <button
                    @click="refreshCompliance"
                    :disabled="loading"
                    class="bg-red-600 hover:bg-red-700 text-white px-6 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                    <svg class="w-4 h-4" :class="{ 'animate-spin': loading }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15">
                        </path>
                    </svg>
                    <span>Refresh</span>
                </button>
            </div>

            <!-- Loading State -->
            <div v-if="loading" class="bg-gray-900 border border-gray-800 rounded-xl p-8 flex items-center justify-center">
                <div class="flex items-center space-x-3">
                    <svg class="animate-spin h-5 w-5 text-red-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                    </svg>
                    <span class="text-gray-400">Loading compliance data...</span>
                </div>
            </div>

            <!-- Error State -->
            <div v-else-if="error" class="bg-red-500/10 border border-red-500/20 rounded-xl p-4">
                <div class="flex items-start space-x-3">
                    <svg class="w-5 h-5 text-red-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z">
                        </path>
                    </svg>
                    <div class="flex-1">
                        <p class="text-sm font-medium text-red-400">Error Loading Data</p>
                        <p class="text-sm text-red-500/80 mt-1">{{ error }}</p>
                        <button 
                            @click="fetchCompliance" 
                            class="mt-3 text-sm text-red-400 hover:text-red-300 underline">
                            Try Again
                        </button>
                    </div>
                </div>
            </div>

            <!-- No Artist ID Warning -->
            <div v-else-if="!artistId" class="bg-yellow-500/10 border border-yellow-500/20 rounded-xl p-4">
                <div class="flex items-start space-x-3">
                    <svg class="w-5 h-5 text-yellow-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z">
                        </path>
                    </svg>
                    <div>
                        <p class="text-sm font-medium text-yellow-400">Artist ID Not Found</p>
                        <p class="text-sm text-yellow-500/80 mt-1">
                            Please log in to view your compliance details.
                        </p>
                    </div>
                </div>
            </div>

            <template v-else-if="artist">
                <!-- Status Banner -->
                <div v-if="!artist.taxCompliant" class="bg-red-500/10 border border-red-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                        <svg class="w-5 h-5 text-red-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z">
                            </path>
                        </svg>
                        <div>
                            <p class="text-sm font-medium text-red-400">Action Required</p>
                            <p class="text-sm text-red-500/80 mt-1">
                                You have outstanding tax obligations that require immediate attention.
                            </p>
                        </div>
                    </div>
                </div>

                <div v-else class="bg-green-500/10 border border-green-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                        <svg class="w-5 h-5 text-green-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z">
                            </path>
                        </svg>
                        <div>
                            <p class="text-sm font-medium text-green-400">All Clear</p>
                            <p class="text-sm text-green-500/80 mt-1">
                                You are currently compliant with your tax obligations.
                            </p>
                        </div>
                    </div>
                </div>

                <!-- Status Overview -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                    <!-- Compliance Status -->
                    <div :class="artist.taxCompliant ? 'bg-green-500/10 border-green-500/20' : 'bg-red-500/10 border-red-500/20'" 
                         class="border rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Tax Compliance</p>
                                <p :class="artist.taxCompliant ? 'text-green-400' : 'text-red-400'" 
                                   class="text-2xl font-bold">
                                    {{ artist.taxCompliant ? 'Compliant' : 'Non-Compliant' }}
                                </p>
                            </div>
                            <svg :class="artist.taxCompliant ? 'text-green-500' : 'text-red-500'" 
                                 class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path v-if="artist.taxCompliant" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z"/>
                            </svg>
                        </div>
                    </div>

                    <!-- Compliance Level -->
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Compliance Level</p>
                                <span :class="getComplianceLevelClass(artist.complianceLevel)" 
                                      class="px-3 py-1 text-sm font-medium rounded-full">
                                    {{ artist.complianceLevel || 'N/A' }}
                                </span>
                            </div>
                            <svg class="w-12 h-12 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/>
                            </svg>
                        </div>
                    </div>

                    <!-- Registered Date -->
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Registered Since</p>
                                <p class="text-xl font-bold text-white">{{ formatDate(artist.registeredDate) }}</p>
                            </div>
                            <svg class="w-12 h-12 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"/>
                            </svg>
                        </div>
                    </div>
                </div>

                <!-- Financial Information -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <h4 class="text-lg font-bold text-white mb-4">Financial Information</h4>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Total Revenue to Date</p>
                            <p class="text-2xl font-bold text-white">${{ formatNumber(artist.totalRevenueToDate || 0) }}</p>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Total Tax Paid</p>
                            <p class="text-2xl font-bold text-green-400">${{ formatNumber(artist.totalTaxPaid || 0) }}</p>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Outstanding Tax</p>
                            <p class="text-2xl font-bold text-red-400">${{ formatNumber(artist.outstandingTax || 0) }}</p>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Total Tax Due</p>
                            <p class="text-2xl font-bold text-yellow-400">${{ formatNumber((artist.totalTaxPaid || 0) + (artist.outstandingTax || 0)) }}</p>
                            <p class="text-xs text-gray-500 mt-1">(Paid + Outstanding)</p>
                        </div>
                    </div>
                </div>

                <!-- Compliance Metrics -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <h4 class="text-lg font-bold text-white mb-4">Compliance Metrics</h4>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm text-gray-400 mb-1">Consecutive Compliant Months</p>
                                    <p class="text-2xl font-bold text-white">{{ artist.consecutiveCompliantMonths || 0 }}</p>
                                </div>
                                <svg class="w-8 h-8 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7h12m0 0l-4-4m4 4l-4 4m0 6H4m0 0l4 4m-4-4l4-4"/>
                                </svg>
                            </div>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm text-gray-400 mb-1">Missed Payments</p>
                                    <p class="text-2xl font-bold text-white">{{ artist.missedPayments || 0 }}</p>
                                </div>
                                <svg class="w-8 h-8 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                </svg>
                            </div>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <div class="flex items-center justify-between">
                                <div>
                                    <p class="text-sm text-gray-400 mb-1">Overdue Payments</p>
                                    <p class="text-xl font-bold text-white">{{ artist.hasOverduePayments ? 'Yes' : 'No' }}</p>
                                </div>
                                <svg :class="artist.hasOverduePayments ? 'text-red-500' : 'text-green-500'" 
                                     class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                </svg>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Additional Information -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <h4 class="text-lg font-bold text-white mb-4">Additional Information</h4>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Digital Tax Registration</p>
                            <div class="flex items-center">
                                <span :class="artist.registeredForDigitalTax ? 'text-green-400' : 'text-red-400'" 
                                      class="text-lg font-bold">
                                    {{ artist.registeredForDigitalTax ? 'Registered' : 'Not Registered' }}
                                </span>
                                <svg v-if="artist.registeredForDigitalTax" class="w-5 h-5 text-green-500 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/>
                                </svg>
                            </div>
                        </div>
                        <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                            <p class="text-sm text-gray-400 mb-2">Last Tax Calculation</p>
                            <p class="text-lg font-bold text-white">{{ formatDate(artist.lastTaxCalculation) }}</p>
                        </div>
                    </div>
                </div>

                <!-- Alerts Section -->
                <div v-if="artist.alerts && artist.alerts.length > 0" class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <h4 class="text-lg font-bold text-white mb-4">Active Alerts ({{ artist.alerts.length }})</h4>
                    <div class="space-y-3">
                        <div v-for="(alert, index) in artist.alerts" :key="index" 
                             class="bg-yellow-500/10 border border-yellow-500/20 rounded-xl p-4">
                            <div class="flex items-start space-x-3">
                                <svg class="w-5 h-5 text-yellow-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z"/>
                                </svg>
                                <div>
                                    <p class="text-sm font-medium text-yellow-400">Alert {{ index + 1 }}</p>
                                    <p class="text-sm text-yellow-500/80 mt-1">{{ alert }}</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Compliance Notes -->
                <div v-if="artist.complianceNotes" class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <h4 class="text-lg font-bold text-white mb-4">Compliance Notes</h4>
                    <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                        <p class="text-sm text-gray-400">{{ artist.complianceNotes }}</p>
                    </div>
                </div>
            </template>
        </div>
    </NuxtLayout>
</template>

<script setup>
const config = useRuntimeConfig()
const artist = ref(null)
const artistId = ref(null)
const loading = ref(true)
const error = ref(null)

// Format number with commas
const formatNumber = (num) => {
    if (!num && num !== 0) return '0'
    return num.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}

// Format date
const formatDate = (dateString) => {
    if (!dateString) return 'N/A'
    const date = new Date(dateString)
    return date.toLocaleDateString('en-US', { year: 'numeric', month: 'short', day: 'numeric' })
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

// Fetch compliance data for specific artist
const fetchCompliance = async () => {
    if (!artistId.value) {
        loading.value = false
        return
    }

    loading.value = true
    error.value = null
    
    try {
        const response = await $fetch(`http://localhost:8080/api/compliance/${artistId.value}`, {
            method: 'GET',
            headers: {
                'accept': '*/*'
            }
        })
        
        artist.value = response
    } catch (err) {
        console.error('Error fetching compliance data:', err)
        error.value = err.message || 'Failed to load compliance data. Please try again.'
    } finally {
        loading.value = false
    }
}

// Refresh compliance data
const refreshCompliance = () => {
    fetchCompliance()
}

// Initialize on mount
onMounted(() => {
    // Get artist ID from in-memory storage (replace with your auth logic)
    // For demonstration, using a test ID
    artistId.value = localStorage.getItem('artistID') // Replace with actual logic to get logged-in artist ID
    
    // You can also get it from route params if needed:
    // const route = useRoute()
    // artistId.value = route.params.id
    
    if (artistId.value) {
        fetchCompliance()
    } else {
        loading.value = false
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