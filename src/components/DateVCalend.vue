<template>
  <div class="dateV-calend-wrapper">
    <!-- calend-header -->
    <div class="date-calend-header"
      @dragstart="e=>CDragstart(e)">
      <div class="last-btn"
        @click="CGoLast()">
        &#60;
      </div>
      <div class="jump-btn"
        @change='CGoAppoint()'>
        <input id="jump-year"
          type="text "
          :value="useData._date.getFullYear()"> 年
        <input id="jump-month"
          type="text "
          :value="useData._date.getMonth()+1"> 月
      </div>
      <div class="next-btn"
        @click="CGoNext()">
        &#62;
      </div>
    </div>
    <!-- calend-header END -->

    <!-- calend-body -->
    <div class="date-calend-body-wrapper "
      id="body-wrapper "
      :style={height:calendHeight}>
      <!-- month -->
      <div v-for="(item, index) in useData.calenders "
        :key="index "
        class="calend-month ">
        <!-- day -->
        <div v-for="(itemDay, indexDay) in item "
          :key="indexDay "
          class="calend-day ">
          <div :class="arrclass(itemDay)"
            @click="CClick(itemDay)">
            {{itemDay.date.getDate()}}
          </div>
        </div>
        <!-- day END -->
      </div>
      <!-- month END -->
    </div>
    <!-- calend-body  END -->
  </div>
</template>

<script>
// import '../assets/css/test.scss'
export default {
  // name:
  data() {
    return {
      cuDate: this._clearHours(new Date()),
      appointDate: undefined,
      useData: null,
      selectDate: this._clearHours(new Date()),
      timer: null,
      isAnimation: false,
      scrollTime: 10,
      DAYTIME: 1000 * 60 * 60 * 24
    }
  },
  beforeCreate() {
  },
  created() {
    this.MLoadModelForDate(this.cuDate)
  },
  beforeMount() {
  },
  mounted() {
  },
  methods: {
    Timeout: function () {
      let _this = this
      setTimeout(() => {
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
    },
    CGoNext: function () {
      let date = this.useData.nextObj._date
      this.MLoadModelForDate(date)
    },
    MSkipDate: function (date) {
      this.appointDate = date
      this.MLoadModelForDate(this.useDate)
    },
    // 用户输入转ArrDate
    VGetDateArrForUser: function () {
      let domYear = document.getElementById('jump-year')
      let domMonth = document.getElementById('jump-month')
      let year = +domYear.value
      let month =
        +(+domMonth.value >= 1 && domMonth.value << 12 && domMonth.value) || 1
      let arrIndex = [year, this._numberToIndex(month), 1]
      return arrIndex
    },
    // Date日期转index
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
      // this.useData.calenders = [
      //   // this.useData.lastObj.calender,
      //   this.useData.calender
      //   // this.useData.nextObj.calender
      // ]
      this.$set(this.useData, 'calenders', [this.useData.calender])
    },
    MCreateMonthData: function (date) {
      let obj = {}
      obj._date = date
      Object.assign(obj, this.MExtractObjForDate(obj._date))
      obj.numbers = this.MGetNumberOfObj(obj)
      obj.weekNo = obj.numbers / 7
      return obj
    },
    MLoadModelForDate: function (date) {
      // 当月
      let obj = this.MCreateMonthData(date)
      this.useData = obj
      this.selectDate = this._translateDateFromArr(obj.arrDate)

      // 下一月
      let nextDate = this._getNextDate(obj.arrDate)
      let nextObj = this.MCreateMonthData(nextDate)
      obj.nextObj = nextObj

      // 上一月
      let lastDate = this._getLastDate(obj.arrDate)
      let lastObj = this.MCreateMonthData(lastDate)
      obj.lastObj = lastObj

      obj.weekNumbers = obj.weekNo + obj.nextObj.weekNo + obj.lastObj.weekNo
      this.MCreateCalendarDateFromType('cu')
      this.MCreateCalendarDateFromType('next')
      this.MCreateCalendarDateFromType('last')
      this.MCompoundCalenders()
    },

    MCreateCalendarDateFromType: function (type) {
      let obj
      let selectDate
      switch (type) {
        case 'cu':
          obj = this.useData
          selectDate = this.selectDate || this._translateDateFromArr(obj.arrDate)
          break
        case 'next':
          obj = this.useData.nextObj
          selectDate = this._translateDateFromArr(obj.arrDate)

          break
        case 'last':
          obj = this.useData.lastObj
          selectDate = this._translateDateFromArr(obj.arrDate)
          break
        default:
          obj = this.useData
          selectDate = this._translateDateFromArr(obj.arrDate)
          break
      }
      obj.calender = getArrCalender.call(this, obj)
      function getArrCalender(obj) {
        let ArrCalender = this._newArray(obj.numbers)
        var [year, month] = obj.arrDate
        ArrCalender = ArrCalender.map((d, i) => {
          d = {}
          d.class = {}
          // 月前空白
          if (i < obj.firstDay.week) {
            d.date =
              this._translateDateFromIndex([
                year,
                month - 1,
                i + 1 - obj.firstDay.week
              ])
            d.class['before-month'] = 1
          } else if (i >= obj.firstDay.week + obj.endDay._date.getDate()) {
            let num = i - (obj.firstDay.week + obj.endDay._date.getDate())
            d.date = this._translateDateFromIndex([
              year,
              month - 1,
              num + 1 + obj.endDay._date.getDate()
            ])
            d.class['later-month'] = 1
          } else {
            d.date = this._translateDateFromIndex([
              year,
              month - 1,
              i - obj.firstDay.week + 1
            ])
            d.class['cu-month'] = 1

            if (d.date - selectDate === 0) {
              d.class['active'] = 1
            } else {
              delete d.class.active
            }
          }
          if (this.isToday(d.date)) {
            d.class['today'] = 1
          }

          // { 'today': isToday(Object.values(itemDay)[0]) }
          // d.arrclass = Object.keys(d.class)
          return d
        })
        return ArrCalender
      }
    },
    _clearHours: function (date) {
      let dateNumber = date.setHours(0, 0, 0, 0)
      let newDate = new Date(dateNumber)
      return newDate
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
    // 提取arrDate endDay firstDay
    MExtractObjForDate: function (date) {
      let arrDate = this._getArrFromDate(date)
      let endDay = this._getEndDay(arrDate)
      let firstDay = this._getFirstDay(arrDate)
      return { arrDate, endDay, firstDay }
    },
    CDragstart(e) {
    },

    CClick(data) {
      if (data.class['cu-month']) {
        this.selectDate = data.date
        this.MCreateCalendarDateFromType('cu')
        this.MCompoundCalenders()
        // this.$set(this.useData)
      }
    },
    isToday(date) {
      return parseInt((new Date().setHours(0, 0, 0, 0) - date) / this.DAYTIME) + '' === '0'
    },
    arrclass: function (data) {
      return Object.keys(data.class)
    }

  },

  computed: {
    // get 选中日期的下标
    numIndexOfSelect: function () {
      let index = 0
      this.useData.calender.forEach((data, i) => {
        let time = data.date - this.selectDate.setHours(0, 0, 0, 0)
        index = data.class.indexOf('before-month') !== -1 ? i + 1 : index
        index = parseInt(time / this.DAYTIME) === 0 ? i : index
      })
      return index
    },
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
  border-bottom: 1px solid #978e8e;
}

.calend-day div {
  display: flex;
  justify-content: center;
  align-items: center;
  flex: 1 1 auto;
}
.before-month,
.later-month {
  /* background: #e6e6e6; */
  color: #d9d9d9;
}
.cu-month {
  /* background: #ec57c8; */
  color: #000000;
  margin: 10px 0;
}
.active {
  background: #00daf9;
}
.today {
  color: red;
}
</style>
