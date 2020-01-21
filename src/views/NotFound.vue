<template>
  <div>
    <h1>Ups, the {{ resource }} you are looking for is not here. 404</h1>
    <router-link :to="{ name: 'event-list' }">Back to the home page</router-link>
  </div>
  
</template>

<script>
  export default {
    props: {
      resource: {
        type: String,
        required: true
      }
    }	
  }
</script>

<style scoped>
  
</style>
