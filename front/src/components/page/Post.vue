<template xmlns:color="http://www.w3.org/1999/xhtml">
  <div>
    <article>
      <a href="#" style="text-decoration: none" @click.prevent="onPost">
        <div class="title">{{ post.title }}</div>
      </a>
      <div class="information">By {{ users[post.userId].login }}</div>
      <div class="body">{{ post.text }}</div>
      <ul class="attachment">
        <li>Announcement of <a href="#">Codeforces Round #510 (Div. 1)</a></li>
        <li>Announcement of <a href="#">Codeforces Round #510 (Div. 2)</a></li>
      </ul>
      <div class="footer">
        <div class="left">
          <img src="../../assets/img/voteup.png" title="Vote Up" alt="Vote Up"/>
          <span class="positive-score">+173</span>
          <img src="../../assets/img/votedown.png" title="Vote Down" alt="Vote Down"/>
        </div>
        <div class="right">
          <img src="../../assets/img/date_16x16.png" title="Publish Time" alt="Publish Time"/>
          A while ago
          <img src="../../assets/img/comments_16x16.png" title="Comments" alt="Comments"/>
          <a href="#">{{ commentsNumber === undefined ? 0 : commentsNumber }}</a>
        </div>
      </div>
      <div class="comment" v-for="comment in comments" :key="comment.id">
        <div>User: {{ users[comment.userId].login }}</div>
        <div>Text: {{ comment.text }}</div>
      </div>
      <div class="new-comment" v-if="userId">
        <form @submit.prevent="onAddComment">
          <div class="field">
            <div class="name">
              <label for="text">Write your comment here</label>
            </div>
            <div class="value">
              <textarea autofocus id="text" name="text" v-model="commentText"/>
            </div>
          </div>
          <div class="field error">{{ error }}</div>
          <div class="button-field">
            <input type="submit" value="Add">
          </div>
        </form>
      </div>
    </article>
  </div>
</template>

<script>
export default {
  name: "Post",
  props: ["post", "users", "comments", "commentsNumber", "userId"],
  data: function () {
    return {
      commentText: "",
      error: ""
    }
  },
  methods: {
    onAddComment: function () {
      this.$root.$emit("onAddComment", this.commentText, this.userId, this.post.id);
    }
  },
  beforeCreate() {
    this.$root.$on("onCommentValidationError", (error) => this.error = error);
  }
}
</script>

<style scoped>
.comment {
  margin-bottom: 0.5rem;
}
</style>