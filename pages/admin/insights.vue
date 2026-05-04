<template>
    <NuxtLayout name="admin-layout">
        <div class="min-h-screen bg-gray-950 pb-12">
            <!-- Header -->
            <div class="flex items-center justify-between mb-8">
                <div>
                    <div class="flex items-center space-x-3 mb-1">
                        <div class="w-8 h-8 rounded-lg bg-gradient-to-br from-purple-600 to-blue-600 flex items-center justify-center">
                            <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"/></svg>
                        </div>
                        <h2 class="text-2xl font-bold text-white">Insights Engine</h2>
                    </div>
                    <p class="text-gray-400 text-sm">AI-powered compliance analysis, tax predictions, and artist risk assessment</p>
                </div>
                <div class="flex items-center space-x-3">
                    <span v-if="lastGenerated" class="text-xs text-gray-500">Last generated: {{ lastGenerated }}</span>
                    <div class="flex items-center bg-gray-800 border border-gray-700 rounded-lg p-1">
                        <button @click="activeFilter = 'all'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeFilter === 'all' ? 'bg-gray-700 text-white' : 'text-gray-400 hover:text-white']">All</button>
                        <button @click="activeFilter = 'danger'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeFilter === 'danger' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">⚠ Critical</button>
                        <button @click="activeFilter = 'warning'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeFilter === 'warning' ? 'bg-yellow-600 text-white' : 'text-gray-400 hover:text-white']">Warning</button>
                        <button @click="activeFilter = 'info'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeFilter === 'info' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white']">Info</button>
                    </div>
                    <!-- Fetch AI Insights Button -->
                    <button @click="fetchInsights" :disabled="loading"
                        class="relative overflow-hidden bg-gradient-to-r from-purple-600 to-blue-600 hover:from-purple-700 hover:to-blue-700 disabled:opacity-60 text-white px-5 py-2.5 rounded-xl font-semibold text-sm transition-all shadow-lg shadow-purple-900/30 flex items-center space-x-2">
                        <div v-if="loading" class="absolute inset-0 bg-gradient-to-r from-purple-500/20 to-blue-500/20 animate-pulse"></div>
                        <svg class="w-4 h-4 relative z-10" :class="{ 'animate-spin': loading }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path v-if="!loading" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"/>
                            <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581"/>
                        </svg>
                        <span class="relative z-10">{{ loading ? 'Generating Insights...' : '✦ Fetch AI Insights' }}</span>
                    </button>
                </div>
            </div>

            <!-- Loading Spinner -->
            <div v-if="loading && artistInsights.length === 0" class="flex flex-col items-center justify-center py-24 space-y-4">
                <div class="relative w-16 h-16">
                    <div class="absolute inset-0 rounded-full border-4 border-purple-900"></div>
                    <div class="absolute inset-0 rounded-full border-4 border-t-purple-500 border-l-blue-500 animate-spin"></div>
                </div>
                <p class="text-gray-400 text-sm">Analysing artist data and generating insights...</p>
            </div>

            <template v-else>
                <!-- ========== GLOBAL STATS BAR ========== -->
                <div class="grid grid-cols-2 md:grid-cols-5 gap-4 mb-6">
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                        <p class="text-gray-500 text-xs mb-1">Total Artists</p>
                        <p class="text-2xl font-black text-white">{{ artistInsights.length }}</p>
                    </div>
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                        <p class="text-gray-500 text-xs mb-1">Total Insights</p>
                        <p class="text-2xl font-black text-purple-400">{{ totalInsightsCount }}</p>
                    </div>
                    <div class="bg-red-900/10 border border-red-900/20 rounded-xl p-4">
                        <p class="text-red-400/70 text-xs mb-1">Critical Alerts</p>
                        <p class="text-2xl font-black text-red-400">{{ criticalCount }}</p>
                    </div>
                    <div class="bg-yellow-900/10 border border-yellow-900/20 rounded-xl p-4">
                        <p class="text-yellow-400/70 text-xs mb-1">Warnings</p>
                        <p class="text-2xl font-black text-yellow-400">{{ warningCount }}</p>
                    </div>
                    <div class="bg-green-900/10 border border-green-900/20 rounded-xl p-4">
                        <p class="text-green-400/70 text-xs mb-1">Compliance Rate</p>
                        <p class="text-2xl font-black text-green-400">{{ complianceRate }}%</p>
                    </div>
                </div>

                <div v-if="riskDashboard || complianceStats" class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                        <p class="text-gray-500 text-xs mb-1">Assessed Artists</p>
                        <p class="text-2xl font-black text-white">{{ riskDashboard?.assessedArtists ?? 0 }}</p>
                    </div>
                    <div class="bg-red-900/10 border border-red-900/20 rounded-xl p-4">
                        <p class="text-red-400/70 text-xs mb-1">High Risk (ML + Rules)</p>
                        <p class="text-2xl font-black text-red-400">{{ riskDashboard?.highRiskCount ?? 0 }}</p>
                    </div>
                    <div class="bg-yellow-900/10 border border-yellow-900/20 rounded-xl p-4">
                        <p class="text-yellow-400/70 text-xs mb-1">Flagged For Audit</p>
                        <p class="text-2xl font-black text-yellow-400">{{ riskDashboard?.flaggedForAudit ?? 0 }}</p>
                    </div>
                    <div class="bg-blue-900/10 border border-blue-900/20 rounded-xl p-4">
                        <p class="text-blue-400/70 text-xs mb-1">ZIM Outstanding Tax</p>
                        <p class="text-2xl font-black text-blue-400">${{ formatCurrency(complianceStats?.totalOutstandingTax ?? 0) }}</p>
                    </div>
                </div>

                <!-- Search -->
                <div class="relative mb-6">
                    <svg class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/></svg>
                    <input v-model="searchQuery" placeholder="Search artists..." class="w-full bg-gray-900 border border-gray-800 rounded-xl pl-10 pr-4 py-3 text-white text-sm placeholder-gray-500 focus:outline-none focus:border-purple-500 transition" />
                </div>

                <!-- Empty state -->
                <div v-if="filteredInsights.length === 0 && artistInsights.length === 0" class="text-center py-24">
                    <div class="w-20 h-20 rounded-2xl bg-gradient-to-br from-purple-900/30 to-blue-900/30 border border-purple-800/30 flex items-center justify-center mx-auto mb-4">
                        <svg class="w-10 h-10 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"/></svg>
                    </div>
                    <p class="text-white font-semibold text-lg mb-1">No Insights Generated</p>
                    <p class="text-gray-500 text-sm mb-6">Click "Fetch AI Insights" to analyse all artists and generate detailed compliance and tax predictions.</p>
                    <button @click="fetchInsights" class="bg-gradient-to-r from-purple-600 to-blue-600 hover:from-purple-700 hover:to-blue-700 text-white px-6 py-3 rounded-xl font-semibold text-sm transition-all shadow-lg">
                        ✦ Generate Insights Now
                    </button>
                </div>

                <!-- ========== ARTIST INSIGHT CARDS ========== -->
                <div class="space-y-4">
                    <div v-for="artist in filteredInsights" :key="artist.id"
                        class="bg-gray-900 border rounded-2xl overflow-hidden transition-all duration-300"
                        :class="highestSeverityClass(artist.insights).border">

                        <!-- Card Header -->
                        <div class="p-5 flex items-center justify-between cursor-pointer" @click="toggleExpanded(artist.id)">
                            <div class="flex items-center space-x-4">
                                <div class="w-11 h-11 rounded-xl flex items-center justify-center text-white font-black text-base shadow"
                                    :class="highestSeverityClass(artist.insights).avatar">
                                    {{ artist.name?.split(' ').map(n => n[0]).join('').slice(0, 2).toUpperCase() }}
                                </div>
                                <div>
                                    <div class="flex items-center space-x-2">
                                        <h3 class="text-white font-bold text-sm">{{ artist.name }}</h3>
                                        <span class="text-[9px] px-2 py-0.5 rounded-full font-semibold uppercase" :class="complianceBadge(artist.complianceLevel)">
                                            {{ artist.complianceLevel || 'Unknown' }}
                                        </span>
                                    </div>
                                    <p class="text-gray-500 text-xs">{{ artist.email }}</p>
                                </div>
                            </div>

                            <div class="flex items-center space-x-6">
                                <!-- Mini stats -->
                                <div class="hidden md:flex items-center space-x-5 text-center">
                                    <!-- <div>
                                        <p class="text-[10px] text-gray-500">Videos</p>
                                        <p class="text-sm font-bold text-white">{{ artist.totalVideos }}</p>
                                    </div>
                                    <div>
                                        <p class="text-[10px] text-gray-500">Shorts</p>
                                        <p class="text-sm font-bold text-red-400">{{ artist.totalShots }}</p>
                                    </div> -->
                                    <div>
                                        <p class="text-[10px] text-gray-500">Total Tax Due</p>
                                        <p class="text-sm font-bold text-white">${{ formatCurrency(artist.totalTaxDue) }}</p>
                                    </div>
                                    <div>
                                        <p class="text-[10px] text-gray-500">Outstanding Tax</p>
                                        <p class="text-sm font-bold" :class="artist.outstandingTax > 0 ? 'text-red-400' : 'text-green-400'">${{ formatCurrency(artist.outstandingTax) }}</p>
                                    </div>
                                    <div>
                                        <p class="text-[10px] text-gray-500">Predicted Increase</p>
                                        <p class="text-sm font-bold text-yellow-400">+${{ formatCurrency(artist.predictedTaxIncrease) }}</p>
                                    </div>
                                </div>

                                <!-- Severity pills -->
                                <div class="flex items-center space-x-1">
                                    <span v-if="artist.insights.filter(i => i.severity === 'danger').length" class="bg-red-600/20 text-red-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-red-600/30">
                                        {{ artist.insights.filter(i => i.severity === 'danger').length }} Critical
                                    </span>
                                    <span v-if="artist.insights.filter(i => i.severity === 'warning').length" class="bg-yellow-600/20 text-yellow-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-yellow-600/30">
                                        {{ artist.insights.filter(i => i.severity === 'warning').length }} Warning
                                    </span>
                                    <span v-if="artist.insights.filter(i => i.severity === 'info').length" class="bg-blue-600/20 text-blue-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-blue-600/30">
                                        {{ artist.insights.filter(i => i.severity === 'info').length }} Info
                                    </span>
                                </div>

                                <!-- Expand icon -->
                                <svg class="w-4 h-4 text-gray-500 transition-transform" :class="{ 'rotate-180': expanded.has(artist.id) }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"/>
                                </svg>
                            </div>
                        </div>

                        <!-- Expanded Section -->
                        <div v-if="expanded.has(artist.id)" class="border-t border-gray-800 bg-gray-950/50">
                            <div class="p-5 grid grid-cols-1 lg:grid-cols-3 gap-5">

                                <!-- Insights list -->
                                <div class="lg:col-span-2 space-y-2">
                                    <h4 class="text-xs text-gray-400 font-semibold uppercase tracking-wider mb-3">Insights & Notifications</h4>
                                    <div v-for="(insight, idx) in artist.insights" :key="idx"
                                        class="flex items-start space-x-3 p-3 rounded-xl border"
                                        :class="insightStyle(insight.severity).bg">
                                        <div class="mt-0.5 shrink-0 w-5 h-5 rounded-full flex items-center justify-center" :class="insightStyle(insight.severity).icon">
                                            <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 20 20">
                                                <path v-if="insight.severity === 'danger'" fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd"/>
                                                <path v-else-if="insight.severity === 'warning'" fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7 4a1 1 0 11-2 0 1 1 0 012 0zm-1-9a1 1 0 00-1 1v4a1 1 0 102 0V6a1 1 0 00-1-1z" clip-rule="evenodd"/>
                                                <path v-else d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z"/>
                                            </svg>
                                        </div>
                                        <div>
                                            <p class="text-sm font-semibold" :class="insightStyle(insight.severity).text">{{ insight.title }}</p>
                                            <p class="text-xs text-gray-400 mt-0.5 leading-relaxed">{{ insight.message }}</p>
                                        </div>
                                    </div>
                                </div>

                                <!-- Tax Prediction Panel -->
                                <div class="space-y-4">
                                    <h4 class="text-xs text-gray-400 font-semibold uppercase tracking-wider">Tax Predictions</h4>
                                    
                                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-4 space-y-3">
                                        <div class="flex justify-between items-center">
                                            <p class="text-xs text-gray-500">Current Outstanding</p>
                                            <p class="text-sm font-bold" :class="artist.outstandingTax > 0 ? 'text-red-400' : 'text-green-400'">${{ formatCurrency(artist.outstandingTax) }}</p>
                                        </div>
                                        <div class="flex justify-between items-center">
                                            <p class="text-xs text-gray-500">Total Tax Due</p>
                                            <p class="text-sm font-bold text-white">${{ formatCurrency(artist.totalTaxDue) }}</p>
                                        </div>

                                        <div class="flex justify-between items-center">
                                            <p class="text-xs text-gray-500">Predicted Increase</p>
                                            <p class="text-sm font-bold text-yellow-400">+${{ formatCurrency(artist.predictedTaxIncrease) }}</p>
                                        </div>
                                        <div class="border-t border-gray-800 pt-3">
                                            <p class="text-xs text-gray-500 mb-1.5">Shorts Contribution</p>
                                            <div class="flex justify-between items-center mb-1">
                                                <span class="text-xs text-red-400">{{ artist.totalShots }} Shorts</span>
                                                <span class="text-xs text-white font-mono">${{ formatCurrency(artist.shortsEstimatedRevenue) }}</span>
                                            </div>
                                            <div class="w-full h-1.5 bg-gray-800 rounded-full">
                                                <div class="h-full bg-red-500 rounded-full" :style="`width: ${artist.shortsPercentage}%`"></div>
                                            </div>
                                            <p class="text-[10px] text-gray-600 mt-1">{{ artist.shortsPercentage }}% of revenue from Shorts</p>
                                        </div>
                                    </div>

                                    <!-- Prediction Bar -->
                                    <div class="bg-gradient-to-br from-purple-950/30 to-blue-950/30 border border-purple-900/20 rounded-xl p-4">
                                        <p class="text-xs text-purple-400 font-semibold mb-2">Tax Trajectory Prediction</p>
                                        <div class="space-y-2">
                                            <div v-for="(pred, i) in artist.monthPredictions" :key="i" class="flex items-center justify-between">
                                                <span class="text-xs text-gray-500 w-20">{{ pred.label }}</span>
                                                <div class="flex-1 mx-3 h-1 bg-gray-800 rounded-full overflow-hidden">
                                                    <div class="h-full rounded-full transition-all" :class="i === 0 ? 'bg-blue-500' : i === 1 ? 'bg-purple-500' : 'bg-indigo-400'"
                                                        :style="`width: ${Math.min((pred.amount / Math.max(...artist.monthPredictions.map(p => p.amount), 1)) * 100, 100)}%`"></div>
                                                </div>
                                                <span class="text-xs font-mono text-white w-16 text-right">${{ formatCurrency(pred.amount) }}</span>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </template>
        </div>
    </NuxtLayout>
</template>

<script setup>
import { ref, computed } from 'vue'

const loading = ref(false)
const error = ref('')
const artistInsights = ref([])
const riskDashboard = ref(null)
const complianceStats = ref(null)
const activeFilter = ref('all')
const searchQuery = ref('')
const expanded = ref(new Set())
const lastGenerated = ref('')

const TAX_RATE = 0.10
const AVG_REVENUE_PER_VIDEO = 8.50
const AVG_SHORTS_REVENUE = 2.20

// ===== COMPUTED =====
const totalInsightsCount = computed(() => artistInsights.value.reduce((s, a) => s + a.insights.length, 0))
const criticalCount = computed(() => artistInsights.value.reduce((s, a) => s + a.insights.filter(i => i.severity === 'danger').length, 0))
const warningCount = computed(() => artistInsights.value.reduce((s, a) => s + a.insights.filter(i => i.severity === 'warning').length, 0))
const complianceRate = computed(() => {
    if (!artistInsights.value.length) return 0
    const compliant = artistInsights.value.filter(a => a.taxCompliant).length
    return Math.round((compliant / artistInsights.value.length) * 100)
})

const filteredInsights = computed(() => {
    let result = artistInsights.value
    if (searchQuery.value) {
        result = result.filter(a => a.name?.toLowerCase().includes(searchQuery.value.toLowerCase()) || a.email?.toLowerCase().includes(searchQuery.value.toLowerCase()))
    }
    if (activeFilter.value !== 'all') {
        result = result.filter(a => a.insights.some(i => i.severity === activeFilter.value))
    }
    // Sort: most critical first
    return [...result].sort((a, b) => {
        const score = (artist) => artist.insights.filter(i => i.severity === 'danger').length * 10 +
            artist.insights.filter(i => i.severity === 'warning').length * 3 +
            artist.insights.filter(i => i.severity === 'info').length
        return score(b) - score(a)
    })
})

// ===== ACTIONS =====
const toggleExpanded = (id) => {
    const s = new Set(expanded.value)
    if (s.has(id)) s.delete(id)
    else s.add(id)
    expanded.value = s
}

const fetchInsights = async () => {
    loading.value = true
    error.value = ''
    artistInsights.value = []

    try {
        let riskResults = []

        // 0. Trigger global compliance & tax recalculation first to ensure fresh data
        try {
            await $fetch('http://localhost:8080/api/compliance/initialize-all-compliance', { method: 'POST' })
        } catch (err) {
            console.error('Failed to initialize compliance:', err)
        }

        // Refresh and pull ML + rules risk analysis.
        try {
            const bulkRisk = await $fetch('http://localhost:8080/api/risk/analyze/all', { method: 'POST' })
            riskResults = bulkRisk?.results || []
        } catch (_) { /* optional */ }

        try {
            riskDashboard.value = await $fetch('http://localhost:8080/api/risk/dashboard')
        } catch (_) {
            riskDashboard.value = null
        }

        try {
            complianceStats.value = await $fetch('http://localhost:8080/api/compliance/statistics')
        } catch (_) {
            complianceStats.value = null
        }

        // 1. Fetch all onboarded artists (non-ADMIN, role-filtered by backend)
        const artistsData = await $fetch('http://localhost:8080/api/artists/get-all-artists')
        const artists = artistsData?.artists || []

        if (artists.length === 0) {
            error.value = 'No artists found on the system.'
            loading.value = false
            return
        }

        // 2. Fetch all tax records (used to build per-artist revenue history)
        let allTaxRecords = []
        try {
            const taxData = await $fetch('http://localhost:8080/api/tax/get-all-tax-records')
            allTaxRecords = taxData?.records || []
        } catch (_) { /* tax records are supplemental */ }

        // 3. Fetch compliance report per artist in parallel
        const complianceResults = await Promise.allSettled(
            artists.map(a => $fetch(`http://localhost:8080/api/compliance/${a.id}`))
        )

        // 4. Build insight cards from real data
        const insights = artists.map((artist, idx) => {
            const compResult = complianceResults[idx]
            const compliance = compResult.status === 'fulfilled' ? compResult.value : null
            const risk = riskResults.find(r => r.artistId === artist.id)

            // Pull live fields from compliance report DTO
            const totalVideos     = compliance?.totalVideosToDate ?? 0
            const totalShots      = compliance?.totalShotsToDate ?? 0
            const outstandingTax  = compliance?.outstandingTax ?? 0
            const totalTaxPaid    = compliance?.totalTaxPaid ?? 0
            const totalTaxDue     = compliance?.totalTaxDue ?? 0
            const taxCompliant    = compliance?.taxCompliant ?? true
            const complianceLevel = compliance?.complianceLevel ?? 'UNKNOWN'
            const missedPayments  = compliance?.missedPayments ?? 0
            const hasOverdue      = compliance?.hasOverduePayments ?? false
            const totalRevenue    = compliance?.totalRevenueToDate ?? 0

            // Tax records for this specific artist — real historical data
            const artistTax = allTaxRecords.filter(r => r.artistId === artist.id)

            // Shorts revenue estimate (Shorts avg. CPM is lower — $2.20/unit)
            const AVG_REVENUE_PER_VIDEO = 8.50
            const AVG_SHORTS_REVENUE    = 2.20
            const shortsEstimatedRevenue = totalShots * AVG_SHORTS_REVENUE
            const totalEstimatedRevenue  = totalRevenue > 0 ? totalRevenue : (totalVideos * AVG_REVENUE_PER_VIDEO) + shortsEstimatedRevenue
            const shortsPercentage       = totalEstimatedRevenue > 0
                ? Math.round((shortsEstimatedRevenue / totalEstimatedRevenue) * 100)
                : 0

            // Predicted next-period tax increase based on current content volume
            const recentVideoCount   = artistTax.length > 0 ? (artistTax[artistTax.length - 1]?.videoCount ?? totalVideos) : totalVideos
            const predictedTaxIncrease = (recentVideoCount * AVG_REVENUE_PER_VIDEO * 0.10) + (totalShots * AVG_SHORTS_REVENUE * 0.10)

            // 3-month tax trajectory derived from actual tax history or prediction
            const monthlyBase = artistTax.length > 0
                ? artistTax.reduce((s, r) => s + (r.taxAmount ?? 0), 0) / artistTax.length
                : predictedTaxIncrease
            const monthPredictions = [
                { label: 'This Month',  amount: monthlyBase },
                { label: 'Next Month',  amount: monthlyBase * 1.08 },
                { label: 'Month +2',    amount: monthlyBase * 1.16 },
            ]

            // ---- Generate rule-based insights from real data ----
            const insightsList = []

            if (hasOverdue) {
                insightsList.push({ severity: 'danger', title: '🚨 Overdue Tax Payments', message: `Outstanding balance of $${formatCurrency(outstandingTax)} is overdue. Immediate action required to avoid penalties under the DST framework.` })
            }
            if (missedPayments > 2) {
                insightsList.push({ severity: 'danger', title: `⚠ ${missedPayments} Missed Payment Periods`, message: `This artist has missed ${missedPayments} tax payment periods. Continued non-payment may result in compliance level dropping to CRITICAL.` })
            }
            if (!taxCompliant && !hasOverdue) {
                insightsList.push({ severity: 'danger', title: '🔴 Non-Compliant Status', message: `${artist.name} is currently flagged as non-compliant by the system. Review their compliance record and contact them if necessary.` })
            }
            if (outstandingTax > 50 && !hasOverdue) {
                insightsList.push({ severity: 'warning', title: '💸 High Outstanding Balance', message: `Unpaid tax balance of $${formatCurrency(outstandingTax)} exceeds the $50 review threshold. Consider issuing a payment reminder.` })
            }
            if (totalShots > 10) {
                insightsList.push({ severity: 'warning', title: `📱 High Shorts Activity — ${totalShots} Shorts Detected`, message: `Shorts contribute an estimated $${formatCurrency(shortsEstimatedRevenue)} (~${shortsPercentage}% of total revenue). Ensure DST calculations correctly include short-form content.` })
            }
            if (shortsPercentage > 40) {
                insightsList.push({ severity: 'warning', title: '📊 Revenue Dominated by Shorts', message: `${shortsPercentage}% of this artist's estimated revenue comes from Shorts. Tax forecasts should weight short-form content CPM separately from long-form.` })
            }
            if (complianceLevel === 'POOR' || complianceLevel === 'CRITICAL') {
                insightsList.push({ severity: 'danger', title: `📉 Compliance Level: ${complianceLevel}`, message: `This artist's compliance rating has fallen to ${complianceLevel}. Admin review of their payment history is strongly recommended.` })
            }
            if (predictedTaxIncrease > 20) {
                insightsList.push({ severity: 'info', title: `📈 Predicted Tax Increase: +$${formatCurrency(predictedTaxIncrease)}`, message: `Based on ${totalVideos} video(s) and ${totalShots} Short(s) on this artist's channel, the next DST period is forecast to increase by approximately $${formatCurrency(predictedTaxIncrease)}.` })
            }
            if (totalVideos > 50) {
                insightsList.push({ severity: 'info', title: `🎬 High Content Volume — ${totalVideos} Videos`, message: `This artist has a large content catalogue. Higher video counts increase DST exposure — ensure revenue reporting is up to date.` })
            }
            if (complianceLevel === 'EXCELLENT' && taxCompliant && outstandingTax === 0) {
                insightsList.push({ severity: 'info', title: '✅ Fully Compliant', message: `${artist.name} has an EXCELLENT compliance rating with no outstanding tax. No action required.` })
            }
            if (insightsList.length === 0) {
                insightsList.push({ severity: 'info', title: 'ℹ No Issues Detected', message: `No compliance issues or significant content trends detected for ${artist.name} at this time.` })
            }

            if (risk?.riskLevel === 'HIGH') {
                insightsList.unshift({
                    severity: 'danger',
                    title: `🧠 ML/Rules High Risk (${Math.round((risk.riskScore || 0) * 100)}%)`,
                    message: risk.recommendation || 'Potential tax evasion behavior detected by ML and rules engine.'
                })
            } else if (risk?.riskLevel === 'MEDIUM') {
                insightsList.unshift({
                    severity: 'warning',
                    title: `🧠 ML/Rules Medium Risk (${Math.round((risk.riskScore || 0) * 100)}%)`,
                    message: 'Potential compliance risk detected. Monitor and review this account.'
                })
            }

            if (risk?.indicators?.length) {
                risk.indicators.slice(0, 3).forEach((indicator) => {
                    const text = String(indicator || '')
                    const isZimTaxSignal =
                        text.toLowerCase().includes('threshold') ||
                        text.toLowerCase().includes('missed tax') ||
                        text.toLowerCase().includes('outstanding')

                    insightsList.push({
                        severity: isZimTaxSignal ? 'warning' : 'info',
                        title: isZimTaxSignal ? '🇿🇼 ZIM Tax Signal' : '📌 Risk Indicator',
                        message: text
                    })
                })
            }

            return {
                id: artist.id,
                name: artist.name,
                email: artist.email,
                taxCompliant,
                complianceLevel,
                outstandingTax,
                totalTaxPaid,
                totalTaxDue,
                totalRevenue,
                totalVideos,
                totalShots,
                shortsEstimatedRevenue,
                shortsPercentage,
                riskScore: risk?.riskScore ?? 0,
                riskLevel: risk?.riskLevel ?? 'LOW',
                predictedTaxIncrease,
                monthPredictions,
                insights: insightsList
            }
        })

        artistInsights.value = insights

        // Auto-expand top 3 critical artists
        const criticalIds = insights
            .filter(a => a.insights.some(i => i.severity === 'danger'))
            .map(a => a.id)
        expanded.value = new Set(criticalIds.slice(0, 3))

        lastGenerated.value = new Date().toLocaleTimeString('en-US', { hour: '2-digit', minute: '2-digit' })

    } catch (e) {
        const msg = e?.data?.error || e?.message || 'Unknown error'
        error.value = `Failed to load insights: ${msg}. Please ensure the backend is running and try again.`
    } finally {
        loading.value = false
    }
}

// ===== STYLING HELPERS =====
const highestSeverityClass = (insights) => {
    if (insights.some(i => i.severity === 'danger')) return { border: 'border-red-900/40', avatar: 'bg-gradient-to-br from-red-600 to-red-800' }
    if (insights.some(i => i.severity === 'warning')) return { border: 'border-yellow-900/30', avatar: 'bg-gradient-to-br from-yellow-600 to-orange-700' }
    return { border: 'border-gray-800', avatar: 'bg-gradient-to-br from-gray-600 to-gray-800' }
}

const insightStyle = (severity) => {
    const map = {
        danger: { bg: 'bg-red-950/20 border-red-900/30', icon: 'bg-red-600 text-white', text: 'text-red-400' },
        warning: { bg: 'bg-yellow-950/20 border-yellow-900/30', icon: 'bg-yellow-600 text-white', text: 'text-yellow-400' },
        info: { bg: 'bg-blue-950/20 border-blue-900/30', icon: 'bg-blue-600 text-white', text: 'text-blue-400' }
    }
    return map[severity] || map.info
}

const complianceBadge = (level) => {
    const map = {
        EXCELLENT: 'bg-green-500/20 text-green-400 border border-green-500/30',
        GOOD: 'bg-blue-500/20 text-blue-400 border border-blue-500/30',
        FAIR: 'bg-yellow-500/20 text-yellow-400 border border-yellow-500/30',
        POOR: 'bg-orange-500/20 text-orange-400 border border-orange-500/30',
        CRITICAL: 'bg-red-500/20 text-red-400 border border-red-500/30'
    }
    return map[level] || map.FAIR
}

const formatCurrency = (v) => {
    if (!v && v !== 0) return '0.00'
    const val = parseFloat(v)
    if (val > 0 && val < 0.01) {
        return val.toLocaleString('en-US', { minimumFractionDigits: 4, maximumFractionDigits: 4 })
    }
    return val.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}
</script>
