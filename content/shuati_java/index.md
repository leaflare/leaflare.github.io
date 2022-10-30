+++
title = "Java记录"
date = 2021-02-10 17:21:06
slug = "202102101721"

[taxonomies]
tags = ["cram" ]
categories = ["cram"]

+++

<!-- more -->

## 数组相关

### Arrays.sort自定义比较器

三种写法，前两种如果比较值超过整形的范围就无法比较了。
三种写法都是表示对二维数组的行从小到大进行比较，其中intervals是二维数组的数组名。

```java
Arrays.sort(intervals, (v1, v2) -> v1[0] - v2[0]);    
    
Arrays.sort(intervals, Comparator.comparingInt(o -> o[0]));
    
Arrays.sort(intervals,new Comparator<int[]>(){
        public int compare(int[] o1,int[] o2){
            if(o1[0] > o2[0]){
                return 1;
            }
            else if(o1[0]==o2[0]){
                return 0;
            }
            else
            return -1;
        }
    });

```

对于compare或者compareTo函数返回值，1表示两个比较的数字位置互换，0和-1则是位置不发生变化。

public int compare(Integer a,Integer b){ return a-b；｝如果a大于b，返回正数，表示要将a和b的位置互换，因此小数在前，大数在后，这就成了升序排列，a小于b也是同理
