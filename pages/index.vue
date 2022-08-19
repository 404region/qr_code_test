<template>
  <MyQrCode />
</template>

<script lang="ts">
import MyQrCode from '../components/MyQrCode.vue';
import { Component, Vue } from "nuxt-property-decorator";

@Component({
    components: { MyQrCode  },
})
export default class IndexPage extends Vue {

}
</script>
