#### 1、this
```
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
```
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


```
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

```
console.log(typeof fn);
function fn() {};
var fn;
```
答案：function

　　因为函数声明优于变量声明。我们知道在代码逐行执行前，函数声明和变量声明会提前进行，而函数声明又会优于变量声明，这里的优于可以理解为晚于变量声明后，如果函数名和变量名相同，函数声明就能覆盖变量声明。所以以上代码将函数声明和变量声明调换顺序还是一样结果。

```
(function hire(){
  function hire(){ return 'ele'; }
  return hire();
  function hire(){ return 'me'; }
})();

```
答案：me

```
var a = {n:1};
var b = a;
a.x = a = {n:2};

alert(a.x);// --> undefined
alert(b.x);// --> [object Object]
```

[javascript 连等赋值问题](https://segmentfault.com/q/1010000002637728)

#### 3、给基本类型数据添加属性，不报错，但取值时是undefined
```
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

```
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

```
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

```
  var arr = [1, 3, 6, 2, 3, 5, 8, 1, 9];

  function unique (arr) {
    return Array.from(new Set(arr))
  }

```
Set 写法：

```
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

```
  function unique (arr) {
    var seen = new Map()
    return arr.filter((a) => !seen.has(a) && seen.set(a, 1))
  }
```

#### 6、找出数字数组中最大的元素（使用Math.max函数）
```
var a = [1, 2, 3, 6, 5, 4];
var ans = Math.max.apply(null, a);
console.log(ans);  // 6

```
这题很巧妙地用了apply，如果不是数组，是很多数字求最大值，我们知道可以这样：

```
var ans = Math.max(1, 2, 3, 4, 5, 6);
console.log(ans); // 6
```



#### 7、实现如下语法的功能：var a = add(2)(3)(4); //9

```

```


#### 8、
```
(function(){
  var a = b = 3;
})();

console.log("a defined? " + (typeof a !== 'undefined'));
console.log("b defined? " + (typeof b !== 'undefined'));`

```
答案：a defined? false
     b defined? true

IIFE 中的赋值过程其实是（赋值过程从右到左）a 是局部变量，b 是全局变量

#### 9、写一个函数，要求其可以将#fff,转成rgb(225,225,225)
```
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
