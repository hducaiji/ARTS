# WEEK01
## 算法题(Algorithm)
0x69 Sqrt(x) \#二分查找
https://leetcode-cn.com/problems/sqrtx/

### 题目
实现 int sqrt(int x) 函数。
计算并返回x的平方根，其中x 是非负整数。
由于返回类型是整数，结果只保留整数的部分，小数部分将被舍去。

### 已知
* x>=0
* 0<= 根号x <=x 

### tips
* Try exploring all integers.
* Use the sorted property of integers to reduced the search space.

### 实现一：二分法
```python
# 速度击败95%
import math
class Solution:
    def mySqrt(self, x: int) -> int:
		  # 最终结果要求向下取整
        max = x
        min = 0
        while max != min: #0
            #mid = math.ceil((max+min)/2) #二分时向上取整
			  mid = (max+min+1)>>1 #用这个在整型二分时向上取整十分好用，不用math库函数
            if mid*mid <= x:
                min = mid
            else:
                max = mid-1
        return max
```
二分法的关键在于：
* 若最终结果要求向下取整时，mid二分时向上取整，`if mid<=最终结果 min = mid`，`其它 max=mid-1`（表示它肯定不是，直接舍去）
* 若最终结果要求向上取整时，mid二分时向下取整，`if mid<最终结果 min = mid+1`，`其它 max=mid`
* 若最终结果本身一定是整数，上面两种方法皆可。
(其实就在解决最后min和max差1时如何处理，mid一定要跳到与结果取整方向相反的能动量上并被加减一（mid二分向何处取整以及`+/-1`的位置解决了这个问题），且mid=最终结果时不能被剔除（if语句=号位置解决了这个问题））

**PS：**
* 除以二并向下取整：`(a+b)//2` `(a+b)>>1` `math.floor((a+b)/2)`
* 除以二并向上取整：`math.ceil((a+b)/2)` ceil-天花板 `(a+b)//2` 加数为整形时可用
* 四舍五入：`math.round()`

### 实现二：牛顿迭代法
 [https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division](https://en.wikipedia.org/wiki/Integer_square_root#Using_only_integer_division) 
它是 牛顿在17世纪提出的一种在 实数域和 复数域上近似求解方程的方法。它其实就是二分法，是二分法的更简化/理论化版本，运算速度以及内存占用应该更优化，不过唯一不足的是，如果结果为0时会在除法处出错。
```python
class Solution:
    def mySqrt(self, x):
        if x <= 1:
            return x
        r = x
        #while r > x / r: r = (r + x / r) // 2
		  while r*r > x: r = (r + x / r) // 2
        return int(r)
```
另一种理解：基本不等式`(a+b)/2 >=√ab` 推导自 `(a-b)^2 >= 0`，注意 a>0 且 b>0
所以 `(r+x/r)/2 >=√x`
所以 `(r+x/r)/2(向下取整) <= √x`时，即为结果。如果要向上取整，结果+1即可或者使用如下代码：
```python
import math
class Solution:
    def mySqrt(self, x):
        if x <= 1:
            return x
        r = x
		  while r*r > x: r = math.ceil((r+x/r)/2)
        return int(r)
```

### 投机取巧 / 包装函数
`return int(x ** 0.5)`
`math.sqrt()`
<br/>

## 技术技巧(Tip)
今天分享关于PHP连接MySQL时预定义变量的方法——perpared statement，有效防止SQL注入。（Web选手泪流满面）
具体见如下向MySQL插入数据的例子（使用说明见注释）：
```php
//面向对象风格
//step1: prepare
if ($stmt = mysqli_prepare($db, "INSERT INTO students VALUES (?, ?, ?, ?, ?, ?)")) {// 创建一个预编译 SQL 语句 
    // step2: 对于参数占位符进行参数值绑定。s代表字符串，i带变整数，b代表blob类型
    mysqli_stmt_bind_param($stmt, "ssssss", $id_card, $name, $school, $school_id, $major, $phone);
    // step3: 执行插入
    mysqli_stmt_execute($stmt);
    // step4: 判断是否插入成功，这里使用affected_rows
    if (mysqli_stmt_affected_rows($stmt)>0) { ... } 
    else {...}
    // step5: 关闭语句句柄 
    mysqli_stmt_close($stmt);
}	
```
值得注意的是，如果要使用num_rows等函数，需要先store_result：
```php
//面向对象风格
$query = "select * from user where email=? and password=?";
if ($stmt = mysqli_prepare($conn, $query)) {
    mysqli_stmt_bind_param($stmt, "ss", $username, $password);
    mysqli_stmt_execute($stmt);
    mysqli_stmt_store_result($stmt); //***
    if (mysqli_stmt_num_rows($stmt) > 0) { //***
        ...
    } else {...}
	  mysqli_stmt_close($stmt);
    exit();
}
```
<br/>

## 分享(Share)
分享一篇我写过的关于DVWA中XSS篇章的分析，花了一些心力，所以这里分享出来作为XSS开篇:
[DVWA-XSS by fseeeye](https://hducaiji.github.io/2018/05/04/lowXSS1/)


