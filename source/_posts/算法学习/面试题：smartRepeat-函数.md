---
title: 面试题：smartRepeat-函数
date: 2021-09-02 21:12:40
category: algorithm
---
编写“智能重复”smartRepeat函数，实现：

将 3[abc] 变为abcabcabc
将 3[2[a]2[b]] 变为 aabbaabbaabb
将 2[1[a]3[b]2[3[c]4[d]]] 变为abbbcccddddcccddddabbbcccddddcccdddd

不用考虑输入字符串是非法的情况，比如：

2[a3[b]]是错误的，应该补一个1，即2[1[a]3[b]]
[abc]是错误的，应该补一个1，即1[abc]

```
/**
 * @param {string} repeatStr
 */
function smartRepeat(repeatStr){
  let numArr=[],//存放数字
      strArr=[];//存放字母
  let str = '';
  for(let i=0;i<repeatStr.length-1;i++){
    console.log('---',numArr,strArr,str)
    let temp = repeatStr.charAt(i);
    if(parseInt(temp)>0){//如果是数字
      numArr.push(parseInt(temp));
    }else{
      if(temp == '['){//发现要循环的起始
        strArr.push('')
      }else if(temp == ']'){//当前字母结束
        // 如果这个字符是]，那么就①将numArr弹栈，②strArr弹栈，③把字符串栈的新栈顶的元素重复刚刚弹出的那个字符串指定次数拼接到新栈顶上。
        let t = numArr.pop();
        let m = strArr.pop();
        strArr[strArr.length-1] += m.repeat(t);
      }else{
        // 如果这个字符是字母，那么此时就把栈顶这项改为这个字母
        strArr[strArr.length-1] += temp;
      }
    } 
  }
  str = strArr[0].repeat(numArr[0])
  return str;
}
```
