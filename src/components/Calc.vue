<script setup>
import { ref, onMounted, computed } from 'vue';
import Select from './Select.vue';
import axios from 'axios';

const necessaryRates = ['KZT', 'CNY'];
const multiplier = 100;
const discountPercentage = 0.8;

const rates = [
  {
    id: 1,
    title: 'Стандарт',
    value: 1,
  },
  {
    id: 2,
    title: 'Продвинутый',
    value: 1.5,
  }
];

const periods = [
  {
    id: 1,
    title: 'Месяц',
    value: 1,
  },
  {
    id: 2,
    title: 'Год',
    value: 12,
  },
];

const currencies = ref([]);

const selectedRate = ref();
const selectedCurrence = ref();
const selectedPeriod = ref();
const periodForMonth = computed(() => selectedPeriod.value === 1);

const multiplieredRateAndPeriod = computed(() => Number(selectedRate.value) * Number(selectedPeriod.value));

const summ = computed(() => {
  if (periodForMonth.value) {
    return multiplieredRateAndPeriod.value * multiplier * (+selectedCurrence.value).toFixed(2);
  }
  return Math.round(multiplieredRateAndPeriod.value * discountPercentage) * multiplier * (+selectedCurrence.value).toFixed(2);
});

const discount = computed(() => {
  return Math.round(multiplieredRateAndPeriod.value) * multiplier * (+selectedCurrence.value).toFixed(2) - summ.value;
});

onMounted(async () => {
  try {
    const { data } = await axios('https://www.cbr-xml-daily.ru/latest.js');
    const res = Object.entries(data.rates)
      .filter(rate => necessaryRates.includes(rate[0]))
      .map(([title, value], i) => ({ id: i, title, value: value }));
      res.push({ id: Math.random(), title: 'RUB', value: 1 });
    currencies.value = res;
  } catch (error) {
    console.log("OOps wrong");
  }
});
</script>

<template>
  <div class="calc">
    <div class="calc__actions">
      <Select title="Выберите тариф" :options="rates" v-model="selectedRate" />
      <Select title="Выберите валюту" :options="currencies" v-model="selectedCurrence" />
      <Select title="Выберите период оплаты" :options="periods" v-model="selectedPeriod" />
    </div>
    <p v-if="summ">Сумма к оплате: {{ summ }}</p>
    <p v-if="discount && !periodForMonth">Скидка: {{ discount }}</p>
    <h3 v-if="!summ">Выберите данные</h3>
  </div>
</template>

<style scoped>
.calc__actions {
  display: flex;
  gap: 16px;
}

@media (max-width: 768px) {
  .calc__actions {
    flex-direction: column;
  }
}
</style>
