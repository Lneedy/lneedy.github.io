# 街头地摊故事集之 apply 与 call

这位少侠，我看你骨骼惊奇器宇非凡，这里有一本《apply大法》送你，日后稍加修炼便可突破前端桎梏,遨游宇宙!
```javascript
/*apply()方法*/
function.apply(thisObj[, argArray])

/*call()方法*/
function.call(thisObj[, arg1[, arg2[, [,...argN]]]]);
```
#### 定义
`apply`：调用一个对象的一个方法，用另一个对象替换当前对象。例如：`小蓝`.apply(`小绿`, arguments);即 `小绿` 应用 `小蓝` 的方法。

`call`：调用一个对象的一个方法，用另一个对象替换当前对象。例如：B.call(A, args1,args2);即A对象调用B对象的方法。

#### 其实他们两个货是一样的, 只是有一点点区别, 在于接受的参数不同而已啦!!! 
```javascript
//用谁看个人喜欢谁啦, 萝卜青菜各有所爱
apply 是接受 this + 参数数组 既 : ( this, [ blue, big, $50 ] )
call  是接受 this + 参数列表 既 : ( this, blue, big, $50 )
```
#### 用法解析
一杯清茶下肚,来听小弟我讲故事,故事是这样开始的:(吾独爱apply!!!)
```javascript
/*服装厂有两个工作团队, 平时都是负责各自的染色工作,平日里也没啥交集*/

//小绿变绿
function green (){
	return 'color change green';
}

//小蓝会变蓝
function blue (){
	return 'color change blue';
}

//但是双十一快到啦, 刚好今年比较流行绿色, 什么绿裤衩, 绿帽子都非常流行
//所以蓝色的需求就少了, 小蓝订单少了, 小绿的工作量又一直直上,直到有一天终于小绿开始做不过来了, 业务需要, 有什么办法能让小蓝也能帮上小绿的工作呢
// 聪明的老板就找来了apply 高级喷漆转换枪来帮小蓝转变颜色.

let cap;//帽子半成品

//第一步在大型喷漆机甲放上 小绿 的 DNA 
green.apply(); // 枪头已上弹

//第二步 小蓝 穿上喷漆机甲准备战斗
green.apply(blue, null);

//大公告成!!! 哈哈 一顶新鲜出炉的绿帽子就做好啦
cap = green.apply(blue, null); // color change green

//老板看了这顶绿帽子 非常满意, 在双十一过后,给 小蓝 跟 小绿 都加了3倍工资
//有了 apply ,从此 小绿 和 小蓝 过上了没羞没臊的幸福生活! 
```
#### 应用场景
#### 绑定this的指向

在非严格模式下当我们第一个参数传递为null或undefined时，函数体内的this会指向默认的宿主对象，在浏览器中则是window
```javascript
let test = function(){
  console.log(this === window);
}

//都指向了 window
test.apply(null);//true
test.apply(undefined);//true
```
#### 使用别人的技能(使用别的表达式的方法)
```javascript	
let hero = {
  name:'蜘蛛侠',
  hasSkill:function (){
	console.log(this.name + '拥有蜘蛛感应,蜘蛛力量,蜘蛛吸附,蜘蛛自愈的能力');
  }
}
let people = {
  name:'大侠伍陆柒',
};
hero.hasSkill();//蜘蛛侠拥有蜘蛛感应,蜘蛛力量,蜘蛛吸附,蜘蛛自愈的能力

//吸星大法
hero.hasSkill.call(people);//大侠伍陆柒也有啦
	
```	
#### 实现继承
```javascript
//动物类
function Animal(name){   
  	this.name = name;   
  	this.showName = function(){   
		console.log(this.name);   
  	}   
}   
 
//猫主子类
function Cat(name){  
  	Animal.call(this, name);  
}   
 
//实例化大菊类
var cat = new Cat("Orange Cat"); 
//调用继承的动物类的方法  
cat.showName(); //Orange Cat
```	
	
#### 其他用法(骚操作)
	
#### 实现运用其他类型的特定方法比如 判断类型 比用 instance 或者 typeof 更准确 
```javascript
console.log(Object.prototype.toString.apply(123)) //[object Number]

console.log(Object.prototype.toString.apply('123')) //[object String]

console.log(Object.prototype.toString.apply(undefined)) //[object Undefined]

console.log(Object.prototype.toString.apply(true)) //[object Boolean]

console.log(Object.prototype.toString.apply({})) //[object Object]

console.log(Object.prototype.toString.apply([])) //[object Array]

console.log(Object.prototype.toString.apply(function(){})) //[object Function]

```
#### 实现类数组添加元素方法
```javascript
//argument 类数组
(function(){
  Array.prototype.push.call(arguments,4);
  console.log(arguments);//[1, 2, 3, 4]
})(1,2,3)
```

#### 转换类似数组的对象
```javascript
//转换类数组为数组 用 Array.prototype.slice.apply
let objArr = {
	0: 1,
	length: 1	
}
let arr = Array.prototype.slice.apply(objArr) //返回一个新数组
console.log(arr) // [1]
console.log(objArr) // 原来的类数组不变
```
#### 实现数组合并
```javascript
//concat 实现方法

let a=[1,2,3],b=[4,5,6];
let c=a.concat(b); // 返回一个新数组
console.log(c);// 1,2,3,4,5,6
console.log(a);// 1,2,3  不改变本身

//Array.prototype.push.apply 实现方法

let a = [1, 2, 3],b = [4, 5, 6];
let c = Array.prototype.push.apply(a, b);
console.log(c);// 6 返回的长度
console.log(a);// 1,2,3,4,5,6
```
	
#### 实现数组查找最大最小数字
```javascript
let max = Math.max.apply(null,[55, 66, 44]);
let min = Math.min.apply(null,[55, 66, 44]);
console.log(max);//66
console.log(min);//44
```
#### 将数组的空元素变为undefined
```javascript
let arr = [1,, 3];
console.log(Array.apply(null,arr)); // [1, undefined, 3]
```





