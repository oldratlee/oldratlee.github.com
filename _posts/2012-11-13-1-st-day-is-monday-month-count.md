---
layout: post
title: 任意挑一年，一号是星期一的月最多有几个？最少有几个？
location: Hangzhou
permalink: /post/2012-11-13/1-st-day-is-monday-month-count
write-time: 2012-11-13 22:30
tags:
- date
- week
- 日期
- 星期
- 计算
- 月份
- 润年
- 日历
- 个数
---

前面的博文“[任意挑一个月，一号是星期一的概率](http://oldratlee.com/post/2012-10-26/probability-of-1st-day-is-monday "任意挑一个月，一号是星期一的概率")”末尾留下了本文问题。

[热爱自然科学的好骚年](http://weibo.com/u/1692008241 "热爱自然科学的好骚年")在[微博](http://weibo.com/1692008241/z4RQIDNCi "微博")中给出的解法，并给出了运行结果。这里完整的记录一下。

# 解法说明

按照[热爱自然科学的好骚年](http://weibo.com/u/1692008241 "热爱自然科学的好骚年")在[这条微博](http://weibo.com/1692008241/z4RQIDNCi "这条微博")中给出的解法：

> 无论是哪一年，1月1号只有星期一至星期日这7种情况；除2月（因为[润年](http://baike.baidu.com/view/29649.htm "润年")的变化，有28天、29天2种情况）外各个月的天数固定；所以每年的日期和星期的组合只有7*2种情况。

穷举这***14种情况***，就可以给出本文问题的结论。

有了思路后，编程写算法到得出结论就只是一二十分钟的问题了。

# 结论说明

写了Python代码计算出14种情况下一年中一号是星期一的月份有哪几个。  
\# 代码在文末。

本文问题的结论是：

> 任意挑一年，一号是星期一的月最多有3个，最少有1个。

详细结论的月份分布见下面的说明。

### 非润年时，一号是星期一的月份：
一月一号是星期日时： 5  
一月一号是星期一时： 1, 10  
一月一号是星期二时： 4, 7  
一月一号是星期三时： 9, 12  
一月一号是星期四时： 6  
一月一号是星期五时： 2, 3, 11  
一月一号是星期六时： 8

### 润年时，一号是星期一的月份：

一月一号是星期日时： 10  
一月一号是星期一时： 1, 4, 7  
一月一号是星期二时： 9, 12  
一月一号是星期三时： 6  
一月一号是星期四时： 3, 11  
一月一号是星期五时： 2, 8  
一月一号是星期六时： 5

### 简单验证

2021年有3个月一号是星期一，2月3月和11月。

2021年不是润年，一月一号是星期五，和上面的计算结果吻合！***Bingo***！！

# 计算的Python代码

{% highlight python %}
# Calculate the months which First day is monday
# My related Blog: http://oldratlee.com/post/2012-11-13/1-st-day-is-monday-month-count

dayCountOfMonthOfYear = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
dayCountOfMonthOfLeapYear = [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]

print "Day count of non-leap year: ", sum(dayCountOfMonthOfYear)
# 365, day count of year
print "Day count of leap year: ", sum(dayCountOfMonthOfLeapYear) 
# 366, day count of Leap year

def getDwsMonths(dayCountOfMonth, firstDay) :
    check = lambda m : (sum(dayCountOfMonth[0 : m]) + firstDay) % 7 == 1
    ret = filter(check, range(0, 12))
    return [x + 1 for x in ret] # adjust month index start with 1

from functools import partial

print "Month list for leap year: ", map(partial(getDwsMonths, dayCountOfMonthOfYear), range(0, 7))
# [[5], [1, 10], [4, 7], [9, 12], [6], [2, 3, 11], [8]]

print "Month list for non-leap year: ", map(partial(getDwsMonths, dayCountOfMonthOfLeapYear), range(0, 7))
# [[10], [1, 4, 7], [9, 12], [6], [3, 11], [2, 8], [5]]
{% endhighlight %}

[放在github gist上的代码段](https://gist.github.com/4071311 "github gist")

![星期](http://m1.img.libdd.com/farm4/2012/1113/23/59919BDD22FFB315A7E687765B8A68C6E91252DEEF8EF_400_400.PNG "星期")