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

===C===
跳转到指定月份
CGoAppoint()
跳转到上一月
CGoLast()
CGoNext()

===V===
获取用户输入
VGetDateArrForUser

===M===
<!-- 指定日期初始化模型； -->
MSkipDate
<!-- 计算月模型应有多少格（包含上下月多余格数） -->
MGetNumberOfObj(obj)  //obj 月模型
<!-- 合并日历关联；主要是用于日后扩展 -->
MCompoundCalenders
<!-- 根据日期创建模型 -->
MCompoundCalenders(date)
<!-- 根据标签创建日历 type: 'cu'、'last'、'next' -->
MCreateCalendarDateFromType(type)
<!-- 提取模型信息 arrDate endDay firstDay -->
MExtractObjForDate(date

===o===
<!-- DateNumber==>DateIndex -->
_numberToIndex
<!-- createDate -->
_translateDateFromStr
_translateDateFromArr
_translateDateFromIndex
<!-- 获取Date的数组信息 -->
_getArrFromDate

<!-- 获取日期str的数组信息 -->
_getArrFromDateStr
<!-- 获取上、下一月的某个date -->
_getLastDate
_getNextDate
<!-- 获取指定日期的最后一天 -->
_getEndDay(arrIndex)
_getFirstDay(arrIndex);
<!-- newArray -->
newArray(min,max)

class:
.calend-month
  .calend-day
    .before-month
    .cu-month
    .later-month

.today
.active
.
