<script setup>
import {ref} from "vue";

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <header class="header header_margin">
    <div class="container header__container">
      <div class="header__left">
        Medods
      </div>
      <div class="header__right">
        <div class="link">
          <a href="https://t.me/pailen" target="_blank">
            <img src="../../public/telegram.svg" alt="pailen" class="link__img">
          </a>
        </div>
        <div class="link">
          <a href="https://wa.me/79185922742">
            <img src="../../public/whats.svg" alt="whats" class="link__img">
          </a>
        </div>
      </div>
    </div>
  </header>
</template>


<style scoped>

</style>