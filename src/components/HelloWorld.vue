<template>
  <div id="hello">
    <label for="input-name">Name:</label>
    <b-form-input id="input-name" v-model="name" placeholder="Dein Name"></b-form-input>
    <b-button pill variant="outline-secondary" v-b-modal.modal-name>Hello</b-button>

    <b-modal id="modal-name" title="Welcome!">
      <p class="my-3">Hallo {{name}}!</p>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      name: ""
    };
  }
};
</script>

<style>
</style>
