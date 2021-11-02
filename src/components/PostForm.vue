<template>
  <form @submit.prevent>
    <h4>Название</h4>

    <div class="form__div">
      <my-input
        v-model='post.title'
        :type="type"
        placeholder="Название"/>
    </div>

    <div class="form__div">
      <my-input
        v-model='post.body'
        :type="type"
        placeholder="Содержание"/>
    </div>

    <my-button
      @click='createPost'
      style='align-self: flex-end'>
      Создать
    </my-button>
  </form>
</template>

<script>
  export default {
    name: 'PostForm',
    data() {
      return {
        post: {
          title: '',
          body: ''
        },
        type: 'text'
      }
    },

    methods: {
      createPost() {
        this.post.id = Date.now()
        this.$emit('create', this.post)
        this.post = {
          title: '',
          body: ''
        }
      }
    }
  };
</script>

<style scoped>
h4 {
  margin-bottom: 10px;
}

form {
  display: flex;
  flex-direction: column;
}

.form__div {
  margin-bottom: 10px;
}

</style>
