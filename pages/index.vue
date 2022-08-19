<template>
  <MyQrCode />
</template>

<script lang="ts">
import Vue from 'vue'
import MyQrCode from '../components/MyQrCode.vue';

export default Vue.extend({
    name: "IndexPage",
    components: { MyQrCode }
})
</script>
