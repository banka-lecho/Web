<template>
  <div class="middle">
    <Sidebar :posts="viewPosts"/>
    <main>
      <Index v-if="page === 'Index'"/>
      <Enter v-if="page === 'Enter'"/>
      <Register v-if="page === 'Register'"/>
      <WritePost v-if="page === 'WritePost'"/>
      <EditPost v-if="page === 'EditPost'"/>
      <Users v-if="page === 'Users'" :users="users"/>
      <Post v-if="page === 'Post'" :post="post" :users="users" :comments="getComments(post)"
            :commentsNumber="getComments(post).length" :userId="userId"/>
    </main>
  </div>
</template>

<script>
import Sidebar from "./sidebar/Sidebar";
import Index from "./page/Index";
import Enter from "./page/Enter";
import Register from "./page/Register";
import WritePost from "./page/WritePost";
import EditPost from "./page/EditPost";
import Users from "./page/Users";
import Post from "./page/Post";

export default {
  name: "Middle",
  data: function () {
    return {
      page: "Index",
      post: undefined
    }
  },
  components: {
    WritePost,
    Enter,
    Register,
    Index,
    Sidebar,
    EditPost,
    Users,
    Post
  },
  props: ["posts", "users", "comments", "userId"],
  methods: {
    getComments: function (post) {
      return Object.values(this.comments).filter(comment => comment.postId === post.id)
    },
  },
  computed: {
    viewPosts: function () {
      return Object.values(this.posts).sort((a, b) => b.id - a.id).slice(0, 2);
    }
  }, beforeCreate() {
    this.$root.$on("onChangePage", (page) => this.page = page)
    this.$root.$on("onPost", (post) => {
      this.post = post;
      this.$root.$emit("onChangePage", "Post")
    })
  }
}
</script>

<style scoped>

</style>
