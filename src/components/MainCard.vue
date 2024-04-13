<script setup lang="ts">
import InputPerPage from './InputPerPage.vue'
import InputQuery from './InputQuery.vue'
import OutputBuckets from './OutputBuckets.vue'
import TableScan from './TableScan.vue'
import Search from './Search.vue'
import axios from 'axios'

import { ref } from 'vue'

const isPerPageSet = ref(false)
const loading = ref(false)
const infoBuckets = ref({
  colisions: 0,
  overflows: 0,
  message: ''
})

const curAV = ref(1)

const recomecar = () => {
  console.log('recomecar')
  isPerPageSet.value = false
}

const avancar = () => {
  console.log('avancar')
  if(isPerPageSet.value === false){
    loading.value = true
    axios.post('http://127.0.0.1:5000/fill-buckets'
    ).then(response => {
      infoBuckets.value = response.data
      isPerPageSet.value = true
      console.log(infoBuckets.value)
      loading.value = false
    }).catch(error => {
      console.log(error)
      loading.value = false
    })
  }

}

const changePage = (page: number) => {
  curAV.value = page
}


</script>

<template>
  <div class="card">
    <h1>
      Sistema Banco de Dados
    </h1>
    <div class="options">
      <button @click="changePage(1)" :class="[curAV === 1 ? 'current' : '', 'btn']">
        AV1
      </button>
      <button @click="changePage(2)" :class="[curAV === 2 ? 'current' : '', 'btn']">
        AV2
      </button>
      <button @click="changePage(3)" :class="[curAV === 3 ? 'current' : '', 'btn inactive']">
        AV3
      </button>
    </div>
    <div class="items" v-if="curAV === 1">
      <InputPerPage  @recomecar="recomecar" @avancar="avancar"/>
      <OutputBuckets v-if="isPerPageSet" :colisions="infoBuckets.colisions" :overflows="infoBuckets.overflows" :message="infoBuckets.message"/>
      <Search v-if="isPerPageSet"/>
      <TableScan v-if="isPerPageSet"/>
    </div>
    <div class="items" v-if="curAV === 2">
      <InputQuery />
    </div>
    <div class="loading-overlay" v-if="loading">
      <div class="loading" >
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
  button.btn{
    background: #36413E;
    color: #fff;
    border: none;
    padding: 10px 20px;
    border-radius: 5px;
    margin: 0 10px;
    cursor: pointer;
    transition: 0.3s;
    position: relative;
    margin-bottom: 10px;
    &:hover{
      background: #2B2B2B;
    }
    &.inactive{
      background: #2B2B2B;
      cursor: not-allowed;
    }
    &::after{
      content: '';
      width: 100%;
      height: 5px;
      background: #435858;
      position: absolute;
      bottom: 0;
      border-radius: 0 0 4px 4px;
      left: 0;
    }
    &::before{
      content: '';
      width: 0;
      height: 0;
      border-left: 5px solid transparent;
      border-right: 5px solid transparent;
      border-top: 5px solid #435858;
      position: absolute;
      bottom: -5px;
      left: 50%;
      transform: translateX(-50%); 
    }
    &::before, &::after{
      opacity: 0;
      transition: all 0.3s ease-in-out;
    }
    &.current{
      &::before, &::after{
        opacity: 1;
      }
    }
  }
  .card{
    background: rgba(54, 65, 62, 0.1);
    backdrop-filter: blur(10px);
    border-radius: 10px;
    width: 100%;
    display: flex;
    flex-direction: column;
    max-height: calc(100vh - 4rem);
    overflow-y: auto;
    padding: 80px 100px;
    &::-webkit-scrollbar {
      width: 10px;
    }
    &::-webkit-scrollbar-thumb {
      background: #36413E;
      border-radius: 10px;
    }
    &::-webkit-scrollbar-track {
      background: transparent;
    }
    @media(max-width: 768px){
      padding: 40px 50px;
    }
    position: relative;
    *{
      text-align: center;
      font-family: 'Switzer', sans-serif;
    }
    h1{
      font-family: 'Khand', sans-serif;
      text-transform: uppercase;
      color: #fff;
    }
  }
  .loading-overlay{
    position: absolute;
    z-index: 1;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.7);
    animation: background 0.5s ease-in-out alternate;
    display: flex;
    justify-content: center;
    align-items: center;
    top: 0;
    left: 0;
    .loading{
      width: 100px;
      height: 100px;
      border-radius: 50%;
      border: 5px solid #36413E;
      border-top: 5px solid #fff;
      animation: spin 2s ease-in-out infinite;
    }
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

  @keyframes background {
    0% {
      background: rgba(0, 0, 0, 0.5);
    }
    50% {
      background: rgba(0, 0, 0, 0.7);
    }
    100% {
      background: rgba(0, 0, 0, 0.5);
    }
  }

</style>
