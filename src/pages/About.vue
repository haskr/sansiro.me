<template>
  <page-box>
    <Header :title="title"></Header>

    <div class="main-area">
      <loading v-if="!showInfo"></loading>
      <transition name="fade">
        <page-padding class="article-body markdown-body" v-html="complieMarkeDown" v-show="showInfo"></page-padding>
      </transition>
    </div>
  </page-box>
</template>

<script>
import Header from '@c/Header'
import Loading from '@c/Loading'
import pageBox from '@c/pageBox'
import pagePadding from '@c/pagePadding'
import {calcTime, htmlDecode, posTop, setLazyLoadImg} from '@u/public'
import hljs from '@u/highlight'
import marked from '@u/marked'

export default {
  name: 'about',
  components: {
    Header, Loading, pageBox, pagePadding
  },
  data () {
    return {
      title: 'About me',
      content: '',
      showInfo: false,
    }
  },
  mounted () {
    posTop();

    this.setMarkDown();
    this.getMyOwnInfo().then(() => {
      setLazyLoadImg();
    });
  },
  computed: {
    complieMarkeDown () {
      return marked(htmlDecode(this.content), {
        sanitize: true
      })
    }
  },
  methods: {
    getMyOwnInfo (name) {
      return new Promise((res, rej) => {
        this.$http.get(`/api/about`)
          .then(data => {
            this.content = data.data.content;

            this.showInfo = true;
            res();
          }).catch(msg => {
            this.$toast({
              msg: '请求失败！',
              type: 'error'
            });
            rej();
          }
        )
      })
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
    }
  }
}
</script>

<style lang="scss" scoped>
.article-body {
  min-height: 200px;
}
.fade-enter-active, .fade-leave-active{
  transition: opacity 1s;
}
.fade-enter, .fade-leave-to {           
  opacity: 0;
}

.my-info {
  display: flex;
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
  min-height: 260px;
}
</style>
