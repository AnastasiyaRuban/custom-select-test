<script setup lang="ts">
    import { ref, watch, onMounted, onUnmounted } from 'vue';

    interface Option {
        value: string;
        label: string;
    }

    const props = defineProps<{
        options: Option[];
        placeholder?: string;
        modelValue?: string;
        disabled?: boolean;
    }>();

    const emit = defineEmits<{
        (e: 'update:modelValue', value: string): void;
    }>();

    const isOpen = ref(false);
    const selected = ref<Option | null>(null);
    const isFocused = ref(false);
    const highlightedOption = ref<Option | null>(null);

    const toggleDropdown = () => {
        if (props.disabled) return;
        isOpen.value = !isOpen.value;
        };

        const selectOption = (option: Option | null) => {
            console.log(option, selected, isOpen)
        if (!option) return;
        selected.value = option;
        emit('update:modelValue', option.value);
        isOpen.value = false;
        };

        const onFocus = () => {
        isFocused.value = true;
        };

        const onBlur = () => {
        isFocused.value = false;
        };

        const navigateUp = () => {
            
        if (!isOpen.value) return;
        let currentIndex = 0;
        props.options.forEach((el, index) => {
            if (el.value == highlightedOption.value?.value) {
                currentIndex = index;
            }
        });
        const newIndex = currentIndex > 0 ? currentIndex - 1 : props.options.length - 1;
        highlightedOption.value = props.options[newIndex];
        console.log(props.options[newIndex], highlightedOption)
        };

        const navigateDown = () => {
        if (!isOpen.value) return;
        let currentIndex = 0;
        props.options.forEach((el, index) => {
            if (el.value == highlightedOption.value?.value) {
                currentIndex = index;
            }
        });
        const newIndex = currentIndex < props.options.length - 1 ? currentIndex + 1 : 0;
        highlightedOption.value = props.options[newIndex];
        console.log(props.options[newIndex], highlightedOption)
        };

        watch(
        () => props.modelValue,
        (newValue) => {
            selected.value = props.options.find((option) => option.value === newValue) || null;
        },
        { immediate: true }
        );

        const handleClickOutside = (event: MouseEvent) => {
        if (!(event.target as HTMLElement).closest('.dropdown')) {
            isOpen.value = false;
        }
    };

    onMounted(() => {
        document.addEventListener('click', handleClickOutside);
    });

    onUnmounted(() => {
        document.removeEventListener('click', handleClickOutside);
    });

    const onNativeSelectChange = (event: Event) => {
        const value = (event.target as HTMLSelectElement).value;
        selectOption(props.options.find((option) => option.value === value) || null);
    };
</script>

<template>
 <div
    class="dropdown"
    :class="{ 'is-open': isOpen, 'is-focused': isFocused, 'is-disabled': disabled }"
    @focus="onFocus"
    @blur="onBlur"
    @keydown.up.prevent="navigateUp"
    @keydown.down.prevent="navigateDown"
    @keydown.enter.prevent="selectOption(highlightedOption)"
    tabindex="0"
  >
    <button
      @click="toggleDropdown"
      class="dropdown-button"
      :disabled="disabled"
      aria-haspopup="listbox"
      aria-controls="dropdown-menu"
      type="button"
    >
      <slot name="placeholder" :selected="selected">
        {{ selected ? selected.label : placeholder }}
      </slot>
    </button>
    <ul
      v-if="isOpen"
      id="dropdown-menu"
      class="dropdown-menu"
      role="listbox"
    >
     
        <li
            v-for="option in options"
          :key="option.value"
          @click="selectOption(option)"
          class="dropdown-item"
          :class="{ 'is-highlighted': option.value === highlightedOption?.value }"
          role="option"
          :aria-selected="option === selected"
        >
          {{ option.label }}
        </li>      
    </ul>
    <select
      v-show="false"
      :value="modelValue"
      @change="onNativeSelectChange"
    >
      <option
        v-for="option in options"
        :key="option.value"
        :value="option.value"
      >
        {{ option.label }}
      </option>
    </select>
  </div>
</template>

<style scoped>
    .dropdown {
        position: relative;
        display: block;
        width: 200px;
    }

    .dropdown-button {
        width: 100%;
        padding: 10px;
        background-color: #7060ff;
        color: rgb(6, 6, 94);
        border: 1px solid #ccc;
        cursor: pointer;
    }

    .dropdown-menu {
        position: absolute;
        top: 100%;
        left: 0;
        width: 100%;
        list-style: none;
        padding: 0;
        margin: 0;
        background-color: #7060ff;
        border: 1px solid #ccc;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    .dropdown-item {
        padding: 10px;
        cursor: pointer;
        color: rgb(6, 6, 94);
    }

    .dropdown-item:hover,
    .dropdown-item.is-highlighted {
        background-color: #89b9da;
    }

    .is-highlighted {
        background-color: #061d2c;
    }

    .is-open .dropdown-menu {
        display: block;
    }

    .is-focused .dropdown-button {
        border-color: #66afe9;
    }

    .is-disabled .dropdown-button {
        opacity: 0.5;
        cursor: not-allowed;
    }
</style>
