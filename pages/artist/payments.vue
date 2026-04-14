<template>
  <NuxtLayout name="artist-layout">
    <div class="min-h-screen bg-gray-850 dark:bg-gray-950 p-4 md:p-6 space-y-6">
      <!-- Header -->
      <div
        class="flex flex-col sm:flex-row sm:justify-between sm:items-center gap-4"
      >
        <div>
          <h1
            class="text-2xl md:text-3xl font-bold text-red-600 dark:text-white"
          >
            Payments
          </h1>
          <p class="text-gray-600 dark:text-gray-400 mt-1">
            Manage your tax payments and history
          </p>
        </div>

        <button
          @click="openPaymentModal"
          class="bg-gradient-to-r from-red-600 to-red-700 hover:from-red-700 hover:to-red-800 text-white px-5 py-3 rounded-lg font-medium flex items-center justify-center gap-2 shadow-lg hover:shadow-xl transition-all duration-200 w-full sm:w-auto"
        >
          <svg
            class="w-5 h-5"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M12 6v6m0 0v6m0-6h6m-6 0H6"
            />
          </svg>
          Make Payment
        </button>
      </div>

      <!-- Compliance Summary Cards -->
      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
        <SummaryCard
          title="Status"
          :value="taxCompliance?.taxCompliant ? 'Compliant' : 'Non-Compliant'"
          :color="taxCompliance?.taxCompliant ? 'green' : 'red'"
          icon="check-circle"
          :trend="taxCompliance?.taxCompliant ? 'positive' : 'negative'"
        />

        <SummaryCard
          title="Total Revenue"
          :value="`$${formatNumber(taxCompliance?.totalRevenueToDate)}`"
          color="blue"
          icon="currency-dollar"
        />

        <SummaryCard
          title="Outstanding Tax"
          :value="`$${formatNumber(taxCompliance?.outstandingTax)}`"
          color="red"
          icon="exclamation-circle"
        />

        <SummaryCard
          title="Total Paid"
          :value="`$${formatNumber(taxCompliance?.totalTaxPaid)}`"
          color="green"
          icon="check-circle"
        />
      </div>

      <!-- Payment History Card -->
      <div
        class="bg-gray-900 rounded-2xl shadow-sm border border-gray-200 border-gray-800 overflow-hidden"
      >
        <div class="px-6 py-4 border-b border-gray-200 dark:border-gray-800">
          <div class="flex items-center justify-between">
            <div>
              <h2 class="text-lg font-semibold text-red-600 dark:text-white">
                Payment History
              </h2>
              <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">
                Recent tax payments and transactions
              </p>
            </div>
            <div class="flex items-center gap-2">
              <button
                @click="refreshPayments"
                class="p-2 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-300 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-800 transition-colors"
                :disabled="isLoading"
              >
                <svg
                  class="w-5 h-5"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"
                  />
                </svg>
              </button>
            </div>
          </div>
        </div>

        <div class="overflow-x-auto">
  <table class="w-full border-collapse">
    <thead class="bg-gray-950 dark:bg-gray-800/50">
      <tr>
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
          Payment ID
        </th>
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
          Amount
        </th>
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
          Method
        </th>
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
          Status
        </th>
        <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase">
          Date
        </th>
      </tr>
    </thead>

    <tbody class="divide-y divide-gray-200 dark:divide-gray-800">

      <!-- Loading State -->
      <tr v-if="isLoading" v-for="i in 3" :key="i">
        <td colspan="5" class="px-6 py-4">
          <div class="animate-pulse space-y-2">
            <div class="h-4 bg-gray-200 dark:bg-gray-800 rounded w-3/4"></div>
          </div>
        </td>
      </tr>

      <!-- Empty State -->
      <tr v-else-if="payments.length === 0">
        <td colspan="5" class="px-6 py-12 text-center">
          <div class="text-gray-500 dark:text-gray-400">
            <svg class="w-12 h-12 mx-auto mb-3 opacity-50" fill="none" stroke="currentColor" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"
                d="M9 12h3.75M9 15h3.75M9 18h3.75" />
            </svg>
            <p class="text-lg font-medium">No payments found</p>
            <p class="text-sm mt-1">Your payment history will appear here</p>
          </div>
        </td>
      </tr>

      <!-- Data Rows -->
      <tr
        v-else
        v-for="p in payments"
        :key="p.paymentID"
        class="hover:bg-gray-800 transition-colors cursor-pointer"
        @click="viewPaymentDetails(p)"
      >
        <td class="px-6 py-4">
          <div class="flex items-center gap-2">
            <span class="font-mono text-sm text-gray-400">
              {{ p.paymentID.slice(0, 8) }}...
            </span>
            <button
              @click.stop="copyPaymentId(p.paymentID)"
              class="text-gray-400 hover:text-gray-300"
            >
              <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M8 16H6a2 2 0 01-2-2V6" />
              </svg>
            </button>
          </div>
        </td>

        <td class="px-6 py-4 font-semibold text-gray-400">
          ${{ formatNumber(p.amount) }}
        </td>

        <td class="px-6 py-4">
          <span :class="badgeMethod(p.method)" class="inline-flex items-center gap-1.5">
            <PaymentMethodIcon :method="p.method" class="w-3 h-3" />
            {{ p.method }}
          </span>
        </td>

        <td class="px-6 py-4">
          <span :class="badgeStatus(p.paymentStatus)" class="inline-flex items-center gap-1.5">
            <span
              class="w-1.5 h-1.5 rounded-full"
              :class="statusDotColor(p.paymentStatus)"
            />
            {{ p.paymentStatus }}
          </span>
        </td>

        <td class="px-6 py-4">
          <div class="text-sm text-gray-400">
            {{ formatDate(p.dateOfPayment) }}
          </div>
          <div class="text-xs text-gray-500">
            {{ formatTime(p.dateOfPayment) }}
          </div>
        </td>
      </tr>

    </tbody>
  </table>
</div>

      </div>

      <!-- Make Payment Modal -->
      <Teleport to="body">
        <div
          v-if="showPaymentModal"
          class="fixed inset-0 z-50 overflow-y-auto"
          aria-labelledby="payment-modal-title"
          role="dialog"
          aria-modal="true"
        >
          <div
            class="flex items-center justify-center min-h-screen px-4 pt-4 pb-20 text-center sm:block sm:p-0"
          >
            <!-- Backdrop -->
            <div
              class="fixed inset-0 bg-black/50 backdrop-blur-sm transition-opacity"
              @click="closeModal"
            ></div>

            <!-- Modal Content -->
            <div
              class="inline-block align-bottom bg-gray-800 dark:bg-gray-900 rounded-2xl shadow-xl transform transition-all sm:my-8 sm:align-middle sm:max-w-lg sm:w-full"
            >
              <!-- Header -->
              <div class="px-6 pt-6 pb-4">
                <div class="flex items-center justify-between">
                  <div>
                    <h3
                      id="payment-modal-title"
                      class="text-xl font-bold text-red-600 dark:text-white"
                    >
                      Make Tax Payment
                    </h3>
                    <p class="text-sm text-gray-400 dark:text-gray-400 mt-1">
                      Enter payment details below
                    </p>
                  </div>
                  <button
                    @click="closeModal"
                    class="text-gray-400 hover:text-gray-600 dark:hover:text-gray-300 p-2 rounded-lg hover:bg-gray-100 dark:hover:bg-gray-800 transition-colors"
                  >
                    <svg
                      class="w-5 h-5"
                      fill="none"
                      stroke="currentColor"
                      viewBox="0 0 24 24"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        stroke-width="2"
                        d="M6 18L18 6M6 6l12 12"
                      />
                    </svg>
                  </button>
                </div>
              </div>

              <!-- Form -->
              <!-- Form -->
              <form
                @submit.prevent="submitPayment"
                class="px-6 pb-6 bg-gray-800 rounded-b-2xl"
              >
                <!-- Grid -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
                  <!-- Email -->
                  <div>
                    <label
                      class="block text-sm font-medium text-gray-300 mb-1 text-left"
                    >
                      Email Address
                    </label>
                    <input
                      v-model="paymentForm.email"
                      type="email"
                      required
                      :disabled="processingPayment"
                      placeholder="you@example.com"
                      class="w-full px-3 py-2.5 rounded-lg bg-gray-900 border border-gray-700 text-white placeholder-gray-500 focus:ring-2 focus:ring-red-500 focus:border-transparent"
                    />
                  </div>

                  <!-- Phone -->
                  <div>
                    <label
                      class="block text-sm font-medium text-gray-300 mb-1 text-left"
                    >
                      Phone Number
                    </label>
                    <input
                      v-model="paymentForm.phoneNumber"
                      type="tel"
                      required
                      :disabled="processingPayment"
                      placeholder="0770000000"
                      class="w-full px-3 py-2.5 rounded-lg bg-gray-900 border border-gray-700 text-white placeholder-gray-500 focus:ring-2 focus:ring-red-500 focus:border-transparent"
                    />
                  </div>

                  <!-- Amount -->
                  <div>
                    <label
                      class="block text-sm font-medium text-gray-300 mb-1 text-left"
                    >
                      Amount
                    </label>
                    <input
                      v-model.number="paymentForm.amount"
                      type="number"
                      min="1"
                      step="0.01"
                      required
                      :disabled="processingPayment"
                      placeholder="0.00"
                      class="w-full px-3 py-2.5 rounded-lg bg-gray-900 border border-gray-700 text-white placeholder-gray-500 focus:ring-2 focus:ring-red-500 focus:border-transparent"
                    />
                    <p class="text-xs text-gray-500 mt-1">
                      Tax amount due: ${{
                        formatNumber(taxCompliance?.outstandingTax)
                      }}
                    </p>
                  </div>

                  <!-- Payment Method (full width) -->
                  <div class="sm:col-span-2">
                    <label
                      class="block text-sm font-medium text-gray-300 mb-2 text-left"
                    >
                      Payment Method
                    </label>
                    <div class="grid grid-cols-3 gap-3">
                      <button
                        v-for="method in paymentMethods"
                        :key="method.id"
                        type="button"
                        @click="paymentForm.method = method.id"
                        :disabled="processingPayment"
                        :class="[
                          'p-3 rounded-lg border transition-all text-sm font-medium',
                          paymentForm.method === method.id
                            ? 'border-red-500 bg-red-600/20 text-white'
                            : 'border-gray-700 bg-gray-900 text-gray-300 hover:border-gray-600',
                        ]"
                      >
                        <component
                          :is="method.icon"
                          class="w-5 h-5 mx-auto mb-1"
                        />
                        {{ method.label }}
                      </button>
                    </div>
                  </div>

                  <!-- Narration (full width) -->
                  <div class="sm:col-span-2">
                    <label
                      class="block text-sm font-medium text-gray-300 mb-1 text-left"
                    >
                      Narration (Optional)
                    </label>
                    <textarea
                      v-model="paymentForm.narration"
                      rows="3"
                      :disabled="processingPayment"
                      placeholder="Add notes about this payment…"
                      class="w-full px-3 py-2.5 rounded-lg bg-gray-900 border border-gray-700 text-white placeholder-gray-500 resize-none focus:ring-2 focus:ring-red-500 focus:border-transparent"
                    ></textarea>
                  </div>
                </div>

                <!-- Actions -->
                <div class="flex gap-3 pt-6">
                  <button
                    type="button"
                    @click="closeModal"
                    class="flex-1 px-4 py-3 rounded-lg border border-gray-700 text-gray-300 hover:bg-gray-700 transition"
                    :disabled="processingPayment"
                  >
                    Cancel
                  </button>

                  <button
                    type="submit"
                    :disabled="processingPayment || !isFormValid"
                    class="flex-1 px-4 py-3 rounded-lg font-medium text-white bg-gradient-to-r from-red-600 to-red-700 hover:from-red-700 hover:to-red-800 disabled:opacity-50 disabled:cursor-not-allowed"
                  >
                    {{ processingPayment ? "Processing…" : "Submit Payment" }}
                  </button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </Teleport>
    </div>
  </NuxtLayout>
</template>

<script setup>
import { ref, computed, watchEffect } from "vue";

// Refs
const showPaymentModal = ref(false);
const processingPayment = ref(false);
const paymentError = ref(null);
const paymentSuccess = ref(null);
const isLoading = ref(true);

// Data
const artistEmail = localStorage.getItem("artistEmail");
const artistId = localStorage.getItem("artistID");

const paymentForm = ref({
  email: artistEmail,
  phoneNumber: "",
  amount: "",
  method: "",
  complianceID: "",
  narration: "",
});

const paymentMethods = [
  {
    id: "ECOCASH",
    label: "Ecocash",
    icon: "EcocashIcon",
    iconColor: "text-green-500",
  },
  {
    id: "ONEMONEY",
    label: "OneMoney",
    icon: "CreditCardIcon",
    iconColor: "text-blue-500",
  }
];

// Fetch data
const { data: complianceData, pending: compliancePending, refresh: refreshCompliance } = await useFetch(
  `http://localhost:8080/api/compliance/${artistId}`,
  { immediate: !!artistId }
);

const {
  data: paymentsData,
  pending,
  refresh,
} = await useFetch(
  `http://localhost:8080/api/paynow/get-all-payments-by-artist/${artistId}`
);

// Computed
const taxCompliance = computed(() => complianceData.value || null);

const payments = computed(() => paymentsData.value || []);

const isFormValid = computed(() => {
  return (
    paymentForm.value.email &&
    paymentForm.value.phoneNumber &&
    paymentForm.value.amount > 0 &&
    paymentForm.value.method &&
    paymentForm.value.complianceID
  );
});

// Watch effects
watchEffect(() => {
  isLoading.value = pending.value || compliancePending.value;
  if (taxCompliance.value?.complianceStatusId) {
    paymentForm.value.complianceID = taxCompliance.value.complianceStatusId;
  }
});

// Methods
const submitPayment = async () => {
  if (!isFormValid.value) return;

  paymentError.value = null;
  paymentSuccess.value = null;
  processingPayment.value = true;

  try {
    await $fetch("http://localhost:8080/api/paynow/makePayment", {
      method: "POST",
      body: paymentForm.value,
    });

    paymentSuccess.value =
      "Payment initiated successfully! Redirecting to payment gateway...";

    // Refresh payment and compliance balances
    await Promise.all([refresh(), refreshCompliance()]);

    // Close modal after success
    setTimeout(() => {
      closeModal();
    }, 2000);
  } catch (error) {
    paymentError.value =
      error.data?.message || "Payment failed. Please try again.";
  } finally {
    processingPayment.value = false;
  }
};

const closeModal = () => {
  showPaymentModal.value = false;
  paymentError.value = null;
  paymentSuccess.value = null;

  // Reset form fields (except email and complianceID)
  paymentForm.value.phoneNumber = "";
  paymentForm.value.amount = "";
  paymentForm.value.method = "";
  paymentForm.value.narration = "";
};

const openPaymentModal = () => {
  const outstanding = Number(taxCompliance.value?.outstandingTax || 0);
  if (outstanding > 0) {
    paymentForm.value.amount = Number(outstanding.toFixed(2));
  }
  showPaymentModal.value = true;
};

const refreshPayments = async () => {
  await Promise.all([refresh(), refreshCompliance()]);
};

const viewPaymentDetails = (payment) => {
  // Implement payment details view
  console.log("View payment:", payment);
};

const copyPaymentId = (id) => {
  navigator.clipboard.writeText(id);
  // Show toast notification
};

const formatNumber = (n) =>
  Number(n || 0).toLocaleString("en-US", {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2,
  });

const formatDate = (d) =>
  new Date(d).toLocaleDateString("en-US", {
    year: "numeric",
    month: "short",
    day: "numeric",
  });

const formatTime = (d) =>
  new Date(d).toLocaleTimeString("en-US", {
    hour: "2-digit",
    minute: "2-digit",
  });

const badgeMethod = (m) => {
  const base =
    "px-3 py-1.5 rounded-full text-xs font-medium inline-flex items-center gap-1.5";
  switch (m) {
    case "ECOCASH":
      return `${base} bg-green-50 dark:bg-green-900/20 text-green-700 dark:text-green-300`;
    case "CARD":
      return `${base} bg-blue-50 dark:bg-blue-900/20 text-blue-700 dark:text-blue-300`;
    case "BANK":
      return `${base} bg-purple-50 dark:bg-purple-900/20 text-purple-700 dark:text-purple-300`;
    default:
      return `${base} bg-gray-50 dark:bg-gray-800 text-gray-700 dark:text-gray-300`;
  }
};

const badgeStatus = (s) => {
  const base = "px-3 py-1.5 rounded-full text-xs font-medium";
  switch (s) {
    case "COMPLETED":
      return `${base} bg-green-50 dark:bg-green-900/20 text-green-700 dark:text-green-300`;
    case "PENDING":
      return `${base} bg-yellow-50 dark:bg-yellow-900/20 text-yellow-700 dark:text-yellow-300`;
    case "FAILED":
      return `${base} bg-red-50 dark:bg-red-900/20 text-red-700 dark:text-red-300`;
    default:
      return `${base} bg-gray-50 dark:bg-gray-800 text-gray-700 dark:text-gray-300`;
  }
};

const statusDotColor = (s) => {
  switch (s) {
    case "COMPLETED":
      return "bg-green-500";
    case "PENDING":
      return "bg-yellow-500";
    case "FAILED":
      return "bg-red-500";
    default:
      return "bg-gray-500";
  }
};
</script>
