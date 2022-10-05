<template>
  <div >
    <h1>Стринца с постами</h1>
    <my-input
        placeholder = "Поиск..."
        v-model = "searchQuery"
        v-focus
    />
    <div class="app__btns">
      <my-button
          @click = "showDialog"
      >
        Создать пост
      </my-button>

      <my-select
          v-model = "selectedSort"
          :options = "sortOptions"
      />
    </div>

    <my-dialog
        v-model:show = "dialogVisible"
    >
      <PostForm
          @create = "createPost"
      />
    </my-dialog>
    <PostList
        v-if ="! isPostsLoading"
        :posts = "sortedAndSearchedPosts"
        @remove = "removePost"
    />
    <div v-else>Идёт загрузка...</div>
    <div
        class = "observer"
        v-intersection = "loadMorePosts"
    ></div>
    <!--    <div class="page__wrapper">
          <div
              class="pageNumber"
              v-for="pageNumber in totalPages"
              :key = "pageNumber"
              :class= "{
                'current-page': page === pageNumber
              }"
              @click = "changePage(pageNumber)"
          >
            {{pageNumber}}
          </div>
        </div>-->
  </div>
</template>

<script>
import PostForm from "@/components/PostForm";
import PostList from "@/components/PostList";
import axios from 'axios';
export default {
  components: { PostList, PostForm},
  data(){
    return {
      posts: [
      ],
      dialogVisible: false,
      isPostsLoading: false,
      selectedSort: '',
      searchQuery: '',
      page: 1,
      limit: 10,
      totalPages: 0,
      sortOptions: [
        {value: 'title', name: 'По названию'},
        {value: 'body', name: 'По содержанию'},
      ]
    }
  },
  methods: {
    createPost(post){
      this.posts.push(post)
      this.dialogVisible = false
    },
    removePost(post){
      this.posts = this.posts.filter(p => p.id !== post.id)
    },
    showDialog(){
      this.dialogVisible = true
    },
    // changePage(pageNumber){
    //   this.page = pageNumber
    // },
    async fetchPosts(){
      try{
        this.isPostsLoading = true
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params:{
            _page: this.page,
            _limit: this.limit
          }
        })
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = response.data
      } catch(e){
        alert('Error')
      } finally {
        this.isPostsLoading = false
      }
    },

    async loadMorePosts(){
      try{
        this.page += 1
        const response = await axios.get('https://jsonplaceholder.typicode.com/posts',{
          params:{
            _page: this.page,
            _limit: this.limit
          }
        })
        this.totalPages = Math.ceil(response.headers['x-total-count'] / this.limit)
        this.posts = [...this.posts, ...response.data]
      } catch(e){
        alert('Error')
      }
    },
  },
  mounted() {
    this.fetchPosts()

/*    const options = {
      rootMargin: '0px',
      threshold: 1.0
    }
    const callback = (entries, observer) => {
      if (entries[0].isIntersecting && this.page < this.totalPages) {
        this.loadMorePosts()
      }
    }
    const observer = new IntersectionObserver(callback, options);
    observer.observe(this.$refs.observer);*/
  },
  computed: {
    sortedPosts() {
      return [...this.posts].sort((post1, post2) => post1[this.selectedSort]?.localeCompare(post2[this.selectedSort]))
    },
    sortedAndSearchedPosts(){
      return this.sortedPosts.filter(post => post.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
    }
  },
  watch: {
    /*    page(){
          this.fetchPosts()
        }*/
  }
}
</script>

<style>

.app__btns{
  margin: 20px 0;
  display: flex;
  justify-content: space-between;
}
/*.page__wrapper{
  display: flex;
  margin-top: 15px;
}
.pageNumber{
  border: 1px solid #000;
  padding: 10px;
  cursor: pointer;
}
.current-page{
  border: 2px solid teal;
}*/
.observer{
  height: 20px;
  background-color: red;
}
</style>