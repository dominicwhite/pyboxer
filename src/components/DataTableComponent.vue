<template>
  <div><h1>Hello {{ name }}!</h1></div>
</template>

<script>
export default {
  props: {
    name: {
      type: String,
      required: true
    }
  }
}
</script>

<style scoped>
h1 {
  color: red;
  display: block;
}
</style>
