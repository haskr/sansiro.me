<template>
  <div class="home">
    <div class="area">
      <Header :title="msg" :img="backimg"></Header>
      <div class="my-info">
        <img :src="avatar" alt="">
        <p>{{ username }}</p>
      </div>
    </div>

    <loading v-if="!showList" class="home-loading"></loading>

    <transition name="fade">
      <div class="article-area" v-show="showList">
        <article-list v-for="(todo, index) in articleList" v-bind:key="index" :article="todo"></article-list>

        <load-more class="home-more" :requestUri="'/api/home/loadmore'" v-on:moreInfo="addToList"></load-more>
      </div>
    </transition>

    <div class="info"></div>
  </div>
</template>

<script>
import Header from '@c/Header'
import Loading from '@c/Loading'
import LoadMore from '@c/LoadMore'
import ArticleList from '@c/ArticleList'

export default {
  name: 'Home',
  components: {
    Header, ArticleList, LoadMore, Loading
  },
  data () {
    return {
      msg: 'Welcome to sansiro.me',
      showList: false,
      articleList: [],
      backimg: '',
      avatar: '',
      username: ''
    }
  },
  activated () {
    this.getHomePageInfo();
  },
  methods: {
    getHomePageInfo() {
      this.$http.get('/blog_api/site/Site/getHomeInfo')
        .then(data => {
          this.articleList = data.list;
          this.backimg = data.backimg;
          this.avatar = data.avatar;
          this.username = data.username

          this.showList = true;
        })
    },
    addToList (data) {
      this.articleList = [...this.articleList, ...data];
    }
  }
}
</script>

<style lang="scss" scoped>
@media screen and (max-width: 480px) {
  .home {
    padding: 0;
  }
}
@media screen and (min-width: 481px) {
  .home {
    padding-top: 170px;

    .area {
      margin: 0 8px;
    }
  }
}

.home {
  max-width: 100%;
  width: 900px;
  margin: 0 auto;

  .fade-enter-active, .fade-leave-active{
    transition: opacity 1s;
  }
  .fade-enter, .fade-leave-to {           
    opacity: 0;
  }

  .area {
    background: #fff;
    @extend %box;

    .my-info {
      display: flex;
      padding: 0 30px;
      flex-direction: row;
      justify-content: space-between;
      align-items: center;
      height: 90px;

      img {
        display: block;
        width: 70px;
        height: 70px;
        border-radius: 50%;
        box-shadow: 2px 3px 6px 0 rgba(0, 0, 0, 0.16)
      }
      p {
        font-weight: 450;
        font-size: 17px;
        text-shadow: 1px 1px 8px #bababa;
      }
    }
  }

  .home-more {
    @extend %box;
    max-width: calc(100% - 16px);
    width: 884px;
    margin: 48px auto 0;
    background-color: #fff;
  }

  .info {
    height: 200px;
  }

  .home-loading {
    background-color: #fff;
    @extend %box;
  }
}
</style>
