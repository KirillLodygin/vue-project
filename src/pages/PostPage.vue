<template>
  <div>
    <h1>Страница с постами</h1>

    <my-input
      v-model='searchQuery'
      placeholder='Поиск...'
    />

    <div class='app__buttons'>
      <my-button
        @click='showDialog'
      >
        Создать пост
      </my-button>

      <my-select
        v-model='selectedSort'
        :options='sortOptions'
      ></my-select>
    </div>

    <my-dialog v-model:show='dialogVisible'>
      <post-form
        @create='createPost'
      />
    </my-dialog>

    <post-list
      @remove='removePost'
      :posts='sortedAndSearchedPosts'
      v-if='!isPostLoading'
    />

    <pulse-loader
      class='pulse-loader'
      :loading='pulseLoader.loading'
      :color='pulseLoader.color'
      :size='pulseLoader.size'
      v-else
    ></pulse-loader>

    <div ref='observer' class='observer'></div>

    <!--    <div class='page__wrapper'>
          <div
            v-for='pageNumber in totalPages'
            :key='pageNumber'
            class='page'
            :class="{
              'current-page': page === pageNumber
            }"
            @click='changePage(pageNumber)'
          >
            {{ pageNumber }}
          </div>
        </div>-->
  </div>
</template>

<script>
import PostForm from './components/PostForm';
import PostList from './components/PostList';

import axios from 'axios';

export default {
  components: {
    PostForm,
    PostList,
  },

  data() {
    return {
      posts: [],
      dialogVisible: false,
      isPostLoading: false,
      pulseLoader: {
        loading: true,
        color: '#2c3e50',
        size: '10px',
      },
      selectedSort: '',
      searchQuery: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        { value: 'title', name: 'По названию' },
        { value: 'body', name: 'По содержимому' },
      ],
    };
  },

  methods: {
    createPost(post) {
      this.posts.unshift(post);
      this.dialogVisible = false;
    },

    removePost(post) {
      this.posts = this.posts.filter(p => p.id !== post.id);
    },

    showDialog() {
      this.dialogVisible = true;
    },

    /* changePage(pageNumber) {
       this.page = pageNumber
     },*/

    async fetchPosts() {
      try {
        this.isPostLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit,
          },
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit);
        this.posts = response.data;
      } catch (e) {
        console.log('Ошибка!');
      } finally {
        this.isPostLoading = false;
      }
    },

    async loadMorePosts() {
      try {
        this.page += 1
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit,
          },
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit);
        this.posts = [...this.posts, ...response.data];
      } catch (e) {
        console.log('Ошибка!');
      }
    },
  },

  mounted() {
    this.fetchPosts();

    const options = {
      rootMargin: '0px',
      threshold: 1.0
    }
    const callback = (entries, observer) => {
      if (entries[0].isIntersecting && this.page < this.totalPages) {
        this.loadMorePosts()
      }
    };
    const observer = new IntersectionObserver(callback, options);
    observer.observe(this.$refs.observer)
  },

  computed: {
    sortedPost() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]));
    },
    sortedAndSearchedPosts() {
      return this.sortedPost.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()));
    },
  },

  watch: {
    /* page(){
       this.fetchPosts()
     }*/
  },
};
</script>

<style>
h1 {
  margin-bottom: 20px;
}

.app__buttons {
  display: flex;
  justify-content: space-between;
  margin-top: 15px;
}

.pulse-loader {
  margin-top: 25px;
}

.observer {
  height: 30px;
}

/*.page__wrapper {
  display: flex;
  margin-top: 15px;
}

.page {
  border: 1px solid black;
  padding: 10px;
}

.current-page {
  border: 2px solid teal;
}*/

</style>
