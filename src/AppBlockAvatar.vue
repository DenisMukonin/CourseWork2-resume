<template>
  <div class="avatar">
    <img :src="text">
  </div>
</template>

<script>
export default {
  props: {
    text: {
      type: String,
      required: true,
      validator(value){
        return value.includes('http',0)
      }
    }
  }
}
</script>

