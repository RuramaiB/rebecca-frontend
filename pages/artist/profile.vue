<template>
  <NuxtLayout name="artist-layout">
    <div class="p-6">
      <div class="max-w-4xl mx-auto">
        <!-- Header -->
        <div class="mb-8">
          <h2 class="text-2xl font-bold text-white mb-2">Profile Settings</h2>
          <p class="text-gray-400">Manage your account information and personal details</p>
        </div>

        <!-- Loading State -->
        <div v-if="isLoading" class="bg-gray-900 border border-gray-800 rounded-xl p-6">
          <div class="flex items-center justify-center py-12">
            <svg class="animate-spin h-10 w-10 text-red-600" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
              <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
              <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
            </svg>
          </div>
        </div>

        <!-- Profile Card -->
        <div v-else class="bg-gray-900 border border-gray-800 rounded-xl p-6">
          <div class="flex items-center justify-between mb-8">
            <div>
              <h3 class="text-xl font-semibold text-white mb-2">Personal Information</h3>
              <p class="text-gray-400 text-sm">Update your personal details and profile picture</p>
            </div>
            <button 
              v-if="!isEditing"
              @click="isEditing = true"
              class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm flex items-center space-x-2"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path>
              </svg>
              <span>Edit Profile</span>
            </button>
          </div>

          <!-- Profile Picture Section -->
          <div class="mb-8">
            <div class="flex items-center space-x-6">
              <div class="relative">
                <!-- Show Google profile picture if available, otherwise show initials -->
                <div v-if="userProfile.picture" class="w-24 h-24 rounded-full overflow-hidden border-2 border-gray-700">
                  <img 
                    :src="userProfile.picture" 
                    :alt="userProfile.name"
                    class="w-full h-full object-cover"
                    @error="handleImageError"
                  >
                </div>
                <div v-else class="w-24 h-24 rounded-full bg-gradient-to-br from-red-600 to-red-700 flex items-center justify-center text-2xl font-bold text-white">
                  {{ userProfile.initials }}
                </div>
                <button 
                  v-if="isEditing"
                  @click="triggerFileUpload"
                  class="absolute bottom-0 right-0 w-8 h-8 bg-gray-800 border border-gray-700 rounded-full flex items-center justify-center hover:bg-gray-700 transition-colors"
                >
                  <svg class="w-4 h-4 text-gray-300" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path>
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"></path>
                  </svg>
                </button>
                <input 
                  ref="fileInput"
                  type="file" 
                  accept="image/*"
                  class="hidden"
                  @change="handleFileUpload"
                >
              </div>
              <div>
                <div class="flex items-center space-x-2">
                  <h4 class="text-lg font-semibold text-white">{{ userProfile.name }}</h4>
                  <span v-if="userProfile.emailVerified" class="flex items-center text-xs bg-green-500/10 text-green-400 px-2 py-1 rounded-full">
                    <svg class="w-3 h-3 mr-1" fill="currentColor" viewBox="0 0 20 20">
                      <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd"></path>
                    </svg>
                    Verified
                  </span>
                </div>
                <p class="text-gray-400">{{ userProfile.role }}</p>
                <p class="text-gray-500 text-sm mt-1">{{ userProfile.email }}</p>
                <div v-if="isEditing" class="mt-2">
                  <p class="text-xs text-gray-500">Click the camera icon to upload a new profile picture</p>
                </div>
              </div>
            </div>
          </div>

          <!-- Compliance & Content Summary -->
          <div v-if="complianceData" class="mb-8 p-6 bg-gray-800/30 border border-gray-700 rounded-xl">
            <h4 class="text-sm font-semibold text-gray-400 uppercase mb-4 flex items-center justify-between">
              <span>Tax & Content Compliance</span>
              <span :class="complianceData.taxCompliant ? 'text-green-400' : 'text-red-400'" class="text-[10px] px-2 py-0.5 rounded-full border border-current font-bold uppercase tracking-widest">
                {{ complianceData.taxCompliant ? 'Compliant' : 'Uncompliant' }}
              </span>
            </h4>
            
            <div class="grid grid-cols-2 lg:grid-cols-4 gap-6">
              <div>
                <p class="text-xs text-gray-500 mb-1">DST Registration</p>
                <p class="text-sm" :class="complianceData.registeredForDigitalTax ? 'text-blue-400' : 'text-gray-400'">
                  {{ complianceData.registeredForDigitalTax ? 'Registered' : 'Pending' }}
                </p>
              </div>
              <div>
                <p class="text-xs text-gray-500 mb-1">Compliance Level</p>
                <p class="text-sm font-semibold" :class="getComplianceLevelClass(complianceData.complianceLevel).split(' ')[1]">
                  {{ complianceData.complianceLevel || 'N/A' }}
                </p>
              </div>
              <div>
                <p class="text-xs text-gray-500 mb-1">Total Content</p>
                <div class="flex items-center space-x-3 mt-1">
                  <div class="text-xs text-white bg-gray-700 px-2 py-0.5 rounded">
                    {{ complianceData.totalVideosToDate || 0 }} Videos
                  </div>
                  <div class="text-[10px] text-red-500 bg-red-500/10 px-2 py-0.5 rounded border border-red-500/20 font-bold">
                    {{ complianceData.totalShotsToDate || 0 }} Shots
                  </div>
                </div>
              </div>
              <div>
                <p class="text-xs text-gray-500 mb-1">Financial Summary</p>
                <p class="text-sm text-white font-medium">
                  Tax Paid: <span class="text-green-400">${{ complianceData.totalTaxPaid?.toLocaleString() || '0' }}</span>
                </p>
                <p class="text-[10px] text-red-400 font-mono mt-0.5">
                  Due: ${{ complianceData.outstandingTax?.toLocaleString() || '0' }}
                </p>
              </div>
            </div>
          </div>

          <!-- Profile Form -->
          <form @submit.prevent="updateProfile" class="space-y-6">
            <div class="grid md:grid-cols-2 gap-6">
              <div>
                <label class="block text-sm font-medium text-gray-400 mb-2">
                  First Name <span class="text-red-500">*</span>
                </label>
                <input
                  v-model="formData.firstName"
                  type="text"
                  required
                  :disabled="!isEditing"
                  :class="[
                    'w-full bg-gray-800 border rounded-lg px-4 py-3 text-white focus:outline-none transition-colors',
                    isEditing 
                      ? 'border-gray-700 focus:border-red-600 focus:ring-1 focus:ring-red-600' 
                      : 'border-gray-800 cursor-not-allowed'
                  ]"
                >
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-400 mb-2">
                  Last Name <span class="text-red-500">*</span>
                </label>
                <input
                  v-model="formData.lastName"
                  type="text"
                  required
                  :disabled="!isEditing"
                  :class="[
                    'w-full bg-gray-800 border rounded-lg px-4 py-3 text-white focus:outline-none transition-colors',
                    isEditing 
                      ? 'border-gray-700 focus:border-red-600 focus:ring-1 focus:ring-red-600' 
                      : 'border-gray-800 cursor-not-allowed'
                  ]"
                >
              </div>
            </div>

            <div>
              <label class="block text-sm font-medium text-gray-400 mb-2">
                Email Address <span class="text-red-500">*</span>
              </label>
              <input
                v-model="formData.email"
                type="email"
                required
                disabled
                class="w-full bg-gray-800 border border-gray-800 rounded-lg px-4 py-3 text-white focus:outline-none transition-colors cursor-not-allowed opacity-60"
              >
              <p class="text-xs text-gray-500 mt-1">Email cannot be changed</p>
            </div>

          

            <!-- Action Buttons -->
            <div v-if="isEditing" class="flex items-center justify-between pt-6 border-t border-gray-800">
              <button
                type="button"
                @click="cancelEditing"
                class="px-6 py-2.5 border border-gray-700 text-gray-300 hover:bg-gray-800 rounded-lg font-medium transition-colors"
              >
                Cancel
              </button>
              <button
                type="submit"
                :disabled="isSaving"
                :class="[
                  'px-6 py-2.5 rounded-lg font-medium transition-colors flex items-center space-x-2',
                  isSaving
                    ? 'bg-gray-700 cursor-not-allowed'
                    : 'bg-red-600 hover:bg-red-700 text-white'
                ]"
              >
                <svg v-if="isSaving" class="animate-spin h-4 w-4 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                <span>{{ isSaving ? 'Saving...' : 'Save Changes' }}</span>
              </button>
            </div>
          </form>
        </div>

        <!-- Success/Error Messages -->
        <div v-if="successMessage" class="mt-6 p-4 bg-green-500/10 border border-green-500/20 rounded-xl">
          <div class="flex items-center">
            <svg class="w-5 h-5 text-green-500 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path>
            </svg>
            <p class="text-sm text-green-400">{{ successMessage }}</p>
          </div>
        </div>

        <div v-if="errorMessage" class="mt-6 p-4 bg-red-500/10 border border-red-500/20 rounded-xl">
          <div class="flex items-center">
            <svg class="w-5 h-5 text-red-500 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-2.5L13.732 4c-.77-.833-1.998-.833-2.732 0L4.346 16.5c-.77.833.192 2.5 1.732 2.5z"></path>
            </svg>
            <p class="text-sm text-red-400">{{ errorMessage }}</p>
          </div>
        </div>
      </div>
    </div>
  </NuxtLayout>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'

// State
const isEditing = ref(false)
const isSaving = ref(false)
const isLoading = ref(true)
const successMessage = ref('')
const errorMessage = ref('')
const fileInput = ref(null)

// User Profile Data
const userProfile = reactive({
  googleAccountId: '',
  name: '',
  email: '',
  picture: '',
  emailVerified: false,
  role: 'User',
  initials: '',
  joinedDate: '',
  lastUpdated: '',
  firstName: '',
  lastName: '',
  
})

// Compliance Data
const complianceData = ref(null)

// Form Data
const formData = reactive({
  firstName: '',
  lastName: '',
  email: '',
  phone: '',
  jobTitle: '',
  department: '',
  bio: ''
})

// Methods
const formatDate = (dateString) => {
  if (!dateString) return 'N/A'
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', {
    month: 'short',
    day: 'numeric',
    year: 'numeric'
  })
}

const loadProfileData = () => {
  formData.firstName = userProfile.firstName
  formData.lastName = userProfile.lastName
  formData.email = userProfile.email
  formData.phone = userProfile.phone
  formData.jobTitle = userProfile.jobTitle
  formData.department = userProfile.department
  formData.bio = userProfile.bio
}

// Fetch profile from backend
const fetchProfile = async () => {
  isLoading.value = true
  errorMessage.value = ''
  
  try {
    // TODO: Get the actual logged-in user's email from your auth system
    // For now, using a default email for testing
    const artistID = localStorage.getItem('artistID')     
    const response = await fetch(`http://localhost:8080/oauth2/get-profile-by-/${artistID}`)
    
    if (!response.ok) {
      throw new Error('Failed to fetch profile')
    }
    
    const data = await response.json()
    
    // Update user profile with backend data
    userProfile.googleAccountId = data.googleAccountId
    userProfile.email = data.email
    userProfile.name = data.name
    userProfile.picture = data.picture
    userProfile.emailVerified = data.emailVerified
    
    // Parse name into first and last name
    const nameParts = data.name.split(' ')
    userProfile.firstName = nameParts[0] || ''
    userProfile.lastName = nameParts.slice(1).join(' ') || ''
    
    // Generate initials
    userProfile.initials = nameParts.map(part => part[0]).join('').toUpperCase().slice(0, 2)
    
    // Set dates
    userProfile.joinedDate = data.createdAt || new Date().toISOString()
    userProfile.lastUpdated = data.updatedAt || new Date().toISOString()
    
    // Fetch compliance data
    const complianceRes = await fetch(`http://localhost:8080/api/compliance/${artistID}`)
    if (complianceRes.ok) {
      complianceData.value = await complianceRes.json()
    }
    
    // Load form data
    loadProfileData()
    
  } catch (error) {
    console.error('Error fetching profile:', error)
    errorMessage.value = 'Failed to load profile data. Please try again.'
  } finally {
    isLoading.value = false
  }
}

const updateProfile = async () => {
  if (!isEditing.value) return

  // Validate form
  if (!formData.firstName || !formData.lastName || !formData.email) {
    errorMessage.value = 'Please fill in all required fields'
    setTimeout(() => errorMessage.value = '', 3000)
    return
  }

  isSaving.value = true
  successMessage.value = ''
  errorMessage.value = ''

  try {
    // TODO: Replace with your actual API endpoint for updating profile
    // Simulate API call
    await new Promise(resolve => setTimeout(resolve, 1500))

    // Update user profile
    userProfile.firstName = formData.firstName
    userProfile.lastName = formData.lastName
    userProfile.name = `${formData.firstName} ${formData.lastName}`
    userProfile.phone = formData.phone
    userProfile.jobTitle = formData.jobTitle
    userProfile.department = formData.department
    userProfile.bio = formData.bio
    userProfile.initials = `${formData.firstName[0]}${formData.lastName[0]}`.toUpperCase()
    userProfile.lastUpdated = new Date().toISOString()

    // Show success message
    successMessage.value = 'Profile updated successfully!'
    isEditing.value = false

    // Clear success message after 3 seconds
    setTimeout(() => {
      successMessage.value = ''
    }, 3000)

  } catch (error) {
    errorMessage.value = 'Failed to update profile. Please try again.'
    console.error('Profile update error:', error)
  } finally {
    isSaving.value = false
  }
}

const cancelEditing = () => {
  loadProfileData()
  isEditing.value = false
  successMessage.value = ''
  errorMessage.value = ''
}

const triggerFileUpload = () => {
  if (fileInput.value) {
    fileInput.value.click()
  }
}

const handleFileUpload = (event) => {
  const file = event.target.files[0]
  if (file) {
    // Validate file size (max 5MB)
    if (file.size > 5 * 1024 * 1024) {
      errorMessage.value = 'File size must be less than 5MB'
      setTimeout(() => errorMessage.value = '', 3000)
      return
    }

    // Validate file type
    if (!file.type.startsWith('image/')) {
      errorMessage.value = 'Please upload a valid image file'
      setTimeout(() => errorMessage.value = '', 3000)
      return
    }

    const reader = new FileReader()
    reader.onload = (e) => {
      // TODO: Upload to your backend server
      // For now, just update the local picture
      userProfile.picture = e.target.result
      successMessage.value = 'Profile picture updated successfully!'
      setTimeout(() => {
        successMessage.value = ''
      }, 3000)
    }
    reader.readAsDataURL(file)
  }
}

const handleImageError = (event) => {
  // Fallback to initials if image fails to load
  console.error('Failed to load profile image')
  userProfile.picture = ''
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

// Initialize form data
onMounted(() => {
  fetchProfile()
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

/* Smooth transitions */
.transition-all {
  transition-property: all;
  transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
  transition-duration: 300ms;
}

/* Input focus styles */
input:focus, select:focus, textarea:focus {
  outline: none;
  box-shadow: 0 0 0 3px rgba(239, 68, 68, 0.1);
}

/* Disabled input styles */
input:disabled, select:disabled, textarea:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

/* Profile image styles */
.w-24.h-24 img {
  object-fit: cover;
}

/* Animation for spinner */
@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.animate-spin {
  animation: spin 1s linear infinite;
}
</style>