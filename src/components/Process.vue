<template>
  <div>
    <KProgress v-bind:percent="percent" />
    <p>
      {{ this.message }}
    </p>
  </div>
</template>

<script>
import KProgress from "k-progress";

export default {
  name: "Process",

  props: ["uuid"],

  data() {
    return {
      percent: 0,
      message: "Подготовка",
    };
  },

  mounted() {
    const eventSource = new EventSource(
      `${process.env.VUE_APP_BACKEND}/${this.uuid}/progress`
    );

    eventSource.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.percent = Math.round(data.progress * 10) / 10;

      console.log(this.percent);

      if (this.percent == 100) {
        this.message = "Расчёт окончен";
        eventSource.close();
        this.$emit("calculation-ok", true);
      }

      if (data.message != null) {
        this.message = data.message;
      }
    };

    eventSource.onerror = () => {
      this.message = "Произошла ошибка";
      eventSource.close();
    };
  },

  components: {
    KProgress,
  },
};
</script>

<style>
</style>