<script setup>

</script>

<template>
  <countyInput v-model:address="address"></countyInput>
  {{ address }}
</template>

<script>
import countyInput from "@/components/county.vue";

export default {
  data() {
    return {
      address: ''
    };
  },
  components: {
    countyInput
  }
}
</script>

<style scoped>
</style>
