<template>
    <div class="card shadow-sm">
        <div class="card-header bg-primary text-white">
            <h2 class="mb-0">Exchange Rates</h2>
        </div>
        <div class="card-body max-height">
            <div class="mb-3 position-sticky top-16">
                <input v-model="filterText" type="text" class="form-control" placeholder="Filter by country or rate" />
            </div>
            <table class="table table-bordered table-hover">
                <thead class="table-light">
                    <tr>
                        <th>Country</th>
                        <th>Rate</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(rate, currency) in filteredRates" :key="currency">
                        <td>{{ getCountryName(currency) }}</td>
                        <td>{{ rate }}</td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'CurrencyTable',
    data() {
        return {
            rates: {},
            currencies: {},
            filterText: '',
        };
    },
    created() {
        this.fetchData();
    },
    computed: {
        filteredRates() {
            const search = this.filterText.toLowerCase();
            return Object.entries(this.rates).filter(
                ([currency, rate]) => {
                    const countryName = this.getCountryName(currency).toLowerCase();
                    return countryName.includes(search) || rate.toString().includes(search);
                }
            );
        },
    },
    methods: {
        async fetchData() {
            try {
                const ratesResponse = await axios.get(
                    'https://api.currencybeacon.com/v1/latest?api_key=1SMxQumaAcC996SUXGsnlBPW44t2RR82'
                );
                this.rates = ratesResponse.data.response.rates;

                const currenciesResponse = await axios.get(
                    'https://api.currencybeacon.com/v1/currencies?api_key=1SMxQumaAcC996SUXGsnlBPW44t2RR82'
                );
                this.currencies = currenciesResponse.data.response;
            } catch (error) {
                console.error('Error fetching data:', error.message);
            }
        },
        getCountryName(currencyCode) {
            const currencyData = this.currencies[currencyCode];
            return currencyData ? currencyData.name : currencyCode;
        },
    },
};
</script>