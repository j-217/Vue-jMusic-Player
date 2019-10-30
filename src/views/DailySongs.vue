<template>
  <div class="daily-songs-box" :style="`background-image: url(http://p4.music.126.net/X8LFsaEXt8wBNAajrPP1oA==/109951164454514159.jpg)`">
    <!-- header -->
    <common-header :title="slideUpFlag ? title : ''"></common-header>
    <!-- body -->
    <div class="body-box">
      <div class="info-box" :class="{'active': slideUpFlag}">
        <div class="pic-box">
          <img src="http://p4.music.126.net/X8LFsaEXt8wBNAajrPP1oA==/109951164454514159.jpg" alt="" class="pic">
        </div>
        <div class="date-box">
          <span class="day">{{ date.getDate() }}</span>
          <span>/</span>
          <span class="month">{{ date.getMonth() + 1 }}</span>
        </div>
      </div>

      <div class="songslist-box">
        <div class="title-box">
          <div class="play-all" @click="playAll(dailySongsList)">
            <van-icon name="play-circle-o" />
            <div class="song-counts">
              <span>播放全部</span>
            </div>
          </div>
          <div class="check-box">
            <van-icon name="bars" />
          </div>
        </div>
        <div class="list-box">
          <ul class="songslist-items" ref="slideList">
            <li class="item" v-for="(item, index) in dailySongsList" :key="index" @click="goSwichSongs(item.id)">
              <div class="song-info" :class="{'active': item.id === curMusic}">
                <span class="play-icon"><van-icon name="volume-o" /></span>
                <span class="song-name">{{ item.name }}</span>
                <span>-</span>
                <span class="singer">{{ item.artists[0].name }}</span>
              </div>
              <span><van-icon name="ellipsis" /></span>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <!-- footer -->
    <nav-footer></nav-footer>
  </div>
</template>
<script>
import CommonHeader from '../components/CommonHeader'
import NavFooter from '../components/NavFooter'
import { mapState } from 'vuex'

export default {
  data(){
    return {
      title: '每日推荐',
      date: new Date(),
      slideUpFlag: false,
    }
  },

  mounted(){
    this.$store.dispatch('getDailySongs')
    this.$refs.slideList.addEventListener('scroll', this.slideUp)
  },

  components: {
    CommonHeader,
    NavFooter,
  },

  computed: {
    ...mapState({
      dailySongsList: state => state.music.dailySongs,
      curMusic: state => state.player.curMusic,
    })
  },

  methods: {
    goSwichSongs(musicId){
      this.$store.dispatch('switchSongs', musicId)
    },
    // 将歌单内歌曲替换至当前播放列表并播放第一首
    playAll(newList){
      this.$store.commit('setPlaylist', newList)
      this.$store.dispatch('switchSongs', newList[0].id)
    },
    // 列表向上滑动后将列表高度放大
    slideUp(e){
      let scrollTop = e.target.scrollTop
      if(scrollTop === 0){
        this.slideUpFlag = false;
      }if(scrollTop > 1){
        this.slideUpFlag = true;
      }
    }
  }
}
</script>
<style scoped lang="less">
@import '../assets/css/base.less';

.daily-songs-box{
  background-color: #eee;
  height: 100%;
  width: 100%;
  position: fixed;
  background-size: cover;
  background-repeat: no-repeat;
  z-index: -2;
  &::after{
    content: '';
    position: absolute;
    top: 0%;
    z-index: -1;
    height: 200%;
    width: 200%;
    background-image: inherit;
    background-size: cover;
    background-repeat: no-repeat;
    filter: blur(1rem);
  }
  .body-box{
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    .info-box{
      position: relative;
      transition: all 0.5s linear;
      &::before,
      &::after{
        content: '';
        display: block;
        width: 0.8rem;
        height: 0.8rem;
        border-radius: 50%;
        background-color: #ccc;
        position: absolute;
        z-index: 1;
        bottom: -1rem;
      }
      &::before{
        left: 25%;
      }
      &::after{
        right: 25%;
      }
      .pic-box{
        width: 100%;
        font-size: 0;
        &::before,
        &::after{
          content: '';
          display: block;
          width: .5rem;
          height: 2rem;
          background-color: #fff;
          border-radius: 0.2rem;
          position: absolute;
          z-index: 2;
          bottom: -0.7rem;
        }
        &::before{
          left: calc(25% + 0.15rem);
        }
        &::after{
          right: calc(25% + 0.15rem);
        }
        img{
          width: 100%;          
        }
      }
      .date-box{
        position: absolute;
        bottom: 1rem;
        left: 1rem;
        font-size: 1.3rem;
        color: #fff;
        .day{
          font-size: 2.5rem;
        }
      }
      &.active{
        height: 3rem;
        filter: blur(2rem);
      }
    }
    .songslist-box{
      display: flex;
      position: relative;
      flex-direction: column;
      background-color: #fff;
      width: 100%;
      height: 100%;
      border-radius: 2rem 2rem 0 0;
      .title-box{
        display: flex;
        position: relative;
        justify-content: space-between;
        align-items: center;
        padding: 1rem;
        color: @darkFontColor; 
        .play-all{
          display: flex;
          justify-content: space-between;
          align-items: center;
          font-size: 2rem;
          .song-counts{
            padding-left: 0.5rem;
            font-size: 1rem;
          }
        }
        .check-box{
          font-size: 2rem;
        }
      }
      .list-box{
        height: 100%;
        width: 100%;
        padding: 0rem 1rem;
        padding-bottom: 4rem;
        .songslist-items{
          height: 100%;
          overflow-y: scroll;
          .item{
            display: flex;
            justify-content: space-between;
            padding-top: 0.5rem;
            align-items: center;
            >span{
              color: @lightFontColor;
              font-size: x-large;
            }
            .song-info{
              display: flex;
              width: 90%;
              align-items: flex-end;
              >span:nth-of-type(3){
                padding: 0 0.3rem;
              }
              .play-icon{
                display: none;
              }
              .song-name{
                display: inline-block;
                max-width: 50%;
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
              }
              .singer{
                display: inline-block;
                font-size: small;
                max-width: 30%;
                white-space: nowrap;
                overflow: hidden;
                text-overflow: ellipsis;
              }
              &.active{
                color: @activeFontColor;
                .play-icon{
                  display: inline-block;
                  padding-right: 0.5rem;
                }
              }
            }
					}
        }
      }
    }
  }
} 

</style>