<template>
  <div class="article-meta">
    <nuxt-link :to="{
      name: 'profile',
      params: {
        username: article.author.username
      }
    }">
      <img :src="article.author.image" />
    </nuxt-link>
    <div class="info">
      <nuxt-link class="author" :to="{
        name: 'profile',
        params: {
          username: article.author.username
        }
      }">
        {{ article.author.username }}
      </nuxt-link>
      <span class="date">{{ article.createdAt | date('MMM DD, YYYY') }}</span>
    </div>
    <template v-if="user.username !== article.author.username">
      <button
        class="btn btn-sm btn-outline-secondary"
        :class="{
          active: article.author.following
        }"
        @click="handleFollowUser"
      >
        <i class="ion-plus-round"></i>
        &nbsp;
        {{ followText }} {{ article.author.username }}
      </button>
      &nbsp;&nbsp;
      <button
        class="btn btn-sm btn-outline-primary"
        :class="{
          active: article.favorited
        }"
        @click="handleFavoriteUser"
      >
        <i class="ion-heart"></i>
        &nbsp;
        {{ favoriteText }} Post <span class="counter">({{ article.favoritesCount }})</span>
      </button>
    </template>
    <template v-else>
      <nuxt-link
        class="btn btn-outline-secondary btn-sm"
        :to="{
          name: 'editor',
          params: {
            article: article
          }
        }"
      >
        <i class="ion-edit"></i>
        &nbsp;
        Edit Article
      </nuxt-link>
      &nbsp;&nbsp;
      <button
        class="btn btn-outline-danger btn-sm"
        @click="handleDeleteArticle"
      >
        <i class="ion-trash-a"></i>
        &nbsp;
        Delete Article
      </button>
    </template>
  </div>
</template>

<script>
import { followUser, unfollowUser } from "@/api/user"
import { addFavorite, deleteFavorite, deleteArticle } from "@/api/article"
import { mapState } from 'vuex'
export default {
  name: 'ArticleMeta',
  data () {
    return { 
      // 跟随处理中
      followProcessing: false,
      // 喜欢处理中
      favoriteProcessing: false
    }
  },
  props: {
    article: {
      type: Object,
      required: true
    }
  },
  computed: {
    ...mapState(['user']),
    favoriteText () {
      return this.article.favorited ? 'Unfavorite' : 'Favorite'
    },
    favoriteCount () {
      return this.article.favoritesCount
    },
    followText () {
      return this.article.author.following ? 'Unfollow' : 'Follow'
    }
  },
  methods: {
    // 删除文章
    async handleDeleteArticle () {
      await deleteArticle(this.article.slug)
      this.$router.push(`/profile/${this.user.username}`)
    },
    // 跟随 作者
    async handleFollowUser () {
      if (!this.followProcessing) {
        this.followProcessing = true
        const processFn = this.article.author.following ? unfollowUser : followUser
        const data = await processFn(this.article.author.username)
        this.$set(this.article, 'author', data.profile)
        this.followProcessing = false
      }
    },
    // 为 文章作者 点赞
    async handleFavoriteUser () {
      if (!this.favoriteProcessing) {
        this.favoriteProcessing = true
        const processFn = this.article.favorited ? deleteFavorite : addFavorite
        const data = await processFn(this.article.slug)
        this.article.favorited = data.article.favorited
        this.article.favoritesCount = data.article.favoritesCount
        this.favoriteProcessing = false
      }
    }
  }
}
</script>

<style>

</style>
