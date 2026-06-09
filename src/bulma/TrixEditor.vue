<script>

import 'trix/dist/trix.css';
import 'trix';
import {
    computed, h, nextTick, ref, watch,
} from 'vue';

export default {
    name: 'EnsoTrixEditor',

    inheritAttrs: false,

    props: {
        modelValue: {
            type: [String, null],
            default: '',
        },
    },

    emits: ['update:modelValue'],

    setup(props, { emit }) {
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

        return () => [
            h('input', {
                id: inputId,
                type: 'hidden',
                value: normalizedValue.value,
            }),
            h('trix-editor', {
                input: inputId,
                ref: trix,
                onTrixChange: update,
            }),
        ];
    },
};

</script>
