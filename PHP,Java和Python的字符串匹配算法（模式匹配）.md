数据结构和算法书一般会介绍KMP算法，其实KMP算法的性能并不好。查看Java源码和PHP源码后，发现他们使用了如下的匹配算法。

各语言使用的匹配算法
- Java使用的是朴素匹配。
    - 为什么java String.contains 没有使用类似KMP字符串匹配算法进行优化？ https://www.zhihu.com/question/27852656
- PHP使用的是首尾匹配法。php7开始，源字符串（haystack）大于等于1024并且目标字符串（needle）大于等于9，会用Sunday算法。
    - zend_memnstr。用于字符串匹配。首尾匹配算法。在Zend/zend_operators.h。
    - zend_memnstr_ex。用于字符串匹配。 Sunday算法。在Zend/zend_operators.c
- Python使用简化的Boyer-Moore算法，并且结合了Horspool和Sunday的一些思路。源码在Objects/stringlib/fastsearch.h。算法介绍 http://effbot.org/zone/stringlib.htm
