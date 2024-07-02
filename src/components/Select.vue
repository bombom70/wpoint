<script setup>
defineProps({
  title: String,
  options: [{
    id: Number,
    title: String,
    value: Number,
  }],
});

const modelValue = defineModel();

</script>

<template>
  <div class="select">
    <span>{{ title }}:</span>
    <select class="select__content" v-model="modelValue">
      <option
        v-for="option in options"
        :key="option.id"
        :value="option.value"
        >
          {{ option.title }}
      </option>
    </select>
  </div>
</template>

<style scoped>
.select {
  display: flex;
  flex-direction: column;
}

.select__content {
  border-radius: 4px;
  padding: 4px;
}
</style>
