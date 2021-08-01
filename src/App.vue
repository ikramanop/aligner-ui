<template>
  <div id="app">
    <h1>Aligner-UI</h1>
    <hr />
    <p v-if="status">Количество воркеров: {{ nodes }}</p>
    <Form v-if="status" v-on:validation-ok="validationOk" />
    <Process
      v-else-if="validation"
      v-on:calculation-ok="calculationOk"
      v-bind:uuid="uuid"
    />
    <Result v-else-if="calculation" />
    <StatusUnhealty v-else />
  </div>
</template>

<script>
import Form from "./components/Form.vue";
import StatusUnhealty from "./components/StatusUnhealthy.vue";
import Process from "./components/Process.vue";
import Result from "./components/Result.vue";

export default {
  name: "App",

  data() {
    return {
      nodes: 0,
      status: false,
      validation: false,
      calculation: false,
      uuid: "",
    };
  },

  created() {
    const requestOptions = {
      method: "GET",
      headers: { "Content-Type": "application/json" },
    };

    fetch(`${process.env.VUE_APP_BACKEND}/health/check`, requestOptions)
      .then((response) => response.json())
      .then((json) => {
        console.log(json);
        if (json.nodes.length == 0) {
          this.status = false;
        } else {
          this.nodes = json.nodes.length;
          this.status = true;
        }
      });
  },

  components: {
    Form,
    StatusUnhealty,
    Process,
    Result,
  },

  methods: {
    validationOk(uuid) {
      this.uuid = uuid;
      this.status = false;
      this.validation = true;
    },

    calculationOk() {
      this.validation = false;
      this.calculation = true;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
