<template>
  <div class="wrapper">
    <div v-if="accessToken">
      Access Token: {{ accessToken }}
    </div>
    <div v-if="expiresAt">
      Expires at: {{ expirationTime }}
    </div>
  </div>
</template>
<script>
export default {
  name: 'Token',
  props: {
    accessToken: {},
    expiresAt: {},
  },
  computed: {
    expirationTime() {
      return new Date(parseInt(this.expiresAt, 10)).toString();
    },
  },
};
</script>
<style scoped>

.wrapper {
  padding: 20px;
}

</style>
