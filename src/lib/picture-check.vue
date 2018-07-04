<template>
    <div class="picture-check-wrapper" @click="handleClose">
      <div class="current-img">
        <ul
          :style="{'width': `${list.length*100}vw`, transition: `${hasAnimate?'.3s':'0s'}`, transform: `translate3d(-${(currentIndex+moveIndex)*100}vw,0,0)`}"
          @touchstart="touchstart"
          @touchmove="touchmove"
          @touchend="touchleave"
        >
          <li v-for="item in list">
            <img :src="item.src" alt="">
          </li>
        </ul>
      </div>
      <div class="menu" v-if="equipment==='pc'">
        <div class="icon left" @click.stop="handlePrev" v-if="currentIndex>0"><img src="./leftArrow.png" alt=""></div>
        <div class="icon right" @click.stop="handleNext" v-if="currentIndex<list.length-1"><img src="./rightArrow.png" alt=""></div>
      </div>
      <div class="page">
        <span v-for="(i,index) in list" :class="{active: currentIndex===index}" @click.stop="handleMove(index)"></span>
      </div>
      <!--<div class="close" @click="handleClose"></div>-->
    </div>
</template>

<script>
  export default {
    props: ['list', 'index', 'equipment'],
    data() {
      return {
        currentSrc: '',
        currentIndex: 0,
        moveIndex: 0,
        startX: 0,
        endX: 0,
        moveX: 0,
        hasAnimate: true
      }
    },
    created() {
      this.currentIndex = this.index;
      this.currentSrc = this.list[this.currentIndex].src;
    },
    methods: {
      handlePrev() {
        this.currentIndex -= 1;
        this.currentSrc = this.list[this.currentIndex].src;
      },
      handleNext() {
        this.currentIndex += 1;
        this.currentSrc = this.list[this.currentIndex].src;
      },
      handleMove(index) {
        this.currentIndex = index;
        this.currentSrc = this.list[this.currentIndex].src;
      },
      handleClose() {
        this.$emit('closeCheck', '');
      },
      touchstart(e) {
        this.startX = e.touches['0'].clientX;
      },
      touchmove(e) {
        this.hasAnimate = false;
        this.moveX = this.startX - e.touches['0'].clientX;
        const wWidth = document.body.clientWidth;
        this.moveIndex = this.moveX / wWidth;
        console.log(this.moveIndex);
      },
      touchleave(e) {
        this.moveIndex = 0;
        this.hasAnimate = true;
        if (this.moveX > 50) {
          this.currentIndex >= this.list.length - 1 ? this.list.length - 1 : this.currentIndex += 1;
        } else if (this.moveX < -50) {
          this.currentIndex <= 0 ? 0 : this.currentIndex -= 1;
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .picture-check-wrapper{
    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    z-index: 99999999;
    background: #000;
    .current-img{
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      ul{
        display: flex;
        li{
          width: 100vw;
          height: 100vh;
          display: flex;
          align-items: center;
          justify-content: center;
          img{
            max-width: 100%;
            max-height: 100%;
          }
        }
      }
    }
    .menu{
      position: absolute;
      left: 0;
      right: 0;
      top: 50%;
      z-index: 3;
      .icon{
        width: 60px;
        height: 60px;
        border-radius: 50%;
        position: absolute;
        top: -40px;
        background: rgba(0,0,0,0.2);
        box-shadow: 0 0 3px #111;
        display: flex;
        align-items: center;
        justify-content: center;
        &:active{
          box-shadow: 0 0 3px #fff;
          img{
            opacity: 0.9;
          }
        }
        &.left{
          left: 30px;
          img{
            margin-right: 5px;
          }
        }
        &.right{
          right: 30px;
          img{
            margin-left: 5px;
          }
        }
        img{
          opacity: 0.5;
        }
      }
    }
    .page{
      position: absolute;
      left: 0;
      right: 0;
      bottom: 0;
      height: 60px;
      background: rgba(0,0,0,0.3);
      display: flex;
      justify-content: center;
      align-items: center;
      span{
        width: 10px;
        height: 10px;
        margin:  0 6px;
        border-radius: 50%;
        background: #fff;
        &.active{
          background: #1296db;
        }
      }
    }
    .close{
      position: absolute;
      right: 0;
      top: 0;
      width: 40px;
      height: 40px;
      background: rgba(0,0,0,0.3);
      display: flex;
      justify-content: center;
      align-items: center;
      &:after{
        content: '';
        width: 20px;
        height: 20px;
        background: url("./close.png") no-repeat center center / cover;
      }
    }
  }
</style>
