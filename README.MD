#### 1、this
```js
var length = 10;
function fn() {
  console.log(this.length);
}

var obj = {
  length: 5,
  method: function(fn) {
    fn();
    arguments[0]();
  }
};

obj.method(fn, 1);

```
输出：10 2

　　第一次输出10应该没有问题。我们知道取对象属于除了点操作符还可以用中括号，所以第二次执行时相当于arguments调用方法，this指向arguments，而这里传了两个参数，故输出arguments长度为2。
　　
#### 2、var和函数的提前声明
```js
function fn(a) {
  console.log(a);
  var a = 2;
  function a() {}
  console.log(a);
}

fn(1);
```
输出：function  a() {}      2

　　[我们知道var和function是会提前声明的，而且function是优先于var声明的（如果同时存在的话），所以提前声明后输出的a是个function，然后代码往下执行a进行重新赋值了，故第二次输出是2](http://www.bootcss.com/article/variable-and-function-hoisting-in-javascript/)。


```js
function xyz(){
   console.log(1);
}

var a = xyz;

xyz = function(){
   console.log(2);
}
a();
```
输出：1

两次XYZ指向了不同的对象。a就近原则，console（1);

```js
console.log(typeof fn);
function fn() {};
var fn;
```
答案：function

　　因为函数声明优于变量声明。我们知道在代码逐行执行前，函数声明和变量声明会提前进行，而函数声明又会优于变量声明，这里的优于可以理解为晚于变量声明后，如果函数名和变量名相同，函数声明就能覆盖变量声明。所以以上代码将函数声明和变量声明调换顺序还是一样结果。

```js
(function hire(){
  function hire(){ return 'ele'; }
  return hire();
  function hire(){ return 'me'; }
})();

```
答案：me

```js
var a = {n:1};
var b = a;
a.x = a = {n:2};

alert(a.x);// --> undefined
alert(b.x);// --> [object Object]
```
赋值是从右到左的，.运算优先于=赋值运算，
`a.x = a = {n:2};`
因此此处赋值可理解为

1. 声明a对象中的x属性，用于赋值，此时b指向a，同时拥有未赋值的x属性
2. 对a对象赋值，此时变量名a改变指针，指向到对象{n:2}
3. 对步骤1中x属性，即b指向对象的x属性赋值

一开始js已经先计算了a.x，便已经解析了这个a.x是对象A的x，所以在同一条公式的情况下再回来给a.x赋值，也不会说重新解析这个a.x为对象B的x。
所以 a.x=a 应理解为对象A的属性x指向了对象B。

赋值结果：

```js
a => {n: 2}
b => {n: 1, x: {n: 2 } }  
```
![image](https://user-images.githubusercontent.com/9944527/28561868-b2c7deea-7152-11e7-9e1d-93aa35e1fb28.png)

[javascript 连等赋值问题](https://segmentfault.com/q/1010000002637728)
[详细解释](http://www.cnblogs.com/huaan011/p/4381703.html)
#### 3、给基本类型数据添加属性，不报错，但取值时是undefined
```js
var a = 10;
a.pro = 10;
console.log(a.pro + a);

var s = 'hello';
s.pro = 'world';
console.log(s.pro + s);
```
答案：NaN undefinedhello

　　给基本类型数据加属性不报错，但是引用的话返回undefined，10+undefined返回NaN，而undefined和string相加时转变成了字符串。
　　
#### 4、判断一个字符串中出现次数最多的字符，并统计次数
* 正则方式：

```js
var s = 'aaabbbcccaaabbbaaabbbbbbbbbb';
var a = s.split('');
a.sort();
s = a.join('');
var pattern = /(\w)\1*/g;      // \1 匹配和第一个括弧内容相同的字符
var ans = s.match(pattern);
ans.sort(function(a, b) {
  return a.length < b.length;
});                              // 两个数比较大小的正确的方法，sort比较不出大小
console.log(ans[0][0] + ': ' + ans[0].length);

```

* hash table表

```js
var s = 'aaabbbcccaaabbbaaa';
var obj = {};
var maxn = -1;
var letter;
for(var i = 0; i < s.length; i++) {
  if(obj[s[i]]) {
    obj[s[i]]++;
    if(obj[s[i]] > maxn) {
      maxn = obj[s[i]];
      letter = s[i];
    }
  } else {
    obj[s[i]] = 1;
    if(obj[s[i]] > maxn) {
      maxn = obj[s[i]];
      letter = s[i];
    }
  }
}

alert(letter + ': ' + maxn);
```
#### 5、去除数组中重复的部分

`es6`写法:

```js
  var arr = [1, 3, 6, 2, 3, 5, 8, 1, 9];

  function unique (arr) {
    return Array.from(new Set(arr))
  }

```
或者

```js
  var arr = [1, 3, 6, 2, 3, 5, 8, 1, 9];

  function unique (arr) {
    return [...new Set(arr)]
  }

```
Set 写法：

```js
  var arr = [1, 3, 6, 2, 3, 5, 8, 1, 9];

  function unique(arr){
    var uniqeArray = [];

    new Set(arr).forEach(function(item) {
       uniqeArray.push(item)
    });
    return uniqeArray;
  }

```
ps: 集合（Set）对象允许你存储任意类型的唯一值（不能重复），无论它是原始值或者是对象引用。

Map 方法：

```js
  function unique (arr) {
    var seen = new Map()
    return arr.filter((a) => !seen.has(a) && seen.set(a, 1))
  }
```
普通的写法：
```js
function unique(arr){
    var uniqeArray = [];
    for(var i = 0;i < arr.length; i++){
        if(uniqeArray.indexOf(arr[i]) == -1) uniqeArray.push(arr[i]);
    }
    return uniqeArray;
}

```

可以利用，object的 key 不能重复的特点。

[数组去重](https://blog.csdn.net/qxuewei/article/details/52837083)

#### 6、找出数字数组中最大的元素（使用Math.max函数）
```js
var a = [1, 2, 3, 6, 5, 4];

// ES5的 写法
var ans = Math.max.apply(null, a);

// ES6 的写法
Math.max(...a)

console.log(ans);  // 6

```
这题很巧妙地用了apply，如果不是数组，是很多数字求最大值，我们知道可以这样：

```js
var ans = Math.max(1, 2, 3, 4, 5, 6);
console.log(ans); // 6
```



#### 7、实现如下语法的功能：var a = add(2)(3)(4); //9

```js

```


#### 8、
```js
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));`

```
答案：a defined? false
     b defined? true

IIFE 中的赋值过程其实是（赋值过程从右到左）a 是局部变量，b 是全局变量

#### 9、setTimeout
```js
for (var i = 0; i < 5; i++) {
    setTimeout(function() {
        console.log(i);
    }, 1000 * i)
}
//以上代码输入什么？

```
以上代码输出5个5，并且每隔1s输出一个，一共用时4s。

```js
for (var i = 0; i < 5; i++) {
    let timer = setTimeout(function() {}, 1000 * i)
    console.log(timer);
}

```
控制台输出了5个不同的定时器ID

```js
for (var i = 0; i < 5; i++) {
    setTimeout((function() {
        console.log(i);
    })(), 1000 * i);
}

```
立即输出1, 2, 3, 4。因为是setTimeout的第一个参数是函数或者字符串，而此时函数又立即执行了。因此，此时的定时器无效了，直接输出1, 2, 3, 4。上面的代码等同于如下

```js
for (var i = 0; i < 5; i++) {
    (function() {
        console.log(i); //0, 1, 2, 3, 4
    })();
}
```

```js
console.log(1);

setTimeout(function() {
  console.log(2);
}, 0);

$.ajax({
    url: "../index.php",  //假如上一级目录下有php文件，并且echo '3';
    data: 'GET',
    success: function(data) {
        console.log(data);
    },      
})

new Promise(function(resolve, reject) {
    console.log(4);
    resolve();
}).then(function() {
    console.log(5);
}).then(function() {
    console.log(6);
})
console.log(7);
```
此时的输出顺序是1, 4, 7, 5, 6, 3, 2。

[js 数组去重](https://www.jianshu.com/p/131ca13e7f28)

#### 10、写一个函数，要求其可以将#fff,转成rgb(225,225,225)
```js
var reg = /^#(\w{3}|\w{6})$/;

function colorRgb(color){
	var sColor = color.toLowerCase();
	if(sColor && reg.test(sColor)){
		if(sColor.length === 4){
			var sColorNew = "#";
			for(var i=1; i<4; i+=1){
				sColorNew += sColor.slice(i,i+1).concat(sColor.slice(i,i+1));	
			}
			sColor = sColorNew;
		}
		//处理六位的颜色值
		var sColorChange = [];
		for(var i=1; i<7; i+=2){
			sColorChange.push(parseInt("0x"+sColor.slice(i,i+2)));	
		}
		return "RGB(" + sColorChange.join(",") + ")";
	}else{
		return sColor;	
	}
};
alert(colorRgb('#efefef'))

```
JavaScript颜色转换的核心就是进制间的转换。RGB格式其实就是十进制表示法，所以，十六进制颜色与RGB颜色的转换就是十六进制与十进制之间的转换。

#### 11.请问下列函数输出什么？
```js
function foo() {
   console.log( this.a )
}
var obj = {
  a: 2,
  foo: foo
}

var a = 'oops, global'
function doFoo(fn) {
  console.log( this.a )
}
doFoo(foo)
setTimeout(obj.foo, 100)
```
这是《You Dont Know JavaScript》上册的一个例子，我们知道 javascript 的作用域是词法作用域，与运行时无关，但是this则例外。它的值取决于调用它的函数的位置。`setTimeout(obj.foo, 100)`实际上是（伪代码）
```js
function setTimeout () {
  obj.foo()
}
window.setTimeout ()
```
而`doFoo(fn)` 实际的调用栈是：
```js
function doFoo(fn) {
  foo() // 当前调用栈是 doFoo，this代表window
}
```
再看下面的例子：
```js
function foo() {
  console.log(this.a)
}
var obj = {
  a: 2
}
var bar = function() {
  foo.call(obj)
}
bar() 
setTimeout( bar, 100)
bar.call(window)
```
上面的例子输出的都是2，由此可见，用 call、apply 修改过的this，是无法的。

## 12、有一个多维数组，需要转换成一维数组。注意：题目的答题时间是5-10分钟。如果超过10分钟没有完成，请停止答题，提交表单。

```
答题代码：
var a = [1, [2, [3, 4]], 5, [6]];

var flatten = function(array) {
// 你需要补充的代码
}

console.log(flatten(a)); // 输出 [1, 2, 3, 4, 5, 6]
```

答：

```js
var a = [1, [2, [3, 4]], 5, [6]];
var f = function(arr, result) {
    arr.forEach(function(item) {
        if (item instanceof Array) {
            f(item, result);
        } else {
            result.push(item);
        }
    })
    return result;
}
var flatten = function(arr) {
    return f(arr, []);
}
console.log(flatten(a));
```
## 13、

```
请把这个重复的元素找出来，要求所有的元素只能访问一次，空间复杂度 O(1)。

如果无法理解O(1)的空间复杂度，做一个简单的解释：
    只能使用常量个空间，使用的空间不能和数组的长度有关系，比如如果使用另外一个长度和给定数组长度一样的数组，则空间复杂度是 O(n)。

注意：题目的答题时间是5-10分钟。如果超过10分钟没有完成，请停止答题，提交表单。

使用下面的代码开始答题：

// 这个方法生成了1001个元素的数组，只有一个元素是重复的。
var getArray = function() {
    var count = 1000;
    var array = [];

    for (var i = 1; i <= count; i++) {
        array.push(i);
    }

    var special = Math.floor(Math.random() * (count + 1));
    array.push(special);
    console.log('重复数字为：' + special);

    return array.sort(function() {
        return Math.random() - 0.5;
    });
};

var findNumber = function(array) {
    //你要实现的代码写在这里。
};

console.log('找到的重复数字为：' + findNumber(getArray()));

```

答： 
```js
var findNumber = function(array) {
   var special = 0;
   array.sort();  // 先排序，相同的就在一起了
   array.sort((next, pre) => {	
	if(next == pre) {
		special = next;
	}
	return 1
    })
    return special;
};

```


## 14、

```
有一个数组，里面只存在 * 和 字母，比如 ['*', 'd', 'c', '*', 'e', '*', 'a', '*']。
现在需要把这个数组中的所有星号移动到左边，所有的字母移动到右边，所有字母的顺序不能改变。要求时间复杂度是 O(n)，空间复杂度是 O(1)。

如果你没有思路同时满足O(n)和O(1)，请满足空间复杂度O(1)的条件，只满足时间复杂度  O(n) 不得分。

如果无法理解时间复杂度和空间复杂度，做一个简单的解释：
1. O(n)的意思是只能对数组做单层 for 循环，可以进行多次。
2. O(1)的意思是只能使用常量个空间，使用的空间不能和数组的长度有关系，比如如果使用另外一个长度和给定数组长度一样的数组，则空间复杂度是 O(n)。

注意：题目的答题时间是5-10分钟。如果超过10分钟没有完成，请停止答题，提交表单。

使用下面的代码开始答题：
var a = ['*', 'd', 'c', '*', 'e', '*', 'a', '*'];

var sort = function(array) {
//请补充此处代码
};

console.log(sort(a));// 应该返回['*', '*', '*' ,'*', 'd', 'c', 'e', 'a']
```
答：

```js

var a = ['*', 'd', 'c', '*', 'e', '*', 'a', '*']

var sort = function(array) {
	array.sort((pre, next)=> {
		if(pre == "*") {
			return -1;
		}
	})
}
sort(a);

```
补充：

sort使用方法：

```js
var arr=[];
arr.sort();//默认排序
arr.sort(comparefn(a,b));//自定义排序比较方法

```js

当没有参数传入的时候，其排序顺序默认为，将待排序数据转换为字符串，并按照Unicode序列排序；当然，比较函数可以自定义，自定义排序函数需要返回值，其返回值为-1，0，1，分别表示a<b, a=b, a>b.

当数组长度小于等于10的时候，采用插入排序，大于10的时候，采用快排。

关于时间复杂度的问题，可以查看https://segmentfault.com/a/1190000003074973

