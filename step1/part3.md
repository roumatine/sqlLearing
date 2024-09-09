# 函数 #
是指一段可以被另一段程序调用的程序或代码

· 字符串函数
    函数                        功能
    CONCAT(str1,str2,...)     字符串拼接,将字符串s1,s2...sn拼接成一个字符串
    LOWER(str)                将字符串str转换为小写
    UPPER(str)                将字符串str转换为大写
    LPAD(str,len,pad)         左边填,用字符串pad对str的左边进行填充,达到n个字符串长度
    RPAD(str,len,pad)         右边填,用字符串pad对str的右边进行填充,达到n个字符串长度
    TRIM(str)                 去掉字符串str头部和尾部的空格
    SUBSTRING(str,start,len)  返回从字符串str从start开始len个长度的字符串(索引值从1开始)
<!-- 使用 select+函数(参数); -->

· 数值函数
    函数                        功能
    CEIL(x)                     向上取整
    FLOOR(x)                    向下取整
    MOD(x,y)                    返回x/y的模
    RAND()                      返回0~1之间的随机数
    ROUND(x,y)                  求参数x的四舍五入的值, 保留y位小数

· 日期函数
    函数                                功能
    CURDATE()                           返回当前日期
    CURTIME()                           返回当前时间
    NOW()                               返回当前日期和时间
    YEAR(date)                          返回日期date的年份
    MONTH(date)                         返回日期date的月份
    DAY(date)                           返回日期date的日
    DATE_ADD(date,interval expr type)   返回日期/时间值加上一个时间间隔interval后的时间值
    DATEDIFF(date1,date2)               返回起始时间和结束时间之间的天数

· 流程函数
    函数                        功能
    IF(value, t, f)             如果value为true, 则返回t, 否则返回f
    IFNULL(value1,value2)       如果value1为NULL, 则返回value2, 否则返回value1
    CASE WHEN [value1] THEN [t1] WHEN [value2] THEN [t2] ... ELSE f END
    如果value1为true, 则返回t1, 如果value2为true, 则返回t2, ..., 否则返回默认值f
    CASE expr WHEN [value1] THEN [t1] WHEN [value2] THEN [t2] ... ELSE f END
    如果expr等于value1, 则返回t1, 如果expr等于value2, 则返回t2, ..., 否则返回默认值