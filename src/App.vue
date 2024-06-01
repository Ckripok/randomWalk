<script setup lang="ts">
import { ref } from 'vue'
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import OneD from './components/oneD.vue'
import type { TabsPaneContext } from 'element-plus'

const activeName = ref('first')

const handleClick = (tab: TabsPaneContext, event: Event) => {
  console.log(tab, event)
}
</script>

<template>
  <header>
    <div class="p-5 box-content border border-color-gray-400 w-full">
      <el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick">
        <el-tab-pane label="Одномерное случайное блуждание с графиком" name="first"
          ><OneD
        /></el-tab-pane>
        <el-tab-pane label="Двумерное случайное блуждание на плоскости" name="second"
          >Двумерное случайное блуждание на плоскости</el-tab-pane
        >
        <el-tab-pane label="Трехмерное случайное блуждание с графиком" name="third"
          >sdfdsfdsfsdfd</el-tab-pane
        >
      </el-tabs>
    </div>
  </header>

  <!-- <RouterView /> -->
</template>

<style scoped>
header {
  width: 100% !important;
  /* line-height: 1.5;
  max-height: 100vh; */
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}

nav a.router-link-exact-active {
  color: var(--color-text);
}

nav a.router-link-exact-active:hover {
  background-color: transparent;
}

nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}

nav a:first-of-type {
  border: 0;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
