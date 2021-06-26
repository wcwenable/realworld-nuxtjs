<template>
  <div>   
    <div v-if="articles.length">
      <div
        class="article-preview"
        v-for="article in articles"
        :key="article.slug"
      >
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
          <button
            class="btn btn-outline-primary btn-sm pull-xs-right"
            :class="{
              active: article.favorited
            }"
            @click="onFavorite(article)"
            :disabled="article.favoriteDisabled"
          >
            <i class="ion-heart"></i> {{ article.favoritesCount }}
          </button>
        </div>
        <nuxt-link
          class="preview-link"
          :to="{
            name: 'article',
            params: {
              slug: article.slug
            }
          }"
        >
          <h1>{{ article.title }}</h1>
          <p>{{ article.description }}</p>
          <span>Read more...</span>
          <ul class="tag-list">
            <li v-for="tag in article.tagList" :key="tag" class="tag-default tag-pill tag-outline">{{ tag }}</li>
          </ul>
        </nuxt-link>
      </div>
    </div>
    <div v-else class="article-preview">
      No articles are here... yet.
    </div>
    
    <!-- 分页列表 -->
    <nav>
      <ul class="pagination">
        <li
          class="page-item"
          :class="{
            active: item === page
          }"
          v-for="item in totalPage"
          :key="item"
        >
          <nuxt-link
            class="page-link"
            :to="{
              name: paginationRouteName,
              path: paginationRoutePath,
              query: {
                page: item,
                tag: $route.query.tag,
                tab: tab
              }
            }"
          >{{ item }}</nuxt-link>
        </li>
      </ul>
    </nav>
    <!-- /分页列表 -->
  </div>
</template>

<script>
import {
  addFavorite,
  deleteFavorite
} from '@/api/article'
export default {
  name: 'ArticleList',
  props: {
    articles: {
      type: Array,
      default () {
        return []
      }
    },
    articlesCount: {
      type: Number,
      default: 0
    },
    limit: {
      type: Number,
      default: 0
    },
    tab: {
      type: String,
      default: ''
    },
    paginationRouteName: {
      type: String,
      default: undefined
    },    
    paginationRoutePath: {
      type: String,
      default: undefined
    },
    page: {
      type: Number,
      default: 1
    }
  },
  computed: {
    totalPage () {
      return Math.ceil(this.articlesCount / this.limit)
    },
  },
  methods: {
    async onFavorite (article) {
      article.favoriteDisabled = true
      if (article.favorited) {
        // 取消点赞
        await deleteFavorite(article.slug)
        article.favorited = false
        article.favoritesCount += -1
      } else {
        // 添加点赞
        await addFavorite(article.slug)
        article.favorited = true
        article.favoritesCount += 1
      }
      article.favoriteDisabled = false
    }
  }
}
</script>