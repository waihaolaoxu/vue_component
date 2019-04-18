<template>
  <div class="over">
    <div class="flexbox bar">
      <div @click="preMonth">
        上一月
      </div>
      <div class="flex tc">
        {{nowYear}}-{{nowMonth+1}}
      </div>
      <div @click="nextMonth">
        下一月
      </div>
    </div>

    <ul>
      <li class="date-tit">日</li>
      <li class="date-tit">一</li>
      <li class="date-tit">二</li>
      <li class="date-tit">三</li>
      <li class="date-tit">四</li>
      <li class="date-tit">五</li>
      <li class="date-tit">六</li>
      <li v-for="(item,index) in monthData" :key="index" :class="{empty:!item.day,curdate:item.day == nowDate}" @click="getDate(item)">{{item.day || "-"}}</li>
    </ul>
  </div>
</template>
<script>
export default {
  data() {
    return {
      nowYear: null,
      nowMonth: null,
      nowDate: null,
      monthData: []
    };
  },
  created() {
    this.init();
  },
  methods: {
    // 日历控件
    init() {
      var D = new Date();
      this.nowMonth = D.getMonth();
      this.nowYear = D.getFullYear();
      this.nowDate = D.getDate();
      this.getCurData();
    },
    // 上一月
    preMonth(){
      if(this.nowMonth>0){
        this.nowMonth --;
      }else{
        this.nowMonth = 12;
        this.nowYear --;
      }
      this.getCurData();
    },
    // 下一月
    nextMonth(){
      if(this.nowMonth<11){
        this.nowMonth ++ ;
      }else{
        this.nowMonth = 0;
        this.nowYear ++;
      }
      this.getCurData();
    },
    getDate(data){
      alert(`${this.nowYear}-${this.nowMonth}-${data.day} 星期 ${data.week}`)
    },
    //获取当前是周几
    getNowDay(n) {
      var D = new Date();
      D.setMonth(this.nowMonth, n);
      return D.getDay()
    },
    //获取当前月有多少天
    getDates(M) {
      var D = new Date();
      D.setMonth(M + 1, 0);
      return D.getDate();
    },
    //获取当前月第一天是星期几
    getMonthOne(M) {
      var D = new Date(this.nowYear, M, 1);
      return D.getDay();
    },
    // 格式化
    dateFormat(y, m, d) {
      //年，月，日
      function f(n) {
        if (n < 10) {
          return "0" + n;
        } else {
          return String(n);
        }
      }
      return y + f(m) + f(d);
    },
    // 获取当前月份数据
    getCurData() {
      var month = this.nowMonth;
      var _push = [];
      var dates = this.getDates(month); //当前月有多少天
      var day = this.getMonthOne(month); //当前月第一天是周几
      if (day != 0) {
        for (var p = 0; p < day; p++) {
          _push.push("");
        }
      }
      for (let s = 1; s <= dates; s++) {
        let data = { 
          day: s,
          week: this.getNowDay(s),
          time:new Date(this.nowYear,this.nowMonth,s).getTime()
        }
        _push.push(data);
      }
      if (day + dates < 42) {
        for (let size = 1; size <= 42 - day - dates; size++) {
          _push.push("");
        }
      }
      this.monthData = _push;
    }
  }
};
</script>
<style lang="scss" scoped>
.bar{
  width: 90%;
  line-height: 4;
  margin:0 auto;
}
ul {
  overflow: hidden;
  text-align: center;
  line-height: 2;
  width: 90%;
  margin: 0 auto;
}
li {
  width: 14%;
  box-sizing: border-box;
  background: #eee;
  margin: 0.14%;
  float: left;
  padding: 10px 0;
  &.empty{
    background: #f8f8f8;
  }
  &.curdate{
    background:rgb(131, 202, 131);
    color:#fff;
  }
  &.date-tit{
    background: #ddd;
  }
}
</style>
