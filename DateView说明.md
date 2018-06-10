size:
   考虑适用使用场景位置；所以size适应容器；
   但height 有要求；

new Date 注意事项
year,month,day是数字时
例如 2018 ,6,9
new Date(year,month,day)
输出的月份是 2018-7-9   //month 是日常生活中的数值-1；
new Date([year,month,day])
输出的是 2018-6-9
为了避免麻烦
数组导入 就正常了；
星期数 也要减一


函数：
获取日期Date
    //字符串转换Date
TranslateDateFromStr
TranslateDateFromArr
TranslateDateFromIndex

由Date获取
getArrFromDate
getArrFormDateStr



