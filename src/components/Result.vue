<template>
  <div>
    <img :src="gifFile" />
    <h2>У Вас всё посчиталось!! :))</h2>
  </div>
</template>

<script>
export default {
  name: "Result",

  data() {
    return {
      gifFile: require("../assets/calculation-ready-icon.gif"),
    };
  },
};
</script>

<style>
img {
  width: auto;
  height: auto;
}
</style>