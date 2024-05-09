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
    <div
      class="node-content"
      :class="{
        leaf: !node.children,
        sigma: node.title.includes('σ'),
        pi: node.title.includes('π'),
        cross: node.title.includes('⋈')
      }"
    >
      <p>
        {{ node.title }}
        <span v-if="node.posicao">({{ node.posicao }})</span>
      </p>
      <div class="children" v-if="node.children">
        <TreeNode v-for="child in node.children" :node="child" :key="child.name" />
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.node-content {
  padding: 10px 6px;
  border: 1px solid #36413e;
  border-radius: 5px;
  margin: 0 5px;
  width: fit-content;

  background: #36413e18;

  > p {
    font-family: 'Khand';
    font-size: 16px;
    color: #fff;
    font-weight: 500;
    margin: 0;
  }
  .children {
    display: flex;
    flex-direction: row;
    margin-top: 10px;
  }
}

.leaf {
  background: #36413e;
}
</style>
