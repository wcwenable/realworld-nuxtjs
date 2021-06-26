<template>
  <div>
    <form class="card comment-form">
      <div class="card-block">
        <textarea v-model="commentToAdd" class="form-control" placeholder="Write a comment..." rows="3"></textarea>
      </div>
      <div class="card-footer">
        <nuxt-link  :to="{
          name: 'profile',
          params: {
            username: user.username
          }
        }">          
          <img v-if="user.image" :src="user.image" class="comment-author-img" />
          <span v-else>{{ user.username }}</span>
        </nuxt-link>
        <button class="btn btn-sm btn-primary" @click="handleAddComment">
        Post Comment
        </button>
      </div>
    </form>

    <div
      class="card"
      v-for="comment in comments"
      :key="comment.id"
    >
      <div class="card-block">
        <p class="card-text">{{ comment.body }}</p>
      </div>
      <div class="card-footer">
        <nuxt-link class="comment-author" :to="{
          name: 'profile',
          params: {
            username: comment.author.username
          }
        }">
          <img :src="comment.author.image" class="comment-author-img" />
        </nuxt-link>
        &nbsp;
        <nuxt-link class="comment-author" :to="{
          name: 'profile',
          params: {
            username: comment.author.username
          }
        }">
          {{ comment.author.username }}
        </nuxt-link>
        <span class="date-posted">{{ comment.createdAt | date('MMM DD, YYYY') }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import { getComments, addComments } from '@/api/article'
import { mapState } from 'vuex'

export default {
  name: 'ArticleComments',
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  data () {
    return {
      comments: [], // 文章列表
      // 待添加的评论信息
      commentToAdd: ""
    }
  },
  computed: {
    ...mapState(['user'])
  },
  async mounted () {
    await this.getCommentsList()
  },
  methods: {
    // 获取评论列表
    async getCommentsList () {
      const data = await getComments(this.article.slug)
      this.comments = data.comments
    },
    // 添加评论
    async handleAddComment () {
      this.commentToAdd && await addComments(this.article.slug, {
        comment: {
          body: this.commentToAdd
        }
      })
      this.commentToAdd = ""
      await this.getCommentsList()

    }
  }
}
</script>

<style>

</style>
