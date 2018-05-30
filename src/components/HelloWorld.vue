<template>
  <div>
    <lottery :beforeLottery="beforeLottery" :afterLottery="afterLottery" :lotteryBtn="lotteryBtn" :prizesList="prizesList" :prize="prize"></lottery>
  </div>
</template>

<script>
  import lottery from './lotter'
export default {
  name: 'HelloWorld',
  data () {
    return {
      prizesList:[
        require('../assets/prizeSmall/1.png'),
        require('../assets/prizeSmall/2.png'),
        require('../assets/prizeSmall/3.png'),
        require('../assets/prizeSmall/4.png'),
        require('../assets/prizeSmall/5.png'),
        require('../assets/prizeSmall/6.png'),
        require('../assets/prizeSmall/7.png'),
        require('../assets/prizeSmall/8.png')
      ],
      lotteryBtn:{
        img: require('../assets/prizeBtn.png')
      },
      prize:0,
      speed:1000,
      cycle:1
    }
  },
  components:{
    lottery:lottery
  },
  methods:{
    // 抽奖前处理函数 , 可用于发送请求获取结果 , 必须定义
    beforeLottery(resolve,reject){
      setTimeout(()=>{
        this.prize = 2;
        resolve();
      },10)
    },
    //抽奖结束回调函数 , 用于处理结果
    afterLottery(){
      alert("draw"+ this.prize)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
