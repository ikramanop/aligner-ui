<template>
  <div class="form">
    <div>
      <span>Использовать файл</span>
      <input type="checkbox" v-model="useFile" />
    </div>
    <div class="form-group" v-if="!useFile">
      <span>Последовательности</span>
      <br />
      <textarea type="text" class="form-field" v-model="sequences" />
    </div>
    <div class="form-group" v-else>
      <span>Последовательности из файла</span>
      <br />
      <input
        type="file"
        class="form-field"
        accept=".fasta"
        @change="loadFile"
      />
    </div>
    <div class="form-group">
      <span>Kd</span>
      <input type="number" class="form-field" v-model="kdValue" />
    </div>
    <div class="form-group">
      <span>R2</span>
      <input type="number" class="form-field" v-model="rSquaredValue" />
    </div>
    <div class="form-group">
      <span>Цена за удаление</span>
      <br />
      <input type="number" class="form-field" v-model="delValue" />
    </div>
    <div class="form-group">
      <span>Размерность</span>
      <br />
      <input type="number" class="form-field" v-model="dimValue" />
    </div>
    <div class="form-group">
      <span>Количество матриц</span>
      <br />
      <input type="number" class="form-field" v-model="matricesVolumeValue" />
    </div>
    <div>
      <button type="submit" class="submit-button" @click="onClick">
        Отправить запрос на вычисление
      </button>
    </div>
    <div v-if="error">
      <p>Произошла ошибка при валидации запроса на вычисление</p>
      <p>Причина: {{ this.errorReason }}</p>
    </div>
  </div>
</template>

<script>
import { v4 as uuidv4 } from "uuid";

export default {
  name: "Form",

  data() {
    return {
      sequences: "",
      kdValue: 0.3,
      rSquaredValue: 0,
      delValue: 8,
      dimValue: 20,
      matricesVolumeValue: 300,
      useFile: false,
      error: false,
      errorReason: null,
      uuid: uuidv4(),
    };
  },

  mounted() {
    this.update();
  },

  methods: {
    onClick() {
      this.update();

      const requestOptions = {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(this.data),
      };

      console.log(requestOptions);

      fetch(`${process.env.VUE_APP_BACKEND}/validate`, requestOptions)
        .then((response) => {
          console.log(response);
          if (response.status !== 200) {
            this.error = true;
          } else {
            this.error = false;
            this.$emit("validation-ok", this.uuid);
          }
          return response.json();
        })
        .then((json) => {
          if (json.message != null) {
            this.errorReason = json.message;
          }
        });
    },

    update() {
      this.data = {
        sequences: this.sequences,
        kdValue: this.kdValue,
        rSquaredValue: this.rSquaredValue,
        dimValue: this.dimValue,
        delValue: this.delValue,
        matricesVolumeValue: this.matricesVolumeValue,
        uuid: this.uuid,
      };
    },

    loadFile(event) {
      const file = event.target.files[0];
      const reader = new FileReader();
      reader.onload = (e) => {
        this.sequences = e.target.result;
      };

      reader.readAsText(file);
    },
  },
};
</script>

<style scoped>
.submit-button {
  background-color: #e7e7e7;
  color: black;
  border: none;
  padding: 15px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}

textarea {
  width: 100%;
  height: 150px;
  padding: 12px 20px;
  box-sizing: border-box;
  border: 2px solid #ccc;
  border-radius: 4px;
  background-color: #f8f8f8;
  resize: none;
}
</style>