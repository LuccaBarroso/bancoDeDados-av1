<script setup lang="ts">
import MainCard from './components/MainCard.vue'
</script>

<template>
  <main>
    <div class="bg"></div>
    <MainCard />
  </main>
</template>

<style lang="scss">
#app {
  display: flex;
  justify-content: center;
  @media (min-width: 992px) {
    max-width: 900px;
    min-width: 900px;
  }
}
p,
label {
  color: white;
}
main {
  width: 100%;
}
.bg {
  position: absolute;
  z-index: 0;
  height: 100vh;
  width: 100vw;
  top: 0;
  left: 0;
  background-image: url('./assets/pattern.png');
  background-size: 300px 300px;
  opacity: 0.1;
  background-repeat: repeat;
  animation: bg 20s linear infinite;
}
@keyframes bg {
  from {
    background-position: 0 0;
  }
  to {
    background-position: 300px 300px;
  }
}
</style>
