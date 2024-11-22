<template>
  <div class="card shadow-sm">
    <div class="card-header bg-primary text-white">
      <h2 class="mb-0">Currency Converter</h2>
    </div>
    <div class="card-body">
      <form @submit.prevent="convertCurrency">
        <div class="row">
          <div class="col-md-6">
            <div class="mb-3 text-start">
              <label for="from" class="form-label">From:</label>
              <select id="from" v-model="fromCurrency" class="form-select" :disabled="loading || !currencies.length">
                <option v-if="loading" value="">Loading...</option>
                <option v-for="currency in currencies" :key="currency.short_code" :value="currency.short_code">
                  {{ currency.name }}
                </option>
              </select>
              <div v-if="!fromCurrency && showError" class="text-danger mt-1">
                Please select a valid currency.
              </div>
            </div>
          </div>
          <div class="col-md-6">
            <div class="mb-3 text-start">
              <label for="to" class="form-label">To:</label>
              <select id="to" v-model="toCurrency" class="form-select" :disabled="loading || !currencies.length">
                <option v-if="loading" value="">Loading...</option>
                <option v-for="currency in currencies" :key="currency.short_code" :value="currency.short_code">
                  {{ currency.name }}
                </option>
              </select>
              <div v-if="!toCurrency && showError" class="text-danger mt-1">
                Please select a valid currency.
              </div>
            </div>
          </div>
        </div>
        <div class="mb-3">
          <label for="amount" class="form-label">Amount:</label>
          <input type="number" id="amount" v-model="fromAmount" class="form-control" placeholder="Enter amount"
            :class="{ 'is-invalid': !isValidAmount && showError }" />
          <div v-if="!isValidAmount && showError" class="text-danger mt-1">
            Please enter a valid amount greater than zero.
          </div>
        </div>
        <button type="submit" class="btn btn-primary w-100 mt-3"
          :disabled="loading || !currencies.length || conversionLoading">
          {{ conversionLoading ? 'Converting...' : 'Convert' }}
        </button>
      </form>
      <div v-if="toAmount !== null" class="alert alert-success mt-3">
        <strong>Converted Amount:</strong> {{ toAmount }}
      </div>
      <div v-if="errorMessage" class="alert alert-danger mt-3">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "CurrencyConverter",
  data() {
    return {
      currencies: [],
      fromCurrency: "",
      toCurrency: "",
      fromAmount: "",
      toAmount: null,
      loading: false,
      conversionLoading: false,
      errorMessage: "",
      showError: false,
    };
  },
  created() {
    this.fetchCurrencies();
  },
  computed: {
    isValidAmount() {
      return this.fromAmount && this.fromAmount > 0;
    },
  },
  methods: {
    async fetchCurrencies() {
      this.loading = true;
      this.errorMessage = "";
      try {
        const response = await axios.get(
          "https://api.currencybeacon.com/v1/currencies?api_key=1SMxQumaAcC996SUXGsnlBPW44t2RR82"
        );

        if (response.data.response) {
          this.currencies = Object.values(response.data.response);
          this.fromCurrency = this.currencies[0].short_code;
          this.toCurrency = this.currencies[1].short_code;
        } else {
          this.errorMessage = "Failed to fetch currency data.";
        }
      } catch (error) {
        this.errorMessage = "Error fetching currencies. Please try again later.";
        console.error("Currency Fetch Error:", error.message);
      } finally {
        this.loading = false;
      }
    },
    async convertCurrency() {
      this.showError = false;

      if (!this.fromCurrency || !this.toCurrency || !this.isValidAmount) {
        this.showError = true;
        return;
      }

      this.conversionLoading = true;
      this.errorMessage = "";

      try {
        const response = await axios.get(
          `https://api.currencybeacon.com/v1/convert?api_key=1SMxQumaAcC996SUXGsnlBPW44t2RR82&from=${this.fromCurrency}&to=${this.toCurrency}&amount=${this.fromAmount}`
        );

        if (response.data.response) {
          this.toAmount = response.data.response.value.toFixed(2);
        } else {
          this.errorMessage = "Failed to convert currency.";
        }
      } catch (error) {
        this.errorMessage = "Error converting currency. Please try again later.";
        console.error("Conversion Error:", error.message);
      } finally {
        this.conversionLoading = false;
      }
    },
  },
};
</script>