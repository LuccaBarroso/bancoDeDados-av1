<script setup lang="ts">
import { ref, watch } from 'vue'
import axios from 'axios'

const search = ref("")
const loading = ref(false)
const message = ref("")
const error = ref("")
const access = ref("")

let changeTimeout: number | null | undefined = null

watch(search, (newVal, oldVal) => {
  if(changeTimeout) clearTimeout(changeTimeout)
  message.value = ""
  error.value = ""
  if(newVal === "") return
  changeTimeout = setTimeout(() => {
    loading.value = true
    axios.get('http://127.0.0.1:5000/search?key=' + newVal
    ).then(response => {
      setTimeout(() => {
        message.value = response.data.result
        loading.value = false
        access.value = response.data.access
      }, 300)
    }).catch(error => {
      console.log(error)
      error.value = error
      loading.value = false
    })
  }, 1000)

})


</script>


<template>
  <div class="per-page">
    <h2>
      Buscar
    </h2>
    <input type="text" v-model="search" placeholder="Digite uma palavra." />
    <div class="loading" v-if="loading">
    </div>
    <p v-if="message">
      {{message}}
    </p>
    <p v-if="error" class="error">
      {{error}}
    </p>
    <p v-if="access">
      quantidade de vezes que o disco foi acessado: {{access}}
    </p>
  </div>
</template>


<style scoped lang="scss">
  .error{
    color: red;
  }
  div.inputs{
    display: flex;
    flex-direction: row;
    justify-content: center;
    margin-top: 10px;
  }
  h2{
    font-family: 'Khand';
    text-transform: uppercase;
    color: #fff;
    font-weight: 800;
    margin-top: 40px;
    font-size: 30px;
  }
  input{
    color: white;
    widows: 100%;
    padding: 10px 20px;
    border: 1px solid #36413E;
    border-radius: 2px;
    background-color: transparent;
    margin-block: 10px;
    font-family: 'Switzer', sans-serif;
    width: 100%;
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
  .loading{
    width: 40px;
    height: 40px;
    border-radius: 50%;
    border: 5px solid #36413E;
    border-top: 5px solid #fff;
    animation: spin 2s ease-in-out infinite;
    margin-inline: auto;
  }
  @keyframes spin {
    0% {
      transform: rotate(0deg) scale(1);
    }
    50% {
      transform: rotate(180deg) scale(1.1);
    }
    100% {
      transform: rotate(360deg) scale(1)
    }
  }

</style>