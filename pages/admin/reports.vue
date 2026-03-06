<template>
    <NuxtLayout name="admin-layout">
        <div class="p-6">
            <!-- Header -->
            <div class="mb-8">
                <div class="flex items-center justify-between">
                    <div>
                        <h2 class="text-2xl font-bold text-white mb-2">Reporting Center</h2>
                        <p class="text-gray-400">Generate detailed reports for tax compliance and artist management</p>
                    </div>
                    <div class="flex items-center space-x-3">
                        <button @click="refreshComplianceData"
                            :disabled="loading"
                            class="bg-gray-800 hover:bg-gray-700 border border-gray-700 text-gray-300 px-4 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15">
                                </path>
                            </svg>
                            <span>{{ loading ? 'Refreshing...' : 'Refresh Data' }}</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Data Status -->
            <div v-if="complianceData.length > 0" class="mb-6">
                <div class="bg-green-500/10 border border-green-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                        <svg class="w-5 h-5 text-green-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z">
                            </path>
                        </svg>
                        <div>
                            <p class="text-sm font-medium text-green-400">Data Loaded</p>
                            <p class="text-sm text-green-500/80 mt-1">
                                Loaded compliance data for {{ complianceData.length }} artists. Ready to generate reports.
                            </p>
                        </div>
                    </div>
                </div>
            </div>
            <div v-else-if="error" class="mb-6">
                <div class="bg-red-500/10 border border-red-500/20 rounded-xl p-4">
                    <div class="flex items-start space-x-3">
                        <svg class="w-5 h-5 text-red-500 mt-0.5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z">
                            </path>
                        </svg>
                        <div>
                            <p class="text-sm font-medium text-red-400">Data Error</p>
                            <p class="text-sm text-red-500/80 mt-1">
                                {{ error }}
                            </p>
                            <button @click="fetchComplianceData" class="mt-2 text-sm text-red-400 hover:text-red-300 underline">
                                Retry Loading Data
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Report Types Grid -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8">
                <!-- Compliance Report -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-red-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                            </svg>
                        </div>
                        <span class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">ZIMRA
                            Ready</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Compliance Status Report</h3>
                    <p class="text-sm text-gray-400 mb-4">Detailed overview of all artists' tax compliance status with
                        ZIMRA regulations</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ complianceReports.length }} reports generated
                        </div>
                        <button @click="generateComplianceReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>

                <!-- Revenue Report -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-green-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z">
                                </path>
                            </svg>
                        </div>
                        <span
                            class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">Financial</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Revenue & Tax Report</h3>
                    <p class="text-sm text-gray-400 mb-4">Comprehensive financial report with revenue breakdown and tax
                        calculations</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ revenueReports.length }} reports generated
                        </div>
                        <button @click="generateRevenueReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>

                <!-- Non-Compliant Report -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-yellow-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-yellow-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z">
                                </path>
                            </svg>
                        </div>
                        <span class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">Urgent</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Non-Compliant Artists</h3>
                    <p class="text-sm text-gray-400 mb-4">Detailed report of artists with outstanding tax obligations
                        and deadlines</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ nonCompliantReports.length }} reports generated
                        </div>
                        <button @click="generateNonCompliantReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>

                <!-- Quarterly Tax Summary -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-blue-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z">
                                </path>
                            </svg>
                        </div>
                        <span
                            class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">Quarterly</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Quarterly Tax Summary</h3>
                    <p class="text-sm text-gray-400 mb-4">Complete quarterly tax summary for ZIMRA submission with
                        payment schedules</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ quarterlyReports.length }} reports generated
                        </div>
                        <button @click="generateQuarterlyReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>

                <!-- Artist Performance -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-purple-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M16 8v8m-4-5v5m-4-2v2m-2 4h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z">
                                </path>
                            </svg>
                        </div>
                        <span
                            class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">Performance</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Artist Performance Report</h3>
                    <p class="text-sm text-gray-400 mb-4">Revenue and tax performance analysis across all registered
                        artists</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ performanceReports.length }} reports generated
                        </div>
                        <button @click="generatePerformanceReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>

                <!-- Audit Trail -->
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 hover:border-gray-700 transition-colors">
                    <div class="flex items-start justify-between mb-4">
                        <div class="p-3 bg-indigo-600/20 rounded-lg">
                            <svg class="w-6 h-6 text-indigo-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z">
                                </path>
                            </svg>
                        </div>
                        <span class="text-xs font-medium px-2 py-1 rounded-full bg-gray-800 text-gray-400">Audit</span>
                    </div>
                    <h3 class="text-lg font-semibold text-white mb-2">Compliance Audit Report</h3>
                    <p class="text-sm text-gray-400 mb-4">Complete audit trail of all compliance activities and status changes</p>
                    <div class="flex items-center justify-between">
                        <div class="text-sm text-gray-500">
                            {{ auditReports.length }} reports generated
                        </div>
                        <button @click="generateAuditReport" :disabled="isGenerating || complianceData.length === 0"
                            class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                    d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                                </path>
                            </svg>
                            <span>Generate</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Report Configuration -->
            <div class="bg-gray-900 border border-gray-800 rounded-xl p-6 mb-8">
                <h3 class="text-lg font-semibold text-white mb-6">Report Configuration</h3>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                    <!-- Date Range -->
                    <div>
                        <label class="block text-sm font-medium text-gray-400 mb-2">Date Range</label>
                        <select v-model="selectedDateRange"
                            class="w-full bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-white focus:outline-none focus:border-red-600">
                            <option value="all">All Time</option>
                            <option value="last-month">Last Month</option>
                            <option value="last-quarter">Last Quarter</option>
                            <option value="last-year">Last Year</option>
                            <option value="ytd">Year to Date</option>
                        </select>
                    </div>

                    <!-- Report Format -->
                    <div>
                        <label class="block text-sm font-medium text-gray-400 mb-2">Format</label>
                        <div class="flex space-x-2">
                            <button @click="selectedFormat = 'pdf'" :class="[
                                'flex-1 px-4 py-2.5 rounded-lg font-medium transition-colors text-sm',
                                selectedFormat === 'pdf'
                                    ? 'bg-red-600 text-white'
                                    : 'bg-gray-800 text-gray-400 hover:bg-gray-700'
                            ]">
                                PDF
                            </button>
                            <button @click="selectedFormat = 'excel'" :class="[
                                'flex-1 px-4 py-2.5 rounded-lg font-medium transition-colors text-sm',
                                selectedFormat === 'excel'
                                    ? 'bg-green-600 text-white'
                                    : 'bg-gray-800 text-gray-400 hover:bg-gray-700'
                            ]">
                                Excel
                            </button>
                        </div>
                    </div>

                    <!-- Include Details -->
                    <div>
                        <label class="block text-sm font-medium text-gray-400 mb-2">Detail Level</label>
                        <select v-model="detailLevel"
                            class="w-full bg-gray-800 border border-gray-700 rounded-lg px-4 py-2.5 text-sm text-white focus:outline-none focus:border-red-600">
                            <option value="summary">Summary</option>
                            <option value="detailed">Detailed</option>
                            <option value="comprehensive">Comprehensive</option>
                        </select>
                    </div>

                    <!-- Generate All -->
                    <div class="flex items-end">
                        <button @click="generateAllReports" :disabled="isGenerating || complianceData.length === 0"
                            class="w-full bg-gradient-to-r from-red-600 to-red-700 hover:from-red-700 hover:to-red-800 text-white px-6 py-2.5 rounded-lg font-medium transition-colors text-sm flex items-center justify-center space-x-2 disabled:opacity-50 disabled:cursor-not-allowed">
                            <svg v-if="isGenerating" class="animate-spin h-4 w-4 text-white"
                                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor"
                                    stroke-width="4"></circle>
                                <path class="opacity-75" fill="currentColor"
                                    d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                                </path>
                            </svg>
                            <span>{{ isGenerating ? 'Generating...' : 'Generate All Reports' }}</span>
                        </button>
                    </div>
                </div>
            </div>

            <!-- Generated Reports -->
            <div class="bg-gray-900 border border-gray-800 rounded-xl overflow-hidden">
                <div class="p-6 border-b border-gray-800">
                    <div class="flex items-center justify-between">
                        <h3 class="text-lg font-semibold text-white">Recently Generated Reports</h3>
                        <div class="flex items-center space-x-3">
                            <button @click="showAllReports = !showAllReports"
                                class="text-sm text-gray-400 hover:text-white transition-colors">
                                {{ showAllReports ? 'Show Less' : 'View All' }}
                            </button>
                        </div>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full">
                        <thead class="bg-gray-800/50">
                            <tr>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Report Name</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Type</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Generated</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Format</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Size</th>
                                <th
                                    class="px6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Status</th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-400 uppercase tracking-wider">
                                    Actions</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-gray-800">
                            <tr v-for="report in displayedReports" :key="report.id"
                                class="hover:bg-gray-800/30 transition-colors">
                                <td class="px-6 py-4">
                                    <div>
                                        <p class="text-sm font-medium text-white">{{ report.name }}</p>
                                        <p class="text-xs text-gray-500">{{ report.description }}</p>
                                    </div>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <span :class="[
                                        'px-2.5 py-1 text-xs font-medium rounded-full',
                                        getReportTypeClass(report.type)
                                    ]">
                                        {{ report.type }}
                                    </span>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <span class="text-sm text-gray-400">{{ formatDate(report.generatedAt) }}</span>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <span class="text-sm font-medium"
                                        :class="report.format === 'PDF' ? 'text-red-400' : 'text-green-400'">
                                        {{ report.format }}
                                    </span>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <span class="text-sm text-gray-400">{{ report.size }}</span>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <span :class="[
                                        'px-2.5 py-1 text-xs font-medium rounded-full',
                                        report.status === 'Completed'
                                            ? 'bg-green-500/10 text-green-400'
                                            : 'bg-yellow-500/10 text-yellow-400'
                                    ]">
                                        {{ report.status }}
                                    </span>
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap">
                                    <div class="flex items-center space-x-3">
                                        <button @click="downloadActualReport(report)"
                                            class="text-gray-400 hover:text-white p-1.5 rounded-lg hover:bg-gray-800 transition-colors"
                                            :title="`Download ${report.format}`">
                                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4">
                                                </path>
                                            </svg>
                                        </button>
                                        <button @click="shareReport(report)"
                                            class="text-gray-400 hover:text-white p-1.5 rounded-lg hover:bg-gray-800 transition-colors"
                                            title="Share Report">
                                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.368 2.684 3 3 0 00-5.368-2.684z">
                                                </path>
                                            </svg>
                                        </button>
                                        <button @click="deleteReport(report.id)"
                                            class="text-gray-400 hover:text-red-400 p-1.5 rounded-lg hover:bg-gray-800 transition-colors"
                                            title="Delete Report">
                                            <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16">
                                                </path>
                                            </svg>
                                        </button>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Empty State -->
                <div v-if="allReports.length === 0" class="p-12 text-center">
                    <div class="w-16 h-16 bg-gray-800 rounded-full flex items-center justify-center mx-auto mb-4">
                        <svg class="w-8 h-8 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                            </path>
                        </svg>
                    </div>
                    <h4 class="text-lg font-semibold text-white mb-2">No Reports Generated Yet</h4>
                    <p class="text-gray-400 max-w-md mx-auto">
                        Generate your first report by selecting a report type above. All generated reports will appear
                        here for easy access.
                    </p>
                </div>
            </div>

            <!-- Report Statistics -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mt-8">
                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <div class="flex items-center justify-between mb-4">
                        <h4 class="text-sm font-medium text-gray-400">Total Reports</h4>
                        <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z">
                            </path>
                        </svg>
                    </div>
                    <div class="text-2xl font-bold text-white mb-2">{{ allReports.length }}</div>
                    <div class="text-xs text-gray-500">Reports generated to date</div>
                </div>

                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <div class="flex items-center justify-between mb-4">
                        <h4 class="text-sm font-medium text-gray-400">Compliance Status</h4>
                        <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z">
                            </path>
                        </svg>
                    </div>
                    <div class="text-2xl font-bold text-white mb-2">{{ compliantArtists.length }}/{{ complianceData.length }}</div>
                    <div class="text-xs text-gray-500">Compliant artists</div>
                </div>

                <div class="bg-gray-900 border border-gray-800 rounded-xl p-6">
                    <div class="flex items-center justify-between mb-4">
                        <h4 class="text-sm font-medium text-gray-400">Total Outstanding</h4>
                        <svg class="w-5 h-5 text-gray-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z">
                            </path>
                        </svg>
                    </div>
                    <div class="text-2xl font-bold text-white mb-2">${{ formatNumber(totalOutstanding) }}</div>
                    <div class="text-xs text-gray-500">Tax outstanding</div>
                </div>
            </div>
        </div>

        <!-- Report Generation Modal -->
        <div v-if="showGenerationModal" class="fixed inset-0 bg-black/70 flex items-center justify-center z-50 p-4">
            <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 max-w-md w-full">
                <div class="text-center mb-6">
                    <div
                        class="w-16 h-16 bg-gradient-to-br from-red-600 to-red-700 rounded-full flex items-center justify-center mx-auto mb-4">
                        <svg class="w-8 h-8 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
                        </svg>
                    </div>
                    <h3 class="text-xl font-semibold text-white mb-2">Generating Report</h3>
                    <p class="text-gray-400 text-sm">{{ generatingMessage }}</p>
                </div>

                <!-- Progress Bar -->
                <div class="mb-6">
                    <div class="h-2 bg-gray-800 rounded-full overflow-hidden">
                        <div class="h-full bg-gradient-to-r from-red-600 to-red-700 transition-all duration-300"
                            :style="{ width: `${generationProgress}%` }"></div>
                    </div>
                    <div class="flex justify-between text-xs text-gray-500 mt-2">
                        <span>Processing...</span>
                        <span>{{ generationProgress }}%</span>
                    </div>
                </div>

                <!-- Status Messages -->
                <div class="space-y-3 mb-6">
                    <div v-for="status in generationStatuses" :key="status.id" class="flex items-center space-x-3">
                        <svg class="w-5 h-5 flex-shrink-0"
                            :class="status.completed ? 'text-green-500' : 'text-gray-600'" fill="currentColor"
                            viewBox="0 0 20 20">
                            <path v-if="status.completed" fill-rule="evenodd"
                                d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z"
                                clip-rule="evenodd" />
                            <circle v-else cx="10" cy="10" r="9" stroke="currentColor" stroke-width="2" fill="none" />
                        </svg>
                        <span class="text-sm" :class="status.completed ? 'text-gray-300' : 'text-gray-500'">
                            {{ status.message }}
                        </span>
                    </div>
                </div>

                <button @click="showGenerationModal = false" v-if="generationProgress === 100"
                    class="w-full py-3 px-4 bg-red-600 hover:bg-red-700 text-white rounded-xl font-medium transition-colors">
                    Complete
                </button>
            </div>
        </div>
    </NuxtLayout>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// State
const searchQuery = ref('')
const isGenerating = ref(false)
const showAllReports = ref(false)
const selectedDateRange = ref('all')
const selectedFormat = ref('pdf')
const detailLevel = ref('summary')
const complianceData = ref([])
const loading = ref(false)
const error = ref(null)

// Report generation modal
const showGenerationModal = ref(false)
const generatingMessage = ref('')
const generationProgress = ref(0)
const generationStatuses = ref([])

// Reports data
const complianceReports = ref([])
const revenueReports = ref([])
const nonCompliantReports = ref([])
const quarterlyReports = ref([])
const performanceReports = ref([])
const auditReports = ref([])

// Computed properties
const allReports = computed(() => [
    ...complianceReports.value,
    ...revenueReports.value,
    ...nonCompliantReports.value,
    ...quarterlyReports.value,
    ...performanceReports.value,
    ...auditReports.value
])

const displayedReports = computed(() => {
    const reports = allReports.value
        .filter(report =>
            report.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
            report.type.toLowerCase().includes(searchQuery.value.toLowerCase())
        )
        .sort((a, b) => new Date(b.generatedAt) - new Date(a.generatedAt))

    return showAllReports.value ? reports : reports.slice(0, 5)
})

const compliantArtists = computed(() => {
    return complianceData.value.filter(artist => artist.taxCompliant)
})

const nonCompliantArtists = computed(() => {
    return complianceData.value.filter(artist => !artist.taxCompliant)
})

const totalOutstanding = computed(() => {
    return nonCompliantArtists.value.reduce(
        (sum, artist) => sum + (artist.outstandingTax || 0),
        0
    )
})

// Methods
const formatDate = (dateString) => {
    const date = new Date(dateString)
    return date.toLocaleDateString('en-US', {
        month: 'short',
        day: 'numeric',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
    })
}

const formatNumber = (num) => {
    if (!num && num !== 0) return '0'
    return num.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })
}

const getReportTypeClass = (type) => {
    const classes = {
        'Compliance': 'bg-red-500/10 text-red-400',
        'Revenue': 'bg-green-500/10 text-green-400',
        'Non-Compliant': 'bg-yellow-500/10 text-yellow-400',
        'Quarterly': 'bg-blue-500/10 text-blue-400',
        'Performance': 'bg-purple-500/10 text-purple-400',
        'Audit': 'bg-indigo-500/10 text-indigo-400'
    }
    return classes[type] || 'bg-gray-500/10 text-gray-400'
}

// Fetch compliance data
const fetchComplianceData = async () => {
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
            complianceData.value = response
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

const refreshComplianceData = async () => {
    await fetchComplianceData()
}

// Generate PDF using jsPDF
const generatePDF = (title, content, headers, data, reportType = 'standard') => {
    const { jsPDF } = window.jspdf
    const doc = new jsPDF({
        orientation: 'landscape',
        unit: 'mm',
        format: 'a4'
    })
    
    // Set document properties
    doc.setProperties({
        title: title,
        subject: `ZimTax ${reportType} Report`,
        author: 'ZimTax Admin System',
        keywords: 'tax, compliance, revenue, artists, zimra',
        creator: 'ZimTax Admin Dashboard'
    })
    
    // Colors
    const primaryColor = [220, 38, 38] // Red-600
    const headerColor = [31, 41, 55] // Gray-800
    const rowColor = [55, 65, 81] // Gray-700
    const textColor = [255, 255, 255] // White
    const lightTextColor = [156, 163, 175] // Gray-400
    
    // Page dimensions
    const pageWidth = doc.internal.pageSize.width
    const pageHeight = doc.internal.pageSize.height
    const margin = 15
    const tableStartY = 45
    let currentY = 20
    
    // Add header with logo
    doc.setFillColor(...primaryColor)
    doc.rect(0, 0, pageWidth, 15, 'F')
    
    // Logo/Title in header
    doc.setTextColor(...textColor)
    doc.setFontSize(12)
    doc.setFont('helvetica', 'bold')
    doc.text('ZIMTAX ADMIN', margin, 10)
    
    // Report type badge
    const reportTypeWidth = doc.getTextWidth(reportType.toUpperCase()) + 10
    doc.setFillColor(0, 0, 0)
    doc.roundedRect(pageWidth - margin - reportTypeWidth, 6, reportTypeWidth, 8, 2, 2, 'F')
    doc.setTextColor(...textColor)
    doc.setFontSize(8)
    doc.text(reportType.toUpperCase(), pageWidth - margin - reportTypeWidth/2, 10.5, { align: 'center' })
    
    // Report title
    doc.setTextColor(0, 0, 0)
    doc.setFontSize(18)
    doc.setFont('helvetica', 'bold')
    doc.text(title, pageWidth/2, 30, { align: 'center' })
    
    // Report metadata
    doc.setFontSize(10)
    doc.setFont('helvetica', 'normal')
    doc.setTextColor(...lightTextColor)
    const generatedDate = new Date().toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
    })
    doc.text(`Generated: ${generatedDate}`, margin, 40)
    doc.text(`Page 1 of 1`, pageWidth - margin, 40, { align: 'right' })
    
    // Add summary content if provided
    if (content) {
        doc.setFontSize(11)
        doc.setTextColor(0, 0, 0)
        doc.setFont('helvetica', 'normal')
        
        // Split content into lines
        const contentLines = doc.splitTextToSize(content, pageWidth - (margin * 2))
        contentLines.forEach(line => {
            if (currentY < tableStartY - 5) {
                doc.text(line, margin, currentY)
                currentY += 7
            }
        })
    }
    
    // Add table if headers and data are provided
    if (headers && data && data.length > 0) {
        currentY = tableStartY
        
        // Calculate column widths based on content
        const columnWidths = calculateColumnWidths(doc, headers, data, pageWidth - (margin * 2))
        
        // Draw table header
        drawTableHeader(doc, headers, columnWidths, margin, currentY, headerColor)
        currentY += 10
        
        // Draw table rows
        data.forEach((row, rowIndex) => {
            // Check if we need a new page
            if (currentY > pageHeight - 20) {
                doc.addPage()
                currentY = 20
                // Draw header on new page
                drawTableHeader(doc, headers, columnWidths, margin, currentY, headerColor)
                currentY += 10
            }
            
            // Alternate row colors
            const rowBgColor = rowIndex % 2 === 0 ? [255, 255, 255] : [249, 250, 251]
            doc.setFillColor(...rowBgColor)
            doc.rect(margin, currentY - 4, pageWidth - (margin * 2), 10, 'F')
            
            // Draw row borders
            doc.setDrawColor(229, 231, 235)
            doc.rect(margin, currentY - 4, pageWidth - (margin * 2), 10)
            
            let xPosition = margin
            headers.forEach((header, colIndex) => {
                const cellValue = formatCellValue(row, header)
                
                // Style based on content
                if (header.toLowerCase().includes('tax') || header.toLowerCase().includes('revenue')) {
                    doc.setTextColor(220, 38, 38) // Red for financial data
                } else if (header.toLowerCase().includes('compliant') && cellValue.toLowerCase() === 'yes') {
                    doc.setTextColor(34, 197, 94) // Green for compliant
                } else if (header.toLowerCase().includes('compliant') && cellValue.toLowerCase() === 'no') {
                    doc.setTextColor(239, 68, 68) // Red for non-compliant
                } else {
                    doc.setTextColor(0, 0, 0)
                }
                
                // Bold for important values
                if (header.toLowerCase().includes('total') || header.toLowerCase().includes('outstanding')) {
                    doc.setFont('helvetica', 'bold')
                } else {
                    doc.setFont('helvetica', 'normal')
                }
                
                // Draw cell content
                doc.setFontSize(9)
                const cellLines = doc.splitTextToSize(cellValue, columnWidths[colIndex] - 4)
                cellLines.forEach((line, lineIndex) => {
                    doc.text(line, xPosition + 2, currentY + (lineIndex * 4))
                })
                
                // Draw vertical line
                if (colIndex < headers.length - 1) {
                    doc.setDrawColor(209, 213, 219)
                    doc.line(
                        xPosition + columnWidths[colIndex],
                        currentY - 4,
                        xPosition + columnWidths[colIndex],
                        currentY + 6
                    )
                }
                
                xPosition += columnWidths[colIndex]
            })
            
            currentY += Math.max(10, (doc.splitTextToSize(Object.values(row).join(' '), pageWidth - (margin * 2)).length * 4))
        })
        
        // Draw footer with totals if applicable
        if (shouldShowFooter(reportType)) {
            currentY += 10
            drawTableFooter(doc, data, headers, margin, currentY, pageWidth, primaryColor)
        }
    }
    
    // Add footer
    doc.setFontSize(8)
    doc.setTextColor(...lightTextColor)
    doc.setFont('helvetica', 'normal')
    doc.text('Confidential - For Internal Use Only', pageWidth/2, pageHeight - 10, { align: 'center' })
    
    // Add page numbers
    const pageCount = doc.internal.getNumberOfPages()
    for (let i = 1; i <= pageCount; i++) {
        doc.setPage(i)
        doc.text(`Page ${i} of ${pageCount}`, pageWidth - margin, pageHeight - 10, { align: 'right' })
    }
    
    // Save the PDF
    doc.save(`${title.replace(/\s+/g, '_')}_${new Date().getTime()}.pdf`)
}

// Helper function to calculate column widths
const calculateColumnWidths = (doc, headers, data, availableWidth) => {
    const minWidth = 20
    const maxWidth = 60
    const defaultWidth = availableWidth / headers.length
    
    // Calculate content widths
    const contentWidths = headers.map((header, index) => {
        // Header width
        const headerWidth = doc.getTextWidth(header) + 10
        
        // Data widths
        const dataWidths = data.map(row => {
            const value = formatCellValue(row, header)
            return doc.getTextWidth(value) + 10
        })
        
        const maxDataWidth = Math.max(...dataWidths)
        return Math.min(maxWidth, Math.max(minWidth, Math.max(headerWidth, maxDataWidth)))
    })
    
    // Adjust widths proportionally to fit available width
    const totalWidth = contentWidths.reduce((sum, width) => sum + width, 0)
    const scaleFactor = availableWidth / totalWidth
    
    return contentWidths.map(width => Math.min(maxWidth, width * scaleFactor))
}

// Helper function to format cell values
const formatCellValue = (row, header) => {
    const key = header.toLowerCase().replace(/\s+/g, '_')
    let value = row[key] || row[header] || ''
    
    // Format based on header type
    if (header.toLowerCase().includes('date')) {
        if (value && value !== 'N/A') {
            try {
                const date = new Date(value)
                return date.toLocaleDateString('en-US', { month: 'short', day: 'numeric', year: 'numeric' })
            } catch {
                return value
            }
        }
    }
    
    if (header.toLowerCase().includes('tax') || header.toLowerCase().includes('revenue') || header.toLowerCase().includes('amount')) {
        if (typeof value === 'number' || !isNaN(value)) {
            const numValue = parseFloat(value)
            if (numValue === 0) return '$0.00'
            if (numValue < 0) return `-$${Math.abs(numValue).toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`
            return `$${numValue.toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`
        }
    }
    
    if (header.toLowerCase().includes('percentage') || header.toLowerCase().includes('rate')) {
        if (typeof value === 'number' || !isNaN(value)) {
            return `${parseFloat(value).toFixed(2)}%`
        }
    }
    
    return String(value)
}

// Helper function to draw table header
const drawTableHeader = (doc, headers, columnWidths, startX, startY, color) => {
    doc.setFillColor(...color)
    doc.rect(startX, startY - 6, columnWidths.reduce((sum, width) => sum + width, 0), 10, 'F')
    
    let xPosition = startX
    headers.forEach((header, index) => {
        doc.setTextColor(255, 255, 255)
        doc.setFont('helvetica', 'bold')
        doc.setFontSize(10)
        
        // Draw header text
        const headerLines = doc.splitTextToSize(header, columnWidths[index] - 4)
        headerLines.forEach((line, lineIndex) => {
            doc.text(line, xPosition + 2, startY + (lineIndex * 4))
        })
        
        // Draw header border
        doc.setDrawColor(107, 114, 128)
        doc.setLineWidth(0.5)
        if (index < headers.length - 1) {
            doc.line(
                xPosition + columnWidths[index],
                startY - 6,
                xPosition + columnWidths[index],
                startY + 4
            )
        }
        
        xPosition += columnWidths[index]
    })
    
    // Draw header bottom border
    doc.setDrawColor(107, 114, 128)
    doc.setLineWidth(1)
    doc.line(
        startX,
        startY + 4,
        startX + columnWidths.reduce((sum, width) => sum + width, 0),
        startY + 4
    )
}

// Helper function to draw table footer with totals
const drawTableFooter = (doc, data, headers, startX, startY, pageWidth, color) => {
    const margin = 15
    const footerWidth = pageWidth - (margin * 2)
    
    // Only add footer for financial reports
    const hasFinancialData = headers.some(h => 
        h.toLowerCase().includes('tax') || 
        h.toLowerCase().includes('revenue') ||
        h.toLowerCase().includes('amount')
    )
    
    if (!hasFinancialData) return
    
    doc.setFillColor(...color)
    doc.rect(startX, startY, footerWidth, 15, 'F')
    
    doc.setTextColor(255, 255, 255)
    doc.setFont('helvetica', 'bold')
    doc.setFontSize(11)
    
    // Calculate totals
    const totals = {}
    headers.forEach(header => {
        const key = header.toLowerCase().replace(/\s+/g, '_')
        if (header.toLowerCase().includes('tax') || header.toLowerCase().includes('revenue') || header.toLowerCase().includes('amount')) {
            const total = data.reduce((sum, row) => {
                const value = row[key] || row[header] || 0
                const numValue = typeof value === 'string' ? 
                    parseFloat(value.replace(/[^0-9.-]+/g, '')) || 0 : 
                    parseFloat(value) || 0
                return sum + numValue
            }, 0)
            totals[header] = total
        }
    })
    
    // Display totals
    let footerText = 'Totals: '
    Object.entries(totals).forEach(([header, total], index) => {
        if (total !== 0) {
            const formattedTotal = `$${Math.abs(total).toLocaleString('en-US', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`
            footerText += `${header}: ${formattedTotal}`
            if (index < Object.keys(totals).length - 1) {
                footerText += ' | '
            }
        }
    })
    
    if (footerText !== 'Totals: ') {
        const footerLines = doc.splitTextToSize(footerText, footerWidth - 10)
        footerLines.forEach((line, index) => {
            doc.text(line, startX + 5, startY + 8 + (index * 5))
        })
    }
}

// Helper function to determine if footer should be shown
const shouldShowFooter = (reportType) => {
    const footerReports = ['revenue', 'tax', 'financial', 'quarterly', 'performance']
    return footerReports.some(type => reportType.toLowerCase().includes(type))
}

// Enhanced version for specific report types
const generateStyledReport = (title, content, headers, data, reportType = 'standard') => {
    // Apply specific styling based on report type
    switch(reportType.toLowerCase()) {
        case 'compliance':
            return generateComplianceReport(title, content, headers, data)
        case 'revenue':
            return generateFinancialReport(title, content, headers, data)
        case 'non-compliant':
            return generateNonCompliantReport(title, content, headers, data)
        case 'quarterly':
            return generateQuarterlyReport(title, content, headers, data)
        case 'performance':
            return generatePerformanceReport(title, content, headers, data)
        case 'audit':
            return generateAuditReport(title, content, headers, data)
        default:
            return generatePDF(title, content, headers, data, reportType)
    }
}


// Generate Excel using xlsx
const generateExcel = (title, data, sheetName = 'Report') => {
    const XLSX = window.XLSX
    const ws = XLSX.utils.json_to_sheet(data)
    const wb = XLSX.utils.book_new()
    XLSX.utils.book_append_sheet(wb, ws, sheetName)
    XLSX.writeFile(wb, `${title.replace(/\s+/g, '_')}_${new Date().getTime()}.xlsx`)
}

// Report generation functions
const generateComplianceReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Compliance Status Report...'
    
    generationStatuses.value = [
        { id: 1, message: 'Collecting compliance data...', completed: false },
        { id: 2, message: 'Analyzing tax compliance status...', completed: false },
        { id: 3, message: 'Calculating compliance metrics...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing report...', completed: false }
    ]
    
    // Simulate generation process
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            // Prepare report data
            const reportData = complianceData.value.map(artist => ({
                artist_name: artist.artistName,
                email: artist.email,
                tax_compliant: artist.taxCompliant ? 'Yes' : 'No',
                compliance_level: artist.complianceLevel,
                total_revenue: `$${formatNumber(artist.totalRevenueToDate)}`,
                total_tax_paid: `$${formatNumber(artist.totalTaxPaid)}`,
                outstanding_tax: `$${formatNumber(artist.outstandingTax)}`,
                last_calculation: formatDate(artist.lastTaxCalculation),
                registered_date: formatDate(artist.registeredDate)
            }))
            
            // Generate report based on format
            if (selectedFormat.value === 'pdf') {
                const headers = ['Artist Name', 'Email', 'Tax Compliant', 'Compliance Level', 'Total Revenue', 'Total Tax Paid', 'Outstanding Tax', 'Last Calculation', 'Registered Date']
                generatePDF(
                    'Compliance Status Report',
                    `Total Artists: ${complianceData.value.length}\nCompliant Artists: ${compliantArtists.value.length}\nNon-Compliant Artists: ${nonCompliantArtists.value.length}\nTotal Outstanding Tax: $${formatNumber(totalOutstanding.value)}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Compliance_Status_Report', reportData)
            }
            
            // Add to reports list
            const newReport = createNewReport('compliance')
            addReportToType('compliance', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generateRevenueReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Revenue & Tax Report...'
    
    generationStatuses.value = [
        { id: 1, message: 'Collecting financial data...', completed: false },
        { id: 2, message: 'Calculating revenue totals...', completed: false },
        { id: 3, message: 'Analyzing tax obligations...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing report...', completed: false }
    ]
    
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            const reportData = complianceData.value.map(artist => ({
                artist_name: artist.artistName,
                total_revenue: artist.totalRevenueToDate,
                total_tax_paid: artist.totalTaxPaid,
                outstanding_tax: artist.outstandingTax,
                total_tax_due: (artist.totalTaxPaid || 0) + (artist.outstandingTax || 0),
                tax_compliance_rate: artist.totalRevenueToDate > 0 ? 
                    ((artist.totalTaxPaid || 0) / artist.totalRevenueToDate * 100).toFixed(2) + '%' : '0%',
                last_calculation: formatDate(artist.lastTaxCalculation)
            }))
            
            const totalRevenue = complianceData.value.reduce((sum, artist) => sum + (artist.totalRevenueToDate || 0), 0)
            const totalTaxPaid = complianceData.value.reduce((sum, artist) => sum + (artist.totalTaxPaid || 0), 0)
            
            if (selectedFormat.value === 'pdf') {
                const headers = ['Artist Name', 'Total Revenue', 'Total Tax Paid', 'Outstanding Tax', 'Total Tax Due', 'Tax Compliance Rate', 'Last Calculation']
                generatePDF(
                    'Revenue & Tax Report',
                    `Total Revenue: $${formatNumber(totalRevenue)}\nTotal Tax Paid: $${formatNumber(totalTaxPaid)}\nTotal Outstanding: $${formatNumber(totalOutstanding.value)}\nOverall Tax Rate: ${totalRevenue > 0 ? ((totalTaxPaid / totalRevenue) * 100).toFixed(2) + '%' : '0%'}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Revenue_Tax_Report', reportData)
            }
            
            const newReport = createNewReport('revenue')
            addReportToType('revenue', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generateNonCompliantReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Non-Compliant Artists Report...'
    
    generationStatuses.value = [
        { id: 1, message: 'Identifying non-compliant artists...', completed: false },
        { id: 2, message: 'Calculating outstanding amounts...', completed: false },
        { id: 3, message: 'Analyzing payment history...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing report...', completed: false }
    ]
    
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            const nonCompliantData = complianceData.value.filter(artist => !artist.taxCompliant)
            const reportData = nonCompliantData.map(artist => ({
                artist_name: artist.artistName,
                email: artist.email,
                outstanding_tax: artist.outstandingTax,
                compliance_level: artist.complianceLevel,
                missed_payments: artist.missedPayments || 0,
                has_overdue_payments: artist.hasOverduePayments ? 'Yes' : 'No',
                needs_attention: artist.needsAttention ? 'Yes' : 'No',
                last_calculation: formatDate(artist.lastTaxCalculation),
                registered_date: formatDate(artist.registeredDate)
            }))
            
            if (selectedFormat.value === 'pdf') {
                const headers = ['Artist Name', 'Email', 'Outstanding Tax', 'Compliance Level', 'Missed Payments', 'Overdue Payments', 'Needs Attention', 'Last Calculation', 'Registered Date']
                generatePDF(
                    'Non-Compliant Artists Report',
                    `Total Non-Compliant Artists: ${nonCompliantData.length}\nTotal Outstanding Tax: $${formatNumber(totalOutstanding.value)}\nAverage Outstanding per Artist: $${formatNumber(totalOutstanding.value / (nonCompliantData.length || 1))}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Non_Compliant_Artists_Report', reportData)
            }
            
            const newReport = createNewReport('non-compliant')
            addReportToType('non-compliant', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generateQuarterlyReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Quarterly Tax Summary...'
    
    generationStatuses.value = [
        { id: 1, message: 'Collecting quarterly data...', completed: false },
        { id: 2, message: 'Calculating quarterly totals...', completed: false },
        { id: 3, message: 'Analyzing quarter performance...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing report...', completed: false }
    ]
    
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            const currentQuarter = Math.floor((new Date().getMonth() + 3) / 3)
            const reportData = complianceData.value.map(artist => ({
                artist_name: artist.artistName,
                quarter: `Q${currentQuarter} ${new Date().getFullYear()}`,
                total_revenue: artist.totalRevenueToDate,
                total_tax_paid: artist.totalTaxPaid,
                outstanding_tax: artist.outstandingTax,
                tax_compliant: artist.taxCompliant ? 'Yes' : 'No',
                compliance_level: artist.complianceLevel,
                last_calculation: formatDate(artist.lastTaxCalculation)
            }))
            
            if (selectedFormat.value === 'pdf') {
                const headers = ['Artist Name', 'Quarter', 'Total Revenue', 'Total Tax Paid', 'Outstanding Tax', 'Tax Compliant', 'Compliance Level', 'Last Calculation']
                generatePDF(
                    'Quarterly Tax Summary',
                    `Quarter: Q${currentQuarter} ${new Date().getFullYear()}\nTotal Artists: ${complianceData.value.length}\nCompliant Rate: ${((compliantArtists.value.length / complianceData.value.length) * 100).toFixed(2)}%\nTotal Outstanding Tax: $${formatNumber(totalOutstanding.value)}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Quarterly_Tax_Summary', reportData)
            }
            
            const newReport = createNewReport('quarterly')
            addReportToType('quarterly', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generatePerformanceReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Artist Performance Report...'
    
    generationStatuses.value = [
        { id: 1, message: 'Analyzing artist performance...', completed: false },
        { id: 2, message: 'Calculating performance metrics...', completed: false },
        { id: 3, message: 'Ranking artists by revenue...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing report...', completed: false }
    ]
    
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            // Sort artists by revenue
            const sortedArtists = [...complianceData.value].sort((a, b) => 
                (b.totalRevenueToDate || 0) - (a.totalRevenueToDate || 0)
            )
            
            const reportData = sortedArtists.map((artist, index) => ({
                rank: index + 1,
                artist_name: artist.artistName,
                total_revenue: artist.totalRevenueToDate,
                total_tax_paid: artist.totalTaxPaid,
                tax_compliance: artist.taxCompliant ? 'Compliant' : 'Non-Compliant',
                compliance_level: artist.complianceLevel,
                consecutive_compliant_months: artist.consecutiveCompliantMonths || 0,
                missed_payments: artist.missedPayments || 0,
                registered_since: formatDate(artist.registeredDate)
            }))
            
            if (selectedFormat.value === 'pdf') {
                const headers = ['Rank', 'Artist Name', 'Total Revenue', 'Total Tax Paid', 'Tax Compliance', 'Compliance Level', 'Consecutive Months', 'Missed Payments', 'Registered Since']
                generatePDF(
                    'Artist Performance Report',
                    `Top Performing Artists Analysis\nTotal Artists: ${complianceData.value.length}\nAverage Revenue: $${formatNumber(complianceData.value.reduce((sum, artist) => sum + (artist.totalRevenueToDate || 0), 0) / complianceData.value.length)}\nCompliant Artists: ${compliantArtists.value.length}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Artist_Performance_Report', reportData)
            }
            
            const newReport = createNewReport('performance')
            addReportToType('performance', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generateAuditReport = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating Compliance Audit Report...'
    
    generationStatuses.value = [
        { id: 1, message: 'Collecting audit trail data...', completed: false },
        { id: 2, message: 'Analyzing compliance history...', completed: false },
        { id: 3, message: 'Compiling status changes...', completed: false },
        { id: 4, message: `Generating ${selectedFormat.value.toUpperCase()} report...`, completed: false },
        { id: 5, message: 'Finalizing audit report...', completed: false }
    ]
    
    const interval = setInterval(() => {
        generationProgress.value += 20
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            const reportData = complianceData.value.map(artist => ({
                artist_name: artist.artistName,
                artist_id: artist.artistId,
                compliance_status: artist.taxCompliant ? 'Compliant' : 'Non-Compliant',
                compliance_level: artist.complianceLevel,
                compliance_notes: artist.complianceNotes || 'No notes',
                has_alerts: artist.alerts && artist.alerts.length > 0 ? 'Yes' : 'No',
                alert_count: artist.alerts ? artist.alerts.length : 0,
                needs_attention: artist.needsAttention ? 'Yes' : 'No',
                last_calculation: formatDate(artist.lastTaxCalculation),
                registered_for_digital_tax: artist.registeredForDigitalTax ? 'Yes' : 'No'
            }))
            
            if (selectedFormat.value === 'pdf') {
                const headers = ['Artist Name', 'Artist ID', 'Compliance Status', 'Compliance Level', 'Compliance Notes', 'Has Alerts', 'Alert Count', 'Needs Attention', 'Last Calculation', 'Digital Tax Registered']
                generatePDF(
                    'Compliance Audit Report',
                    `Compliance Audit Trail - Generated ${new Date().toLocaleDateString()}\nTotal Artists Audited: ${complianceData.value.length}\nArtists Needing Attention: ${complianceData.value.filter(a => a.needsAttention).length}\nTotal Alerts: ${complianceData.value.reduce((sum, artist) => sum + (artist.alerts ? artist.alerts.length : 0), 0)}`,
                    headers,
                    reportData
                )
            } else {
                generateExcel('Compliance_Audit_Report', reportData)
            }
            
            const newReport = createNewReport('audit')
            addReportToType('audit', newReport)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 1500)
        }
    }, 300)
}

const generateAllReports = async () => {
    if (isGenerating.value || complianceData.value.length === 0) return
    
    isGenerating.value = true
    showGenerationModal.value = true
    generationProgress.value = 0
    generatingMessage.value = 'Generating All Reports...'
    
    const reportTypes = ['compliance', 'revenue', 'non-compliant', 'quarterly', 'performance', 'audit']
    const totalSteps = reportTypes.length * 5
    let completedSteps = 0
    
    generationStatuses.value = [
        { id: 1, message: 'Initializing batch report generation...', completed: false },
        { id: 2, message: 'Processing compliance reports...', completed: false },
        { id: 3, message: 'Processing revenue reports...', completed: false },
        { id: 4, message: 'Processing non-compliant reports...', completed: false },
        { id: 5, message: 'Finalizing all reports...', completed: false }
    ]
    
    const interval = setInterval(() => {
        completedSteps += 1
        generationProgress.value = Math.min((completedSteps / totalSteps) * 100, 100)
        
        if (generationProgress.value >= 20) generationStatuses.value[0].completed = true
        if (generationProgress.value >= 40) generationStatuses.value[1].completed = true
        if (generationProgress.value >= 60) generationStatuses.value[2].completed = true
        if (generationProgress.value >= 80) generationStatuses.value[3].completed = true
        if (generationProgress.value >= 100) generationStatuses.value[4].completed = true
        
        if (generationProgress.value >= 100) {
            clearInterval(interval)
            
            // Generate all reports sequentially
            setTimeout(() => {
                reportTypes.forEach((type, index) => {
                    setTimeout(() => {
                        const newReport = createNewReport(type)
                        addReportToType(type, newReport)
                    }, index * 500)
                })
            }, 1000)
            
            setTimeout(() => {
                showGenerationModal.value = false
                isGenerating.value = false
                generationProgress.value = 0
                generationStatuses.value = []
            }, 3000)
        }
    }, 200)
}

const createNewReport = (type) => {
    const reportNames = {
        'compliance': 'Compliance Status Report',
        'revenue': 'Revenue & Tax Summary',
        'non-compliant': 'Non-Compliant Artists Report',
        'quarterly': 'Quarterly Tax Summary',
        'performance': 'Artist Performance Report',
        'audit': 'Compliance Audit Report'
    }

    const reportDescriptions = {
        'compliance': 'Detailed compliance overview for ZIMRA submission',
        'revenue': 'Financial breakdown with tax calculations',
        'non-compliant': 'Outstanding tax obligations and deadlines',
        'quarterly': `Q${Math.floor((new Date().getMonth() + 3) / 3)} ${new Date().getFullYear()} tax summary`,
        'performance': 'Revenue performance analysis by artist',
        'audit': 'Complete compliance audit trail'
    }

    const typeLabels = {
        'compliance': 'Compliance',
        'revenue': 'Revenue',
        'non-compliant': 'Non-Compliant',
        'quarterly': 'Quarterly',
        'performance': 'Performance',
        'audit': 'Audit'
    }

    const format = selectedFormat.value === 'pdf' ? 'PDF' : 'Excel'
    const size = selectedFormat.value === 'pdf' ? '2.4 MB' : '1.8 MB'

    return {
        id: Date.now() + Math.random(),
        name: reportNames[type],
        description: reportDescriptions[type],
        type: typeLabels[type],
        generatedAt: new Date().toISOString(),
        format: format,
        size: size,
        status: 'Completed',
        dataType: type,
        formatType: selectedFormat.value
    }
}

const addReportToType = (type, report) => {
    switch (type) {
        case 'compliance':
            complianceReports.value.unshift(report)
            break
        case 'revenue':
            revenueReports.value.unshift(report)
            break
        case 'non-compliant':
            nonCompliantReports.value.unshift(report)
            break
        case 'quarterly':
            quarterlyReports.value.unshift(report)
            break
        case 'performance':
            performanceReports.value.unshift(report)
            break
        case 'audit':
            auditReports.value.unshift(report)
            break
    }
}

const downloadActualReport = (report) => {
    // Regenerate the report on download
    switch (report.dataType) {
        case 'compliance':
            generateComplianceReport()
            break
        case 'revenue':
            generateRevenueReport()
            break
        case 'non-compliant':
            generateNonCompliantReport()
            break
        case 'quarterly':
            generateQuarterlyReport()
            break
        case 'performance':
            generatePerformanceReport()
            break
        case 'audit':
            generateAuditReport()
            break
    }
}

const shareReport = (report) => {
    const shareText = `Check out this ${report.name} from ZimTax Admin - Generated on ${formatDate(report.generatedAt)}`
    alert(`Share option for: ${report.name}\n\n${shareText}`)
}

const deleteReport = (reportId) => {
    if (confirm('Are you sure you want to delete this report?')) {
        const arrays = [complianceReports, revenueReports, nonCompliantReports, quarterlyReports, performanceReports, auditReports]
        arrays.forEach(array => {
            const index = array.value.findIndex(r => r.id === reportId)
            if (index > -1) {
                array.value.splice(index, 1)
            }
        })
    }
}

// Load external libraries for PDF and Excel generation
const loadExternalLibraries = () => {
    // Load jsPDF
    const jsPDFScript = document.createElement('script')
    jsPDFScript.src = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js'
    document.head.appendChild(jsPDFScript)
    
    // Load xlsx
    const xlsxScript = document.createElement('script')
    xlsxScript.src = 'https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js'
    document.head.appendChild(xlsxScript)
}

// Initialize
onMounted(() => {
    loadExternalLibraries()
    fetchComplianceData()
    
    // Add some sample reports
    const sampleReports = [
        { type: 'compliance', name: 'Monthly Compliance Report', description: 'March 2024 compliance status', size: '1.2 MB' },
        { type: 'revenue', name: 'Revenue Summary Q1 2024', description: 'Quarterly revenue breakdown', size: '2.1 MB' },
        { type: 'non-compliant', name: 'Overdue Tax Report', description: 'Artists with overdue payments', size: '0.8 MB' }
    ]

    sampleReports.forEach((sample, index) => {
        const report = {
            id: index + 1,
            name: sample.name,
            description: sample.description,
            type: sample.type === 'compliance' ? 'Compliance' :
                sample.type === 'revenue' ? 'Revenue' :
                    sample.type === 'non-compliant' ? 'Non-Compliant' : 'Performance',
            generatedAt: new Date(Date.now() - (index * 24 * 60 * 60 * 1000)).toISOString(),
            format: index % 2 === 0 ? 'PDF' : 'Excel',
            size: sample.size,
            status: 'Completed',
            dataType: sample.type,
            formatType: index % 2 === 0 ? 'pdf' : 'excel'
        }
        addReportToType(sample.type, report)
    })
})
</script>

<style>
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

/* Smooth transitions */
.transition-all {
    transition-property: all;
    transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
    transition-duration: 300ms;
}

/* Gradient text */
.gradient-text {
    background: linear-gradient(to right, #ef4444, #dc2626);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
</style>