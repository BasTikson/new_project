<template>

  <h1> Страница с постами! </h1>
  <my-input
      class="input_search_posts"
      v-model="searchQuery"
      placeholder="Поиск постов..."
  >
  </my-input>

  <div class="app__btns">
    <my-button
        @click="fetchPosts"
    >
      Получить посты!
    </my-button>
    <my-button
        @click="showDialog"
    >
      Создать пост!
    </my-button>
    <my-select
        v-model="selectedSort"
        :options="sortOptions"
    />

  </div>


  <my-dialog
      v-model:show="dialogVisible"
  >
    <post-form
        @create="createPost"
    />
  </my-dialog>

  <post-list
      :posts="sortedAndSearchPosts"
      @remove="removePost"
      v-if="!isPostLoading"
  />
  <div v-else> Идет загрузка...</div>

  <div ref="observer" class="observer"></div>

  <!--    <div class="page__wrapper">-->
  <!--      <div-->
  <!--          v-for="pageNumber in totalPage"-->
  <!--          :key="pageNumber"-->
  <!--          class="page"-->
  <!--          :class="{-->
  <!--            'current-page': page === pageNumber-->
  <!--          }"-->
  <!--          @click="changePage(pageNumber)"-->
  <!--      >-->
  <!--        {{ pageNumber }}-->
  <!--      </div>-->
  <!--    </div>-->

</template>

<script>
import PostList from "@/components/PostList.vue";
import PostForm from "@/components/PostForm.vue";
import axios from "axios";


export default {
  components: {
    PostList, PostForm
  },
  data() {
    return {
      posts: [],
      selectedSort: "",
      searchQuery: "",
      sortOptions: [
        {value: "title", label: "По названию"},
        {value: "body", label: "По содержанию"}
      ],
      dialogVisible: false,
      isPostLoading: false,
      page: 1,
      limit: 10,
      totalPage: 0,
    }
  },
  methods: {
    createPost(post) {
      this.posts.push(post);
      this.dialogVisible = false;
    },
    removePost(post) {
      this.posts = this.posts.filter(p => p.id !== post.id);
    },
    showDialog() {
      this.dialogVisible = true;
    },
    async fetchPosts() {
      try {
        this.isPostLoading = true;
        const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit,
          }
        })
        this.totalPage = Math.ceil(res.headers["x-total-count"] / this.limit);
        this.posts = res.data
      } catch (error) {
        alert("ОщИбка");
      } finally {
        this.isPostLoading = false;
      }
    },
    async loadMorePosts() {
      try {
        this.page += 1;
        const res = await axios.get('https://jsonplaceholder.typicode.com/posts', {
          params: {
            _page: this.page,
            _limit: this.limit,
          }
        })
        this.totalPage = Math.ceil(res.headers["x-total-count"] / this.limit);
        this.posts = [...this.posts, ...res.data];
      } catch (error) {
        alert("ОщИбка");
      }
    }


  },
  mounted() {
    this.fetchPosts()
    const options = {
      rootMargin: "0px",
      threshold: 1.0,
    };
    const callback = (entries, observer) => {
      if (entries[0].isIntersecting && this.page < this.totalPage) {
        this.loadMorePosts();
      }
    };
    const observer = new IntersectionObserver(callback, options);
    observer.observe(this.$refs.observer)

  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchPosts() {
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }

  },
  // watch: {
  //   page(){
  //     this.fetchPosts();
  //   }
  //
  // }
}
</script>

<style>
.app__btns {
  display: flex;
  justify-content: space-between;
}

.input_search_posts {
  margin-bottom: 20px;
}

.page__wrapper {
  display: flex;
  margin-top: 15px;
}

.page {
  border: 1px solid black;
  padding: 10px;
}

.current-page {
  border: 2px solid green;
}

.observer {
  height: 30px;
  background: aqua;
}

</style>