<script setup lang="ts">
import { ref, watch } from 'vue'
import axios from 'axios'

const amount = ref()
const items = ref([])
const loading = ref(false)

let amountTimeout: number | null | undefined = null

watch(amount, (newVal, oldVal) => {
  if(amountTimeout) clearTimeout(amountTimeout)
  if(newVal === "") return
  amountTimeout = setTimeout(() => {
    loading.value = true
    axios.get('http://127.0.0.1:5000/table-scan?amount=' + newVal
    ).then(response => {
      setTimeout(() => {
        items.value = response.data.result
        loading.value = false
      }, 300)
    }).catch(error => {
      console.log(error)
      loading.value = false
    })
  }, 1000)

})


</script>


<template>
  <div class="per-page">
    <h2>
      Table Scan
    </h2>
    <input type="text" v-model="amount" placeholder="Quantos item vc quer ver ?" />
    <div class="loading" v-if="loading">
    </div>
    <div class="grid-items">
      <div class="item" v-for="item in items" :key="item.id">
        <p>
          {{item}}
        </p>
      </div>
    </div>
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
  .grid-items{
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 20px;
    margin-top: 20px;
    .item{
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      text-align: left;
      background: rgba(54, 65, 62, 0.1);
      backdrop-filter: blur(10px);
      border-radius: 10px;
      text-align: center;
      padding: 20px;
    }
    @media (max-width: 768px){
      grid-template-columns: 1fr;
    }
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

</style>import type { text } from 'stream/consumers';
