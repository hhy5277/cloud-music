<template>
  <div>
    <div class="header">
      <img class="audio" src="../../assets/audio.png">    
      <input class="search" type="search" placeholder="但愿日子清净，抬头遇见的都是柔情" 
        @input="search"
        v-model="key"
      >
      <router-link :to="{name: 'musicPlay'}">
        <img class="music" src="../../assets/music.png" @click="setShowPlayer(true)">
      </router-link>
    </div>   
    <div class="searchSongs" v-show="isSearch">
      <div class="song" v-for="(item, index) in songs" :key="item.id" @click="play(item, index)">
        <img src="../../assets/search1.png" class="playing">
        <label class="songName">{{ item.name}}</label>
        <label class="writer"> {{ getWriterAlbum(item.ar, item.al.name) }}</label>
        <span :class="{showMv: item.mv}"></span>
      </div> 
    </div>
  </div>
</template> 

<script>
  import api from '../../api/index.js'
  import { mapMutations, mapState } from 'vuex';
  
  export default {
    created() {
      // 重新进入搜索页面后还保留刚才的搜索结果
      this.key = this.searchKey;
      if(this.searchKey) {
        api.search(this.searchKey, (res) => {
          if(res.length) {
            this.songs = res.slice();
          }
          else {
            alert("搜索不到该歌曲");
          }
        })
      }
    },
    data() {
      return {
        key: '',
        songs: [],
      }
    },
    methods: {
      search() {
        if(this.key) {
            api.search(this.key, (res) => {
              if(res.length) {
                this.songs = res.slice();
              }
              else {
                alert("搜索不到该歌曲");
              }
           })
        }
        else {
          this.songs = [];
        }
      },
      getWriterAlbum(name, album) {
        let str = '';
        for(let i=0; i<name.length; i++) {
          str += name[i].name;
          if(i !== name.length - 1)  str += '/';  
        } 
        return  str + ' - ' + album;
      },
      play(item, index) {  
        this.setSongId(item.id);
        this.setCurrentIndex(index);
        this.setPlayingList(this.songs);
        this.setPlayingListId('searchList');
        this.$router.push({
          name: 'musicPlay'
        });
      },
      ...mapMutations([
        'setShowPlayer',
        'setIsSearch',
        'setSearchKey',
        'setSongId',
        'setCurrentIndex',
        'setPlayingList',
        'setPlayingListId'
      ])
    },
    computed: {
      ...mapState([
        'isSearch',
        'searchKey'
      ])
    },
    watch: {
      key: function() {
        this.setSearchKey(this.key);
        if(this.key) {
          this.setIsSearch(true);
        }
        else {
          this.setIsSearch(false);
        }
      }
    }
  }
</script>

<style src="../../css/explore/header.css" scoped>
  /* 通过@import或import引入的css文件,scoped不起作用 */
  /* @import '../../css/explore/header.css' */
</style>