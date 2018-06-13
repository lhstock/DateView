<template>
  <div class="dateV-calend-wrapper">
<!-- <div></div> -->
      <!-- calend-header -->
    <div class="date-calend-header" @dragstart="e=>CDragstart(e)">
               <div class="last-btn" @click="CGoLast()">
        &#60;
      </div>
      <div class="jump-btn" @change='CGoAppoint()'>
        <input id="jump-year" type="text " :value="useData._date.getFullYear()"> 年
        <input id="jump-month" type="text " :value="useData._date.getMonth()+1"> 月
        <!-- <input id="jump-day" type="text " :value="cuDate.getDate()"> 日 -->
        <!-- {{cuDate}} -->
      </div>
      <div class="next-btn" @click="CGoNext()">
        &#62;
      </div>
    </div>
    <!-- @mousedown="onMousedown(DomScroll) "
      @mouseup="onMounseup(DomScroll) "
       @scroll="MScroll(DomScroll,scrollTime) " -->
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
  data () {
    return {
      cuDate: new Date(),
      appointDate: undefined,
      useData: null,
      timer: null,
      isAnimation: false,
      scrollTime: 10
      // calendHeight:'calc(100vw / 7 * )'
    }
  },
  beforeCreate () {
    console.log(this.cuData, 1)
  },
  created () {
    console.log(this.cuData, 2)
    this.MLoadModelForDate(this.cuDate)
    // this.Timeout();
  },
  beforeMount () {
    console.log(this.cuData, 3)
    console.log('a')
  },
  mounted () {
    console.log(this.cuData, 4)
    // var result = x * value / 100;
    console.log('===end===')
  },
  methods: {
    Timeout: function () {
      let _this = this
      setTimeout(() => {
        console.log('=========设置appointDate=========', this)
        let string = '2018-9-10'
        var date = _this._translateDateFromStr(string)
        _this.MSkipDate(date)
      }, 10000)
    },
    CGoAppoint: function () {
      let arrIndex = this.VGetDateArrForUser()
      let date = this._translateDateFromIndex(arrIndex)
      this.MSkipDate(date)
    },
    CGoLast: function () {
      let date = this.useData.lastObj._date
      this.MLoadModelForDate(date)
      console.log('golast', this.useData)
    },
    CGoNext: function () {
      let date = this.useData.nextObj._date
      this.MLoadModelForDate(date)
      console.log('gonext', this.useData)
    },
    MSkipDate: function (date) {
      this.appointDate = date
      this.MLoadModelForDate(this.useDate)
    },
    VGetDateArrForUser: function () {
      let domYear = document.getElementById('jump-year')
      let domMonth = document.getElementById('jump-month')
      let year = +domYear.value
      let month =
        +(+domMonth.value >= 1 && domMonth.value << 12 && domMonth.value) || 1
      let arrIndex = [year, this._numberToIndex(month), 1]
      return arrIndex
    },
    _numberToIndex: function (num) {
      return num - 1
    },
    // 字符串转换Date
    _translateDateFromStr: function (str) {
      return new Date(str)
    },
    // 日期数组转换Date
    _translateDateFromArr: function (arr) {
      return new Date(arr)
    },
    // 日期下标转换Date
    _translateDateFromIndex: function (arr) {
      return new Date(...arr)
    },
    // 获取日期数组 来源：date数据
    _getArrFromDate: function (date) {
      let year = date.getFullYear()
      let month = date.getMonth()
      let day = date.getDate()
      return [year, month + 1, day]
    },

    // 获取日期数组 来源：date字符串
    _getArrFromDateStr: function (str) {
      let arr = str.split('-').map(d => {
        return Number(d)
      })
      return arr
    },
    // 获取下一月Date
    _getNextDate: function () {
      let arr = this.useData.arrDate
      let [year, month, day] = [...arr]
      // 注意翻到所在月份 日期需指定当前日期
      day =
        year === this.cuData.arrDate[0] && month + 1 === this.cuData.arrDate[1]
          ? this.cuData.arrDate[2]
          : 1
      return this._translateDateFromIndex([year, month, day])
    },
    // 获取上一月Date
    _getLastDate: function () {
      let arr = this.useData.arrDate
      let [year, month, day] = [...arr]
      // 注意翻到所在月份 日期需指定当前日期
      day =
        year === this.cuData.arrDate[0] && month - 1 === this.cuData.arrDate[1]
          ? this.cuData.arrDate[2]
          : 1

      return this._translateDateFromIndex([year, month - 2, day])
    },
    _getEndDay: function (arrIndex) {
      let [year, month] = [...arrIndex]
      let _date = new Date(year, month, 0)
      let obj = { _date }
      obj.week = _date.getDay()
      return obj
    },
    _getFirstDay: function (arrIndex) {
      let [year, month] = [...arrIndex]
      let _date = new Date(year, month - 1, 1)
      let obj = { _date }
      obj.week = _date.getDay()
      return obj
    },
    // 计算该月要多少格;
    MGetNumberOfObj: function (obj) {
      let weekFirst = obj.firstDay.week
      let weekEnd = obj.endDay.week
      let endDay = obj.endDay
      let numbers = weekFirst + endDay._date.getDate() + 6 - weekEnd // % 6;
      return numbers
    },
    MCompoundCalenders: function () {
      this.useData.calenders = [
        // this.useData.lastObj.calender,
        this.useData.calender
        // this.useData.nextObj.calender
      ]
    },
    MLoadModelForDate: function (date) {
      let obj = {}
      obj._date = date
      this.useData = obj
      // 添加最后一天 第一天  日期数组
      // obj = { ...obj, ...this.MExtractObjForDate(date) };
      Object.assign(obj, this.MExtractObjForDate(date))
      obj.numbers = this.MGetNumberOfObj(obj)
      obj.weekNo = obj.numbers / 7

      // 下一月
      let nextObj = {}
      nextObj._date = this._getNextDate(obj.arrDate)
      // 添加最后一天 第一天 日期数组
      // nextObj = { ...nextObj, ...this.MExtractObjForDate(nextObj._date) };
      Object.assign(nextObj, this.MExtractObjForDate(nextObj._date))
      nextObj.numbers = this.MGetNumberOfObj(nextObj)
      nextObj.weekNo = nextObj.numbers / 7
      obj.nextObj = nextObj

      // 上一月
      let lastObj = {}
      lastObj._date = this._getLastDate(obj.arrDate)
      // 添加最后一天 第一天 日期数组
      // lastObj = { ...lastObj, ...this.MExtractObjForDate(lastObj._date) };
      Object.assign(lastObj, this.MExtractObjForDate(lastObj._date))
      lastObj.numbers = this.MGetNumberOfObj(lastObj)
      lastObj.weekNo = lastObj.numbers / 7
      obj.lastObj = lastObj
      obj.weekNumbers = obj.weekNo + obj.nextObj.weekNo + obj.lastObj.weekNo
      this.MCreateCalendarDateFromType('cu')
      this.MCreateCalendarDateFromType('next')
      this.MCreateCalendarDateFromType('last')
      this.MCompoundCalenders()
      // obj.calenders = [
      // obj.lastObj.calender,
      // obj.calender
      // obj.nextObj.calender
      // ];
    },

    MCreateCalendarDateFromType: function (type) {
      let obj
      switch (type) {
        case 'cu':
          obj = this.useData
          break
        case 'next':
          obj = this.useData.nextObj

          break
        case 'last':
          obj = this.useData.lastObj
          break
        //   default:
        //     break;
      }
      obj.calender = getArrCalender.call(this, obj)
      function getArrCalender (obj) {
        let ArrCalender = this._newArray(obj.numbers)
        var [year, month] = obj.arrDate
        ArrCalender = ArrCalender.map((d, i) => {
          // 月前空白
          if (i < obj.firstDay.week) {
            d = {}
            // d.beforeDate = new Date(year, month - 1, i - obj.firstDay.week);
            d.beforeDate = this._translateDateFromIndex([
              year,
              month - 1,
              i + 1 - obj.firstDay.week
            ])
          } else if (i >= obj.firstDay.week + obj.endDay._date.getDate()) {
            d = {}
            let num = i - (obj.firstDay.week + obj.endDay._date.getDate())
            // d.laterDate = new Date(
            //   year,
            //   month - 1,
            //   num + obj.endDay._date.getDate()
            // );
            d.laterDate = this._translateDateFromIndex([
              year,
              month - 1,

              num + 1 + obj.endDay._date.getDate()
            ])
          } else {
            d = {}
            d.date = this._translateDateFromIndex([
              year,
              month - 1,
              i - obj.firstDay.week + 1
            ])
          }
          return d
        })

        return ArrCalender
      }
    },
    _newArray: function (a, b) {
      let min, max
      if (!b) {
        min = 0
        max = a
      } else {
        min = a
        max = b
      }
      var arr = []
      for (let index = min; index < max; index++) {
        arr.push(index)
      }
      return arr
    },
    MExtractObjForDate: function (date) {
      let arrDate = this._getArrFromDate(date)
      let endDay = this._getEndDay(arrDate)
      let firstDay = this._getFirstDay(arrDate)
      return { arrDate, endDay, firstDay }
    },
    CDragstart (e) {
      console.log('===start===\n', e)
    }
  },

  computed: {
    cuData: function () {
      let obj = {}
      obj._date = this.cuDate
      obj.arrDate = this._getArrFromDate(obj._date)

      return obj
    },
    // 计算展现月份
    useDate: function () {
      let date = this.appointDate || this.cuDate
      return date
    },
    calendHeight: function () {
      // return "calc(100vw / 7 * 5)";
      // ~"100% - @{diff}"
      return `calc(100vw / 7 * ${this.useData.weekNo})`
    },
    cellwidth: function () {
      let w = window
      let d = document
      let e = d.documentElement
      let g = d.getElementsByTagName('body')[0]
      let x = w.innerWidth || e.clientWidth || g.clientWidth
      // let y = w.innerHeight || e.clientHeight || g.clientHeight
      let s = 100 / 7 * x / 100
      return s
    },
    DomScroll: function () {
      return document.getElementById('body-wrapper')
    },
    DomHeight: function () {
      let arr = []
      let before = this.cellwidth * this.useData.lastObj.weekNo
      let cu = this.cellwidth * this.useData.weekNo
      // let later = this.cellwidth * this.useData.nextObj.weekNo
      arr = [0, before, cu + before]
      return arr
    }
  }
}
</script>

<style>
.dateV-calend-wrapper {
  width: 100%;
  /* height: 100%; */
  /* flex: 1 1 auto; */
}
.date-calend-header {
  height: 100px;
  width: 100%;
  z-index: 100;
  position: absolute;
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
  margin-top: 100px;
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
