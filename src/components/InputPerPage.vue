<script setup lang="ts">
import { ref, defineEmits } from 'vue'
import axios from 'axios'

const emits = defineEmits(["recomecar", "avancar"])

const itemsPerPage = ref(5)
const confirmed = ref(false)

const submit = () => {	
  // make api request
  console.log(itemsPerPage.value)
  confirmed.value = true

  axios.post('http://127.0.0.1:5000/config', {
    records_page: itemsPerPage.value
  }).then(response => {
    emits("avancar");
  }).catch(error => {
    console.log(error)
  })
}
const edit = () => {
  confirmed.value = false
  emits("recomecar");
}
</script>


<template>
  <div class="per-page">
    <p>
      Quantos items por página ?
    </p>
    <input type="number" v-model="itemsPerPage" :disabled="confirmed" />
    <div class="inputs">
      <button @click="submit" v-if="!confirmed" class="confirmar">Confirmar</button>
      <button @click="edit" v-else>Editar</button>
    </div>
  </div>
</template>


<style scoped lang="scss">
  .per-page{
    display: flex;
    flex-direction: column;
    align-items: center;
    p{
      margin-top: 10px;
      font-family: 'Switzer', sans-serif;
    }
  }
  div.inputs{
    display: flex;
    flex-direction: row;
    justify-content: center;
    margin-top: 10px;
  }
  input{
    color: white;
    padding: 10px 20px;
    border: 1px solid #36413E;
    border-radius: 2px;
    background-color: transparent;
    margin-block: 10px;
    font-family: 'Switzer', sans-serif;
  }
  button{
    padding: 10px 20px;
    border-radius: 2px;
    border: none;
    background: #36413E;
    color: white;
    cursor: pointer;
    font-family: 'Switzer', sans-serif;
    text-transform: uppercase;
    &.confirmar{
      background: #2c5b4e;
    }
  }

</style>