<template>
  <div class="dateV-calend-wrapper">
    {{cuData}}
    <p>
      {{useData}}
      <!-- {{useDate}} -->
    </p>

    <div class="date-calend-header">
      a
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
      useData: null
    };
  },
  beforeCreate() {
    console.log(this.cuData, 1);
  },
  created() {
    let _this = this;
    console.log(this.cuData, 2);
    this.InitUseTimeData(this.cuDate);
    this.Timeout();
  },
  beforeMount() {
    console.log(this.cuData, 3);
    console.log("a");
  },
  mounted() {
    console.log(this.cuData, 4);
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
    Next: function() {
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
      let year = date.getUTCFullYear();
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
      day = month + 1 === this.cuData.arrDate[1] ? this.cuData[2] : 1;
      return this.TranslateDateFromIndex([year, month, day]);
    },
    //获取上一月Date
    getLastDate: function() {
      let arr = this.useData.arrDate;
      let [year, month, day] = [...arr];
      //当前月 固定到本日
      day = month - 1 === this.cuData.arrDate[1] ? this.cuData[2] : 1;

      return this.TranslateDateFromIndex([year, month - 2, day]);
    },
    getEndDay: function(arr) {
      let [year, month, day] = [...arr];
      let date = new Date(year, month, 0);
      let obj = {};
      obj.date = date;
      return date;
    },
    getFirstDay: function(arr) {
      let [year, month, day] = [...arr];
      let date = new Date(year, month - 1, 1);
      return date;
    },
    // getNumForArr: function(arr) {
    //   let num = date.getDate();
    //   return num;
    // },
    InitUseTimeData: function(date) {
      let obj = {};
      obj._date = date;
      this.useData = obj;
      obj.arrDate = this.getArrFromDate(obj._date);
      obj.endDay = this.getEndDay(obj.arrDate);
      obj.firstDay = this.getFirstDay(obj.arrDate);

      // obj.sum = this.get;
      //下一月
      let nextObj = {};
      nextObj._date = this.getNextDate(obj.arrDate);
      obj.nextObj = nextObj;
      //上一月
      let lastObj = {};
      lastObj._date = this.getLastDate(obj.arrDate);
      obj.lastObj = lastObj;
    }
  },

  computed: {
    cuData: function() {
      console.log("!!!!");
      let obj = {};
      obj._date = this.cuDate;
      obj.arrDate = this.getArrFromDate(obj._date);
      return obj;
    },
    //计算展现月份
    useDate: function() {
      let date = this.appointDate || this.cuDate;
      return date;
    }

    // useDate: function() {
    // switch (typeof this.useDay) {
    //   case "string":
    //     console.log("cuData======\n", "string===>     ", this.appointDate);
    //     return "aaa";
    //     break;
    //   case "object":
    //     console.log(
    //       "cuData======\n",
    //       "object===>     ",
    //       this.useDay.toDateString()
    //     );
    //     return this.useDay.toDateString();
    //     break;
    //   default:
    //     console.log("cuData======\n", "other===>     ", this.appointDate);
    //     break;
    // }
    // return new Date();
    // }
  }
};
</script>
