# 第五章 第 19 节 前端基础-应用 5

> 原文：[`www.nowcoder.com/tutorial/10072/fcfce977ba7c4b969d89e07cdaf99476`](https://www.nowcoder.com/tutorial/10072/fcfce977ba7c4b969d89e07cdaf99476)

#### 8.18 **给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度**

**参考答案：**

思路分析：

对字符串进行遍历，使用`String.prototype.indexOf()`实时获取遍历过程中的无重复子串并存放于`str`，并保存当前状态最长无重复子串的长度为`res`，当遍历结束时，res 的值即为无重复字符的最长子串的长度。

代码示例：

```cpp
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    var res = 0; // 用于存放当前最长无重复子串的长度
    var str = ""; // 用于存放无重复子串
    var len = s.length;
    for(var i = 0; i < len; i++) {
      var char = s.charAt(i);
      var index = str.indexOf(char);
      if(index === -1) {
        str += char;
        res = res < str.length ? str.length : res;
      } else {
        str = str.substr(index + 1) + char;
      }
    }
    return res; 
};
```

#### 8.19 去掉字符串前后的空格

**参考答案：**

第五种方法在处理长字符串时效率最高

第一种：循环检查替换

```cpp
//供使用者调用  
function trim(s){  
  return trimRight(trimLeft(s));  
}  
//去掉左边的空白  
function trimLeft(s){  
    if(s == null) {  
      return "";  
    }  
  var whitespace = new String(" \t\n\r");  
  var str = new String(s);  
  if (whitespace.indexOf(str.charAt(0)) != -1) {  
    var j=0, i = str.length;  
    while (j < i && whitespace.indexOf(str.charAt(j)) != -1){  
      j++;  
    }  
    str = str.substring(j, i);  
  }  
  return str;  
}  
//去掉右边的空白 www.2cto.com 
function trimRight(s){  
  if(s == null) return "";  
  var whitespace = new String(" \t\n\r");  
  var str = new String(s);  
  if (whitespace.indexOf(str.charAt(str.length-1)) != -1){  
    var i = str.length - 1;  
    while (i >= 0 && whitespace.indexOf(str.charAt(i)) != -1){  
      i--;  
    }  
    str = str.substring(0, i+1);  
  }  
  return str;  
} 
```

第二种：正则替换

```cpp
<SCRIPT LANGUAGE="JavaScript">  
String.prototype.Trim = function()  {  
  return this.replace(/(^\s*)|(\s*$)/g, "");  
}  
String.prototype.LTrim = function()  {  
  return this.replace(/(^\s*)/g, "");  
}  
String.prototype.RTrim = function()  {  
  return this.replace(/(\s*$)/g, "");  
}  
</SCRIPT> 
```

第三种：使用 jquery

```cpp
$.trim(str) 
//jquery 内部实现为：
function trim(str){   
    return str.replace(/^(\s|\u00A0)+/,'').replace(/(\s|\u00A0)+$/,'');   
}   
```

第四种：使用 motools

```cpp
function trim(str){   
    return str.replace(/^(\s|\xA0)+|(\s|\xA0)+$/g, '');   
}  
```

第五种：裁剪字符串方式

```cpp
function trim(str){   
    str = str.replace(/^(\s|\u00A0)+/,'');   
    for(var i=str.length-1; i>=0; i--){   
        if(/\S/.test(str.charAt(i))){   
            str = str.substring(0, i+1);   
            break;   
        }   
    }   
    return str;   
}  
```

#### 8.20 "判断输出 console.log(0 == [])console.log([1] == [1])"

**参考答案：**

```cpp
console.log([]==[]);  // false
console.log([]== 0);  // true
```

解析：

原始值的比较是值的比较：
它们的值相等时它们就相等（==）
对象和原始值不同，对象的比较并非值的比较,而是引用的比较：
即使两个对象包含同样的属性及相同的值，它们也是不相等的
即使两个数组各个索引元素完全相等，它们也是不相等的,所以[]!=[]

​ []==0,是数组进行了隐士转换，空数组会转换成数字 0，所以相等

#### 8.21 三数之和

**参考答案：**

题目描述

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

```cpp
//例如, 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
//满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

解答

这题我们才用排序+双指针的思路来做，遍历排序后的数组，定义指针 l 和 r,分别从当前遍历元素的下一个元素和数组的最后一个元素往中间靠拢，计算结果跟目标对比。

```cpp
var threeSum = function(nums) {
    if(nums.length < 3){
        return [];
    }

    let res = [];
    // 排序
    nums.sort((a, b) => a - b);
    for(let i = 0; i < nums.length; i++){
        if(i > 0 && nums[i] == nums[i-1]){
            // 去重
            continue;
        }
        if(nums[i] > 0){
            // 若当前元素大于 0，则三元素相加之后必定大于 0
            break;
        }
        // l 为左下标，r 为右下标
        let l = i + 1; r = nums.length - 1;
        while(l<r){
            let sum = nums[i] + nums[l] + nums[r];
            if(sum == 0){
                res.push([nums[i], nums[l], nums[r]]);
                while(l < r && nums[l] == nums[l+1]){
                    l++
                }
                while(l < r && nums[r] == nums[r-1]){
                    r--;
                }
                l++;
                r--;
            }
            else if(sum < 0){
                l++;
            }
            else if(sum > 0){
                r--;
            }
        }
    }

    return res;
};
```