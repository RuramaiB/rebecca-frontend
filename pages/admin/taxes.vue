<template>
    <NuxtLayout name="admin-layout">
        <div class="min-h-screen bg-gray-950 pb-12">

            <!-- Header -->
            <div class="flex items-center justify-between mb-6">
                <div>
                    <h2 class="text-2xl font-bold text-white mb-1">Tax Records</h2>
                    <p class="text-gray-400 text-sm">All artist video data, viewership statistics, and calculated Digital Service Tax</p>
                </div>
                <div class="flex items-center space-x-3">
                    <!-- View Toggle -->
                    <div class="flex items-center bg-gray-800 border border-gray-700 rounded-lg p-1">
                        <button @click="viewMode = 'table'" :class="['px-3 py-1.5 rounded-md text-xs font-medium flex items-center space-x-1.5 transition-all', viewMode === 'table' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">
                            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 10h18M3 14h18M10 6h4M10 18h4M3 6h4M17 6h4M3 18h4M17 18h4"/></svg>
                            <span>Video Table</span>
                        </button>
                        <button @click="viewMode = 'cards'" :class="['px-3 py-1.5 rounded-md text-xs font-medium flex items-center space-x-1.5 transition-all', viewMode === 'cards' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">
                            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z"/></svg>
                            <span>Period Cards</span>
                        </button>
                    </div>

                    <button @click="loadVideoTable" :disabled="videoLoading" v-if="viewMode === 'table'"
                        class="bg-red-600 hover:bg-red-700 disabled:opacity-50 text-white px-4 py-2 rounded-lg text-sm font-medium flex items-center space-x-2 transition">
                        <svg class="w-4 h-4" :class="{ 'animate-spin': videoLoading }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
                        </svg>
                        <span>{{ videoLoading ? 'Loading...' : 'Refresh' }}</span>
                    </button>
                    <button @click="refreshData" :disabled="pending" v-else
                        class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2 rounded-lg text-sm font-medium flex items-center space-x-2 transition">
                        <svg class="w-4 h-4" :class="{ 'animate-spin': pending }" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/>
                        </svg>
                        <span>Refresh</span>
                    </button>
                    <button @click="exportToCSV" class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2 rounded-lg text-sm font-medium flex items-center space-x-2 transition">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"/></svg>
                        <span>Export CSV</span>
                    </button>
                </div>
            </div>

            <!-- ========== SUMMARY STAT TILES ========== -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4 mb-6">
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                    <p class="text-gray-500 text-xs mb-1">Total Records</p>
                    <p class="text-2xl font-black text-white">{{ taxRecords.length }}</p>
                </div>
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-4">
                    <p class="text-gray-500 text-xs mb-1">Total Videos Tracked</p>
                    <p class="text-2xl font-black text-white">{{ totalVideoCount }}</p>
                </div>
                <div class="bg-red-900/10 border border-red-900/20 rounded-xl p-4">
                    <p class="text-red-400/70 text-xs mb-1">Total Tax Generated</p>
                    <p class="text-2xl font-black text-red-400">${{ formatCurrency(totalTaxGenerated) }}</p>
                </div>
                <div class="bg-green-900/10 border border-green-900/20 rounded-xl p-4">
                    <p class="text-green-400/70 text-xs mb-1">Total Gross Revenue</p>
                    <p class="text-2xl font-black text-green-400">${{ formatCurrency(totalRevenue) }}</p>
                </div>
            </div>

            <!-- ========== VIDEO TABLE VIEW ========== -->
            <template v-if="viewMode === 'table'">
                <!-- Filter + search row -->
                <div class="flex items-center justify-between mb-4 gap-3">
                    <div class="relative flex-1">
                        <svg class="absolute left-3 top-1/2 -translate-y-1/2 w-4 h-4 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"/></svg>
                        <input v-model="videoSearch" placeholder="Search by artist or video title..."
                            class="w-full bg-gray-900 border border-gray-800 rounded-xl pl-10 pr-4 py-2.5 text-sm text-white placeholder-gray-500 focus:outline-none focus:border-red-500 transition" />
                    </div>
                    <div class="flex items-center bg-gray-800 border border-gray-700 rounded-lg p-1">
                        <button @click="videoFilter = 'all'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', videoFilter === 'all' ? 'bg-gray-700 text-white' : 'text-gray-400 hover:text-white']">All</button>
                        <button @click="videoFilter = 'shorts'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', videoFilter === 'shorts' ? 'bg-red-600 text-white' : 'text-gray-400 hover:text-white']">Shorts</button>
                        <button @click="videoFilter = 'longform'" :class="['px-3 py-1.5 rounded-md text-xs font-medium transition-all', videoFilter === 'longform' ? 'bg-blue-600 text-white' : 'text-gray-400 hover:text-white']">Long-form</button>
                    </div>
                    <p class="text-gray-500 text-xs whitespace-nowrap">{{ filteredVideoRows.length }} videos</p>
                </div>

                <!-- Loading -->
                <div v-if="videoLoading" class="bg-gray-900 border border-gray-800 rounded-2xl p-16 text-center">
                    <div class="inline-block animate-spin rounded-full h-10 w-10 border-4 border-gray-700 border-t-red-600 mb-4"></div>
                    <p class="text-gray-400">Loading video data from all artist channels...</p>
                </div>

                <!-- No data -->
                <div v-else-if="videoRows.length === 0" class="bg-gray-900 border border-gray-800 rounded-2xl p-16 text-center">
                    <svg class="w-16 h-16 text-gray-700 mx-auto mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.069A1 1 0 0121 8.871v6.258a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z"/></svg>
                    <p class="text-gray-400 font-medium mb-1">No video data loaded</p>
                    <p class="text-gray-600 text-sm mb-5">Click "Refresh" to fetch all artist video data from YouTube</p>
                    <button @click="loadVideoTable" class="bg-red-600 hover:bg-red-700 text-white px-5 py-2.5 rounded-xl text-sm font-medium transition">Load Video Data</button>
                </div>

                <!-- TABLE -->
                <div v-else class="bg-gray-900 border border-gray-800 rounded-2xl overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full">
                            <thead>
                                <tr class="border-b border-gray-800 bg-gray-950/50">
                                    <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider w-8">#</th>
                                    <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Thumbnail</th>
                                    <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Video Title</th>
                                    <th class="text-left py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Artist</th>
                                    <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Views</th>
                                    <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Likes</th>
                                    <th class="text-right py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Comments</th>
                                    <th class="text-center py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Type</th>
                                    <th class="text-center py-3 px-4 text-[10px] font-semibold text-gray-400 uppercase tracking-wider">Duration</th>
                                    <th class="text-right py-3 px-4 text-[10px] font-semibold text-red-400 uppercase tracking-wider">Tax Due (DST)</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-800/50">
                                <tr v-for="(row, idx) in paginatedVideoRows" :key="`${row.artistId}_${row.videoId}`"
                                    class="hover:bg-gray-800/30 transition-colors group">
                                    <td class="py-3 px-4 text-gray-600 text-xs">{{ (videoPage - 1) * videoPerPage + idx + 1 }}</td>
                                    <!-- Thumbnail -->
                                    <td class="py-3 px-4">
                                        <div class="w-16 h-9 bg-gray-800 rounded overflow-hidden flex-shrink-0">
                                            <img :src="`https://img.youtube.com/vi/${row.videoId}/default.jpg`"
                                                class="w-full h-full object-cover"
                                                @error="e => e.target.parentElement.classList.add('flex','items-center','justify-center')"
                                            />
                                        </div>
                                    </td>
                                    <!-- Title -->
                                    <td class="py-3 px-4 max-w-xs">
                                        <p class="text-white text-xs font-medium line-clamp-2 leading-snug">{{ row.title }}</p>
                                        <p class="text-gray-600 text-[10px] mt-0.5">{{ formatDate(row.publishedAt) }}</p>
                                    </td>
                                    <!-- Artist -->
                                    <td class="py-3 px-4">
                                        <div class="flex items-center space-x-2">
                                            <div class="w-7 h-7 rounded-full bg-gradient-to-br from-red-600 to-red-800 flex items-center justify-center text-white text-[9px] font-black flex-shrink-0">
                                                {{ row.artistName?.split(' ').map(n => n[0]).join('').slice(0, 2).toUpperCase() }}
                                            </div>
                                            <div>
                                                <p class="text-white text-xs font-semibold leading-tight">{{ row.artistName }}</p>
                                                <p class="text-gray-500 text-[10px] truncate max-w-[120px]">{{ row.artistEmail }}</p>
                                            </div>
                                        </div>
                                    </td>
                                    <!-- Views -->
                                    <td class="py-3 px-4 text-right">
                                        <span class="text-white text-xs font-bold">{{ formatNumber(row.viewCount) }}</span>
                                    </td>
                                    <!-- Likes -->
                                    <td class="py-3 px-4 text-right">
                                        <span class="text-green-400 text-xs font-bold">{{ formatNumber(row.likeCount) }}</span>
                                    </td>
                                    <!-- Comments -->
                                    <td class="py-3 px-4 text-right">
                                        <span class="text-blue-400 text-xs font-bold">{{ formatNumber(row.commentCount) }}</span>
                                    </td>
                                    <!-- Type -->
                                    <td class="py-3 px-4 text-center">
                                        <span v-if="row.isShort" class="bg-red-600/20 text-red-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-red-600/30">SHORT</span>
                                        <span v-else class="bg-blue-600/20 text-blue-400 text-[9px] font-bold px-2 py-0.5 rounded-full border border-blue-600/30">VIDEO</span>
                                    </td>
                                    <!-- Duration -->
                                    <td class="py-3 px-4 text-center">
                                        <span class="text-gray-400 text-xs font-mono">{{ formatDuration(row.duration) }}</span>
                                    </td>
                                    <!-- Tax -->
                                    <td class="py-3 px-4 text-right">
                                        <div class="flex flex-col items-end">
                                            <span class="text-red-400 text-sm font-black">${{ formatCurrency(row.estimatedTax) }}</span>
                                            <span class="text-gray-600 text-[9px]">10% DST</span>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                            <!-- Footer totals -->
                            <tfoot>
                                <tr class="border-t-2 border-gray-700 bg-gray-950">
                                    <td colspan="4" class="py-3 px-4 text-xs text-gray-400 font-semibold">
                                        Showing {{ (videoPage-1)*videoPerPage+1 }}–{{ Math.min(videoPage*videoPerPage, filteredVideoRows.length) }} of {{ filteredVideoRows.length }} videos
                                    </td>
                                    <td class="py-3 px-4 text-right text-xs font-bold text-white">{{ formatNumber(filteredVideoRows.reduce((s,r) => s+(r.viewCount||0), 0)) }}</td>
                                    <td class="py-3 px-4 text-right text-xs font-bold text-green-400">{{ formatNumber(filteredVideoRows.reduce((s,r) => s+(r.likeCount||0), 0)) }}</td>
                                    <td class="py-3 px-4 text-right text-xs font-bold text-blue-400">{{ formatNumber(filteredVideoRows.reduce((s,r) => s+(r.commentCount||0), 0)) }}</td>
                                    <td colspan="2" class="py-3 px-4"></td>
                                    <td class="py-3 px-4 text-right text-sm font-black text-red-400">${{ formatCurrency(filteredVideoRows.reduce((s,r) => s+r.estimatedTax, 0)) }}</td>
                                </tr>
                            </tfoot>
                        </table>
                    </div>

                    <!-- Table Pagination -->
                    <div class="border-t border-gray-800 px-4 py-3 flex items-center justify-between bg-gray-950/30">
                        <div class="flex items-center space-x-2">
                            <select v-model="videoPerPage" class="bg-gray-800 border border-gray-700 rounded-lg px-3 py-1.5 text-xs text-gray-300">
                                <option :value="25">25 rows</option>
                                <option :value="50">50 rows</option>
                                <option :value="100">100 rows</option>
                            </select>
                        </div>
                        <div class="flex items-center space-x-1">
                            <button @click="videoPage = 1" :disabled="videoPage === 1" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30 transition">«</button>
                            <button @click="videoPage--" :disabled="videoPage === 1" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30 transition">‹</button>
                            <span class="px-3 py-1.5 bg-red-600 rounded text-xs text-white font-bold">{{ videoPage }}</span>
                            <span class="text-gray-500 text-xs px-1">of {{ totalVideoPages }}</span>
                            <button @click="videoPage++" :disabled="videoPage === totalVideoPages" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30 transition">›</button>
                            <button @click="videoPage = totalVideoPages" :disabled="videoPage === totalVideoPages" class="px-2 py-1.5 rounded text-xs text-gray-400 hover:text-white disabled:opacity-30 transition">»</button>
                        </div>
                    </div>
                </div>
            </template>

            <!-- ========== PERIOD CARDS VIEW ========== -->
            <template v-else>
                <div v-if="pending" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center">
                    <div class="inline-block animate-spin rounded-full h-8 w-8 border-4 border-gray-700 border-t-red-600"></div>
                    <p class="text-gray-400 mt-4">Loading tax records...</p>
                </div>
                <div v-else-if="taxRecords.length === 0" class="bg-gray-900 border border-gray-800 rounded-xl p-12 text-center">
                    <p class="text-gray-400">No tax records found</p>
                </div>
                <div v-else class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div v-for="record in paginatedRecords" :key="record.id"
                        class="bg-gray-900 border border-gray-800 rounded-2xl overflow-hidden hover:border-gray-700 transition-all group flex flex-col">
                        <div class="p-5 border-b border-gray-800">
                            <div class="flex justify-between items-start mb-3">
                                <div class="flex items-center space-x-3">
                                    <div class="w-10 h-10 rounded-full bg-gradient-to-br from-red-600 to-red-700 flex items-center justify-center text-white font-bold text-sm">
                                        {{ record.artistName?.split(' ').map(n => n[0]).join('').slice(0, 2).toUpperCase() }}
                                    </div>
                                    <div>
                                        <h3 class="text-white font-bold text-sm">{{ record.artistName }}</h3>
                                        <p class="text-gray-500 text-[10px] truncate max-w-[140px]">{{ record.artistEmail }}</p>
                                    </div>
                                </div>
                                <div class="bg-gray-800 text-gray-300 text-[10px] px-2 py-1 rounded-md border border-gray-700 font-mono">{{ formatPeriod(record.period) }}</div>
                            </div>
                        </div>
                        <div class="p-5 flex-grow">
                            <div class="grid grid-cols-2 gap-3 mb-4">
                                <div class="bg-gray-800/40 rounded-xl p-3">
                                    <p class="text-[10px] text-gray-500 mb-1">Videos</p>
                                    <p class="text-xl font-bold text-white">{{ record.videoCount || 0 }}</p>
                                </div>
                                <div class="bg-red-950/10 rounded-xl p-3 border border-red-900/10">
                                    <p class="text-[10px] text-red-400/70 mb-1">Shorts</p>
                                    <p class="text-xl font-bold text-red-500">{{ record.shotCount || 0 }}</p>
                                </div>
                            </div>
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <p class="text-[10px] text-gray-500 mb-0.5">Gross Revenue</p>
                                    <p class="text-sm font-bold text-white">${{ formatCurrency(record.grossRevenue) }}</p>
                                </div>
                                <div class="text-right">
                                    <p class="text-[10px] text-gray-500 mb-0.5">Tax Generated</p>
                                    <p class="text-sm font-bold text-red-400">${{ formatCurrency(record.taxAmount) }}</p>
                                </div>
                            </div>
                        </div>
                        <div class="p-4 bg-gray-800/30 border-t border-gray-800 flex justify-between items-center">
                            <div>
                                <p class="text-[9px] text-gray-500 uppercase">Net Amount</p>
                                <p class="text-sm font-bold text-green-400">${{ formatCurrency(record.netAmount) }}</p>
                            </div>
                            <button @click="viewRecordDetails(record)" class="bg-gray-800 hover:bg-red-600 text-gray-300 hover:text-white px-3 py-1.5 rounded-lg text-xs font-medium transition border border-gray-700 hover:border-red-500">
                                Inspect
                            </button>
                        </div>
                    </div>
                </div>

                <!-- Pagination for cards -->
                <div v-if="taxRecords.length > 0" class="flex items-center justify-between mt-6">
                    <p class="text-gray-400 text-sm">Showing {{ startItem }}–{{ endItem }} of {{ taxRecords.length }}</p>
                    <div class="flex gap-1">
                        <button @click="currentPage = Math.max(1, currentPage-1)" :disabled="currentPage === 1" class="px-3 py-1.5 bg-gray-800 text-gray-300 rounded-lg text-sm disabled:opacity-40">Previous</button>
                        <button v-for="page in displayedPages" :key="page" @click="currentPage = page"
                            class="px-3 py-1.5 rounded-lg text-sm" :class="currentPage === page ? 'bg-red-600 text-white' : 'bg-gray-800 text-gray-300 hover:bg-gray-700'">{{ page }}</button>
                        <button @click="currentPage = Math.min(totalPages, currentPage+1)" :disabled="currentPage === totalPages" class="px-3 py-1.5 bg-gray-800 text-gray-300 rounded-lg text-sm disabled:opacity-40">Next</button>
                    </div>
                </div>
            </template>
        </div>

        <!-- Record Detail Modal -->
        <div v-if="selectedRecord" class="fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center p-4 z-50" @click.self="selectedRecord = null">
            <div class="bg-gray-900 border border-gray-800 rounded-2xl max-w-2xl w-full max-h-[90vh] overflow-y-auto shadow-2xl">
                <div class="flex items-center justify-between p-6 border-b border-gray-800">
                    <div>
                        <h3 class="text-xl font-bold text-white">Tax Record — {{ selectedRecord.artistName }}</h3>
                        <p class="text-gray-400 text-sm mt-0.5">{{ formatPeriod(selectedRecord.period) }} · ID: {{ selectedRecord.id?.slice(0,8) }}...</p>
                    </div>
                    <button @click="selectedRecord = null" class="p-2 rounded-lg hover:bg-gray-800 text-gray-400 hover:text-white transition">
                        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                    </button>
                </div>
                <div class="p-6 space-y-5">
                    <div class="grid grid-cols-2 gap-4">
                        <div class="bg-gray-800/50 rounded-xl p-4"><p class="text-gray-500 text-xs mb-1">Email</p><p class="text-white font-medium text-sm">{{ selectedRecord.artistEmail }}</p></div>
                        <div class="bg-gray-800/50 rounded-xl p-4"><p class="text-gray-500 text-xs mb-1">Artist ID</p><p class="text-white font-mono text-xs">{{ selectedRecord.artistId }}</p></div>
                        <div class="bg-gray-800/50 rounded-xl p-4"><p class="text-gray-500 text-xs mb-1">Videos</p><p class="text-white font-bold text-xl">{{ selectedRecord.videoCount || 0 }}</p></div>
                        <div class="bg-red-950/20 border border-red-900/30 rounded-xl p-4"><p class="text-red-400/70 text-xs mb-1">Shorts</p><p class="text-red-400 font-bold text-xl">{{ selectedRecord.shotCount || 0 }}</p></div>
                        <div class="bg-gray-800/50 rounded-xl p-4"><p class="text-gray-500 text-xs mb-1">Gross Revenue</p><p class="text-white font-bold text-xl">${{ formatCurrency(selectedRecord.grossRevenue) }}</p></div>
                        <div class="bg-red-950/20 border border-red-900/30 rounded-xl p-4 col-span-2">
                            <p class="text-gray-500 text-xs mb-2 font-semibold">Financial Breakdown</p>
                            <div class="space-y-1">
                                <p class="text-white text-xs">Base Tax: <span class="float-right font-mono">${{ formatCurrency(selectedRecord.baseTaxAmount) }}</span></p>
                                <p class="text-yellow-500 text-xs">Interest (10%/mo): <span class="float-right font-mono">${{ formatCurrency(selectedRecord.interestAmount) }}</span></p>
                                <div class="border-t border-red-900/50 mt-2 pt-2">
                                    <p class="text-red-400 text-lg font-black">Total Due: <span class="float-right">${{ formatCurrency(selectedRecord.taxAmount) }}</span></p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="p-6 border-t border-gray-800 flex justify-end">
                    <button @click="selectedRecord = null" class="px-4 py-2 bg-gray-800 hover:bg-gray-700 text-white rounded-lg text-sm transition">Close</button>
                </div>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const DST_PER_MILLION = 150
const INTEREST_RATE = 0.10

// ----- State -----
const viewMode = ref('table')
const selectedRecord = ref(null)
const currentPage = ref(1)
const itemsPerPage = ref(12)

// Video table state
const videoRows = ref([])
const videoLoading = ref(false)
const videoSearch = ref('')
const videoFilter = ref('all')
const videoPage = ref(1)
const videoPerPage = ref(25)

// ----- Tax Records (existing API) -----
const { data: apiData, pending, error, refresh } = await useFetch('http://localhost:8080/api/tax/get-all-tax-records')
const taxRecords = computed(() => apiData.value?.records || [])

// Summary stats from tax records
const totalVideoCount = computed(() => taxRecords.value.reduce((s, r) => s + (r.videoCount || 0), 0))
const totalTaxGenerated = computed(() => taxRecords.value.reduce((s, r) => s + (r.taxAmount || 0), 0))
const totalRevenue = computed(() => taxRecords.value.reduce((s, r) => s + (r.grossRevenue || 0), 0))

// Card pagination
const totalPages = computed(() => Math.ceil(taxRecords.value.length / itemsPerPage.value))
const paginatedRecords = computed(() => {
    const s = (currentPage.value - 1) * itemsPerPage.value
    return taxRecords.value.slice(s, s + itemsPerPage.value)
})
const startItem = computed(() => taxRecords.value.length === 0 ? 0 : (currentPage.value - 1) * itemsPerPage.value + 1)
const endItem = computed(() => Math.min(currentPage.value * itemsPerPage.value, taxRecords.value.length))
const displayedPages = computed(() => {
    const pages = []
    let s = Math.max(1, currentPage.value - 2)
    let e = Math.min(totalPages.value, s + 4)
    for (let i = s; i <= e; i++) pages.push(i)
    return pages
})
watch(itemsPerPage, () => { currentPage.value = 1 })

// ----- Video Table Logic -----
const filteredVideoRows = computed(() => {
    let rows = videoRows.value
    if (videoFilter.value === 'shorts') rows = rows.filter(r => r.isShort)
    if (videoFilter.value === 'longform') rows = rows.filter(r => !r.isShort)
    if (videoSearch.value) {
        const q = videoSearch.value.toLowerCase()
        rows = rows.filter(r => r.title?.toLowerCase().includes(q) || r.artistName?.toLowerCase().includes(q))
    }
    return rows
})

const totalVideoPages = computed(() => Math.max(1, Math.ceil(filteredVideoRows.value.length / videoPerPage.value)))
const paginatedVideoRows = computed(() => {
    const s = (videoPage.value - 1) * videoPerPage.value
    return filteredVideoRows.value.slice(s, s + videoPerPage.value)
})
watch([videoSearch, videoFilter, videoPerPage], () => { videoPage.value = 1 })

const loadVideoTable = async () => {
    videoLoading.value = true
    videoRows.value = []
    try {
        // 1. Get all artists
        const artistsData = await $fetch('http://localhost:8080/api/artists/get-all-artists')
        const artists = artistsData?.artists || []

        // Build a quick tax lookup: per artistId, what's the avg tax per video
        const taxByArtist = {}
        taxRecords.value.forEach(r => {
            if (!taxByArtist[r.artistId]) taxByArtist[r.artistId] = { totalTax: 0, totalVideos: 0 }
            taxByArtist[r.artistId].totalTax += r.taxAmount || 0
            taxByArtist[r.artistId].totalVideos += r.videoCount || 1
        })

        // 2. Per artist: fetch channel then videos
        const results = await Promise.allSettled(
            artists.map(async (artist) => {
                // Get channel info
                const chanData = await $fetch(`http://localhost:8080/api/youtube/channel?artistId=${artist.id}`)
                if (!chanData?.channelId) return []

                // Get videos
                const vidData = await $fetch(`http://localhost:8080/api/youtube/videos/${chanData.channelId}?artistId=${artist.id}`)
                const videos = vidData?.videos || []

                return videos.map(v => {
                    const views = parseInt(v.viewCount) || 0
                    const months = calculateMonthsSince(v.publishedAt)
                    const baseTax = (views / 1000) * (DST_PER_MILLION / 1000)
                    const interest = baseTax * INTEREST_RATE * months
                    const estimatedTax = baseTax + interest

                    return {
                        videoId: v.videoId || v.id,
                        title: v.title,
                        publishedAt: v.publishedAt,
                        duration: v.duration,
                        viewCount: views,
                        likeCount: parseInt(v.likeCount) || 0,
                        commentCount: parseInt(v.commentCount) || 0,
                        isShort: isShort(v.duration),
                        artistId: artist.id,
                        artistName: artist.name,
                        artistEmail: artist.email,
                        estimatedTax
                    }
                })
            })
        )

        // Flatten all video rows, sort by views descending
        const allRows = results
            .filter(r => r.status === 'fulfilled')
            .flatMap(r => r.value)
            .sort((a, b) => b.viewCount - a.viewCount)

        videoRows.value = allRows

    } catch (e) {
        console.error('Failed to load video table:', e)
    } finally {
        videoLoading.value = false
    }
}

// Auto-load video table on mount
onMounted(() => {
    loadVideoTable()
})

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
                return parseInt(duration.substring(mIdx + 1, duration.indexOf('S'))) === 0
            }
            return true
        }
    }
    return true
}

const formatDuration = (iso) => {
    if (!iso) return '—'
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

const formatPeriod = (period) => {
    if (!period) return 'N/A'
    const [year, month] = period.split('-')
    return new Date(year, parseInt(month) - 1).toLocaleDateString('en-US', { year: 'numeric', month: 'long' })
}

const viewRecordDetails = (record) => { selectedRecord.value = record }
const refreshData = () => { refresh() }

const exportToCSV = () => {
    const rows = viewMode.value === 'table' ? filteredVideoRows.value : taxRecords.value
    if (!rows.length) return

    let csv = ''
    if (viewMode.value === 'table') {
        csv = 'Artist,Email,Video Title,Views,Likes,Comments,Type,Duration,Estimated Tax (DST)\n'
        rows.forEach(r => {
            csv += `"${r.artistName}","${r.artistEmail}","${r.title}",${r.viewCount},${r.likeCount},${r.commentCount},${r.isShort ? 'Short' : 'Video'},"${formatDuration(r.duration)}",${r.estimatedTax.toFixed(2)}\n`
        })
    } else {
        csv = 'Artist,Email,Period,Videos,Shorts,Gross Revenue,Tax Amount,Net Amount\n'
        rows.forEach(r => {
            csv += `"${r.artistName}","${r.artistEmail}","${formatPeriod(r.period)}",${r.videoCount},${r.shotCount},${r.grossRevenue},${r.taxAmount},${r.netAmount}\n`
        })
    }

    const blob = new Blob([csv], { type: 'text/csv' })
    const url = URL.createObjectURL(blob)
    const a = document.createElement('a')
    a.href = url
    a.download = `tax-${viewMode.value}-${new Date().toISOString().split('T')[0]}.csv`
    a.click()
    URL.revokeObjectURL(url)
}
</script>

<style scoped>
.line-clamp-2 {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
::-webkit-scrollbar { width: 6px; height: 6px; }
::-webkit-scrollbar-track { background: #1f2937; }
::-webkit-scrollbar-thumb { background: #4b5563; border-radius: 3px; }
::-webkit-scrollbar-thumb:hover { background: #6b7280; }
</style>