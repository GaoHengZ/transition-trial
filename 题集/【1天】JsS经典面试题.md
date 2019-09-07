```javascript
function Foo() {
	getName = function () {
		console.log(1);
	};
	return this;
}

Foo.getName = function () {
	console.log(2);
};
Foo.prototype.getName = function () {
	console.log('baidu' && 'google');
};
var getName = function () {
	console.log(4);
};

function getName() {
	console.log(5);
}

// 请写出一下的输出结果
Foo.getName();
getName();
Foo().getName();
getName();
new Foo.getName();
new Foo().getName();
new new Foo().getName();
```
---      

---
## 答案：
先输出一下结果：
```javascript
Foo.getName();                                       //  2  
getName();                                          //  4  
Foo().getName();                                   //  1  
getName();                                        //  1  
new Foo.getName();                               //  2  
new Foo().getName();                            // google  
new new Foo().getName();                       // google 
```

## 解析
具备```逻辑运算符、运算符的优先级、声明变量和声明函数的提升优先级、原型继承、闭包、覆盖```等知识点。
下面就开始吧！
我先将每块函数进行编号：
```javascript
// 方式1：一个构造函数嘛，里面有个全部变量getName 指向一个匿名函数（小心闭包）  
function Foo() {
   getName = function () {
      console.log (1);
   };
   return this;
}
// 方式2：构造函数的一个属性getName 指向一个匿名函数  
Foo.getName = function () {
   console.log (2);
};
// 方式3：构造函数的原型上有个getName方法  
Foo.prototype.getName = function () {
   console.log('baidu' && 'google');
};
// 方式4：定义一个变量指针指向一个匿名函数  
var getName = function () {
   console.log (4);
};
// 方式5：声明一个叫getName的有名函数  
function getName() {
   console.log (5);
}  
```
接下来再分析执行语句的代码：
```
>>执行语句第一行： Foo.getName() 
         此句应该没什么要说的吧，就是调用方式2的函数代码块，结果为“2”！
```
```
>>执行语句第二行：getName() 
         如果单独看这条执行语句，一般会有个全局声明的函数方式5"function getName(){} ",
         但要是放在这么个复杂的上下文环境中，那肯定是不一样的啦，
         因为还有个方式4 “var getName = function(){}” 来捣乱，这就要考察一个知识点:当定义的变量和声明的函数重名了怎么办？
         答：它们都会进行预解析，函数声明提前于变量声明，但是最终会被变量覆盖！so方式4获胜，输出结果为“4”！
```
```
>>执行语句第三行：Foo().getName()
        本句，有个执行顺序的，先执行方式1的“Foo()”,结果是"this" 并指向window，
        并产生了一个全局getName(window.getName)指针指向一个匿名函数，
        然后再执行"this.getName()" , 
        其实就是执行刚刚造出来的那个全局getName指向的匿名函数，所以输出“1”.
```
```
>>执行语句第四行：getName()
        此句执行的是方式1执行出来的那个全局变量getName 指针指向的匿名函数，
        另外，此时方式4已经被覆盖了！所以结果为 “1”.
```
```
>>执行语句第五行：new Foo.getName()
        首先还是先看运算符优先级 【new Foo() >  Foo() > new Foo】，
        先运算方式2的Foo.getName() 结果为“2”，再new一个Foo实例对象。
```
```
>>执行语句第六行：new Foo().getName()
        先执行 new Foo(), 结果产生一个新的实例对象，
        并且继承了Foo()这个构造函数中的getName方法，
        所以再执行方式3函数块，
        而接着有涉及到另一个知识点：逻辑运算符运算， 
        对于 “&&”来说，如果前者为真，那么就去执行后者，否则只执行前者；
        对于“||”来说，如果前者为真，只执行前者，后者不必执行，否则还得执行后者。
        因此结果为 “google”。
```
```
>>执行语句第七行：new new Foo().getName()
        先执行new Foo(),变成了 new Foo的实例对象.getName(), 
        然后再执行 Foo的实例对象.getName(),又回到了方式3函数块，
        结果为“google”，
        最后执行new Foo的实例对象。
```
