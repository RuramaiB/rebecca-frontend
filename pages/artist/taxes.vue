<template>
    <NuxtLayout name="artist-layout">
        <div class="min-h-screen bg-gray-950 pb-12">

            <!-- Header -->
            <div class="flex items-center justify-between mb-6">
                <div>
                    <h2 class="text-2xl font-bold text-white mb-1">My Tax Records</h2>
                    <p class="text-gray-400 text-sm">Per-video Digital Service Tax breakdown — 10% DST applied to every video, no minimums</p>
                </div>
                <div class="flex items-center space-x-3">
                    <button @click="loadData" :disabled="loading"
                        class="bg-red-600 hover:bg-red-700 disabled:opacity-50 text-white px-4 py-2 rounded-lg text-sm font-medium flex items-center space-x-2 transition">
                        <svg class="w-4 h-4" :class="{ 'animate-spin': loading }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
                        </svg>
                        <span>{{ loading ? 'Loading...' : 'Sync Channel' }}</span>
                    </button>
                    <button @click="exportCSV" :disabled="videoRows.length === 0"
                        class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2 rounded-lg text-sm font-medium flex items-center space-x-2 transition disabled:opacity-40">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/></svg>
                        <span>Export CSV</span>
                    </button>
                </div>
            </div>

            <!-- Error -->
            <div v-if="error" class="mb-6 p-4 bg-red-500/10 border border-red-500/30 rounded-xl text-red-400 text-sm flex items-center space-x-2">
                <svg class="w-5 h-5 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
                <span>{{ error }}</span>
            </div>

            <!-- ===== SUMMARY TILES ===== -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-6">
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                    <p class="text-gray-500 text-xs mb-1">Total Videos</p>
                    <p class="text-2xl font-black text-white">{{ videoRows.length }}</p>
                    <p class="text-gray-600 text-[10px] mt-0.5">{{ shortVideos.length }} Shorts · {{ longVideos.length }} Long-form</p>
                </div>
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                    <p class="text-gray-500 text-xs mb-1">Total Views</p>
                    <p class="text-2xl font-black text-white">{{ formatNumber(totalViews) }}</p>
                </div>
                <div class="bg-red-900/10 border border-red-900/20 rounded-xl p-4">
                    <p class="text-red-400/70 text-xs mb-1">Outstanding Tax Due</p>
                    <p class="text-2xl font-black text-red-400">${{ formatCurrency(officialOutstandingTax) }}</p>
                    <p class="text-red-400/50 text-[10px] mt-0.5">Official backend compliance balance</p>
                </div>
                <div class="bg-green-900/10 border border-green-900/20 rounded-xl p-4">
                    <p class="text-green-400/70 text-xs mb-1">Est. Gross Revenue</p>
                    <p class="text-2xl font-black text-green-400">${{ formatCurrency(totalRevenue) }}</p>
                    <p class="text-gray-600 text-[10px] mt-0.5">views × $0.0015 CPM</p>
                </div>
            </div>

            <!-- Month-end forecast banner -->
            <div class="mb-6 bg-gradient-to-r from-red-950/40 via-gray-900 to-gray-900 border border-red-900/30 rounded-2xl p-5 flex items-center justify-between gap-4">
                <div>
                    <p class="text-xs text-red-400 font-semibold uppercase tracking-wider mb-1">📅 {{ currentMonthName }} Forecast</p>
                    <p class="text-3xl font-black text-white">${{ formatCurrency(officialOutstandingTax) }}</p>
                    <p class="text-gray-400 text-sm mt-1">Current official outstanding balance from tax records</p>
                </div>
                <div class="flex items-center space-x-6 text-center">
                    <div>
                        <p class="text-xs text-gray-500 mb-1">Days elapsed</p>
                        <p class="text-xl font-bold text-white">{{ daysElapsed }}<span class="text-gray-500 text-sm">/{{ daysInMonth }}</span></p>
                        <div class="w-20 h-1.5 bg-gray-800 rounded-full mt-1">
                            <div class="h-full bg-red-600 rounded-full" :style="`width:${monthPct}%`"></div>
                        </div>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 mb-1">DST Rate</p>
                        <p class="text-xl font-bold text-yellow-400">10%</p>
                        <p class="text-[10px] text-gray-600">No minimums</p>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 mb-1">Per-video avg</p>
                        <p class="text-xl font-bold text-blue-400">${{ formatCurrency(avgOfficialTaxPerVideo) }}</p>
                    </div>
                </div>
            </div>

            <!-- Loading skeleton -->
            <div v-if="loading" class="bg-gray-900 border border-gray-800 rounded-2xl p-16 text-center">
                <div class="inline-block animate-spin rounded-full h-10 w-10 border-4 border-gray-700 border-t-red-600 mb-4"></div>
                <p class="text-gray-400">Fetching your channel videos...</p>
            </div>

            <!-- Empty -->
            <div v-else-if="videoRows.length === 0 && !loading" class="bg-gray-900 border border-gray-800 rounded-2xl p-16 text-center">
                <svg class="w-16 h-16 text-gray-700 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.069A1 1 0 0121 8.871v6.258a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/></svg>
                <p class="text-gray-400 font-medium mb-1">No videos found</p>
                <p class="text-gray-600 text-sm mb-5">Connect your YouTube channel to see per-video tax calculations</p>
                <button @click="loadData" class="bg-red-600 hover:bg-red-700 text-white px-5 py-2.5 rounded-xl text-sm font-medium transition">Sync Channel</button>
            </div>

            <!-- ===== VIDEO TABLE ===== -->
            <div v-else class="bg-gray-900 border border-gray-800 rounded-2xl overflow-hidden">
                <!-- Filter row -->
                <div class="flex items-center justify-between p-4 border-b border-gray-800 gap-3">
                    <div class="relative flex-1">
                        <svg class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/></svg>
                        <input v-model="search" placeholder="Search videos..." class="w-full bg-gray-800 border border-gray-700 rounded-lg pl-9 pr-4 py-2 text-sm text-white placeholder-gray-500 focus:outline-none focus:border-red-500 transition" />
                    </div>
                    <div class="flex items-center bg-gray-800 border border-gray-700 rounded-lg p-0.5">
                        <button @click="filter = 'all'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition', filter === 'all' ? 'bg-gray-700 text-white' : 'text-gray-400 hover:text-white']">All ({{ videoRows.length }})</button>
                        <button @click="filter = 'shorts'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition', filter === 'shorts' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">Shorts ({{ shortVideos.length }})</button>
                        <button @click="filter = 'long'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition', filter === 'long' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white']">Videos ({{ longVideos.length }})</button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead>
                            <tr class="border-b border-gray-800 bg-gray-950/40">
                                <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider w-8">#</th>
                                <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Thumbnail</th>
                                <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Video</th>
                                <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Views</th>
                                <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Likes</th>
                                <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Comments</th>
                                <th class="text-center py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Type</th>
                                <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Est. Revenue</th>
                                <th class="text-right py-3 px-4 text-[10px] font-semibold text-red-400 uppercase tracking-wider">DST (10%)</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-gray-800/40">
                            <tr v-for="(row, idx) in paginatedRows" :key="row.videoId"
                                class="hover:bg-gray-800/30 transition-colors cursor-pointer"
                                @click="selected = row">
                                <td class="py-3 px-4 text-gray-600 text-xs">{{ (page-1)*perPage + idx + 1 }}</td>
                                <td class="py-3 px-4">
                                    <div class="w-16 h-9 bg-gray-800 rounded overflow-hidden">
                                        <img :src="`https://img.youtube.com/vi/${row.videoId}/default.jpg`" class="w-full h-full object-cover" />
                                    </div>
                                </td>
                                <td class="py-3 px-4 max-w-xs">
                                    <p class="text-white text-xs font-medium line-clamp-2 leading-snug">{{ row.title }}</p>
                                    <p class="text-gray-600 text-[10px] mt-0.5">{{ formatDate(row.publishedAt) }}</p>
                                </td>
                                <td class="py-3 px-4 text-right"><span class="text-white text-sm font-bold">{{ formatNumber(row.viewCount) }}</span></td>
                                <td class="py-3 px-4 text-right"><span class="text-green-400 text-sm font-bold">{{ formatNumber(row.likeCount) }}</span></td>
                                <td class="py-3 px-4 text-right"><span class="text-blue-400 text-sm font-bold">{{ formatNumber(row.commentCount) }}</span></td>
                                <td class="py-3 px-4 text-center">
                                    <span v-if="row.isShort" class="bg-red-600/20 text-red-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-red-600/30">SHORT</span>
                                    <span v-else class="bg-blue-600/20 text-blue-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-blue-600/30">VIDEO</span>
                                </td>
                                <td class="py-3 px-4 text-right"><span class="text-gray-300 text-xs font-mono">${{ formatCurrency(row.estRevenue) }}</span></td>
                                <td class="py-3 px-4 text-right">
                                    <div class="flex flex-col items-end">
                                        <span class="text-red-400 text-sm font-black">${{ formatCurrency(row.dst) }}</span>
                                        <span class="text-gray-600 text-[9px]">DST 10%</span>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                        <tfoot>
                            <tr class="border-t-2 border-gray-700 bg-gray-950">
                                <td colspan="3" class="py-3 px-4 text-xs text-gray-400 font-semibold">
                                    Showing {{ (page-1)*perPage+1 }}–{{ Math.min(page*perPage, filteredRows.length) }} of {{ filteredRows.length }} videos
                                </td>
                                <td class="py-3 px-4 text-right text-xs font-bold text-white">{{ formatNumber(filteredRows.reduce((s,r) => s+r.viewCount,0)) }}</td>
                                <td class="py-3 px-4 text-right text-xs font-bold text-green-400">{{ formatNumber(filteredRows.reduce((s,r) => s+r.likeCount,0)) }}</td>
                                <td class="py-3 px-4 text-right text-xs font-bold text-blue-400">{{ formatNumber(filteredRows.reduce((s,r) => s+r.commentCount,0)) }}</td>
                                <td class="py-3 px-4"></td>
                                <td class="py-3 px-4 text-right text-xs font-bold text-gray-300">${{ formatCurrency(filteredRows.reduce((s,r) => s+r.estRevenue,0)) }}</td>
                                <td class="py-3 px-4 text-right text-sm font-black text-red-400">${{ formatCurrency(filteredRows.reduce((s,r) => s+r.dst,0)) }}</td>
                            </tr>
                        </tfoot>
                    </table>
                </div>

                <!-- Pagination -->
                <div class="border-t border-gray-800 px-4 py-3 flex items-center justify-between bg-gray-950/30">
                    <select v-model="perPage" class="bg-gray-800 border border-gray-700 rounded-lg px-3 py-1.5 text-xs text-gray-300">
                        <option :value="25">25 rows</option>
                        <option :value="50">50 rows</option>
                        <option :value="100">100 rows</option>
                    </select>
                    <div class="flex items-center space-x-1">
                        <button @click="page = 1" :disabled="page === 1" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30">«</button>
                        <button @click="page--" :disabled="page === 1" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30">‹</button>
                        <span class="px-3 py-1.5 bg-red-600 rounded text-xs text-white font-bold">{{ page }}</span>
                        <span class="text-gray-500 text-xs px-1">of {{ totalPages }}</span>
                        <button @click="page++" :disabled="page === totalPages" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30">›</button>
                        <button @click="page = totalPages" :disabled="page === totalPages" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30">»</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Video Detail Modal -->
        <div v-if="selected" class="fixed inset-0 bg-black/70 backdrop-blur-sm z-50 flex items-center justify-center p-4" @click.self="selected = null">
            <div class="bg-gray-900 border border-gray-700 rounded-2xl w-full max-w-lg shadow-2xl">
                <div class="p-5 border-b border-gray-800 flex items-start justify-between">
                    <div>
                        <div class="flex items-center space-x-2 mb-1">
                            <span v-if="selected.isShort" class="bg-red-600 text-white text-[9px] font-bold px-2 py-0.5 rounded">SHORT</span>
                            <span v-else class="bg-blue-600 text-white text-[9px] font-bold px-2 py-0.5 rounded">VIDEO</span>
                            <span class="text-gray-500 text-xs font-mono">{{ formatDuration(selected.duration) }}</span>
                        </div>
                        <h3 class="text-white font-bold text-base leading-snug">{{ selected.title }}</h3>
                        <p class="text-gray-500 text-xs mt-0.5">{{ formatDate(selected.publishedAt) }}</p>
                    </div>
                    <button @click="selected = null" class="text-gray-400 hover:text-white transition ml-3">
                        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                    </button>
                </div>
                <div class="p-5 space-y-4">
                    <div class="aspect-video bg-gray-800 rounded-xl overflow-hidden">
                        <img :src="`https://img.youtube.com/vi/${selected.videoId}/hqdefault.jpg`" class="w-full h-full object-cover" />
                    </div>
                    <div class="grid grid-cols-2 gap-3">
                        <div class="bg-gray-800/50 rounded-xl p-3 text-center"><p class="text-gray-500 text-xs mb-1">Views</p><p class="text-xl font-black text-white">{{ formatNumber(selected.viewCount) }}</p></div>
                        <div class="bg-gray-800/50 rounded-xl p-3 text-center"><p class="text-gray-500 text-xs mb-1">Likes</p><p class="text-xl font-black text-green-400">{{ formatNumber(selected.likeCount) }}</p></div>
                        <div class="bg-gray-800/50 rounded-xl p-3 text-center"><p class="text-gray-500 text-xs mb-1">Est. Revenue</p><p class="text-xl font-black text-white">${{ formatCurrency(selected.estRevenue) }}</p></div>
                        <div class="bg-red-950/30 border border-red-900/30 rounded-xl p-3 text-center"><p class="text-red-400/70 text-xs mb-1">DST (10%)</p><p class="text-xl font-black text-red-400">${{ formatCurrency(selected.dst) }}</p></div>
                    </div>
                    <div class="bg-gray-800/30 rounded-xl p-3">
                        <p class="text-gray-500 text-xs mb-2 font-semibold">Tax Calculation</p>
                        <p class="text-gray-400 text-xs leading-relaxed">
                            Base: (<span class="font-mono text-white">{{ formatNumber(selected.viewCount) }}</span> views / 1000) × 0.15 = <span class="text-white">${{ formatCurrency(selected.baseTax) }}</span><br/>
                            Interest: <span class="text-white">${{ formatCurrency(selected.baseTax) }}</span> × 10% × <span class="text-white">{{ selected.monthsOverdue }}</span> mo = <span class="text-yellow-500">${{ formatCurrency(selected.interest) }}</span><br/>
                            <span class="text-red-400 font-bold">Total: ${{ formatCurrency(selected.dst) }}</span>
                        </p>
                    </div>
                    <a :href="`https://www.youtube.com/watch?v=${selected.videoId}`" target="_blank"
                        class="flex items-center justify-center space-x-2 w-full py-2.5 bg-red-600 hover:bg-red-700 text-white rounded-xl font-medium transition text-sm">
                        <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><path d="M19.615 3.184c-3.604-.246-11.631-.245-15.23 0-3.897.266-4.356 2.62-4.385 8.816.029 6.185.484 8.549 4.385 8.816 3.6.245 11.626.246 15.23 0 3.897-.266 4.356-2.62 4.385-8.816-.029-6.185-.484-8.549-4.385-8.816zm-10.615 12.816v-8l8 3.993-8 4.007z"/></svg>
                        <span>Watch on YouTube</span>
                    </a>
                </div>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

// ---- DST Formula (View-based + Interest Penalty) ----
// Base Tax = (views / 1000) * 0.15
// Interest = Base * 10% * Months Since Upload
const DST_PER_MILLION = 150
const INTEREST_RATE = 0.10

// ---- State ----
const loading    = ref(false)
const error      = ref('')
const videoRows  = ref([])
const compliance = ref(null)
const search     = ref('')
const filter     = ref('all')
const page       = ref(1)
const perPage    = ref(25)
const selected   = ref(null)

// ---- Month info ----
const now          = new Date()
const currentMonthName = now.toLocaleDateString('en-US', { month: 'long', year: 'numeric' })
const daysInMonth  = new Date(now.getFullYear(), now.getMonth() + 1, 0).getDate()
const daysElapsed  = now.getDate()
const monthPct     = computed(() => Math.round((daysElapsed / daysInMonth) * 100))

// ---- Derived collections ----
const shortVideos = computed(() => videoRows.value.filter(r => r.isShort))
const longVideos  = computed(() => videoRows.value.filter(r => !r.isShort))

const filteredRows = computed(() => {
    let rows = videoRows.value
    if (filter.value === 'shorts') rows = rows.filter(r => r.isShort)
    if (filter.value === 'long')   rows = rows.filter(r => !r.isShort)
    if (search.value) {
        const q = search.value.toLowerCase()
        rows = rows.filter(r => r.title?.toLowerCase().includes(q))
    }
    return rows
})

const totalPages    = computed(() => Math.max(1, Math.ceil(filteredRows.value.length / perPage.value)))
const paginatedRows = computed(() => {
    const s = (page.value - 1) * perPage.value
    return filteredRows.value.slice(s, s + perPage.value)
})
watch([search, filter, perPage], () => { page.value = 1 })

// ---- Totals ----
const totalViews   = computed(() => videoRows.value.reduce((s, r) => s + r.viewCount, 0))
const totalRevenue = computed(() => videoRows.value.reduce((s, r) => s + r.estRevenue, 0))
const totalDST     = computed(() => videoRows.value.reduce((s, r) => s + r.dst, 0))
const avgTaxPerVideo = computed(() => videoRows.value.length ? totalDST.value / videoRows.value.length : 0)
const officialOutstandingTax = computed(() => Number(compliance.value?.outstandingTax || 0))
const avgOfficialTaxPerVideo = computed(() => {
    if (!videoRows.value.length) return officialOutstandingTax.value
    return officialOutstandingTax.value / videoRows.value.length
})

// ---- Fetch ----
const loadData = async () => {
    loading.value = true
    error.value   = ''
    videoRows.value = []

    const artistId = localStorage.getItem('artistID')
    if (!artistId) {
        error.value = 'Artist session not found. Please log in again.'
        loading.value = false
        return
    }

    try {
        const complianceData = await $fetch(`http://localhost:8080/api/compliance/${artistId}`)
        compliance.value = complianceData

        const chanData = await $fetch(`http://localhost:8080/api/youtube/channel?artistId=${artistId}`)
        if (!chanData?.channelId) {
            error.value = 'YouTube channel not connected. Please authorise your channel from the dashboard.'
            loading.value = false
            return
        }

        const vidData = await $fetch(`http://localhost:8080/api/youtube/videos/${chanData.channelId}?artistId=${artistId}`)
        const videos  = vidData?.videos || []

        videoRows.value = videos.map(v => {
            // Calculate DST with interest
            const views         = parseInt(v.viewCount) || 0
            const months        = calculateMonthsSince(v.publishedAt)
            const baseTax       = (views / 1000) * (DST_PER_MILLION / 1000)
            const interest      = baseTax * INTEREST_RATE * months
            const dst           = baseTax + interest
            const estRevenue    = (views / 1000) * (1500 / 1000)

            return {
                videoId:      v.videoId || v.id,
                title:        v.title,
                publishedAt:  v.publishedAt,
                duration:     v.duration,
                viewCount:    views,
                likeCount:    parseInt(v.likeCount)    || 0,
                commentCount: parseInt(v.commentCount) || 0,
                isShort:      isShort(v.duration),
                estRevenue,
                baseTax,
                interest,
                monthsOverdue: months,
                dst
            }
        }).sort((a, b) => b.viewCount - a.viewCount)

    } catch (e) {
        const msg = e?.data?.error || e?.message || 'Unknown error'
        error.value = `Could not load channel data: ${msg}`
    } finally {
        loading.value = false
    }
}

onMounted(loadData)

// ---- Utilities ----
const isShort = (duration) => {
    if (!duration) return false
    if (duration.includes('H')) return false
    if (duration.includes('M')) {
        const m = parseInt(duration.match(/(\d+)M/)?.[1] || '0')
        if (m > 1) return false
        if (m === 1) return duration.includes('S') ? parseInt(duration.match(/(\d+)S/)?.[1] || '0') === 0 : true
    }
    return true
}
const formatDuration = (iso) => {
    if (!iso) return '—'
    const h = iso.match(/(\d+)H/)?.[1], m = iso.match(/(\d+)M/)?.[1], s = iso.match(/(\d+)S/)?.[1]
    const pad = n => String(n || 0).padStart(2, '0')
    return h ? `${h}:${pad(m)}:${pad(s)}` : `${m || 0}:${pad(s)}`
}
const formatNumber = (n) => {
    if (!n && n !== 0) return '—'
    const num = parseInt(n)
    if (num >= 1000000) return (num / 1000000).toFixed(1) + 'M'
    if (num >= 1000)    return (num / 1000).toFixed(1) + 'K'
    return num.toLocaleString()
}
const formatCurrency = (v) => {
    if (!v && v !== 0) return '0.00'
    const val = parseFloat(v)
    if (val > 0 && val < 0.01) {
        return val.toLocaleString('en-US', { minimumFractionDigits: 4, maximumFractionDigits: 4 })
    }
    return val.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}
const calculateMonthsSince = (dateStr) => {
    if (!dateStr) return 0
    const uploadDate = new Date(dateStr)
    const now = new Date()
    const months = (now.getFullYear() - uploadDate.getFullYear()) * 12 + (now.getMonth() - uploadDate.getMonth())
    return Math.max(0, months)
}

const formatDate = (d) => {
    if (!d) return '—'
    return new Date(d).toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' })
}

const exportCSV = () => {
    if (!filteredRows.value.length) return
    let csv = 'Video Title,Published,Type,Views,Likes,Comments,Duration,Est Revenue ($),DST 10% ($)\n'
    filteredRows.value.forEach(r => {
        csv += `"${r.title}","${formatDate(r.publishedAt)}","${r.isShort ? 'Short' : 'Video'}",${r.viewCount},${r.likeCount},${r.commentCount},"${formatDuration(r.duration)}",${r.estRevenue.toFixed(2)},${r.dst.toFixed(2)}\n`
    })
    const a = document.createElement('a')
    a.href = URL.createObjectURL(new Blob([csv], { type: 'text/csv' }))
    a.download = `my-tax-records-${new Date().toISOString().split('T')[0]}.csv`
    a.click()
}
</script>

<style scoped>
.line-clamp-2 { display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; }
::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: #1f2937; }
::-webkit-scrollbar-thumb { background: #4b5563; border-radius: 3px; }
</style>