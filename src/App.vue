<template>
  <div class="app">
    <h1>Страница с постами</h1>
    <MyInput placeholder="Поиск..." v-model="searchQuery"/>
    <div class="app_btns">
      <MyButton style="margin: 15px 0;" @click="showDialog">
        Создать пост
      </MyButton>
      <MySelect
          v-model:model-value="selectedSort"
          :options="sortOptions"
      />
    </div>
    <MyDialog v-model:show="dialogVisible">
      <PostForm @create="createPost"/>
    </MyDialog>
    <PostList v-if="!loading" @remove="removePost" :posts="sortedAndSearchedPosts"/>
    <div v-else>Идет загрузка...</div>
    <div ref="observer" class="observer"></div>
<!--    <div class="page__wrapper">
      <div
          v-for="pageNumber in totalPages"
          :key="pageNumber"
          class="page"
          :class="{'current_page': page === pageNumber }"
          @click="changePage(pageNumber)"
      >
        {{ pageNumber }}
      </div>
    </div>-->
  </div>
</template>

<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import MyDialog from "@/components/UI/MyDialog";
import MyButton from "@/components/UI/MyButton";
import axios from 'axios';
import MySelect from "@/components/UI/MySelect";
import MyInput from "@/components/UI/MyInput";

export default {
  name: 'App',
  components: {MyInput, MySelect, MyButton, MyDialog, PostForm, PostList},
  data() {
    return {
      posts: [],
      dialogVisible: false,
      loading: false,
      selectedSort: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержимому'},
      ],
      searchQuery: ''
    }
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(item => item.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async loadMorePosts() {
      try {
        this.page += 1;
        this.isPostLoading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit
          }
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = [...this.posts, ...response.data];
      } catch (e) {
        alert('Error')
      } finally {
        this.loading = false;
      }
    },
    async fetchPosts() {
      try {
        this.loading = true;
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit
          }
        });
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = response.data;
      } catch (e) {
        alert('Error')
      } finally {
        this.loading = false;
      }
    },
/*    changePage(pageNumber) {
      this.page = pageNumber;
    }*/
  },
  mounted() {
    this.fetchPosts();
    const options = {
      rootMargin: '0px',
      threshold: 1.0
    }

    const callback = (entries) => {
      if (entries[0].isIntersecting) {
        this.loadMorePosts()
      }
    };

    const observer = new IntersectionObserver(callback, options);
    observer.observe(this.$refs.observer);
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => {
        return post1[this.selectedSort]?.localeCompare(post2[this.selectedSort])
      })
    },
    sortedAndSearchedPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }
  },
  watch: {
/*    page() {
      this.fetchPosts();
    }*/
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.app {
  padding: 20px;
}

.app_btns {
  display: flex;
  margin: 15px 0;
  justify-content: space-between;
}

/*.page__wrapper {
  display: flex;
  margin-top: 15px;
}

.page {
  border: 1px solid black;
  padding: 10px;
  margin: 5px;
}

.current_page {
  border: 2px solid teal;
}*/
</style>
