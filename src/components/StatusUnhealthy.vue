<template>
  <div>
    <img :src="svgFile" />
    <h2>Сервис недоступен</h2>
  </div>
</template>

<script>
export default {
  name: "StatusUnhealthy",

  data() {
    return {
      svgFile: require("../assets/status-unhealthy-icon.svg"),
    };
  },
};
</script>

<style scoped>
img {
  width: 30%;
  height: auto;
}
</style>