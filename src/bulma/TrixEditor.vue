<template>
    <input :id="inputId"
        type="hidden"
        :value="normalizedValue">
    <trix-editor :input="inputId"
        ref="trix"
        @trix-change="update"/>
</template>

<script setup>

import 'trix/dist/trix.css';
import 'trix';
import { computed, nextTick, ref, watch } from 'vue';

defineOptions({
    inheritAttrs: false,
});

const props = defineProps({
    modelValue: {
        type: [String, null],
        default: '',
    },
});

const emit = defineEmits(['update:modelValue']);
const inputId = `trix-${Math.random().toString(36).slice(2)}`;
const trix = ref(null);
const normalizedValue = computed(() => props.modelValue ?? '');

const update = event => emit('update:modelValue', event.target.value);

watch(() => props.modelValue, value => nextTick(() => {
    const html = value ?? '';

    if (trix.value && trix.value.value !== html) {
        trix.value.editor.loadHTML(html);
    }
}));

</script>
