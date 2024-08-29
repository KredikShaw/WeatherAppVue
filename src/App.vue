<template>
  <Weather></Weather>
</template>

<script>
  import 'vue3-toastify/dist/index.css';
  import Weather from './components/Weather.vue';
  
  export default{
    components:{
      Weather,
    },
  }
</script>