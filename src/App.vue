<template>
  <div id="app" class="container mt-5">
    <main>
      <div class="row">
        <div class="col-md-10 mx-auto">
          <CurrencyConverter />
        </div>
      </div>
      <div class="row mt-4">
        <div class="col-md-10 mx-auto">
          <CurrencyTable />
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import CurrencyConverter from './components/CurrencyConverter.vue';
import CurrencyTable from './components/CurrencyTable.vue';

export default {
  name: 'App',
  components: {
    CurrencyConverter,
    CurrencyTable,
  },
};
</script>