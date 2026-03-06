<template>
  <div class="min-h-screen bg-gray-950">
    <!-- Navigation -->
    <nav class="fixed top-0 left-0 right-0 z-50 bg-gray-900/95 backdrop-blur-sm border-b border-gray-800">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex items-center justify-between h-16">
          <!-- Logo -->
          <div class="flex items-center">
            <div class="flex items-center space-x-3">
              <span class="text-xl font-bold text-white"> MTax<span class="text-red-500"> Studio</span></span>
            </div>
          </div>

        

          <!-- CTA Buttons -->
          <div class="flex items-center space-x-4">
            <button @click="router.push('/auth/login')" class="bg-red-600 hover:bg-red-700 text-white px-6 py-2 rounded-lg font-medium transition-colors">
              Sign In
            </button>
            
          </div>
        </div>
      </div>

      <!-- Mobile menu -->
      <div v-if="isMobileMenuOpen" class="md:hidden bg-gray-900 border-b border-gray-800">
        <div class="px-2 pt-2 pb-3 space-y-1 sm:px-3">
          <!-- <a href="#features" @click="isMobileMenuOpen = false" class="text-gray-300 hover:text-white block px-3 py-2 text-base font-medium">Features</a>
          <a href="#how-it-works" @click="isMobileMenuOpen = false" class="text-gray-300 hover:text-white block px-3 py-2 text-base font-medium">How It Works</a>
          <a href="#pricing" @click="isMobileMenuOpen = false" class="text-gray-300 hover:text-white block px-3 py-2 text-base font-medium">Pricing</a>
          <a href="#testimonials" @click="isMobileMenuOpen = false" class="text-gray-300 hover:text-white block px-3 py-2 text-base font-medium">Testimonials</a>
          <a href="#contact" @click="isMobileMenuOpen = false" class="text-gray-300 hover:text-white block px-3 py-2 text-base font-medium">Contact</a> -->
          <div class="pt-4 pb-3 border-t border-gray-800">
            <button @click="router.push('/login'); isMobileMenuOpen = false" class="w-full bg-red-600 hover:bg-red-700 text-white px-6 py-3 rounded-lg font-medium transition-colors">
              Get Started
            </button>
          </div>
        </div>
      </div>
    </nav>

    <!-- Hero Section -->
    <section class="relative pt-32 pb-20 md:pt-40 md:pb-28 overflow-hidden">
      <!-- Background Gradient -->
      <div class="absolute inset-0 bg-gradient-to-br from-gray-900 via-gray-950 to-black"></div>
      <div class="absolute -top-40 -right-40 w-80 h-80 bg-red-600/10 rounded-full blur-3xl"></div>
      <div class="absolute -bottom-40 -left-40 w-80 h-80 bg-blue-600/10 rounded-full blur-3xl"></div>
      
      <div class="relative max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center">
          <h1 class="text-4xl md:text-6xl font-bold text-white mb-6">
            Streamline Zimbabwean Musician
            <span class="text-transparent bg-clip-text bg-gradient-to-r from-red-500 to-red-600">Tax Management</span>
          </h1>
          <p class="text-xl text-gray-400 max-w-3xl mx-auto mb-10">
            The comprehensive platform for tracking, calculating, and managing tax obligations for Zimbabwe's music industry. 
            Automate compliance and focus on what matters most.
          </p>
          <div class="flex flex-col sm:flex-row items-center justify-center gap-4 mb-16">
            <button @click="router.push('/login')" class="bg-red-600 hover:bg-red-700 text-white px-8 py-4 rounded-xl font-semibold text-lg transition-all duration-200 transform hover:scale-105">
              Start Free Trial
            </button>
            <button @click="scrollToFeatures" class="bg-gray-800 hover:bg-gray-700 text-white px-8 py-4 rounded-xl font-semibold text-lg border border-gray-700 transition-colors">
              See Features
            </button>
          </div>
          
          <!-- Stats -->
          <div class="grid grid-cols-2 md:grid-cols-4 gap-6 max-w-3xl mx-auto">
            <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
              <div class="text-3xl font-bold text-white mb-2">500+</div>
              <div class="text-sm text-gray-400">Artists Registered</div>
            </div>
            <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
              <div class="text-3xl font-bold text-white mb-2">$2M+</div>
              <div class="text-sm text-gray-400">Tax Collected</div>
            </div>
            <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
              <div class="text-3xl font-bold text-white mb-2">98%</div>
              <div class="text-sm text-gray-400">Compliance Rate</div>
            </div>
            <div class="bg-gray-900/50 backdrop-blur-sm border border-gray-800 rounded-xl p-6">
              <div class="text-3xl font-bold text-white mb-2">24/7</div>
              <div class="text-sm text-gray-400">Support Available</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- System Description -->
    <section class="py-20 bg-gradient-to-b from-gray-900 to-gray-950">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center mb-16">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-4">Powerful Tax Management Platform</h2>
          <p class="text-xl text-gray-400 max-w-3xl mx-auto">
            Designed specifically for Zimbabwe's unique tax regulations and the dynamic music industry
          </p>
        </div>
        
        <div class="grid md:grid-cols-2 gap-12 items-center">
          <div>
            <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
              <div class="flex items-center space-x-3 mb-6">
                <div class="w-12 h-12 bg-red-600/20 rounded-xl flex items-center justify-center">
                  <svg class="w-6 h-6 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                  </svg>
                </div>
                <h3 class="text-2xl font-semibold text-white">Compliance Made Simple</h3>
              </div>
              <p class="text-gray-400 mb-6">
                Our system automatically calculates tax obligations based on Zimbabwe Revenue Authority (ZIMRA) regulations, 
                ensuring full compliance with local tax laws. We handle everything from VAT to income tax specific to the 
                entertainment industry.
              </p>
              <ul class="space-y-3">
                <li class="flex items-center text-gray-300">
                  <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                  </svg>
                  Automated ZIMRA tax calculations
                </li>
                <li class="flex items-center text-gray-300">
                  <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                  </svg>
                  Real-time compliance monitoring
                </li>
                <li class="flex items-center text-gray-300">
                  <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                  </svg>
                  Automated payment reminders
                </li>
              </ul>
            </div>
          </div>
          
          <div>
            <div class="relative">
              <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
                <div class="flex items-center space-x-3 mb-6">
                  <div class="w-12 h-12 bg-blue-600/20 rounded-xl flex items-center justify-center">
                    <svg class="w-6 h-6 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
                    </svg>
                  </div>
                  <h3 class="text-2xl font-semibold text-white">For Artists & Managers</h3>
                </div>
                <p class="text-gray-400 mb-6">
                  Whether you're an independent artist or manage multiple musicians, our platform provides the tools you need 
                  to stay tax-compliant. Track earnings from streaming platforms, live performances, and merchandise sales in one place.
                </p>
                <ul class="space-y-3">
                  <li class="flex items-center text-gray-300">
                    <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                    </svg>
                    Multi-artist management dashboard
                  </li>
                  <li class="flex items-center text-gray-300">
                    <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                    </svg>
                    Revenue tracking across platforms
                  </li>
                  <li class="flex items-center text-gray-300">
                    <svg class="w-5 h-5 text-green-500 mr-3" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                    </svg>
                    Automated tax filing reports
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Features Section -->
    <section id="features" class="py-20 bg-gray-950">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center mb-16">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-4">Powerful Features</h2>
          <p class="text-xl text-gray-400 max-w-3xl mx-auto">
            Everything you need to manage musician taxes efficiently and effectively
          </p>
        </div>

        <div class="grid md:grid-cols-3 gap-8">
          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-red-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">Automated Tax Calculations</h3>
            <p class="text-gray-400">
              Automatic calculation of all applicable taxes based on Zimbabwean tax laws. Supports multiple revenue streams including streaming, live performances, and merchandise.
            </p>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-blue-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">Real-time Monitoring</h3>
            <p class="text-gray-400">
              Monitor tax compliance across all artists in real-time. Get instant alerts for upcoming deadlines and non-compliance issues.
            </p>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-green-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">Comprehensive Reporting</h3>
            <p class="text-gray-400">
              Generate detailed tax reports for ZIMRA submissions. Export financial statements, tax summaries, and compliance certificates.
            </p>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-purple-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-purple-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">Secure Data Protection</h3>
            <p class="text-gray-400">
              Bank-level security with end-to-end encryption. Your financial data is protected with military-grade security protocols.
            </p>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-yellow-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-yellow-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">API Integrations</h3>
            <p class="text-gray-400">
              Connect with streaming platforms (YouTube, Spotify, Apple Music), payment processors, and accounting software.
            </p>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8 hover:border-gray-700 transition-colors">
            <div class="w-14 h-14 bg-pink-600/20 rounded-xl flex items-center justify-center mb-6">
              <svg class="w-7 h-7 text-pink-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M18 9v3m0 0v3m0-3h3m-3 0h-3m-2-5a4 4 0 11-8 0 4 4 0 018 0zM3 20a6 6 0 0112 0v1H3v-1z" />
              </svg>
            </div>
            <h3 class="text-xl font-semibold text-white mb-4">Dedicated Support</h3>
            <p class="text-gray-400">
              Get help from tax experts specializing in Zimbabwean entertainment industry regulations. Available 24/7.
            </p>
          </div>
        </div>
      </div>
    </section>

    <!-- How It Works -->
    <section id="how-it-works" class="py-20 bg-gradient-to-b from-gray-950 to-gray-900">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center mb-16">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-4">How It Works</h2>
          <p class="text-xl text-gray-400 max-w-3xl mx-auto">
            Get started with ZimTax Admin in three simple steps
          </p>
        </div>

        <div class="grid md:grid-cols-3 gap-8">
          <div class="relative">
            <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
              <div class="absolute -top-4 -left-4 w-10 h-10 bg-red-600 rounded-full flex items-center justify-center text-white font-bold text-lg">
                1
              </div>
              <div class="w-16 h-16 bg-red-600/20 rounded-xl flex items-center justify-center mb-6">
                <svg class="w-8 h-8 text-red-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z" />
                </svg>
              </div>
              <h3 class="text-xl font-semibold text-white mb-4">Register Artists</h3>
              <p class="text-gray-400">
                Add musicians to your dashboard with their basic information and revenue sources. Connect their streaming accounts for automatic tracking.
              </p>
            </div>
          </div>

          <div class="relative">
            <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
              <div class="absolute -top-4 -left-4 w-10 h-10 bg-red-600 rounded-full flex items-center justify-center text-white font-bold text-lg">
                2
              </div>
              <div class="w-16 h-16 bg-blue-600/20 rounded-xl flex items-center justify-center mb-6">
                <svg class="w-8 h-8 text-blue-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
              </div>
              <h3 class="text-xl font-semibold text-white mb-4">Track & Calculate</h3>
              <p class="text-gray-400">
                Our system automatically tracks revenue and calculates tax obligations according to ZIMRA regulations. Real-time updates keep you informed.
              </p>
            </div>
          </div>

          <div class="relative">
            <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
              <div class="absolute -top-4 -left-4 w-10 h-10 bg-red-600 rounded-full flex items-center justify-center text-white font-bold text-lg">
                3
              </div>
              <div class="w-16 h-16 bg-green-600/20 rounded-xl flex items-center justify-center mb-6">
                <svg class="w-8 h-8 text-green-500" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4M7.835 4.697a3.42 3.42 0 001.946-.806 3.42 3.42 0 014.438 0 3.42 3.42 0 001.946.806 3.42 3.42 0 013.138 3.138 3.42 3.42 0 00.806 1.946 3.42 3.42 0 010 4.438 3.42 3.42 0 00-.806 1.946 3.42 3.42 0 01-3.138 3.138 3.42 3.42 0 00-1.946.806 3.42 3.42 0 01-4.438 0 3.42 3.42 0 00-1.946-.806 3.42 3.42 0 01-3.138-3.138 3.42 3.42 0 00-.806-1.946 3.42 3.42 0 010-4.438 3.42 3.42 0 00.806-1.946 3.42 3.42 0 013.138-3.138z" />
                </svg>
              </div>
              <h3 class="text-xl font-semibold text-white mb-4">Submit & Monitor</h3>
              <p class="text-gray-400">
                Generate ZIMRA-ready reports, submit payments, and monitor compliance status. Get automated reminders for upcoming deadlines.
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Testimonials -->
    <section id="testimonials" class="py-20 bg-gray-950">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="text-center mb-16">
          <h2 class="text-3xl md:text-4xl font-bold text-white mb-4">Trusted by Music Industry Leaders</h2>
          <p class="text-xl text-gray-400 max-w-3xl mx-auto">
            See what artists and managers are saying about ZimTax Admin
          </p>
        </div>

        <div class="grid md:grid-cols-3 gap-8">
          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
            <div class="flex items-center space-x-4 mb-6">
              <div class="w-12 h-12 bg-gradient-to-br from-red-600 to-red-700 rounded-full"></div>
              <div>
                <h4 class="font-semibold text-white">Music Manager</h4>
                <p class="text-sm text-gray-500">Harare Record Label</p>
              </div>
            </div>
            <p class="text-gray-400 italic mb-6">
              "Managing taxes for 20+ artists was a nightmare until we found ZimTax Admin. The automated calculations and ZIMRA compliance features have saved us hundreds of hours."
            </p>
            <div class="flex text-yellow-400">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
            </div>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
            <div class="flex items-center space-x-4 mb-6">
              <div class="w-12 h-12 bg-gradient-to-br from-blue-600 to-blue-700 rounded-full"></div>
              <div>
                <h4 class="font-semibold text-white">Independent Artist</h4>
                <p class="text-sm text-gray-500">Afro-fusion Musician</p>
              </div>
            </div>
            <p class="text-gray-400 italic mb-6">
              "As a musician, taxes were always confusing and stressful. ZimTax Admin makes it simple. I can focus on my music while knowing my taxes are handled properly."
            </p>
            <div class="flex text-yellow-400">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
            </div>
          </div>

          <div class="bg-gray-900 border border-gray-800 rounded-2xl p-8">
            <div class="flex items-center space-x-4 mb-6">
              <div class="w-12 h-12 bg-gradient-to-br from-green-600 to-green-700 rounded-full"></div>
              <div>
                <h4 class="font-semibold text-white">Record Label CEO</h4>
                <p class="text-sm text-gray-500">Bulawayo Based Label</p>
              </div>
            </div>
            <p class="text-gray-400 italic mb-6">
              "The compliance monitoring has prevented multiple potential issues with ZIMRA. The peace of mind alone is worth the investment."
            </p>
            <div class="flex text-yellow-400">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20">
                <path d="M9.049 2.927c.3-.921 1.603-.921 1.902 0l1.07 3.292a1 1 0 00.95.69h3.462c.969 0 1.371 1.24.588 1.81l-2.8 2.034a1 1 0 00-.364 1.118l1.07 3.292c.3.921-.755 1.688-1.54 1.118l-2.8-2.034a1 1 0 00-1.175 0l-2.8 2.034c-.784.57-1.838-.197-1.539-1.118l1.07-3.292a1 1 0 00-.364-1.118L2.98 8.72c-.783-.57-.38-1.81.588-1.81h3.461a1 1 0 00.951-.69l1.07-3.292z" />
              </svg>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- CTA Section -->
    <section class="py-20 bg-gradient-to-r from-gray-900 via-red-900/20 to-gray-900">
      <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8 text-center">
        <h2 class="text-3xl md:text-4xl font-bold text-white mb-6">
          Ready to Streamline Your Tax Management?
        </h2>
        <p class="text-xl text-gray-400 mb-10">
          Join hundreds of musicians and managers who trust ZimTax Admin for their tax compliance needs.
        </p>
        <div class="flex flex-col sm:flex-row items-center justify-center gap-4">
          <button @click="router.push('/auth/login')" class="bg-white hover:bg-gray-100 text-gray-900 px-8 py-4 rounded-xl font-semibold text-lg transition-all duration-200 transform hover:scale-105">
            Get Started
          </button>
         
        </div>
        <p class="text-gray-500 text-sm mt-6">No credit card required</p>
      </div>
    </section>

    <!-- Footer -->
    <footer id="contact" class="bg-gray-900 border-t border-gray-800 py-12">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="grid md:grid-cols-4 gap-8">
          <div>
            <div class="flex items-center space-x-3 mb-6">
              
              <span class="text-xl font-bold text-white">MTax<span class="text-red-500"> Studio</span></span>
            </div>
            <p class="text-gray-400 text-sm">
              The leading tax management platform for Zimbabwe's music industry. Making tax compliance simple and efficient.
            </p>
          </div>

          <div>
            <h3 class="text-lg font-semibold text-white mb-6">Product</h3>
            <ul class="space-y-3">
              <li><a href="#features" class="text-gray-400 hover:text-white transition-colors">Features</a></li>
              <li><a href="#pricing" class="text-gray-400 hover:text-white transition-colors">Pricing</a></li>
              <li><a href="#testimonials" class="text-gray-400 hover:text-white transition-colors">Testimonials</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Documentation</a></li>
            </ul>
          </div>

          <div>
            <h3 class="text-lg font-semibold text-white mb-6">Company</h3>
            <ul class="space-y-3">
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">About Us</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Careers</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Blog</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Contact</a></li>
            </ul>
          </div>

          <div>
            <h3 class="text-lg font-semibold text-white mb-6">Legal</h3>
            <ul class="space-y-3">
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Privacy Policy</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Terms of Service</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Cookie Policy</a></li>
              <li><a href="#" class="text-gray-400 hover:text-white transition-colors">Compliance</a></li>
            </ul>
          </div>
        </div>

        <div class="mt-12 pt-8 border-t border-gray-800 flex flex-col md:flex-row justify-between items-center">
          <p class="text-gray-500 text-sm">
            © 2026 MTax Studio. All rights reserved. Designed for Zimbabwe's music industry.
          </p>
          <div class="flex space-x-6 mt-4 md:mt-0">
            <a href="#" class="text-gray-400 hover:text-white transition-colors">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/>
              </svg>
            </a>
            <a href="#" class="text-gray-400 hover:text-white transition-colors">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M23.953 4.57a10 10 0 01-2.825.775 4.958 4.958 0 002.163-2.723c-.951.555-2.005.959-3.127 1.184a4.92 4.92 0 00-8.384 4.482C7.69 8.095 4.067 6.13 1.64 3.162a4.822 4.822 0 00-.666 2.475c0 1.71.87 3.213 2.188 4.096a4.904 4.904 0 01-2.228-.616v.06a4.923 4.923 0 003.946 4.827 4.996 4.996 0 01-2.212.085 4.936 4.936 0 004.604 3.417 9.867 9.867 0 01-6.102 2.105c-.39 0-.779-.023-1.17-.067a13.995 13.995 0 007.557 2.213c9.053 0 13.998-7.496 13.998-13.985 0-.21 0-.42-.015-.63A9.935 9.935 0 0024 4.59z"/>
              </svg>
            </a>
            <a href="#" class="text-gray-400 hover:text-white transition-colors">
              <svg class="w-6 h-6" fill="currentColor" viewBox="0 0 24 24">
                <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433c-1.144 0-2.063-.926-2.063-2.065 0-1.138.92-2.063 2.063-2.063 1.14 0 2.064.925 2.064 2.063 0 1.139-.925 2.065-2.064 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/>
              </svg>
            </a>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const isMobileMenuOpen = ref(false)

const scrollToFeatures = () => {
  const featuresSection = document.getElementById('features')
  if (featuresSection) {
    featuresSection.scrollIntoView({ behavior: 'smooth' })
  }
  isMobileMenuOpen.value = false
}

// Handle anchor link clicks for smooth scrolling
const handleAnchorClick = (e) => {
  const href = e.target.getAttribute('href')
  if (href && href.startsWith('#')) {
    e.preventDefault()
    const targetId = href.substring(1)
    const targetElement = document.getElementById(targetId)
    if (targetElement) {
      targetElement.scrollIntoView({ behavior: 'smooth' })
    }
    isMobileMenuOpen.value = false
  }
}
</script>

<style scoped>
/* Custom scrollbar */
::-webkit-scrollbar {
  width: 10px;
}

::-webkit-scrollbar-track {
  background: #111827;
}

::-webkit-scrollbar-thumb {
  background: #374151;
  border-radius: 5px;
}

::-webkit-scrollbar-thumb:hover {
  background: #4b5563;
}

/* Smooth animations */
.section-enter {
  opacity: 0;
  transform: translateY(20px);
}

.section-enter-active {
  transition: opacity 0.5s, transform 0.5s;
}

/* Gradient text animation */
@keyframes gradient {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

.animate-gradient {
  background-size: 200% auto;
  animation: gradient 3s ease infinite;
}
</style>