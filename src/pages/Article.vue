<template>
  <page-box>
    <Header :title="article.title" :img="article.headpic"></Header>
    <div class="my-info">
      <img src="//59.110.213.152/cdn/images/default-head.jpg" alt="">
      <p>{{ calcArticleTime(article.time) }}</p>
    </div>
    <div class="main-area">
      <loading class="article-loading" v-if="!showArticle"></loading>
      <transition name="fade">
        <page-padding class="article-body markdown-body" v-html="complieMarkeDown" v-show="showArticle"></page-padding>
      </transition>
    </div>
  </page-box>
</template>

<script>
import Header from '@c/Header'
import Loading from '@c/Loading'
import pageBox from '@c/pageBox'
import pagePadding from '@c/pagePadding'
import {calcTime, htmlDecode, setLazyLoadImg, posTop} from '@u/public'
import hljs from '@u/highlight'
import marked from '@u/marked'

import domtoimage from 'dom-to-image'

export default {
  name: 'articlePage',
  components: {
    Header, Loading, pageBox, pagePadding
  },
  data () {
    return {
      showArticle: false,
      article: window.articleInfo || {}
    }
  },
  mounted () {
    posTop();

    this.setMarkDown();
    this.getArticleInfo(this.$route.params.name).then(() => {

      setLazyLoadImg();
    });
  },
  computed: {
    complieMarkeDown () {
      let res = marked(htmlDecode(this.article.content), {
        sanitize: true
      })

      setTimeout(() => {
        this.clickPicture();
      }, 0)

      return res;
    }
  },
  methods: {
    getArticleInfo (name) {
      return new Promise((res, rej) => {
        this.$http.get(`/api/article?index=${name}`)
          .then(data => {
            this.article = data.data;

            this.showArticle = true;

            res();
          }).catch(msh => {
            this.$router.push('/error');

            rej();
          })
      })
    },
    calcArticleTime (time) {
      return calcTime(time);
    },
    setMarkDown () {
      marked.setOptions({
        renderer: new marked.Renderer(),
        gfm: true,
        tables: true,
        breaks: false,
        pedantic: false,
        sanitize: true,
        smartLists: true,
        smartypants: false,
        highlight: function(code) {
          return hljs.highlightAuto(code).value
        }
      });
    },
    clickPicture () {
      let imgs = document.getElementsByClassName('clickable');
      for(let img of imgs) {
        img.onclick = () => {
          this.$show({
            url: img.dataset.src
          })
        }
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.my-info {
  display: flex;
  // padding: 0 30px;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  height: 80px;

  img {
    display: block;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    box-shadow: 2px 3px 6px 0 rgba(0, 0, 0, 0.16)
  }

  p {
    font-weight: 450;
    font-size: 16px;
    text-shadow: 1px 1px 8px #bababa;
  }
}

.main-area {
  min-height: 400px;

  .article-loading {
    margin: 40px 0;
  }

  .fade-enter-active, .fade-leave-active{
    transition: opacity 1s;
  }
  .fade-enter, .fade-leave-to {           
    opacity: 0;
  }

  .other {
    height: 100px;
  }
}
</style>
