<script setup lang="ts">
import { ref } from 'vue'
import axios from 'axios'
import TreeNode from './TreeNode.vue'
import QueryHistory from './QueryHistory.vue'
import { querySuggestions } from '@/mocks'

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

  if (!query.value) {
    loading.value = false

    result.value = { valid: null, tree: null, expression: '' }
    return
  }

  try {
    const response = await axios.post('http://127.0.0.1:5000/validate', { query: query.value })

    let tree = response.data.tree
    let currentNumber = { count: 1 } // Inicializa a contagem começando de 1

    numberTreeNodes(tree, currentNumber) // Função para numerar os nós

    result.value = { valid: response.data.valid, tree: tree, expression: response.data.expression }
    if (response.data.valid) {
      if (querySuggestions.includes(query.value)) {
        return
      }

      localStorage.setItem(
        'queries',
        JSON.stringify([
          ...new Set([query.value, ...JSON.parse(localStorage.getItem('queries') || '[]')])
        ])
      )

      const event = new StorageEvent('storage', {
        key: 'queries', // The key in local storage that changed
        url: window.location.href, // The URL where the change occurred
        storageArea: localStorage // Reference to the storage area
      })

      // Dispatch the event to trigger the storage event listener
      window.dispatchEvent(event)
    }
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

const handleQueryUpdate = (newQuery: string) => {
  query.value = newQuery // Update the current query
  applyChanges() // Apply changes to the query
}
</script>

<template>
  <div class="per-page">
    <p>Digite a sua query:</p>
    <div class="input-wrapper">
      <input type="text" v-model="query" placeholder="Digite a sua query" @input="changedInput" />
      <QueryHistory @update:query="handleQueryUpdate" />
    </div>
    <div class="display-result empty" v-if="!loading && result.valid === null">
      <p>Insira uma Query</p>
    </div>
    <div class="display-result valid" v-if="!loading && result.valid === true">
      <p>Query Válida!</p>
    </div>
    <div class="display-result invalid" v-if="!loading && result.valid === false">
      <p>Query Invalida!</p>
    </div>
    <p class="result-expression" v-if="!loading && result.expression">
      {{ result.expression }}
    </p>
    <div class="display-result" v-if="!loading && result.tree !== null && result.valid === true">
      <div class="tree">
        <TreeNode :node="result.tree" />
      </div>
    </div>
    <div class="loading-overlay" v-if="loading">
      <div class="loading"></div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.input-wrapper {
  display: flex;
  align-items: center;
  gap: 16px;
  width: 100%;

  input {
    width: 100%;
  }
}

.display-result {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 5px;
  width: 100%;

  &.valid {
    background: #2c5b4e;
  }
  &.invalid {
    background: #5b2c2c;
  }
  &.empty {
    background: #36413e;
  }
  p {
    font-family: 'Switzer', sans-serif;
    margin-top: 0 !important;
    font-weight: 800;
    font-size: 20px;
  }
}

.result-expression {
  margin: 0 5px;
  padding: 10px 6px;

  border: 1px solid #36413e;
  border-radius: 5px;
}

.tree {
  max-width: 100%;
  overflow: auto;

  &::-webkit-scrollbar {
    width: 10px;
  }
  &::-webkit-scrollbar-thumb {
    background: #36413e;
    border-radius: 10px;
  }
  &::-webkit-scrollbar-track {
    background: transparent;
  }
}
.per-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
  p {
    margin-top: 10px;
    font-family: 'Switzer', sans-serif;
  }
}
div.inputs {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
input {
  width: 100%;
  color: white;
  padding: 10px 20px;
  border: 1px solid #36413e;
  border-radius: 2px;
  background-color: transparent;
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
