<template>
  <div v-title class="me-ct-body" :data-title="title">
    <el-container class="me-ct-container">
      <el-main>
        <div class="me-ct-title me-area">
          <template v-if="this.$route.params.type === 'tag'">
            <img class="me-ct-picture" :src="ct.avatar?ct.avatar:defaultAvatar">
            <h3 class="me-ct-name">{{ ct.tagName }}</h3>
          </template>

          <template v-else>
            <img class="me-ct-picture" :src="ct.avatar?ct.avatar:defaultAvatar">
            <h3 class="me-ct-name">{{ ct.categoryName }}</h3>
            <p>{{ ct.description }}</p>
          </template>

          <span class="me-ct-meta">{{ ct.articles }} 文章</span>
        </div>

        <div class="me-ct-articles">
          <article-scroll-page v-bind="article" />
        </div>
      </el-main>
    </el-container>
  </div>
</template>

<script>
import ArticleScrollPage from '@/views/common/ArticleScrollPage'
import { getArticlesByCategory, getArticlesByTag } from '@/api/article'
import { getTagDetail } from '@/api/tag'
import { getCategoryDetail } from '@/api/category'
import defaultAvatar from '@/assets/img/logo.png'

export default {
  name: 'BlogCategoryTag',
  components: {
    ArticleScrollPage
  },
  data() {
    return {
      defaultAvatar: defaultAvatar,
      ct: {},
      article: {
        query: {
          tagId: '',
          categoryId: ''
        }
      }
    }
  },
  computed: {
    title() {
      if (this.$route.params.type === 'tag') {
        return `${this.ct.tagName} - 标签 - For Fun`
      }
      return `${this.ct.categoryName} - 文章分类 - For Fun`
    }
  },
  watch: {
    '$route': 'getCategoryOrTagAndArticles'
  },
  created() {
    this.getCategoryOrTagAndArticles()
  },
  methods: {
    getCategoryOrTagAndArticles() {
      const id = this.$route.params.id
      const type = this.$route.params.type
      if (type === 'tag') {
        this.getTagDetail(id)
        this.article.query.tagId = id
      } else {
        this.getCategoryDetail(id)
        this.article.query.categoryId = id
      }
    },
    getCategoryDetail(id) {
      const that = this
      getCategoryDetail(id).then(data => {
        that.ct = data.data
      }).catch(error => {
        if (error !== 'error') {
          that.$message({ type: 'error', message: '文章分类加载失败', showClose: true })
        }
      })
    },
    getTagDetail(id) {
      const that = this
      getTagDetail(id).then(data => {
        that.ct = data.data
      }).catch(error => {
        if (error !== 'error') {
          that.$message({ type: 'error', message: '标签加载失败', showClose: true })
        }
      })
    },
    getArticlesByCategory(id) {
      const that = this
      getArticlesByCategory(id).then(data => {
        that.articles = data.data
      }).catch(error => {
        if (error !== 'error') {
          that.$message({ type: 'error', message: '文章加载失败', showClose: true })
        }
      })
    },
    getArticlesByTag(id) {
      const that = this
      getArticlesByTag(id).then(data => {
        that.articles = data.data
      }).catch(error => {
        if (error !== 'error') {
          that.$message({ type: 'error', message: '文章加载失败', showClose: true })
        }
      })
    }
  }
}
</script>

<style>
  .me-ct-body {
    margin: 60px auto 140px;
    min-width: 100%;
  }

  .el-main {
    padding: 0;
  }

  .me-ct-title {
    text-align: center;
    height: 150px;
    padding: 20px;
  }

  .me-ct-picture {
    width: 60px;
    height: 60px;
  }

  .me-ct-name {
    font-size: 28px;
  }

  .me-ct-meta {
    font-size: 12px;
    color: #969696;
  }

  .me-ct-articles {
    width: 640px;
    margin: 30px auto;
  }

</style>
