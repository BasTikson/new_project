<template>
  <div class="post">
    <div>
      <div>ID: <strong> {{ post.id }}</strong></div>
      <div>Название: <strong> {{ post.title }}</strong></div>
      <div>Описание: <strong> {{ post.body }}</strong></div>
    </div>
    <div class="post__btns">
      <my-button
          class="btn"
          @click="$emit('remove', post)"
      >
        Удалить
      </my-button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    post: {
      type: Object,
      required: true
    }
  }
}
</script>

<style scoped>
.post {
  padding: 15px;
  border: 2px solid #22a758;
  margin: 5px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
</style>
