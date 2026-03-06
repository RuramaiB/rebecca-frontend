<template>
  <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-gray-950 via-gray-900 to-black px-4">

    <!-- Background grid -->
    <div
      class="absolute inset-0 opacity-[0.04] bg-[radial-gradient(#ffffff_1px,transparent_1px)] [background-size:24px_24px]" />

    <!-- Card -->
    <div class="relative w-full max-w-md">
      <div class="bg-gray-900/80 backdrop-blur-xl border border-gray-800 rounded-3xl shadow-2xl p-8">

        <!-- Brand -->
        <div class="text-center mb-8">
          <div
            class="mx-auto mb-4 h-14 w-14 rounded-2xl bg-gradient-to-br from-red-600 to-red-700 flex items-center justify-center shadow-lg">
            <span class="text-white font-bold text-xl">ZT</span>
          </div>
          <span class="text-xl font-bold text-white"> MTax<span class="text-red-500"> Studio</span></span>
          <p class="text-sm text-gray-400 mt-1">
            Zimbabwe Musician Tax Administration
          </p>
        </div>

        <!-- Welcome -->
        <div class="mb-6">
          <h2 class="text-lg font-medium text-white">Sign in</h2>
          <p class="text-sm text-gray-400">
            Use your Google account to access the admin dashboard
          </p>
        </div>

        <!-- Google Login -->
        <button @click="handleGoogleLogin" :disabled="isLoading" class="group w-full flex items-center justify-center gap-3 rounded-xl border border-gray-700 bg-gray-800/60 px-4 py-3.5 font-medium text-white transition
                 hover:bg-gray-800 hover:border-gray-600 active:scale-[0.98]
                 disabled:opacity-60 disabled:cursor-not-allowed">
          <svg class="h-5 w-5" viewBox="0 0 24 24">
            <path fill="#4285F4"
              d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z" />
            <path fill="#34A853"
              d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z" />
            <path fill="#FBBC05"
              d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22z" />
            <path fill="#EA4335"
              d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z" />
          </svg>

          <span>
            {{ isLoading ? 'Authenticating with Google…' : 'Continue with Google' }}
          </span>
        </button>

        <!-- Error -->
        <div v-if="error" class="mt-5 rounded-xl border border-red-500/20 bg-red-500/10 px-4 py-3 text-sm text-red-400">
          {{ error }}
        </div>

        <!-- Success -->
        <div v-if="success"
          class="mt-5 rounded-xl border border-green-500/20 bg-green-500/10 px-4 py-3 text-sm text-green-400">
          {{ success }}
        </div>

        <!-- Footer -->
        <div class="mt-8 border-t border-gray-800 pt-5 text-center text-xs text-gray-500">
          By continuing you agree to the
          <a href="#" class="text-red-400 hover:underline">Terms</a>
          &
          <a href="#" class="text-red-400 hover:underline">Privacy Policy</a>
        </div>
      </div>

      <!-- Back -->
      <div class="mt-6 text-center">
        <a href="/" class="inline-flex items-center gap-2 text-sm text-gray-500 hover:text-gray-300">
          <svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-width="2" stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7 7-7M3 12h18" />
          </svg>
          Back to home
        </a>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()

const isLoading = ref(false)
const error = ref('')
const success = ref('')
const role = ref('')

const API_BASE_URL = 'http://localhost:8080/oauth2'

// Check for token in URL on component mount
onMounted(() => {
  const token = route.query.token
  role.value = route.query.role

  if (token) {
    console.log('🔑 Token detected in URL')
    handleTokenFromBackend(token)
  }
})

const handleTokenFromBackend = async (token) => {
  try {
    localStorage.clear()
    function parseOAuthToken(str) {
      const result = {}

      const pairs = str
        .replace(/^OAuthToken\(|\)$/g, '')
        .split(/,(?![^\[]*\])/)

      pairs.forEach(p => {
        const [key, value] = p.split('=')
        result[key.trim()] = value?.trim()
      })

      return result
    }

    const parsed = parseOAuthToken(token)

    localStorage.setItem('artistID', parsed.artistId)
    localStorage.setItem('accessToken', parsed.accessToken)
    localStorage.setItem('refreshToken', parsed.refreshToken)
    localStorage.setItem('expiry', parsed.expiresAt)


    success.value = 'Login successful! Redirecting...'

    

    if (role.value === 'ADMIN') {
         router.replace({ query: {} })
      console.log('🚀 Redirecting to /admin/')
      setTimeout(() => router.push('/admin/'), 1000)
      return
    } else {
         router.replace({ query: {} })
      console.log('🚀 Redirecting to /artist/')
      setTimeout(() => router.push('/artist/'), 1000)
      return
    }
    // Clean URL
 

  } catch (e) {
    console.error('🔥 Error handling token:', e)
    error.value = 'Failed to process authentication'
  }
}

const handleGoogleLogin = async () => {
  console.log('🔵 handleGoogleLogin() invoked')

  error.value = ''
  success.value = ''
  isLoading.value = true

  const code = route.query.code
  const errorParam = route.query.error

  console.log('📌 Route query:', route.query)

  try {
    // =========================
    // ERROR HANDLING
    // =========================
    if (errorParam) {
      console.log('❌ OAuth error:', errorParam)
      if (errorParam === 'access_denied') {
        error.value = 'Google sign-in was cancelled'
      } else if (errorParam === 'invalid-callback') {
        error.value = 'Authentication failed. Please try again.'
      } else {
        error.value = 'An error occurred during sign-in'
      }
      router.replace({ query: {} })
      isLoading.value = false
      return
    }

    // =========================
    // CALLBACK FLOW
    // =========================
    if (code) {
      console.log('🟢 OAuth callback flow detected')
      console.log('➡️ Code:', code)

      success.value = 'Processing Google authentication...'

      // Backend will handle the redirect with token
      // The callback endpoint does response.sendRedirect() 
      // which will redirect to /auth/login?token=xxx
      // So we just need to let the browser follow the redirect

      const callbackUrl = `${API_BASE_URL}/callback/google?code=${encodeURIComponent(code)}`

      console.log('🌍 Redirecting to callback endpoint:', callbackUrl)
      window.location.href = callbackUrl

      return
    }

    // =========================
    // LOGIN INIT FLOW
    // =========================
    console.log('🟡 No code found — starting Google login')

    const response = await fetch(`${API_BASE_URL}/login`, {
      headers: { Accept: 'application/json' }
    })

    console.log('⬅️ Login endpoint status:', response.status)

    if (!response.ok) {
      throw new Error('Failed to initiate Google login')
    }

    const data = await response.json()
    console.log('✅ Login response:', data)

    if (!data.authorizationUrl) {
      throw new Error('No authorization URL received')
    }

    console.log('🌍 Redirecting to Google:', data.authorizationUrl)
    window.location.href = data.authorizationUrl

  } catch (e) {
    console.error('🔥 OAuth flow error:', e)
    error.value = e.message || 'Google sign-in failed'
    isLoading.value = false
  }
}
</script>


<style scoped>
/* Optional smooth entrance animation */
@keyframes fadeUp {
  from {
    opacity: 0;
    transform: translateY(12px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.relative {
  animation: fadeUp 0.4s ease-out;
}
</style>