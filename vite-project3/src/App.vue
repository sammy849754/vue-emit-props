<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="container">
    <header>
      <div class="wrapper">
        <nav>
          <RouterLink to="/">Home</RouterLink> |
          <RouterLink to="/about">About</RouterLink> |
          <RouterLink to="/hw">第三週作業</RouterLink> |
        </nav>
      </div>
    </header>
    
    <RouterView />
  </div>
</template>