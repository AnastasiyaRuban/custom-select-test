<script setup lang="ts">
import { ref } from 'vue';
import CustomSelect from './components/CustomSelect.vue'

const options = [
  { value: 'JS', label: 'JavaScript' },
  { value: 'TS', label: 'TypeScript' },
  { value: 'PHP', label: 'PHP' },
  { value: 'Верстка', label: 'Веб-верстка' },
  { value: 'Laravel', label: 'Laravel' },
  { value: 'Symfony', label: 'Symfony' },
  { value: 'MySQL', label: 'MySQL' },
];

const selectedValue = ref('');
const isDisabled = ref(false);
</script>

<template>
  <h1>
    Тестовое Skillbox
  </h1>
  <div class="container">
    <form>
      <CustomSelect
          :options="options"
          placeholder="Выберите курс"
          v-model="selectedValue"
        :disabled="isDisabled"
      >
        <template #placeholder="{ selected }">
            <span v-if="selected">{{ selected.label }}</span>
          <span v-else>Список курсов</span>
        </template>
      </CustomSelect>

    </form>

  </div>
</template>

<style scoped>
  .container {
    display: flex;
    justify-content: center;
  }
</style>
