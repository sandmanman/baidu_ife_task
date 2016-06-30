# 前端技术学习记录

[Baidu IFE](https://github.com/baidu-ife/ife)


### 任务中提到及遇见的问题记录

##### JavaScript 的性能优化-加载和执行

[JavaScript 的性能优化：加载和执行](http://www.ibm.com/developerworks/cn/web/1308_caiys_jsload/index.html)

- 优化 JavaScript 的首要规则：将脚本放在底部
- 尽可能的合并压缩文件，减少性能消耗
- 无阻塞的脚本：在页面加载完成后才加载 JavaScript 代码，这就意味着在 window 对象的 onload事件触发后再下载脚本
- 动态加载：

  通过检查 readyState 状态加载 JavaScript 脚本
  ```javascript
  // JavaScript 文件的动态加载函数封装
  function loadScript(url, callback){
    var script = document.createElement ("script")
    script.type = "text/javascript";
    if (script.readyState){ //IE
        script.onreadystatechange = function(){
            if (script.readyState == "loaded" || script.readyState == "complete"){
                script.onreadystatechange = null;
                callback();
            }
        };
    } else { //Others
        script.onload = function(){
            callback();
        };
    }
    script.src = url;
    document.getElementsByTagName("head")[0].appendChild(script);
  }
  // 调用
  loadScript("script.js", function(){
    console.log("File is loaded!");
  });
  // 加载多个
  loadScript("script1.js", function(){
    loadScript("script2.js", function(){
        loadScript("script3.js", function(){
            console.log("All files are loaded!");
        });
    });
  });
  ```


##### 数据类型

- 原始类型：
  - Boolean 布尔类型
  - Null Null 类型只有一个值： null
  - Undefined 一个没有被赋值的变量会有个默认值 undefined
  - Number 数字类型
  - String 字符串类型
- Object 对象

- 判断数组

  ```javascript
  // 判断arr是否为一个数组，返回一个bool值
  function isArray(arr) {
     return Object.prototype.toString.call(arr) === '[object Array]';
  }
  ```
  ECMAScript5，判断数组类型可以直接使用 <code>Array.isArray()</code>

- 判断函数

  ```javascript
  // 判断fn是否为一个函数，返回bool值
  function isFn(fn) {
    return typeof(fn) === 'function';
  }
  ```

----------

##### 了解值类型和引用类型的区别，了解各种对象的读取、遍历方式，并在util.js中实现以下方法：

**完全不懂什么递归啊**

```javascript
// 使用递归来实现一个深度克隆，可以复制一个目标对象，返回一个完整拷贝
// 被复制的对象类型会被限制为数字、字符串、布尔、日期、数组、Object对象。不会包含函数、正则对象等
function cloneObject(src) {
  var reslut;
  if ( typeof(src) === 'object' ) {
      // 对象为日期对象时也直接赋值
      if ( Object.prototype.toString.call(src) === '[object Date]' ) {
          reslut = src;
      } else {
        // 判断对象的类型是array还是object
        reslut = ( Object.prototype.toString.call(src) === '[object Array]' ) ? [] : {};
        for (var i in src){
            if (src.hasOwnProperty(i)) { // 排除继承属性
                if ( typeof src[i] === 'object' ) {
                    reslut[i] = cloneObject(src[i]); // 递归赋值
                } else {
                    reslut[i] = src[i]; //直接赋值
                }
            }
        }
      }
  } else {
    // 原始类型直接赋值
    reslut = src;
  }
  return reslut;
}
// 代码来源https://segmentfault.com/a/1190000004205425
```

-----------------

##### 学习数组、字符串、数字等相关方法

- 数组去重复

  循环遍历对象数据，使用indexOf筛选是否已包含某元素，使用push添加到新的数组。indexOf() IE9下版本不支持，需考虑兼容问题，参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)

  ```javascript
  // 对数组进行去重操作，并返回去重后的数据
  function uniqArray(arr) {
      var reslut = [];
      for (var i = 0, arrLength = arr.length; i < arrLength; i++) {
          if ( reslut.indexOf(arr[i]) === -1 ) { // 查找是否已经包含该元素
              reslut.push(arr[i]); // 添加到新的数组
          }
      }
      return reslut;
  }
  ```

- 字符串去除首尾空白或Tab

  [正则表达式了解](https://github.com/sandmanman/code-life/blob/master/javascript_node/RegExp.md)

  ```javascript
  // 实现一个简单的trim函数，用于去除一个字符串，头部和尾部的空白字符
  // 假定空白字符只有半角空格、Tab
  // 练习通过循环，以及字符串的一些基本方法，分别扫描字符串str头部和尾部是否有连续的空白字符，并且删掉他们，最后返回一个完成去除的字符串
  function simpleTrim(str) {
      var result = '';
      // 从字符串首循环查找
      for (var i = 0, str_l = str.length; i < str_l; i++) {
          if ( str[i] != ' ' && str[i] != '\t' ) {
              break;
          }
      }
      // 从字符串尾循环查找
      for (var j = str.length -1; j >=0; j--) {
          if ( str[i] != ' ' && str[i] != '\t' ) {
              break;
          }
      }
      reslut = str.slice(i, j+1);
      // slice(beginSlice[, endSlice])方法提取字符串中的一部分，并返回这个新的字符串
      // beginSlice从0开始，endSlice如果为负则从尾及-1开始

      return reslut;
  }
  // 正则实现trim函数
  function trim(str) {
    var result = '';
    result = str.replace(/^\s+|\s+$/g, '');
    // replace() 方法使用一个替换值（replacement）替换掉一个匹配模式（pattern）在原字符串中某些或所有的匹配项，并返回替换后的字符串
    return result;
  }
  // 使用示例
  var str = '   hi!  ';
  str = trim(str);
  console.log(str); // 'hi!'
  ```
