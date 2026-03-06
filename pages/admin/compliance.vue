<template>
    <NuxtLayout name="admin-layout">
        <div class="space-y-6 pt-6">
            
            <div class="flex items-center justify-between">
                <div>
                    <h2 class="text-2xl font-bold text-white mb-2">Compliance Monitoring</h2>
                    <p class="text-gray-400">Monitor and manage tax compliance across all artists</p>
                </div>
                <button
                    @click="sendReminders"
                    :disabled="loading || nonCompliantArtists.length === 0"
                    class="bg-red-600 hover:bg-red-700 text-white px-6 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                            d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z">
                        </path>
                    </svg>
                    <span>Send Reminders</span>
                </button>
            </div>
<!-- Summary Stats -->
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Total Artists</p>
                                <p class="text-2xl font-bold text-white">{{ artists.length }}</p>
                            </div>
                            <div class="w-12 h-12 bg-blue-500/10 rounded-lg flex items-center justify-center">
                                <svg class="w-6 h-6 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"/>
                                </svg>
                            </div>
                        </div>
                    </div>

                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Non-Compliant</p>
                                <p class="text-2xl font-bold text-red-400">{{ nonCompliantArtists.length }}</p>
                            </div>
                            <div class="w-12 h-12 bg-red-500/10 rounded-lg flex items-center justify-center">
                                <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z"/>
                                </svg>
                            </div>
                        </div>
                    </div>

                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Compliant</p>
                                <p class="text-2xl font-bold text-green-400">{{ compliantArtists.length }}</p>
                            </div>
                            <div class="w-12 h-12 bg-green-500/10 rounded-lg flex items-center justify-center">
                                <svg class="w-6 h-6 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                </svg>
                            </div>
                        </div>
                    </div>

                    <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                        <div class="flex items-center justify-between">
                            <div>
                                <p class="text-sm text-gray-400 mb-1">Total Outstanding</p>
                                <p class="text-2xl font-bold text-white">${{ formatNumber(totalOutstanding) }}</p>
                            </div>
                            <div class="w-12 h-12 bg-yellow-500/10 rounded-lg flex items-center justify-center">
                                <svg class="w-6 h-6 text-yellow-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                </svg>
                            </div>
                        </div>
                    </div>
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

            <template v-else>
                <!-- Warning Banner -->
                <div v-if="nonCompliantArtists.length > 0" class="bg-yellow-500/10 border border-yellow-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                        <svg class="w-5 h-5 text-yellow-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z">
                            </path>
                        </svg>
                        <div>
                            <p class="text-sm font-medium text-yellow-400">Attention Required</p>
                            <p class="text-sm text-yellow-500/80 mt-1">
                                {{ nonCompliantArtists.length }} artists have outstanding tax obligations that require
                                immediate attention.
                            </p>
                        </div>
                    </div>
                </div>
                

                <!-- Success Banner -->
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
                                All artists are currently compliant with their tax obligations.
                            </p>
                        </div>
                    </div>
                </div>

                <!-- Compliance Filter Tabs -->
                <div class="flex space-x-4 mb-4 border-b border-gray-800">
                    <button
                        @click="activeTab = 'all'"
                        :class="activeTab === 'all' ? 'border-red-500 text-white' : 'border-transparent text-gray-400 hover:text-gray-300'"
                        class="px-4 py-2 text-sm font-medium border-b-2 transition-colors">
                        All Artists ({{ artists.length }})
                    </button>
                    <button
                        @click="activeTab = 'non-compliant'"
                        :class="activeTab === 'non-compliant' ? 'border-red-500 text-white' : 'border-transparent text-gray-400 hover:text-gray-300'"
                        class="px-4 py-2 text-sm font-medium border-b-2 transition-colors">
                        Non-Compliant ({{ nonCompliantArtists.length }})
                    </button>
                    <button
                        @click="activeTab = 'compliant'"
                        :class="activeTab === 'compliant' ? 'border-green-500 text-white' : 'border-transparent text-gray-400 hover:text-gray-300'"
                        class="px-4 py-2 text-sm font-medium border-b-2 transition-colors">
                        Compliant ({{ compliantArtists.length }})
                    </button>
                </div>

                <!-- Artists Table -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl overflow-hidden">
                    <div class="overflow-x-auto">
                        <table class="w-full">
                            <thead class="bg-gray-800/50">
                                <tr>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Artist Name</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Email</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Total Tax Due</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Last Calculation</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Compliance Level</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Tax Status</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Revenue</th>
                                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                        Actions</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-gray-800">
                                <tr v-for="artist in filteredArtists" :key="artist.artistId"
                                    :class="getRowClass(artist)">
                                    <td class="px-6 py-4">
                                        <div class="flex items-center space-x-3">
                                            <div class="w-8 h-8 bg-gray-700 rounded-full flex items-center justify-center">
                                                <span class="text-xs font-medium text-gray-300">{{ getInitials(artist.artistName) }}</span>
                                            </div>
                                            <span class="text-sm font-medium text-white">{{ artist.artistName }}</span>
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="text-sm text-gray-400">{{ artist.email }}</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span :class="artist.outstandingTax > 0 ? 'text-red-400' : 'text-green-400'" 
                                              class="text-sm font-medium">
                                            ${{ formatNumber(artist.totalTaxDue) }}
                                        </span>
                                        <div v-if="artist.outstandingTax > 0" class="text-xs text-red-500/70">
                                            (${{ formatNumber(artist.outstandingTax) }} outstanding)
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="text-sm text-gray-400">{{ formatDate(artist.lastTaxCalculation) }}</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span :class="getComplianceLevelClass(artist.complianceLevel)" 
                                            class="px-2 py-1 text-xs font-medium rounded-full">
                                            {{ artist.complianceLevel }}
                                        </span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="flex flex-col space-y-1">
                                            <span :class="artist.taxCompliant ? 'text-green-400' : 'text-red-400'" 
                                                  class="text-sm font-medium flex items-center">
                                                <svg :class="artist.taxCompliant ? 'text-green-500' : 'text-red-500'" 
                                                     class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 20 20">
                                                    <path v-if="artist.taxCompliant" fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd"/>
                                                    <path v-else fill-rule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clip-rule="evenodd"/>
                                                </svg>
                                                {{ artist.taxCompliant ? 'Compliant' : 'Non-Compliant' }}
                                            </span>
                                            <span v-if="artist.needsAttention" class="text-xs text-red-400 flex items-center">
                                                <svg class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 20 20">
                                                    <path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd"/>
                                                </svg>
                                                Needs Attention
                                            </span>
                                            <span v-if="artist.alerts && artist.alerts.length > 0" class="text-xs text-yellow-400">
                                                {{ artist.alerts.length }} alert(s)
                                            </span>
                                        </div>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <span class="text-sm text-gray-400">${{ formatNumber(artist.totalRevenueToDate) }}</span>
                                    </td>
                                    <td class="px-6 py-4 whitespace-nowrap">
                                        <div class="flex items-center space-x-2">
                                            <button
                                                v-if="!artist.taxCompliant"
                                                @click="sendReminderToArtist(artist)"
                                                class="text-sm text-red-400 hover:text-red-300 hover:underline transition-colors">
                                                Send Reminder
                                            </button>
                                            <button
                                                @click="viewDetails(artist)"
                                                class="text-sm text-gray-400 hover:text-white hover:underline transition-colors">
                                                View Details
                                            </button>
                                        </div>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    
                    <!-- Empty State -->
                    <div v-if="filteredArtists.length === 0" class="text-center py-8">
                        <svg class="w-12 h-12 text-gray-600 mx-auto" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                        </svg>
                        <p class="text-gray-500 mt-2">No artists found for this filter</p>
                    </div>
                </div>
            </template>
        </div>

        <!-- Details Modal -->
        <div v-if="showDetailsModal" class="fixed inset-0 z-50 overflow-y-auto" aria-labelledby="modal-title" role="dialog" aria-modal="true">
            <!-- Background overlay -->
            <div class="fixed inset-0 bg-black/70 transition-opacity" aria-hidden="true"></div>

            <!-- Modal -->
            <div class="flex min-h-screen items-center justify-center p-4">
                <div class="relative bg-gray-900 border border-gray-800 rounded-xl shadow-2xl w-full max-w-4xl overflow-hidden">
                    <!-- Modal header -->
                    <div class="border-b border-gray-800 px-6 py-4">
                        <div class="flex items-center justify-between">
                            <div class="flex items-center space-x-3">
                                <div class="w-10 h-10 bg-gray-700 rounded-full flex items-center justify-center">
                                    <span class="text-sm font-medium text-gray-300">{{ getInitials(selectedArtist?.artistName) }}</span>
                                </div>
                                <div>
                                    <h3 class="text-lg font-bold text-white" id="modal-title">
                                        {{ selectedArtist?.artistName }} - Compliance Details
                                    </h3>
                                    <p class="text-sm text-gray-400">{{ selectedArtist?.email }}</p>
                                </div>
                            </div>
                            <button 
                                @click="showDetailsModal = false"
                                class="text-gray-400 hover:text-white transition-colors">
                                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                                </svg>
                            </button>
                        </div>
                    </div>

                    <!-- Modal content -->
                    <div class="px-6 py-4 max-h-[70vh] overflow-y-auto">
                        <!-- Status Overview -->
                        <div class="mb-6">
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
                                <!-- Compliance Status -->
                                <div :class="selectedArtist?.taxCompliant ? 'bg-green-500/10 border-green-500/20' : 'bg-red-500/10 border-red-500/20'" 
                                     class="border rounded-xl p-4">
                                    <div class="flex items-center justify-between">
                                        <div>
                                            <p class="text-sm text-gray-400 mb-1">Tax Compliance</p>
                                            <p :class="selectedArtist?.taxCompliant ? 'text-green-400' : 'text-red-400'" 
                                               class="text-xl font-bold">
                                                {{ selectedArtist?.taxCompliant ? 'Compliant' : 'Non-Compliant' }}
                                            </p>
                                        </div>
                                        <svg :class="selectedArtist?.taxCompliant ? 'text-green-500' : 'text-red-500'" 
                                             class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path v-if="selectedArtist?.taxCompliant" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                            <path v-else stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z"/>
                                        </svg>
                                    </div>
                                </div>

                                <!-- Compliance Level -->
                                <div class="bg-gray-800/50 border border-gray-700 rounded-xl p-4">
                                    <div class="flex items-center justify-between">
                                        <div>
                                            <p class="text-sm text-gray-400 mb-1">Compliance Level</p>
                                            <span :class="getComplianceLevelClass(selectedArtist?.complianceLevel)" 
                                                  class="px-3 py-1 text-sm font-medium rounded-full">
                                                {{ selectedArtist?.complianceLevel || 'N/A' }}
                                            </span>
                                        </div>
                                        <svg class="w-8 h-8 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/>
                                        </svg>
                                    </div>
                                </div>

                                <!-- Registered Date -->
                                <div class="bg-gray-800/50 border border-gray-700 rounded-xl p-4">
                                    <div class="flex items-center justify-between">
                                        <div>
                                            <p class="text-sm text-gray-400 mb-1">Registered Since</p>
                                            <p class="text-xl font-bold text-white">{{ formatDate(selectedArtist?.registeredDate) }}</p>
                                        </div>
                                        <svg class="w-8 h-8 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"/>
                                        </svg>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- Financial Information -->
                        <div class="mb-6">
                            <h4 class="text-lg font-bold text-white mb-4">Financial Information</h4>
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Total Revenue to Date</p>
                                    <p class="text-2xl font-bold text-white">${{ formatNumber(selectedArtist?.totalRevenueToDate || 0) }}</p>
                                </div>
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Total Tax Paid</p>
                                    <p class="text-2xl font-bold text-green-400">${{ formatNumber(selectedArtist?.totalTaxPaid || 0) }}</p>
                                </div>
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Outstanding Tax</p>
                                    <p class="text-2xl font-bold text-red-400">${{ formatNumber(selectedArtist?.outstandingTax || 0) }}</p>
                                </div>
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Total Tax Due</p>
                                    <p class="text-2xl font-bold text-yellow-400">${{ formatNumber(selectedArtist?.totalTaxDue || 0) }}</p>
                                    <p class="text-xs text-gray-500 mt-1">(Paid + Outstanding)</p>
                                </div>
                            </div>
                        </div>

                        <!-- Compliance Metrics -->
                        <div class="mb-6">
                            <h4 class="text-lg font-bold text-white mb-4">Compliance Metrics</h4>
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <div class="flex items-center justify-between">
                                        <div>
                                            <p class="text-sm text-gray-400 mb-1">Consecutive Compliant Months</p>
                                            <p class="text-2xl font-bold text-white">{{ selectedArtist?.consecutiveCompliantMonths || 0 }}</p>
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
                                            <p class="text-2xl font-bold text-white">{{ selectedArtist?.missedPayments || 0 }}</p>
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
                                            <p class="text-xl font-bold text-white">{{ selectedArtist?.hasOverduePayments ? 'Yes' : 'No' }}</p>
                                        </div>
                                        <svg :class="selectedArtist?.hasOverduePayments ? 'text-red-500' : 'text-green-500'" 
                                             class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z"/>
                                        </svg>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- Additional Information -->
                        <div class="mb-6">
                            <h4 class="text-lg font-bold text-white mb-4">Additional Information</h4>
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Digital Tax Registration</p>
                                    <div class="flex items-center">
                                        <span :class="selectedArtist?.registeredForDigitalTax ? 'text-green-400' : 'text-red-400'" 
                                              class="text-lg font-bold">
                                            {{ selectedArtist?.registeredForDigitalTax ? 'Registered' : 'Not Registered' }}
                                        </span>
                                        <svg v-if="selectedArtist?.registeredForDigitalTax" class="w-5 h-5 text-green-500 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/>
                                        </svg>
                                    </div>
                                </div>
                                <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                    <p class="text-sm text-gray-400 mb-2">Last Tax Calculation</p>
                                    <p class="text-lg font-bold text-white">{{ formatDate(selectedArtist?.lastTaxCalculation) }}</p>
                                </div>
                            </div>
                        </div>

                        <!-- Alerts Section -->
                        <div v-if="selectedArtist?.alerts && selectedArtist.alerts.length > 0" class="mb-6">
                            <h4 class="text-lg font-bold text-white mb-4">Active Alerts ({{ selectedArtist.alerts.length }})</h4>
                            <div class="space-y-3">
                                <div v-for="(alert, index) in selectedArtist.alerts" :key="index" 
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
                        <div v-if="selectedArtist?.complianceNotes" class="mb-6">
                            <h4 class="text-lg font-bold text-white mb-4">Compliance Notes</h4>
                            <div class="bg-gray-800/30 border border-gray-700 rounded-xl p-4">
                                <p class="text-sm text-gray-400">{{ selectedArtist.complianceNotes }}</p>
                            </div>
                        </div>

                        <!-- Action Buttons -->
                        <div class="flex items-center justify-between pt-4 border-t border-gray-800">
                            <div class="flex items-center space-x-2">
                                <span :class="selectedArtist?.needsAttention ? 'text-red-400' : 'text-green-400'" 
                                      class="text-sm font-medium flex items-center">
                                    <svg class="w-4 h-4 mr-1" fill="currentColor" viewBox="0 0 20 20">
                                        <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm1-12a1 1 0 10-2 0v4a1 1 0 00.293.707l2.828 2.829a1 1 0 101.415-1.415L11 9.586V6z" clip-rule="evenodd"/>
                                    </svg>
                                    {{ selectedArtist?.needsAttention ? 'Requires Attention' : 'No Action Required' }}
                                </span>
                            </div>
                            <div class="flex items-center space-x-3">
                                <button
                                    v-if="!selectedArtist?.taxCompliant"
                                    @click="sendReminderToArtist(selectedArtist)"
                                    class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors flex items-center">
                                    <svg class="w-4 h-4 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 4.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z"/>
                                    </svg>
                                    Send Reminder
                                </button>
                                <button
                                    @click="showDetailsModal = false"
                                    class="bg-gray-800 hover:bg-gray-700 text-white px-4 py-2 rounded-lg text-sm font-medium transition-colors">
                                    Close
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
const config = useRuntimeConfig()
const artists = ref([])
const activeTab = ref('all')
const showDetailsModal = ref(false)
const selectedArtist = ref(null)

const normalizedArtists = computed(() => {
    return artists.value.map(artist => ({
        ...artist,
        totalTaxDue: (artist.totalTaxPaid || 0) + (artist.outstandingTax || 0)
    }))
})

const loading = ref(true)
const error = ref(null)

// Computed property for non-compliant artists
const nonCompliantArtists = computed(() => {
    return normalizedArtists.value.filter(a => !a.taxCompliant)
})

// Computed property for compliant artists
const compliantArtists = computed(() => {
    return normalizedArtists.value.filter(a => a.taxCompliant)
})

// Computed property for filtered artists based on active tab
const filteredArtists = computed(() => {
    switch (activeTab.value) {
        case 'non-compliant':
            return nonCompliantArtists.value
        case 'compliant':
            return compliantArtists.value
        default:
            return normalizedArtists.value
    }
})

// Computed property for total outstanding tax
const totalOutstanding = computed(() => {
    return nonCompliantArtists.value.reduce(
        (sum, artist) => sum + (artist.outstandingTax || 0),
        0
    )
})

// Get row background class based on compliance status
const getRowClass = (artist) => {
    if (!artist.taxCompliant) {
        return 'bg-red-500/5 hover:bg-red-500/10 transition-colors'
    }
    return 'hover:bg-gray-800/50 transition-colors'
}

// Fetch compliance data from API
const fetchCompliance = async () => {
    loading.value = true
    error.value = null
    
    try {
        const response = await $fetch('http://localhost:8080/api/compliance/initialize-all-compliance', {
            method: 'POST',
            headers: {
                'accept': '*/*'
            }
        })
        
        if (Array.isArray(response)) {
            artists.value = response
        } else {
            throw new Error('Invalid response format')
        }
    } catch (err) {
        console.error('Error fetching compliance data:', err)
        error.value = err.message || 'Failed to load compliance data. Please try again.'
    } finally {
        loading.value = false
    }
}

// Get initials from artist name
const getInitials = (name) => {
    if (!name) return '??'
    return name.split(' ').map(n => n[0]).join('').toUpperCase()
}

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

// Send reminder to specific artist
const sendReminderToArtist = (artist) => {
    // Implement reminder logic here
    alert(`Sending reminder to ${artist.artistName}`)
}

// View artist details - opens modal
const viewDetails = (artist) => {
    selectedArtist.value = artist
    showDetailsModal.value = true
}

// Send reminders to all non-compliant artists
const sendReminders = () => {
    if (nonCompliantArtists.value.length === 0) return
    
    const confirmed = confirm(`Send reminders to ${nonCompliantArtists.value.length} non-compliant artists?`)
    if (confirmed) {
        // Implement bulk reminder logic here
        alert(`Sending reminders to ${nonCompliantArtists.value.length} artists`)
    }
}

// Close modal when clicking outside
const closeModal = (event) => {
    if (event.target === event.currentTarget) {
        showDetailsModal.value = false
    }
}

// Fetch data on component mount
onMounted(() => {
    fetchCompliance()
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

/* Modal animations */
.modal-enter-active,
.modal-leave-active {
    transition: opacity 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
    opacity: 0;
}

.modal-content-enter-active,
.modal-content-leave-active {
    transition: transform 0.3s ease, opacity 0.3s ease;
}

.modal-content-enter-from {
    opacity: 0;
    transform: scale(0.95);
}

.modal-content-leave-to {
    opacity: 0;
    transform: scale(1.05);
}
</style>