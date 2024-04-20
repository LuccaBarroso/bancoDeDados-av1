<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import TreeNode from './TreeNode.vue'

const emits = defineEmits(['recomecar', 'avancar'])

const query = ref<string>('')
const result = ref<{ valid: boolean | null; tree: any | null; expression: string }>({
  valid: null,
  tree: null,
  expression: ''
})
const loading = ref<boolean>(false)

let timeout: ReturnType<typeof setTimeout> | undefined

const numberTreeNodes = (node: any, currentNumber: { count: number }) => {
  if (!node) return
  // Primeiro percorra todos os filhos do nó atual da esquerda para a direita
  if (node.children) {
    for (let i = 0; i < node.children.length; i++) {
      numberTreeNodes(node.children[i], currentNumber)
    }
  }
  // Depois de percorrer os filhos, atribua o número ao nó atual
  node.posicao = currentNumber.count
  currentNumber.count++
}

const applyChanges = async () => {
  loading.value = true
  try {
    const response = await axios.post('http://127.0.0.1:5000/validate', { query: query.value })

    let tree = response.data.tree
    let currentNumber = { count: 1 } // Inicializa a contagem começando de 1

    numberTreeNodes(tree, currentNumber) // Função para numerar os nós

    result.value = { valid: response.data.valid, tree: tree, expression: response.data.expression }
  } catch (error) {
    console.error(error)
  } finally {
    loading.value = false
  }
}

const changedInput = () => {
  if (timeout) {
    clearTimeout(timeout)
  }
  loading.value = true
  timeout = setTimeout(applyChanges, 400)
}
</script>

<template>
  <div class="per-page">
    <p>Digite a sua query:</p>
    <input type="text" v-model="query" placeholder="Digite a sua query" @input="changedInput" />
    <div class="display-result valid" v-if="!loading && result.valid === true">
      <p>Query Válida!</p>
    </div>
    <div class="display-result invalid" v-if="!loading && result.valid === false">
      <p>Query Invalida!</p>
    </div>
    <p>
      {{ result.expression }}
    </p>
    <div class="display-result" v-if="!loading && result.tree !== null && result.valid === true">
      <div class="tree" style="max-width: 100%; overflow: auto">
        <TreeNode :node="result.tree" />
      </div>
    </div>
    <div class="loading-overlay" v-if="loading">
      <div class="loading"></div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.display-result {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 5px;
  margin-top: 10px;
  border: 1px solid #36413e;
  padding: 10px 50px;
  &.valid {
    background: #2c5b4e;
  }
  &.invalid {
    background: #5b2c2c;
  }
  p {
    font-family: 'Switzer', sans-serif;
    margin-top: 0 !important;
    font-weight: 800;
    font-size: 20px;
  }
}
.per-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  p {
    margin-top: 10px;
    font-family: 'Switzer', sans-serif;
  }
}
div.inputs {
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin-top: 10px;
}
input {
  width: 100%;
  color: white;
  padding: 10px 20px;
  border: 1px solid #36413e;
  border-radius: 2px;
  background-color: transparent;
  margin-block: 10px;
  font-family: 'Switzer', sans-serif;
}
button {
  padding: 10px 20px;
  border-radius: 2px;
  border: none;
  background: #36413e;
  color: white;
  cursor: pointer;
  font-family: 'Switzer', sans-serif;
  text-transform: uppercase;
  &.confirmar {
    background: #2c5b4e;
  }
}
.loading-overlay {
  .loading {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    border: 5px solid #36413e;
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
    transform: rotate(360deg) scale(1);
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
