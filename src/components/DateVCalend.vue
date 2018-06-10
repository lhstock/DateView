<template>
  <div class="dateV-calend-wrapper">

    <div class="date-calend-header">
      <div class="last-btn" @click="onGoLast()">
        << </div>
          <div class="jump-btn" @change='onGoAppoint()'>
            <input id="jump-year" type="text " :value="useData._date.getFullYear()"> 年
            <input id="jump-month" type="text " :value="useData._date.getMonth()+1"> 月
            <!-- <input id="jump-day" type="text " :value="cuDate.getDate()"> 日 -->
            <!-- {{cuDate}} -->
          </div>
          <div class="next-btn" @click="onGoNext()">
            >>
          </div>
      </div>
      <!-- @mousedown="onMousedown(DomScroll) "
      @mouseup="onMounseup(DomScroll) "
       @scroll="onScroll(DomScroll,scrollTime) " -->
      <div class="date-calend-body-wrapper " id="body-wrapper " :style={height:calendHeight}>
        <!-- {{calendHeight}} -->
        <div v-for="(item, index) in useData.calenders " :key="index " :id="index===0 ? 'beforeMonth' : (index===1 ? 'useMonth' : 'laterMonth') " class="calend-month ">
          <div v-for="(itemDay, indexDay) in item " :key="indexDay " class="calend-day ">
            <div>
              {{( itemDay['beforeDate']|| itemDay['date']|| itemDay['laterDate']).getDate() }}
            </div>
          </div>
        </div>
      </div>
    </div>
</template>
<script>
export default {
  // name:
  data() {
    return {
      cuDate: new Date(),
      appointDate: undefined,
      useData: null,
      timer: null,
      isAnimation: false,
      scrollTime: 10
      // calendHeight:'calc(100vw / 7 * )'
    };
  },
  beforeCreate() {
    console.log(this.cuData, 1);
  },
  created() {
    let _this = this;
    console.log(this.cuData, 2);
    this.InitUseTimeData(this.cuDate);
    // this.Timeout();
  },
  beforeMount() {
    console.log(this.cuData, 3);
    console.log("a");
  },
  mounted() {
    console.log(this.cuData, 4);
    // var result = x * value / 100;
    var wrapper = document.getElementById("body-wrapper");
    // wrapper.addEventListener(
    //   "scroll",
    //   () => {
    //     console.log(" scroll " + this.$refs.viewBox.scrollTop);
    //     //以下是我自己的需求，向下滚动的时候显示“我是有底线的（类似支付宝）”
    //     this.isScroll = this.$refs.viewBox.scrollTop > 0;
    //   },
    //   false
    // );
    // let beforeMonth = document.getElementById("beforeMonth");
    console.log(wrapper);
    // var cu = document.getElementById("useMonth");
    // wrapper.scrollTop = this.cellwidth * this.useData.lastObj.weekNo;
    // this.DomScroll.scrollTop = this.DomHeight[1];
    console.log("===end===");
  },
  methods: {
    Timeout: function() {
      let _this = this;
      setTimeout(() => {
        console.log("=========设置appointDate=========", this);
        let string = "2018-9-10";
        var date = _this.TranslateDateFromStr(string);
        _this.skipDate(date);
      }, 10000);
    },
    skipDate: function(date) {
      this.appointDate = date;
      this.InitUseTimeData(this.useDate);
    },
    DataToNext: function() {
      // this.appointDate = "1";
    },
    //字符串转换Date
    TranslateDateFromStr: function(str) {
      return new Date(str);
    },
    //日期数组转换Date
    TranslateDateFromArr: function(arr) {
      return new Date(arr);
    },
    //日期下标转换Date
    TranslateDateFromIndex: function(arr) {
      return new Date(...arr);
    },
    //获取日期数组 来源：date数据
    getArrFromDate: function(date) {
      let year = date.getFullYear();
      let month = date.getMonth();
      let day = date.getDate();
      return [year, month + 1, day];
    },
    //获取日期数组 来源：date字符串
    getArrFromDateStr: function(str) {
      let arr = str.split("-").map(d => {
        return Number(d);
      });
      return arr;
    },
    //获取下一月Date
    getNextDate: function() {
      let arr = this.useData.arrDate;
      let [year, month, day] = [...arr];
      //当前月 固定到本日
      day =
        year === this.cuData.arrDate[0] && month + 1 === this.cuData.arrDate[1]
          ? this.cuData.arrDate[2]
          : 1;
      return this.TranslateDateFromIndex([year, month, day]);
    },
    //获取上一月Date
    getLastDate: function() {
      let arr = this.useData.arrDate;
      let [year, month, day] = [...arr];
      //当前月 固定到本日
      day =
        year === this.cuData.arrDate[0] && month - 1 === this.cuData.arrDate[1]
          ? this.cuData.arrDate[2]
          : 1;

      return this.TranslateDateFromIndex([year, month - 2, day]);
    },
    getEndDay: function(arr) {
      let [year, month, day] = [...arr];
      let _date = new Date(year, month, 0);
      let obj = { _date };
      obj.week = _date.getDay();
      return obj;
    },
    getFirstDay: function(arr) {
      let [year, month, day] = [...arr];
      let _date = new Date(year, month - 1, 1);
      let obj = { _date };
      obj.week = _date.getDay();
      return obj;
    },
    //计算该月要多少格;
    getNumberOfObj: function(obj) {
      let weekFirst = obj.firstDay.week;
      let weekEnd = obj.endDay.week;
      let endDay = obj.endDay;
      let numbers = weekFirst + endDay._date.getDate() + 6 - weekEnd; //% 6;
      return numbers;
    },
    compoundCalenders: function() {
      this.useData.calenders = [
        // this.useData.lastObj.calender,
        this.useData.calender
        // this.useData.nextObj.calender
      ];
    },
    InitUseTimeData: function(date) {
      let obj = {};
      obj._date = date;
      this.useData = obj;
      //添加最后一天 第一天  日期数组
      // obj = { ...obj, ...this.createObjForDate(date) };
      Object.assign(obj, this.createObjForDate(date));
      obj.numbers = this.getNumberOfObj(obj);
      obj.weekNo = obj.numbers / 7;

      //下一月
      let nextObj = {};
      nextObj._date = this.getNextDate(obj.arrDate);
      //添加最后一天 第一天 日期数组
      // nextObj = { ...nextObj, ...this.createObjForDate(nextObj._date) };
      Object.assign(nextObj, this.createObjForDate(nextObj._date));
      nextObj.numbers = this.getNumberOfObj(nextObj);
      nextObj.weekNo = nextObj.numbers / 7;
      obj.nextObj = nextObj;

      //上一月
      let lastObj = {};
      lastObj._date = this.getLastDate(obj.arrDate);
      //添加最后一天 第一天 日期数组
      // lastObj = { ...lastObj, ...this.createObjForDate(lastObj._date) };
      Object.assign(lastObj, this.createObjForDate(lastObj._date));
      lastObj.numbers = this.getNumberOfObj(lastObj);
      lastObj.weekNo = lastObj.numbers / 7;
      obj.lastObj = lastObj;
      obj.weekNumbers = obj.weekNo + obj.nextObj.weekNo + obj.lastObj.weekNo;
      this.createCalendarDateFromType("cu");
      this.createCalendarDateFromType("next");
      this.createCalendarDateFromType("last");
      this.compoundCalenders();
      // obj.calenders = [
      // obj.lastObj.calender,
      // obj.calender
      // obj.nextObj.calender
      // ];
    },
    createCalendarDateFromType: function(type) {
      let obj;
      switch (type) {
        case "cu":
          obj = this.useData;
          break;
        case "next":
          obj = this.useData.nextObj;

          break;
        case "last":
          obj = this.useData.lastObj;
          break;
        //   default:
        //     break;
      }
      obj.calender = getArrCalender.call(this, obj);
      function getArrCalender(obj) {
        let ArrCalender = this.newArray(obj.numbers);
        var [year, month, day] = obj.arrDate;
        ArrCalender = ArrCalender.map((d, i) => {
          // 月前空白
          if (i < obj.firstDay.week) {
            d = {};
            // d.beforeDate = new Date(year, month - 1, i - obj.firstDay.week);
            d.beforeDate = this.TranslateDateFromIndex([
              year,
              month - 1,
              i + 1 - obj.firstDay.week
            ]);
          } else if (i >= obj.firstDay.week + obj.endDay._date.getDate()) {
            d = {};
            let num = i - (obj.firstDay.week + obj.endDay._date.getDate());
            // d.laterDate = new Date(
            //   year,
            //   month - 1,
            //   num + obj.endDay._date.getDate()
            // );
            d.laterDate = this.TranslateDateFromIndex([
              year,
              month - 1,

              num + 1 + obj.endDay._date.getDate()
            ]);
          } else {
            d = {};
            d.date = this.TranslateDateFromIndex([
              year,
              month - 1,
              i - obj.firstDay.week + 1
            ]);
          }
          return d;
        });

        return ArrCalender;
      }
    },
    newArray: function(a, b) {
      let min, max;
      if (!b) {
        min = 0;
        max = a;
      } else {
        min = a;
        max = b;
      }
      var arr = [];
      for (let index = min; index < max; index++) {
        arr.push(index);
      }
      return arr;
    },
    createObjForDate: function(date) {
      let arrDate = this.getArrFromDate(date);
      let endDay = this.getEndDay(arrDate);
      let firstDay = this.getFirstDay(arrDate);
      return { arrDate, endDay, firstDay };
    },
    onScroll: function(dom, time) {
      console.log(this);
      let _this = this;
      if (!this.isAnimation) {
        console.log("bool");
      } else {
        console.log("0000");
        if (this.oldScrollNum <= _this.DomHeight[1] / 2) {
          let newHeight = 0;
          let margin = Math.abs(newHeight - oldHeight);
          let cellMargin = margin / 100;
          let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
          timer2 = setInterval(() => {
            i++;
            dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
            i > 100 && clearInterval(timer2);
          }, 1);
        } else if (
          dom.scrollTop >=
          _this.DomHeight[1] + (_this.DomHeight[2] - _this.DomHeight[1]) / 2
        ) {
          let newHeight = _this.DomHeight[2];
          let margin = Math.abs(newHeight - oldHeight);
          let cellMargin = margin / 100;
          let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
          timer2 = setInterval(() => {
            i++;
            dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
            i > 100 && clearInterval(timer2);
          }, 1);
        } else {
          let newHeight = _this.DomHeight[1];
          let margin = Math.abs(newHeight - oldHeight);
          let cellMargin = margin / 100;
          let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
          timer2 = setInterval(() => {
            i++;
            dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
            i > 100 && clearInterval(timer2);
          }, 1);
        }
      }
      // let timer = null;
      // let _this = this;
      // this.onScroll = function() {
      //   clearTimeout(timer);
      //   // if (!_this.isAnimation) {
      //   let oldHeight = dom.scrollTop;
      //   timer = setTimeout(() => {
      //     _this.isAnimation = true;
      //     let i = 0;
      //     let timer2 = null;
      //     if (dom.scrollTop <= _this.DomHeight[1] / 2) {
      //       let newHeight = 0;
      //       let margin = Math.abs(newHeight - oldHeight);
      //       let cellMargin = margin / 100;
      //       let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
      //       timer2 = setInterval(() => {
      //         i++;
      //         dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
      //         i > 100 && clearInterval(timer2);
      //       }, 1);
      //     } else if (
      //       dom.scrollTop >=
      //       _this.DomHeight[1] + (_this.DomHeight[2] - _this.DomHeight[1]) / 2
      //     ) {
      //       let newHeight = _this.DomHeight[2];
      //       let margin = Math.abs(newHeight - oldHeight);
      //       let cellMargin = margin / 100;
      //       let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
      //       timer2 = setInterval(() => {
      //         i++;
      //         dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
      //         i > 100 && clearInterval(timer2);
      //       }, 1);
      //     } else {
      //       let newHeight = _this.DomHeight[1];
      //       let margin = Math.abs(newHeight - oldHeight);
      //       let cellMargin = margin / 100;
      //       let plusAndMinus = newHeight - oldHeight < 0 ? -1 : 1;
      //       timer2 = setInterval(() => {
      //         i++;
      //         dom.scrollTop = oldHeight + i * cellMargin * plusAndMinus;
      //         i > 100 && clearInterval(timer2);
      //       }, 1);
      //     }
      //   }, time);
      // };
    },
    onGoAppoint: function() {
      let domYear = document.getElementById("jump-year");
      let domMonth = document.getElementById("jump-month");
      let domDay = document.getElementById("jump-day");
      let year = +domYear.value;
      let month =
        +(+domMonth.value >= 1 && domMonth.value << 12 && domMonth.value) || 1;
      let arrIndex = [year, month - 1, 1];
      let date = this.TranslateDateFromIndex(arrIndex);
      this.skipDate(date);
      domYear.value = year;
      domMonth.value = month;
      console.log("change", this.useData);
    },
    onGoLast: function() {
      let date = this.useData.lastObj._date;

      this.InitUseTimeData(date);
      console.log("golast", this.useData);
      // let obj = this.useData;
      // var { _date, arrDate, endDay, firstDay, numbers, weekNo, calender } = obj;
      // obj.nextObj = Object.assign(obj.nextObj, {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // });
      // var {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // } = obj.lastObj;
      // obj = Object.assign(obj, {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // });
      // //上一月
      // let lastObj = {};
      // lastObj._date = this.getLastDate(obj.arrDate);
      // //添加最后一天 第一天 日期数组
      // Object.assign(lastObj, this.createObjForDate(lastObj._date));
      // lastObj.numbers = this.getNumberOfObj(lastObj);
      // lastObj.weekNo = lastObj.numbers / 7;
      // obj.lastObj = lastObj;
      // this.createCalendarDateFromType("last");
      // this.compoundCalenders();
      // console.log("last", this.useData);
    },
    onGoNext: function() {
      let date = this.useData.nextObj._date;

      this.InitUseTimeData(date);
      console.log("gonext", this.useData);

      // let obj = this.useData;
      // var { _date, arrDate, endDay, firstDay, numbers, weekNo, calender } = obj;
      // obj.lastObj = Object.assign(obj.lastObj, {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // });
      // var {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // } = obj.nextObj;
      // obj = Object.assign(obj, {
      //   _date,
      //   arrDate,
      //   endDay,
      //   firstDay,
      //   numbers,
      //   weekNo,
      //   calender
      // });
      // //下一月
      // let nextObj = {};
      // nextObj._date = this.getNextDate(obj.arrDate);
      // //添加最后一天 第一天 日期数组
      // // nextObj = { ...nextObj, ...this.createObjForDate(nextObj._date) };
      // Object.assign(nextObj, this.createObjForDate(nextObj._date));
      // nextObj.numbers = this.getNumberOfObj(nextObj);
      // nextObj.weekNo = nextObj.numbers / 7;
      // obj.nextObj = nextObj;

      // // //上一月
      // // let lastObj = {};
      // // lastObj._date = this.getLastDate(obj.arrDate);
      // // //添加最后一天 第一天 日期数组
      // // // lastObj = { ...lastObj, ...this.createObjForDate(lastObj._date) };
      // // Object.assign(lastObj, this.createObjForDate(lastObj._date));
      // // lastObj.numbers = this.getNumberOfObj(lastObj);
      // // lastObj.weekNo = lastObj.numbers / 7;
      // // obj.lastObj = lastObj;
      // // obj.weekNumbers = obj.weekNo + obj.nextObj.weekNo + obj.lastObj.weekNo;
      // // this.createCalendarDateFromType("cu");
      // this.createCalendarDateFromType("next");
      // // this.createCalendarDateFromType("last");
      // this.compoundCalenders();
      // // console.log("last", this.useData);
      // console.log("next", this.useData);
    }
  },

  computed: {
    cuData: function() {
      let obj = {};
      obj._date = this.cuDate;
      obj.arrDate = this.getArrFromDate(obj._date);

      return obj;
    },
    //计算展现月份
    useDate: function() {
      let date = this.appointDate || this.cuDate;
      return date;
    },
    calendHeight: function() {
      // return "calc(100vw / 7 * 5)";
      // ~"100% - @{diff}"
      return `calc(100vw / 7 * ${this.useData.weekNo})`;
    },
    cellwidth: function() {
      let w = window,
        d = document,
        e = d.documentElement,
        g = d.getElementsByTagName("body")[0],
        x = w.innerWidth || e.clientWidth || g.clientWidth,
        y = w.innerHeight || e.clientHeight || g.clientHeight;
      let s = 100 / 7 * x / 100;
      return s;
    },
    DomScroll: function() {
      return document.getElementById("body-wrapper");
    },
    DomHeight: function() {
      let arr = [];
      let before = this.cellwidth * this.useData.lastObj.weekNo;
      let cu = this.cellwidth * this.useData.weekNo;
      let later = this.cellwidth * this.useData.nextObj.weekNo;
      arr = [0, before, cu + before];
      return arr;
    }
  }
};
</script>

<style>
.dateV-calend-wrapper {
  width: 100%;
  /* height: 100%; */
  /* flex: 1 1 auto; */
}
.date-calend-header {
  height: 100px;
  display: flex;
  flex-direction: row;
  background: red;
}
.last-btn,
.next-btn {
  width: 100px;
  height: 100px;
  text-align: center;
  line-height: 100px;
}
.jump-btn {
  flex: 1 1 auto;
  display: flex;
  justify-content: center;
  flex-direction: row;
  align-items: center;
}
.jump-btn > input {
  width: 50px;
}
.date-calend-body-wrapper {
  overflow: auto;
}
.calend-month {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}
.calend-day {
  width: calc(100vw / 7);
  height: calc(100vw / 7);
  display: flex;
}
.calend-day div {
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid #ffffff;
  flex: 1 1 auto;
}
</style>
