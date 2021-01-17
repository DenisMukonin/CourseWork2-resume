<template>
  <label for="type">Тип блока</label>
  <select id="type" v-model="valueBlock" @change="$emit('blockType', valueBlock)">
    <option selected value="title">Заголовок</option>
    <option value="subtitle">Подзаголовок</option>
    <option value="avatar">Аватар</option>
    <option value="text">Текст</option>
  </select>
</template>

<script>
export default {
  data() {
    return {
      valueBlock: this.typeBlock
    }
  },
  emits: {
    'blockType' : null
  },
  props: {
    typeBlock: {
      type: String,
      required: true
    }
  }
}
</script>

<style scoped>

</style>
