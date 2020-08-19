<template>
    <vue-trix v-model="$attrs.value"
        v-bind="$attrs"
        v-on="$listeners"
        class="trix"/>
</template>

<script>
import VueTrix from 'vue-trix';

export default {
    name: 'Wysiwyg',

    components: { VueTrix },
};
</script>
<style lang="scss">
trix-toolbar .trix-button-group--file-tools {
    display: none;
}
.trix {
    background-color: white;
}
</style>
