<template>
  <div class="home-page">

    <div class="banner">
      <div class="container">
        <h1 class="logo-font">拉勾教育-导管</h1>
        <p>A place to share your knowledge.-共享知识之地</p>
      </div>
    </div>

    <div class="container page">
      <div class="row">

        <div class="col-md-9">
          <div class="feed-toggle">
            <ul class="nav nav-pills outline-active">
              <li v-if="user" class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'your_feed'
                  }"
                  exact
                  :to="{
                    name: 'home',
                    query: {
                      tab: 'your_feed'
                    }
                  }"
                >Your Feed</nuxt-link>
              </li>
              <li class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'global_feed'
                  }"
                  exact
                  :to="{
                    name: 'home'
                  }"
                >Global Feed</nuxt-link>
              </li>
              <li v-if="tag" class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'tag'
                  }"
                  exact
                  :to="{
                    name: 'home',
                    query: {
                      tab: 'tag',
                      tag: tag
                    }
                  }"
                ># {{ tag }}</nuxt-link>
              </li>
            </ul>
          </div>
          <article-list :articles="articles" :articlesCount="articlesCount" :limit="limit" :tab="tab" :page="page" paginationRouteName="home"></article-list>
        </div>

        <div class="col-md-3">
          <div class="sidebar">
            <p>Popular Tags</p>

            <div class="tag-list">
              <nuxt-link
                :to="{
                  name: 'home',
                  query: {
                    tab: 'tag',
                    tag: item
                  }
                }"
                class="tag-pill tag-default"
                v-for="item in tags"
                :key="item"
              >{{ item }}</nuxt-link>
            </div>
          </div>
        </div>

      </div>
    </div>

  </div>
</template>

<script>
import {
  getArticles,
  getYourFeedArticles
} from '@/api/article'
import { getTags } from '@/api/tag'
import { mapState } from 'vuex'

import ArticleList from '@/components/ArticleList'

export default {
  name: 'HomeIndex',
  components: {
    ArticleList
  },
  data () {
    return {
      tags: []
    }
  },
  async asyncData ({ query }) {
    const page = Number.parseInt(query.page|| 1)
    const limit = 20
    const tab = query.tab || 'global_feed'
    const tag = query.tag

    const loadArticles = tab === 'global_feed'
      ? getArticles
      : getYourFeedArticles

    const [ articleRes, tagRes ] = await Promise.all([
      loadArticles({
        limit,
        offset: (page - 1) * limit,
        tag
      }),
      getTags()
    ])

    const { articles = [], articlesCount = 0 } = articleRes
    let { tags = [] } = tagRes
    let xx = decodeURI ('%E2%80%8C')
    let reg =RegExp(xx,'g')
    tags = tags.map(item=>item. replace(reg, '')).filter(item=> !!item)
    tags = new Set(tags) //去重

    articles.forEach(article => article.favoriteDisabled = false)

    return {
      articles, // 文章列表
      articlesCount, // 文章总数
      tags, // 标签列表
      limit, // 每页大小
      page, // 页码
      tab, // 选项卡
      tag // 数据标签
    }
  },
  watchQuery: ['page', 'tag', 'tab'],
  computed: {
    ...mapState(['user'])
  }
}
</script>

<style>

</style>
