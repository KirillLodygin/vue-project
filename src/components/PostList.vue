<template>
  <div class='postList' v-if='posts.length > 0'>
    <h3>Список постов</h3>
    <transition-group name='post-list'>
      <post-item
        v-for='post in posts'
        :post='post'
        :key='post.id'
        @remove="$emit('remove', post)"
      />
    </transition-group>
  </div>

  <h2 v-else style='color: red'>
    Список постов пуст
  </h2>
</template>

<script>
import PostItem from './PostItem';

export default {
  name: 'PostList',
  components: { PostItem },
  props: {
    posts: {
      type: Array,
      required: true,
    },
  },
};
</script>

<style scoped>
  .postList {
    margin-top: 10px;
  }

  .post-list-item {
    display: inline-block;
    margin-right: 10px;
  }
  .post-list-enter-active,
  .post-list-leave-active {
    transition: all .6s ease;
  }
  .post-list-enter-from,
  .post-list-leave-to {
    opacity: 0;
    transform: translateY(30px);
  }
  .post-list-move {
    transition: transform 1s ease;
  }
</style>
