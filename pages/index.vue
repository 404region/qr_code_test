<template>
  <QrCode />
</template>

<script lang="ts">
import Vue from 'vue'
import QrCode from '../components/QrCode.vue';

export default Vue.extend({
    name: "IndexPage",
    components: { QrCode }
})
</script>
