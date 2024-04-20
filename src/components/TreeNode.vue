<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps({
  node: {
    type: Object,
    required: true
  }
})
</script>

<template>
  <div class="node">
    <div class="node-content">
      <p>
        {{ node.title }} <span v-if="node.posicao">({{ node.posicao }})</span>
      </p>
      <div class="children" v-if="node.children">
        <TreeNode v-for="child in node.children" :node="child" :key="child.name" />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.node-content {
  > p {
    font-family: 'Khand';
    font-size: 20px;
    color: #fff;
    font-weight: 800;
    margin: 0;
  }
  padding: 10px;
  border: 1px solid #36413e;
  border-radius: 5px;
  margin: 5px;
  .children {
    display: flex;
    flex-direction: row;
    margin-top: 10px;
  }
}
</style>
