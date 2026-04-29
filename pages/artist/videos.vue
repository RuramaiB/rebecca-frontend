<template>
    <NuxtLayout name="artist-layout">
        <div class="min-h-screen bg-gray-950 pb-12">
            <!-- Header -->
            <div class="flex items-center justify-between mb-8">
                <div>
                    <h2 class="text-2xl font-bold text-white mb-1">My Videos & Channel</h2>
                    <p class="text-gray-400 text-sm">Live data from your YouTube channel · Used to calculate your Digital Service Tax</p>
                </div>
                <div class="flex items-center space-x-3">
                    <!-- View Toggle -->
                    <div class="flex items-center bg-gray-800 border border-gray-700 rounded-lg p-1">
                        <button @click="activeTab = 'all'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeTab === 'all' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">All Videos</button>
                        <button @click="activeTab = 'shorts'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeTab === 'shorts' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">Shorts</button>
                        <button @click="activeTab = 'long'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', activeTab === 'long' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">Long-form</button>
                    </div>
                    <button @click="fetchData" :disabled="loading"
                        class="bg-red-600 hover:bg-red-700 disabled:opacity-50 text-white px-4 py-2 rounded-lg text-sm font-medium transition flex items-center space-x-2">
                        <svg class="w-4 h-4" :class="{ 'animate-spin': loading }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
                        </svg>
                        <span>{{ loading ? 'Syncing...' : 'Sync Channel' }}</span>
                    </button>
                </div>
            </div>

            <!-- Global Error -->
            <div v-if="error" class="mb-6 p-4 bg-red-500/10 border border-red-500/30 rounded-xl text-red-400 text-sm flex items-center space-x-2">
                <svg class="w-5 h-5 shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/></svg>
                <span>{{ error }}</span>
            </div>

            <!-- ========== TAX FORECAST BANNER ========== -->
            <div class="mb-6 bg-gradient-to-r from-red-950/50 via-gray-900 to-gray-900 border border-red-900/30 rounded-2xl p-6 relative overflow-hidden">
                <div class="absolute inset-0 bg-gradient-to-r from-red-600/5 to-transparent pointer-events-none"></div>
                <div class="relative z-10 flex items-center justify-between gap-6">
                    <div class="flex-1">
                        <div class="flex items-center space-x-2 mb-1">
                            <svg class="w-4 h-4 text-red-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"/></svg>
                            <span class="text-xs text-red-400 font-semibold uppercase tracking-wider">Tax Forecast · {{ currentMonthName }}</span>
                        </div>
                        <p class="text-3xl font-black text-white">${{ formatCurrency(estimatedMonthEndTax) }}</p>
                        <p class="text-gray-400 text-sm mt-1">Estimated Digital Service Tax due by end of {{ currentMonthName }}</p>
                    </div>

                    <div class="flex items-center space-x-6">
                        <div class="text-center">
                            <p class="text-xs text-gray-500 mb-1">Days Elapsed</p>
                            <p class="text-xl font-bold text-white">{{ daysElapsed }}<span class="text-gray-500 text-sm">/{{ daysInMonth }}</span></p>
                            <div class="w-24 h-1.5 bg-gray-800 rounded-full mt-1.5">
                                <div class="h-full bg-red-600 rounded-full transition-all" :style="`width: ${monthProgress}%`"></div>
                            </div>
                        </div>
                        <div class="text-center">
                            <p class="text-xs text-gray-500 mb-1">Tax Rate</p>
                            <p class="text-xl font-bold text-yellow-400">10%</p>
                            <p class="text-xs text-gray-500">DST</p>
                        </div>
                        <div class="text-center">
                            <p class="text-xs text-gray-500 mb-1">Projected Revenue</p>
                            <p class="text-xl font-bold text-green-400">${{ formatCurrency(projectedMonthRevenue) }}</p>
                            <p class="text-xs text-gray-500">by month end</p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Loading skeleton -->
            <div v-if="loading && !channelData" class="space-y-6">
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div v-for="i in 4" :key="i" class="bg-gray-900 border border-gray-800 rounded-xl p-5 animate-pulse">
                        <div class="h-3 bg-gray-800 rounded w-2/3 mb-3"></div>
                        <div class="h-8 bg-gray-800 rounded w-1/2"></div>
                    </div>
                </div>
            </div>

            <template v-else>
                <!-- ========== CHANNEL STATS ========== -->
                <div v-if="channelData" class="mb-6 bg-gray-900 border border-gray-800 rounded-2xl p-6">
                    <div class="flex items-center space-x-4 mb-5">
                        <div class="w-14 h-14 rounded-2xl bg-gradient-to-br from-red-600 to-red-800 flex items-center justify-center shadow-lg">
                            <svg class="w-7 h-7 text-white" fill="currentColor" viewBox="0 0 24 24">
                                <path d="M19.615 3.184c-3.604-.246-11.631-.245-15.23 0-3.897.266-4.356 2.62-4.385 8.816.029 6.185.484 8.549 4.385 8.816 3.6.245 11.626.246 15.23 0 3.897-.266 4.356-2.62 4.385-8.816-.029-6.185-.484-8.549-4.385-8.816zm-10.615 12.816v-8l8 3.993-8 4.007z"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="text-lg font-bold text-white">{{ channelData.title }}</h3>
                            <p class="text-gray-400 text-sm truncate max-w-md">{{ channelData.description?.substring(0, 100) || 'YouTube Channel' }}{{ channelData.description?.length > 100 ? '...' : '' }}</p>
                        </div>
                    </div>
                    <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                        <div class="bg-gray-800/50 rounded-xl p-4">
                            <p class="text-gray-500 text-xs mb-1">Subscribers</p>
                            <p class="text-2xl font-bold text-white">{{ formatNumber(channelData.subscriberCount) }}</p>
                        </div>
                        <div class="bg-gray-800/50 rounded-xl p-4">
                            <p class="text-gray-500 text-xs mb-1">Total Videos</p>
                            <p class="text-2xl font-bold text-white">{{ formatNumber(channelData.videoCount) }}</p>
                        </div>
                        <div class="bg-gray-800/50 rounded-xl p-4">
                            <p class="text-gray-500 text-xs mb-1">Total Views</p>
                            <p class="text-2xl font-bold text-white">{{ formatNumber(channelData.viewCount) }}</p>
                        </div>
                        <div class="bg-gray-800/50 rounded-xl p-4">
                            <p class="text-gray-500 text-xs mb-1">Synced Videos</p>
                            <p class="text-2xl font-bold text-white">{{ videos.length }}</p>
                        </div>
                    </div>
                </div>

                <!-- ========== CONTENT BREAKDOWN ========== -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-5">
                        <div class="flex items-center justify-between mb-3">
                            <p class="text-gray-400 text-xs uppercase tracking-wider font-semibold">All Content</p>
                            <span class="text-2xl font-black text-white">{{ videos.length }}</span>
                        </div>
                        <div class="w-full h-1.5 bg-gray-800 rounded-full">
                            <div class="h-full bg-gradient-to-r from-red-600 to-red-400 rounded-full" style="width: 100%"></div>
                        </div>
                    </div>
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-5">
                        <div class="flex items-center justify-between mb-3">
                            <p class="text-gray-400 text-xs uppercase tracking-wider font-semibold">Regular Videos</p>
                            <span class="text-2xl font-black text-white">{{ longFormVideos.length }}</span>
                        </div>
                        <div class="w-full h-1.5 bg-gray-800 rounded-full">
                            <div class="h-full bg-blue-500 rounded-full transition-all" :style="`width: ${videos.length ? (longFormVideos.length / videos.length * 100) : 0}%`"></div>
                        </div>
                        <p class="text-xs text-gray-500 mt-1">{{ videos.length ? Math.round(longFormVideos.length / videos.length * 100) : 0 }}% of content</p>
                    </div>
                    <div class="bg-red-900/10 border border-red-900/20 rounded-xl p-5">
                        <div class="flex items-center justify-between mb-3">
                            <p class="text-red-400/80 text-xs uppercase tracking-wider font-semibold">Shorts (&lt;60s)</p>
                            <span class="text-2xl font-black text-red-400">{{ shortVideos.length }}</span>
                        </div>
                        <div class="w-full h-1.5 bg-gray-800 rounded-full">
                            <div class="h-full bg-red-500 rounded-full transition-all" :style="`width: ${videos.length ? (shortVideos.length / videos.length * 100) : 0}%`"></div>
                        </div>
                        <p class="text-xs text-red-400/50 mt-1">{{ videos.length ? Math.round(shortVideos.length / videos.length * 100) : 0 }}% of content · contribute to DST</p>
                    </div>
                </div>

                <!-- ========== VIDEO GRID ========== -->
                <div v-if="filteredVideos.length === 0 && !loading" class="bg-gray-900 border border-gray-800 rounded-2xl p-16 text-center">
                    <svg class="w-16 h-16 text-gray-700 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.069A1 1 0 0121 8.871v6.258a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/></svg>
                    <p class="text-gray-400 font-medium">No videos found</p>
                    <p class="text-gray-600 text-sm mt-1">Click "Sync Channel" to fetch your latest videos from YouTube</p>
                </div>

                <div v-else>
                    <div class="flex items-center justify-between mb-4">
                        <h3 class="text-white font-semibold">{{ tabLabel }} <span class="text-gray-500 font-normal text-sm">({{ filteredVideos.length }})</span></h3>
                        <div class="relative">
                            <input v-model="searchQuery" placeholder="Search videos..." 
                                class="bg-gray-800 border border-gray-700 rounded-lg px-4 py-2 text-sm text-white placeholder-gray-500 focus:outline-none focus:border-red-500 w-60" />
                        </div>
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
                        <div v-for="video in searchedVideos" :key="video.videoId || video.id"
                            class="bg-gray-900 border border-gray-800 rounded-xl overflow-hidden hover:border-gray-700 transition-all group cursor-pointer"
                            @click="selectedVideo = video">
                            
                            <!-- Thumbnail -->
                            <div class="relative aspect-video bg-gray-800 overflow-hidden">
                                <img 
                                    :src="`https://img.youtube.com/vi/${video.videoId || video.id}/hqdefault.jpg`"
                                    :alt="video.title"
                                    class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500"
                                    @error="e => e.target.style.display='none'"
                                />
                                <!-- Shorts badge -->
                                <div v-if="isShort(video.duration)" class="absolute top-2 left-2 bg-red-600 text-white text-[9px] font-bold px-1.5 py-0.5 rounded">
                                    SHORT
                                </div>
                                <!-- Duration badge -->
                                <div class="absolute bottom-2 right-2 bg-black/70 text-white text-[10px] px-1.5 py-0.5 rounded font-mono">
                                    {{ formatDuration(video.duration) }}
                                </div>
                            </div>

                            <!-- Info -->
                            <div class="p-3">
                                <p class="text-white text-xs font-semibold line-clamp-2 mb-2 leading-snug">{{ video.title }}</p>
                                <div class="grid grid-cols-3 gap-1 text-center">
                                    <div class="bg-gray-800/50 rounded p-1.5">
                                        <p class="text-[9px] text-gray-500">Views</p>
                                        <p class="text-xs font-bold text-white">{{ formatNumber(video.viewCount) }}</p>
                                    </div>
                                    <div class="bg-gray-800/50 rounded p-1.5">
                                        <p class="text-[9px] text-gray-500">Likes</p>
                                        <p class="text-xs font-bold text-white">{{ formatNumber(video.likeCount) }}</p>
                                    </div>
                                    <div class="bg-gray-800/50 rounded p-1.5">
                                        <p class="text-[9px] text-gray-500">Comments</p>
                                        <p class="text-xs font-bold text-white">{{ formatNumber(video.commentCount) }}</p>
                                    </div>
                                </div>
                                <p class="text-gray-600 text-[9px] mt-2">Published {{ formatDate(video.publishedAt) }}</p>
                            </div>
                        </div>
                    </div>
                </div>
            </template>

            <!-- ========== VIDEO DETAIL MODAL ========== -->
            <div v-if="selectedVideo" class="fixed inset-0 bg-black/70 z-50 flex items-center justify-center p-4 backdrop-blur-sm" @click.self="selectedVideo = null">
                <div class="bg-gray-900 border border-gray-700 rounded-2xl w-full max-w-2xl max-h-[90vh] overflow-y-auto shadow-2xl">
                    <div class="p-6 border-b border-gray-800 flex items-start justify-between">
                        <div class="flex-1 pr-4">
                            <div class="flex items-center space-x-2 mb-2">
                                <span v-if="isShort(selectedVideo.duration)" class="bg-red-600 text-white text-[10px] font-bold px-2 py-0.5 rounded">SHORT</span>
                                <span class="bg-gray-800 text-gray-400 text-[10px] font-mono px-2 py-0.5 rounded">{{ formatDuration(selectedVideo.duration) }}</span>
                            </div>
                            <h3 class="text-white font-bold text-lg leading-snug">{{ selectedVideo.title }}</h3>
                        </div>
                        <button @click="selectedVideo = null" class="text-gray-400 hover:text-white transition">
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                        </button>
                    </div>
                    <div class="p-6 space-y-6">
                        <!-- Thumbnail Preview -->
                        <div class="aspect-video bg-gray-800 rounded-xl overflow-hidden">
                            <img :src="`https://img.youtube.com/vi/${selectedVideo.videoId || selectedVideo.id}/maxresdefault.jpg`" class="w-full h-full object-cover" />
                        </div>

                        <!-- Stats grid -->
                        <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
                            <div class="bg-gray-800/50 rounded-xl p-3 text-center">
                                <p class="text-gray-500 text-xs mb-1">Views</p>
                                <p class="text-xl font-black text-white">{{ formatNumber(selectedVideo.viewCount) }}</p>
                            </div>
                            <div class="bg-gray-800/50 rounded-xl p-3 text-center">
                                <p class="text-gray-500 text-xs mb-1">Likes</p>
                                <p class="text-xl font-black text-green-400">{{ formatNumber(selectedVideo.likeCount) }}</p>
                            </div>
                            <div class="bg-gray-800/50 rounded-xl p-3 text-center">
                                <p class="text-gray-500 text-xs mb-1">Comments</p>
                                <p class="text-xl font-black text-blue-400">{{ formatNumber(selectedVideo.commentCount) }}</p>
                            </div>
                            <div class="bg-gray-800/50 rounded-xl p-3 text-center">
                                <p class="text-gray-500 text-xs mb-1">Duration</p>
                                <p class="text-xl font-black text-yellow-400">{{ formatDuration(selectedVideo.duration) }}</p>
                            </div>
                        </div>

                        <!-- Tax Implication -->
                        <div class="bg-red-950/20 border border-red-900/30 rounded-xl p-4">
                            <p class="text-red-400 text-xs font-semibold uppercase tracking-wider mb-2">Tax Implication</p>
                            <p class="text-gray-300 text-sm">This {{ isShort(selectedVideo.duration) ? 'Short' : 'video' }} contributes to your <span class="text-white font-semibold">Digital Service Tax (DST)</span> calculation for {{ currentMonthName }}.</p>
                            <p class="text-gray-500 text-xs mt-2">Estimated per-video tax contribution: <span class="text-red-400 font-mono">${{ formatCurrency(estimatedTaxPerVideo) }}</span></p>
                        </div>

                        <!-- Link to YouTube -->
                        <a :href="`https://www.youtube.com/watch?v=${selectedVideo.videoId || selectedVideo.id}`" target="_blank"
                            class="flex items-center justify-center space-x-2 w-full py-3 bg-red-600 hover:bg-red-700 text-white rounded-xl font-medium transition text-sm">
                            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><path d="M19.615 3.184c-3.604-.246-11.631-.245-15.23 0-3.897.266-4.356 2.62-4.385 8.816.029 6.185.484 8.549 4.385 8.816 3.6.245 11.626.246 15.23 0 3.897-.266 4.356-2.62 4.385-8.816-.029-6.185-.484-8.549-4.385-8.816zm-10.615 12.816v-8l8 3.993-8 4.007z"/></svg>
                            <span>Watch on YouTube</span>
                        </a>
                    </div>
                </div>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

// ---- DST Formula (No 10% tax rate anymore, just view-based) ----
// Tax = (viewCount / 1000) * (DST_PER_MILLION / 1000)
const DST_PER_MILLION = 150 // $150 per 1,000,000 views (equiv to previous 10% of $1.50 CPM)

// ----- Refs -----
const loading = ref(false)
const error = ref('')
const channelData = ref(null)
const videos = ref([])
const activeTab = ref('all')
const searchQuery = ref('')
const selectedVideo = ref(null)

const TAX_RATE = 0.10

// ----- Current Month Info  -----
const now = new Date()
const currentMonthName = now.toLocaleDateString('en-US', { month: 'long', year: 'numeric' })
const daysInMonth = new Date(now.getFullYear(), now.getMonth() + 1, 0).getDate()
const daysElapsed = now.getDate()
const monthProgress = computed(() => Math.round((daysElapsed / daysInMonth) * 100))

// ----- Derived Video Lists -----
const shortVideos = computed(() => videos.value.filter(v => isShort(v.duration)))
const longFormVideos = computed(() => videos.value.filter(v => !isShort(v.duration)))

const filteredVideos = computed(() => {
    if (activeTab.value === 'shorts') return shortVideos.value
    if (activeTab.value === 'long') return longFormVideos.value
    return videos.value
})

const searchedVideos = computed(() => {
    if (!searchQuery.value) return filteredVideos.value
    return filteredVideos.value.filter(v => v.title?.toLowerCase().includes(searchQuery.value.toLowerCase()))
})

const tabLabel = computed(() => {
    if (activeTab.value === 'shorts') return 'Shorts'
    if (activeTab.value === 'long') return 'Long-form Videos'
    return 'All Videos'
})

// ----- Tax Forecast (view-based, no thresholds) -----
// Total DST = (sum of all video views / 1000) * (DST_PER_MILLION / 1000)
const totalViews = computed(() => videos.value.reduce((s, v) => s + (parseInt(v.viewCount) || 0), 0))
const totalDST = computed(() => (totalViews.value / 1000) * (DST_PER_MILLION / 1000))

// Per-video avg DST (shown in modal and forecast widget)
const estimatedTaxPerVideo = computed(() => {
    if (!videos.value.length) return 0
    return totalDST.value / videos.value.length
})

// The projected month-end tax = total DST across all current videos
// (no extrapolation needed — each video's DST is already based on actual views)
const projectedMonthRevenue = computed(() => (totalViews.value / 1000) * (1500 / 1000)) // Keep $1.50 CPM for revenue display
const estimatedMonthEndTax = computed(() => totalDST.value)

// ----- Data Fetching -----
const fetchData = async () => {
    loading.value = true
    error.value = ''

    const artistId = localStorage.getItem('artistID')
    if (!artistId) {
        error.value = 'Artist session not found. Please log in again.'
        loading.value = false
        return
    }

    try {
        // 1. Fetch channel info
        const chanData = await $fetch(`http://localhost:8080/api/youtube/channel?artistId=${artistId}`)
        channelData.value = chanData

        // 2. Fetch videos — revenue & DST are calculated client-side from view counts
        if (chanData?.channelId) {
            const vidData = await $fetch(`http://localhost:8080/api/youtube/videos/${chanData.channelId}?artistId=${artistId}`)
            videos.value = vidData?.videos || []
        } else {
            error.value = 'YouTube channel not connected. Please authorise your channel from the dashboard.'
        }
    } catch (e) {
        const msg = e?.data?.error || e?.message || 'Failed to connect to backend'
        error.value = `Could not load channel data: ${msg}`
    } finally {
        loading.value = false
    }
}

// ----- Utilities -----
const isShort = (duration) => {
    if (!duration) return false
    if (duration.includes('H')) return false
    if (duration.includes('M')) {
        const mIdx = duration.indexOf('M')
        const tIdx = duration.indexOf('T')
        const mins = parseInt(duration.substring(tIdx + 1, mIdx))
        if (mins > 1) return false
        if (mins === 1) {
            if (duration.includes('S')) {
                const secs = parseInt(duration.substring(mIdx + 1, duration.indexOf('S')))
                return secs === 0
            }
            return true
        }
    }
    return true
}

const formatDuration = (iso) => {
    if (!iso) return '?:??'
    const h = iso.match(/(\d+)H/)?.[1]
    const m = iso.match(/(\d+)M/)?.[1]
    const s = iso.match(/(\d+)S/)?.[1]
    const pad = n => String(n || 0).padStart(2, '0')
    if (h) return `${h}:${pad(m)}:${pad(s)}`
    return `${m || 0}:${pad(s)}`
}

const formatNumber = (n) => {
    if (!n && n !== 0) return '—'
    const num = parseInt(n)
    if (num >= 1000000) return (num / 1000000).toFixed(1) + 'M'
    if (num >= 1000) return (num / 1000).toFixed(1) + 'K'
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

const formatDate = (d) => {
    if (!d) return '—'
    return new Date(d).toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' })
}

onMounted(fetchData)
</script>

<style scoped>
.line-clamp-2 {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
</style>
