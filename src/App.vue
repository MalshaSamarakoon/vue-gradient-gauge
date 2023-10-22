<template>
  <CircleGauge :intervals="intervals" :size="300" />
  </template>
 
 <script lang="ts">
 import { defineComponent, ref } from 'vue';
 import CircleGauge from './components/CircleGauge.vue';
 
 export default defineComponent({
   name: 'App',
   components: {
     CircleGauge,
   },
   setup() {
     const intervals = ref([
       { value: 0, color: '#E1CA46' },
       { value: 50, color: '#75C952' },
       { value: 100, color: '#469ED5' },
     ]);
 
     return { intervals };
   },
 });
 </script>
 
 <style>
 #app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
 }
 </style>
 
 