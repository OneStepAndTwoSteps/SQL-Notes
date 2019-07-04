## SQL聚集函数

__SQL聚集函数包括五类，分别是：__

    COUNT()         总行数          忽略为NULL的字段
    MAX()           最大值          忽略为NULL的字段
    MIN()           最小值          忽略为NULL的字段
    SUM()           求和            忽略为NULL的字段
    AVG()           平均值          忽略为NULL的字段


### SQL聚集函数-举例说明：

当我们要查询年龄大于20岁的学生人数，这里 __注意如果某个人age这个字段为NULL，则不会被COUNT函数统计计数进去__
    
    SQL：SELECT COUNT(*) FROM students WHERE age > 20

当我们要查询学生中，男生年龄最大的年龄是多少

    SQL：SELECT MAX(age) FROM students WHERE gender = '男'

如果我们要统计学生中男生的人数，和年纪最大、最小、平均、总和

    SQL: SELECT COUNT(*), AVG(age), MAX(age), MIN(age), SUM(age) FROM students WHERE gender = '男'


需要说明的是 AVG、MAX、MIN 等聚集函数也会自动忽略值为 NULL 的数据行，MAX 和 MIN 函数也可以用于字符串类型数据的统计，如果是英文字母，则按照 A—Z 的顺序排列，越往后，数值越大。__如果是汉字则按照全拼拼音进行排列__。比如：

    SQL：SELECT MIN(CONVERT(name USING gbk)), MAX(CONVERT(name USING gbk)) FROM students


__这里说明一下，我们通过使用CONVERT(name USING gbk)将 name(汉字) 字段统一转化为 gbk 类型，然后再使用 MIN 和 MAX 取最小值和最大值。__

我们也可以对数据行中不同的取值进行聚集，先用 DISTINCT 函数取不同的数据，然后再使用聚集函数。比如我们想要查询不同身高的同学的数量是多少。

    SQL: SELECT COUNT(DISTINCT height) FROM students
    
假如我们想要统计不同身高值的英雄的平均身高，保留小数点后两位。首先需要取不同身高值，即DISTINCT height，然后针对它们取平均值，即AVG(DISTINCT height)，最后再针对这个值保留小数点两位，也就是ROUND(AVG(DISTINCT height), 2)。

    SQL: SELECT ROUND(AVG(DISTINCT height), 2) FROM students

### 对数据进行分组，进行聚集统计：

__我们在做统计的时候，可能需要先对数据按照不同的数值进行分组，然后对这些分好的组进行聚集统计。对数据进行分组，需要使用 GROUP BY 子句。__

比如我们想按照学生的身高进行分组，并统计每组的学生人数。 __注意:如果我们的某个学生的height是NULL，也会被GROUP BY 拉出来__
    
    SQL: SELECT COUNT(*), height FROM students GROUP BY height


运行结果：(虚构)

    COUNT(*)    height

    10            175
    1             169
    14            165
    3             160
    2             NULL




