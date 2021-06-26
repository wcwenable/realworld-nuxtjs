<template>
  <div class="profile-page">

    <div class="user-info">
      <div class="container">
        <div class="row">

          <div class="col-xs-12 col-md-10 offset-md-1">
            <img src="http://i.imgur.com/Qr71crq.jpg" class="user-img" />
            <h4>{{ profile.username }}</h4>
            <p>
              {{ profile.bio }}
            </p>
            <button class="btn btn-sm btn-outline-secondary action-btn" @click="$router.push('/settings')">
              <i class="ion-gear-a"></i>
              &nbsp;
               Edit Profile Settings
            </button>
          </div>

        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">

        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active">             
              <li class="nav-item"> 
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'my_article'
                  }"
                  exact
                  :to="{
                    path: `/profile/${user.username}`,
                    query: {
                      tab: 'my_article',
                      author: user.username
                    }
                  }"
                >My Articles</nuxt-link>
              </li>             
              <li class="nav-item"> 
                <nuxt-link
                  class="nav-link"
                  :class="{
                    active: tab === 'favorite_article'
                  }"
                  exact
                  :to="{
                    path: `/profile/${user.username}`,
                    query: {
                      tab: 'favorite_article',
                      favorited: user.username
                    }
                  }"
                >Favorited Articles</nuxt-link>
              </li>
            </ul>
          </div>
          
          <article-list :articles="articles" :articlesCount="articlesCount" :limit="limit" :tab="tab" :page="page" :paginationRoutePath="`/profile/${user.username}`"></article-list>
        </div>

      </div>
    </div>

  </div>
</template>

<script>
import { getProfile } from '@/api/user'
import { mapState } from 'vuex'
import {
  getArticles,
} from '@/api/article'

import ArticleList from '@/components/ArticleList'
export default {
  middleware: 'authenticated',
  name: 'UserProfile',
  components: {
    ArticleList
  },
  computed: {
    ...mapState(['user'])
  },
  async asyncData({isDev, route, store, env, params, query, req, res, redirect, error}) {
    console.log(route, 'route');
    const username = store.state.user.username
    const { profile } = await getProfile(username)
    const page = Number.parseInt(query.page|| 1)
    const limit = 20
    const tab = query.tab || 'my_article'
    let { author , favorited } = query
    // const tag = query.tag
    if (!author && !favorited) {
      author = username
    }

    const articleRes = await getArticles({
        limit,
        offset: (page - 1) * limit,
        author,
        favorited,
        tab: query.tab
      })

    const { articles, articlesCount } = articleRes

    articles.forEach(article => article.favoriteDisabled = false)

    return {
      articles, // 文章列表
      articlesCount, // 文章总数
      limit, // 每页大小
      page, // 页码
      tab,
      profile
    }
  },
  watchQuery: ['page', 'tab'],
}
</script>

<style>

</style>
