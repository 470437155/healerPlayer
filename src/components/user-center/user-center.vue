<template>
    <transition name="slide">
      <div class="user-center">
        <div class="back" @click="back">
          <i class="icon-back"></i>
        </div>
        <div class="switches-wrapper">
          <switches @switch="switchItem"
            :switches="switches" :currentIndex="currentIndex"></switches>
        </div>
        <div class="play-btn" ref="playBtn" @click="random">
          <i class="icon-play"></i>
          <span class="text">随机播放全部</span>
        </div>
        <div class="list-wrapper" ref="listWrapper">
          <scroll ref="favoriteList" v-if="currentIndex===0" class="list-scroll" :data="favoriteList">
          <div class="list-inner">
            <song-list :songs="favoriteList" @select="selectSong">
            </song-list>
          </div>
          </scroll>
          <scroll ref="playList" v-if="currentIndex===1" class="list-scroll" :data="playHistory">
          <div class="list-inner">
            <song-list :songs="playHistory" @select="selectSong">
            </song-list>
          </div>
          </scroll>
        </div>
        <div class="no-result-wrapper" v-show="noResult">
          <no-result :title="noResultDesc"></no-result>
        </div>
      </div>
    </transition>
</template>

<script>
  import Switches from 'base/switches/switches'
  import NoResult from 'base/no-result/no-result'
  import {mapGetters,mapActions,mapMutations} from 'vuex'
  import SongList from 'base/song-list/song-list'
  import Scroll from 'base/scroll/scroll'
  import Song from 'common/js/song'
  import {playListMixin} from "common/js/mixin";

  export default {
    mixins:playListMixin,
    data(){
      return{
        currentIndex:0,
        switches:[
          {name:'我喜欢的'},
          {name:'最近听的'}
        ]
      }
    },
    computed:{
      noResult(){
        if(this.currentIndex===0){
          return !this.favoriteList.length
        }else{
          return 0
        }
      },
      noResultDesc(){
        if(this.currentIndex===0){
          return '怎么可以一个喜欢都没有,快去点红心吧'
        }else{
          return '本功能即将上线,敬请期待'
        }
      },
      ...mapGetters([
        'favoriteList',
        'playHistory'
      ])
    },
		components:{
		  Switches,
      SongList,
      Scroll,
      NoResult
    },
    methods:{
      rita(){
        let singer={
          id:1,
          name:'袁企鹅(宝宝)',
          avatar:''
        }
        this.$router.push({
          path:'/singer/ywtrita'
        })
        console.log(123)
        this.setSinger(singer)
      },
      handlePlaylist(list){
        const bottom = playlist.length>0? '60px':''
        this.$refs.listWrapper.style.bottom = bottom
        this.$refs.favoritrList && this.$refs.playList.refresh()
      },
      switchItem(index){
        this.currentIndex=index
      },
      selectSong(song){
        this.insertSong(new Song(song))
      },
      back(){
        this.$router.back()
      },
      random(){
        let list=this.currentIndex===0?this.favoriteList:this.playHistory
        if(list.length===0){
          return
        }
        list=list.map((song)=>{
          return new Song(song)
        })
        this.randomPlay({list})
      },
      ...mapActions([
        'insertSong',
        'randomPlay'
      ]),
      ...mapMutations({
        setSinger:'SET_SINGER'
      })
    }
	}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .user-center
    position: fixed
    top: 0
    bottom: 0
    z-index: 100
    width: 100%
    background: $color-background
    &.slide-enter-active, &.slide-leave-active
      transition: all 0.3s
    &.slide-enter, &.slide-leave-to
      transform: translate3d(100%, 0, 0)
    .back
      position absolute
      top: 0
      left: 6px
      z-index: 50
      .icon-back
        display: block
        padding: 10px
        font-size: $font-size-large-x
        color: $color-theme
    .switches-wrapper
      margin: 10px 0 30px 0
      .to-rita
        position: absolute
        top: 15px
        right: 15px
        height: 20px
        width: 20px
    .play-btn
      box-sizing: border-box
      width: 135px
      padding: 7px 0
      margin: 0 auto
      text-align: center
      border: 1px solid  $color-text-l
      color: $color-text-l
      border-radius: 100px
      font-size: 0
      .icon-play
        display: inline-block
        vertical-align: middle
        margin-right: 6px
        font-size: $font-size-medium-x
      .text
        display: inline-block
        vertical-align: middle
        font-size: $font-size-small
    .list-wrapper
      position: absolute
      top: 110px
      bottom: 0
      width: 100%
      .list-scroll
        height: 100%
        overflow: hidden
        .list-inner
          padding: 20px 30px
    .no-result-wrapper
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
</style>
