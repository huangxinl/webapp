<template>
    <transition name="slider">
        <music-list :songs = "songs" :bg-image= "bgImage" :title= "title"></music-list>
    </transition>
</template>

<script>
import {mapGetters} from 'vuex'
import {getSingerDetail} from 'api/singer'
import {ERR_OK} from 'api/config'
import MusicList from 'components/music-list/music-list'
import {createSong} from 'common/js/song'

export default {
  name: 'SingerDetail',
  data() {
    return {
      songs: []
    }
  },
  components: {
    MusicList
  },
  computed: {
    title() {
      return this.singer.name
    },
    bgImage() {
      return this.singer.avatar
    },
    ...mapGetters([
      'singer'
    ])
  },
  created() {
    this._getDetail()
  },
  methods: {
    _getDetail() {
        if (!this.singer.id) {
          this.$router.push('/singer')
          return
        }
        getSingerDetail(this.singer.id).then(res => {
            if (res.code === ERR_OK) {
            //  this.songs = res.data.list
              this.songs = this._normalizeSongs(res.data.list)
              console.log(this.songs)
          }
        })
      },
    _normalizeSongs(list){
      let ret = []
      list.forEach( (item) => {
        let {musicData} = item   //es6 结构赋值
           if(musicData.songid && musicData.albummid) {
              ret.push(createSong(musicData))
           }
      })
      return ret
    }


  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
@import "~common/stylus/variable"
.slider-enter-active, .slider-leave-active 
    transition all 0.3s
.slider-enter, .slider-leave-to
    transform translate3d(100%,0,0)
  .singer-detail
    position: fixed
    z-index 100
    top 0
    left 0
    right 0
    bottom 0
    background-color $color-background
</style>
