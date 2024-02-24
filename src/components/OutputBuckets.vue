<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'

const props = defineProps({
  colisions: {
    type: Number,
    required: true
  },
  overflows: {
    type: Number,
    required: true
  },
  message: {
    type: String,
    required: true
  }
})

</script>


<template>
  <div class="result">
    <h2>
      Resultado
    </h2>
    <div class="topo">
      <div class="item">
        <label for="colisions">
          Colisões:
        </label>
        <input type="text" :value="colisions" disabled>
      </div>
      <div class="item">
        <label for="Overflows">
          Overflows:
        </label>
        <input type="text" :value="overflows" disabled>
      </div>
    </div>
    <div class="message">
      <p>
        {{message}}
      </p>
    </div>

   
  </div>
</template>


<style scoped lang="scss">
  h2{
    font-family: 'Khand';
    text-transform: uppercase;
    color: #fff;
    font-weight: 800;
    margin-top: 40px;
    font-size: 30px;
  }
  .topo{
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    .item{
      display: flex;
      flex-direction: column;
      justify-content: flex-start;
      text-align: left;
    }
    @media (max-width: 768px){
      grid-template-columns: 1fr;
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