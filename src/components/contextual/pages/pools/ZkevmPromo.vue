<script setup lang="ts">
import useNetwork from '@/composables/useNetwork';
const { networkSlug } = useNetwork();
</script>

<template>
  <section
    v-if="networkSlug != 'zkevm'"
    class="box-border overflow-hidden px-4 xl:px-0 mr-6 mb-8 w-full rounded-xl promo"
  >
    <div class="promo-bg">
      <div
        class="flex flex-col lg:flex-row lg:justify-between lg:items-end p-4"
      >
        <div class="z-10 max-w-3xl">
          <h5 class="mb-1 font-bold text-white">
            Get ready for BAL incentives for Polygon zkEVM
          </h5>
          <p class="md:text-sm leading-6 text-white">
            BAL incentives on selected Polygon zkEVM pools go live on Thursday
            29 June. veBAL holders can vote for these pools now. LPs can prepare
            by bridging and adding liquidity to the Polygon zkEVM network.
          </p>
        </div>
        <div class="z-10 mt-4">
          <div class="promo-btn">
            <router-link to="/zkevm">Launch Polygon zkEVM</router-link>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.promo-bg {
  background: url('/images/backgrounds/zkevm-promo-bg.jpg');
  @apply transition-all duration-1000 rounded-xl overflow-hidden relative bg-cover h-auto bg-no-repeat;
}

.promo-bg::after {
  content: '';
  background: inherit;
  @apply absolute top-0 right-0 bottom-0 left-0 bg-cover origin-center transition-all duration-500 ease-out;
}

.promo-bg:hover::after {
  @apply scale-125;
}

.promo-btn {
  @apply text-white text-sm border border-white p-2 rounded-lg bg-black/10 hover:bg-black/20 focus:bg-black/20 transition-colors inline-block cursor-pointer;

  font-variation-settings: 'wght' 600;
}
</style>
