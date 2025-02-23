<template>
  <div class="article-detail">
    <article-toolbar
      ref="articleToolbar"
      :article="article"
      @openOriginalArticle="openWebArticle"
    />
    <div class="content-wrapper">
      <perfect-scrollbar
        v-if="!originalArticle"
        class="article-contentarea"
        :class="{ 'px-4': !originalArticle, 'px-0 py-0': originalArticle }"
        :style="{ fontFamily: currentFontStyle }"
      >
        <div
          v-if="article !== null && article.content !== null && !emptyState && !originalArticle"
          class="article-wrap"
          :class="{ 'offset-content': fontSettingsOn }"
          :style="{ fontSize: `${currentFontSize}% !important` }"
        >
          <h2>
            <strong>{{ article.title }}</strong><br>
            <small><span v-if="article.date_published">{{ article.date_published }} </span> <span v-if="article.author">by {{ article.author }}</span>  <strong v-if="article.date_published || article.date_published">&#183;</strong> {{ article.readtime }}</small>
          </h2>
          <div
            v-if="article.content"
            class="article-detail"
            v-html="article.content"
          />
          <div
            v-if="!article.content"
            class="article-detail"
          >
            {{ article.description }}
          </div>
        </div>
      </perfect-scrollbar>
      <div
        v-if="originalArticle && !emptyState"
        class="article-contentarea"
        :class="{ 'px-4': !originalArticle, 'px-0 py-0': originalArticle }"
        :style="{ fontFamily: currentFontStyle }"
      >
        <div
          v-if="article !== null && article.content !== null && !emptyState && originalArticle"
          class="web-wrap"
          :style="{ fontSize: `${currentFontSize}% !important` }"
        >
          <webview
            id="foo"
            :src="articleUrl"
            style="height: 100vh"
            nodeintegration
          />
        </div>
      </div>
      <div
        v-if="article !== null && article.content === null && emptyState && !originalArticle"
        class="article-contentarea  px-4"
      >
        <div
          class="article-detail d-flex flex-column justify-content-center align-items-center
"
        >
          <h3 class="mb-4">
            Whoops! not able to load content.
          </h3>
          <a
            :href="article.url"
            class="btn btn-primary btn-outline-primary js-external-link"
          >
            View it on web
          </a>
        </div>
      </div>
      <div
        v-if="loading"
        class="article-contentarea loading-state px-4"
      >
        <loader v-if="loading" />
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    id: {
      type: String,
      default: null
    },
    article: {
      type: Object,
      default: null
    },
    emptyState: {
      type: Boolean,
      default: null
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      originalArticle: false,
      articleUrl: null
    }
  },
  computed: {
    currentFontStyle () {
      return this.$store.state.Article.fontStyle
    },
    currentFontSize () {
      return this.$store.state.Article.fontSize
    },
    fontSettingsOn () {
      return this.$store.state.Article.fontSettingOn
    }
  },
  watch: {
    '$route.fullPath': {
      immediate: true, // Immediate option to call watch handler on first mount
      handler () {
        this.resetData()
      }
    }
  },
  methods: {
    resetData () {
      this.originalArticle = false
      this.articleUrl = null
    },
    openWebArticle (original, data) {
      this.originalArticle = original
      this.articleUrl = data
    }
  }
}
</script>
<style lang="scss">

.content-wrapper {
  overflow: hidden;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
}

// Default color mode
:root {
  & .article-contentarea {
    --h2-color: inherit;
    --h2-small-color: inherit;
    --text-color: inherit;
    --paragraph-color: inherit;
  }
}

// Dark color mode
.app-sunsetmode {
  & .article-contentarea {
    --text-color-gray: #000;
    --h2-color: var(--text-color);
    --h2-small-color: var(--text-color-gray);
    --paragraph-color: var(--text-color-gray);
  }
}
.app-darkmode {
  & .article-contentarea {
    --text-color-gray: #c8cacc;
    --h2-color: var(--text-color);
    --h2-small-color: var(--text-color-gray);
    --paragraph-color: var(--text-color-gray);
  }
}

.article-detail {
  position: relative;
  flex-grow: 1;
  height: 100%;

  background: var(--background-color);
}

.article-contentarea  {

  display: block;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  top: 41px;
  margin: 0;
  overflow: hidden;
  z-index: 2;
  padding: 15px 15px 30px;
  height: calc(100% - 45px);

  background-color: var(--background-color);
  h1,
  h2 {
    font-size: 2em;
    color: var(--text-color);
    small {
      font-size: 14px;
      color: var(--h2-small-color);
    }
  }

  ul {
    color: var(--text-color);
  }

  address,
  figure,
  blockquote,
  h3,
  h4 {
    color: var(--text-color);
  }

  b {
    color: var(--text-color);
  }

  div,
  div > p,
  p {
    color: var(--paragraph-color);
  }
}

.article-detail {
  img {
    display: block;
    max-width: 100%;
    margin-bottom: 15px;
  }

  h2 {
    margin-bottom: 25px;
  }

  h3 {
    font-size: 22px;
    margin-top: 15px;
    line-height: 29px;
    font-weight: 700;
  }

  iframe {
    display:block;
    width: 100%;
    height: 500px;
    border: 0;
  }

  .col {
    padding-left: 0;
    padding-right: 0;
  }
}

.loading-state {
  display: flex;
  justify-content: center;
  align-items: center;
}

.offset-content {
  padding-top: 60px;
}

.web-wrap {
  max-width: 100%;
}

.article-wrap {
  max-width: 700px;
  margin: 0 auto;

  img {
    display: block;
    max-width: 100%;
    margin-bottom: 15px;
    width: 100%;
  }
}
</style>
