<template>
  <div class="iv-lottery">
    <ul>
      <li class="row">
        <div :class="['col-4' , {active : activeClass[index]}]" v-for="(prize,index) in lis1">
          <img :src="prize">
        </div>
      </li>
      <li class="row">
        <div :class="['col-4' , {active : activeClass[7]}]">
          <img :src="prizesList[7]">
        </div>
        <div class="col-4" @click="startLottery">
          <img :src="lotteryBtn.img">
        </div>
        <div :class="['col-4' , {active : activeClass[3]}]">
          <img :src="prizesList[3]">
        </div>
      </li>
      <li class="row">
        <div :class="['col-4' , {active : activeClass[6-index]}]" v-for="(prize,index) in lis2">
          <img :src="prize">
        </div>
      </li>
    </ul>
  </div>
</template>


<script type="text/javascript">

  export default {
    name: 'swiper',
    data() {
      return {
        activeClass: [false, false, false, false, false, false, false, false],
        index: -1,
        count: 8,
        timer: null,
        times: 0,
        speedData: 100,
        lock: false,
        afterLotteryHandler: null
      };
    },
    props: {
      prizesList: {
        type: Array,
        default() {
          return [];
        }
      },
      lotteryBtn: {
        type: Object,
        default() {
          return {
            img: ''
          };
        }
      },
      beforeLottery: {
        type: Function,
        default() {
          throw new Error('you must define beforeLottery before draw a lottery ');
        }
      },
      afterLottery: {
        type: Function,
        // eslint-disable-next-line
        default() {
          console.warn('you can use afterLottery after rolling');
        }
      },
      prize: {
        type: Number,
        default: 0
      },
      speed: {
        type: Number,
        default: 100
      },
      cycle: {
        type: Number,
        default: 20
      }
    },
    computed: {
      lis1() {
        return this.prizesList.slice(0, 3);
      },
      lis2() {
        return this.prizesList.slice(4, 7).reverse();
      }
    },
    created() {
      this.speedData = this.speed;
      this.afterLotteryHandler = this.afterLottery;
    },
    beforeDestroy() {
      this.afterLotteryHandler = () => {
      };
    },
    methods: {
      startLottery() {
        if (!this.lock) {
          let promise = () => {
            return new Promise((resolve, reject) => {
              this.lock = true;
              this.beforeLottery(resolve, reject);
            });
          };
          let start = async () => {
            try {
              await promise();
              this.roll();
            } catch (e) {
              e();
            }
          };
          start();
        }
      },
      _rollHandler() {
        var index = this.index;
        var count = this.count;
        for (let i = 0, len = this.activeClass.length; i < len; i++) {
          this.activeClass[i] = false;
        }
        index += 1;
        if (index > count - 1) {
          index = 0;
        }
        this.activeClass[index] = true;
        this.index = index;
        return false;
      },
      roll() {
        // eslint-disable-next-line
        this.activeClass = this.activeClass.map(item => item = false);
        this.times += 1;
        this._rollHandler();
        if (this.times > this.cycle + 10 && this.prize == this.index) {
          clearTimeout(this.timer);
          setTimeout(
            () => {
              this._showResult();
            }
            , 1000);
          this.lock = false;
          this.index = -1;
          this.count = 8;
          this.timer = null;
          this.speedData = this.speed;
          this.times = 0;
        } else {
          if (this.times < this.cycle) {
            this.speedData -= 2;
          } else {
            if (this.times > this.cycle + 10 && ((this.prize == 0 && this.index == 7) || this.prize == this.index + 1)) {
              this.speedData += 110;
            } else {
              this.speedData += 20;
            }
          }
          if (this.speedData < 40) {
            this.speedData = 40;
          }
          this.timer = setTimeout(
            () => {
              this.roll();
            }
            , this.speedData);
        }
        return false;
      },
      _showResult() {
        this.afterLotteryHandler();
      }
    }
  };
</script>
<style lang="scss">

  //color
  $textDefault: #333333;
  $textLight	: #a6a6a6;
  $lineGray	: #e0e0e0;


  $white		: #ffffff;
  $orange		: #f97d00;
  $orangeLight: #ff9329;
  $blue 		: #2e9fe5;
  $blueLight 	: #52b5f3;
  $gray 		: #d8d8d8;
</style>
<style lang="scss">
  //base
  $phone:750;
  $url:'../../img/';
  $remBase:$phone/20;
  //font-size
  @function torem($size) {
    $remSize: $size / $remBase;
    @return $remSize * 1rem;
  }

  //ellipsis
  @mixin ellipsis(){
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }

  //flexbox
  @mixin flexbox(){
    //OLD - iOS 6-, Safari 3.1-6
    display: -webkit-box;
    //NEW - Chrome
    display: -webkit-flex;
    display: flex;
  }
  //flex
  @mixin flex($per){
    -webkit-box-flex:$per;
    -webkit-flex:$per;
    flex:$per;
    //下面3个属性是为了兼容安卓4.3
    display:block;
    width:0%;
    overflow:hidden;
  }
  //arrow
  @mixin arrow($deg){
    content: '';
    display:inline-block;
    margin-top:torem(-5);
    width:torem(10);
    height:torem(10);
    border-right:1px solid #C7C7C7;
    border-top:1px solid #C7C7C7;
    @include transform(rotate($deg));
  }
  //center
  @mixin center(){
    display:-webkit-box;
    display:-moz-box;
    display:-ms-box;
    display:-o-box;
    display:box;
    -webkit-box-pack: center;
    box-pack:center;
    -webkit-box-align:center;
    box-align:center;
  }
  // border-bottom
  @mixin borderBottom(){
    &:before{
      content: '';
      display: block;
      width: 100%;
      height: 1px;
      background: $lineGray;
      position: absolute;
      bottom:0;
      left: 0;
      @include transform(scaleY(0.5));
    }
  }
  //keyframes
  @mixin keyframes($animateName){
    @-webkit-keyframes #{$animateName}
    {
      @content;
    }
    @keyframes #{$animateName}
    {
      @content;
    }
  }
  //animation
  @mixin animation($str) {
    animation: $str;
    -webkit-animation: $str;
    // -moz-animation: $str;
    // -ms-animation: $str;
    // -o-animation: $str;
  }
  //transition
  @mixin transition($transition){
    transition:$transition;
    -webkit-transition:$transition;
    // -moz-transition:$transition;
    // -ms-transition:$transition;
    // -o-transition:$transition;
  }
  //transform
  @mixin transform($transition){
    transform:$transition;
    -webkit-transform:$transition;
    // -moz-transform:$transition;
    // -ms-transform:$transition;
    // -o-transform:$transition;
  }
</style>
<style lang="scss">
  @mixin cols(){
    @for $index from 1 through 12{
      .row .col-#{$index}{
        width: $index/12*100%;
        float: left;
        box-sizing:border-box;
      }
    }
  }

  // offset
  @mixin offset(){
    @for $index from 1 through 12{
      .row .col-offset-#{$index}{
        margin-left : $index/12*100%;
      }
    }
  }


  .row{
    display:block;
    &:after{
      content: '';
      display: block;
      width: 0;
      height: 0;
      clear: both;
    }
  }

  @include cols();
  @include offset();
</style>
<style lang="scss">
  .iv-lottery {
    width: 100%;
    background-size: cover;

    ul {
      margin: 0 auto;
      width: 10rem;
      box-sizing: border-box;
      background-size: contain;
      background-repeat: no-repeat;
      padding: 0.831rem 0.768rem 0.768rem 0.768rem;
      position: relative;
      z-index: 1;

      li {
        overflow:hidden;
        position: relative;
      }

      li div {
        padding: torem(1.2);
        position: relative;

        &.active:before {
          content: '';
          display: block;
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          border-radius: 5px;
          background: rgba(255, 135, 46, 0.62); }
      }

      li div img{
        width: 100%;
        vertical-align: middle;
      }

    }

  }
</style>
<style lang="scss"></style>
