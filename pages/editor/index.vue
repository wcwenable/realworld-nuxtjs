<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        {{article}}
        <div class="col-md-10 offset-md-1 col-xs-12">
          <form @submit.prevent="handlePublishArticle515">
            <fieldset>
              <fieldset class="form-group">
                  <input v-model="article.title" type="text" class="form-control form-control-lg" placeholder="Article Title">
              </fieldset>
              <fieldset class="form-group">
                  <input v-model="article.description" type="text" class="form-control" placeholder="What's this article about?">
              </fieldset>
              <fieldset class="form-group">
                  <textarea v-model="article.body" class="form-control" rows="8" placeholder="Write your article (in markdown)"></textarea>
              </fieldset>
              <fieldset class="form-group">
                  <input v-model="article.tagList" type="text" class="form-control" placeholder="Enter tags"><div class="tag-list"></div>
              </fieldset>
              <button class="btn btn-lg pull-xs-right btn-primary" type="button" @click="handlePublishArticle">
                  Publish Article
              </button>
            </fieldset>
          </form>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
import { createArticle, updateArticle } from '@/api/article'
// import MarkdownIt from 'markdown-it'
export default {
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: 'authenticated',
  name: 'EditorIndex',
  data () {
    return {
      article: {
        title: '',
        description: '',
        body: '',
        tagList: ''
      }
    }
  },
  mounted() {
    // const md = new MarkdownIt()
    this.article = this.$route.params.article || this.article
    if (Array.isArray(this.article.tagList)) {
      this.article.tagList = this.article.tagList.join(',')
    }
    // this.article.body = md.render(this.article.body)
  },
  methods: {
    async handlePublishArticle () {
      debugger
      this.article.tagList = this.article.tagList && this.article.tagList.split(',')

      this.article.slug ? await updateArticle(this.article.slug,this.article) : await createArticle({ 
        article: this.article
      })

      this.$router.push({
        name: 'home',
        tab: 'your_feed'
      })
    }
  }
}
</script>

<style>

</style>
