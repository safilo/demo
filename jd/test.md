title: 第一周
date: 2016/07/08
---
### 1.声明和定义的概念   
#### 声明：`告诉浏览器有这么一个名字；但是不知道值； eg:var a;`   
#### 定义：`给之前声明的这个名字赋值；eg:var a=2;`   
#### 注：对带var和带function的声明和定义是不同的：   
> __带var：在预解释阶段，只声明，不定义__  
> __带function：在预解释阶段，声明+定义__   

### 2.作用域：
#### 全局作用域：  
在浏览器打开HTML页面的时候，会形成一个供JS执行的环境；在全局作用域环境下，里面所有的变量和函数，实际上就是window的属性和方法  
#### 私有作用域：  
在函数被调用的时候，形成一个私有作用域；

### 3.预解释：  
在当前作用域下，JS代码执行之前，浏览器会把带var和带function进行提前声明或者定  
域：空间或者范围

#### 关于函数的无节操：  
##### 1.变量声明，只走"="的左边；声明，不定义,如果把函数赋值给变量，按变量来声明
```
console.log(a); //undefined
var a=function a(){console.log(2)};
```
##### 2.自执行函数不预解释，只有执行到他的时候，声明+定义+执行同步完成
##### 3.不管if条件是否成立，都走预解释  
```
var n=12;
if(n%2==0){
    function sum(){
        alert('偶数')
    }
}else{
    function sum(){
        alert('奇数')
    }
}
sum()
```
>注意：不要在if语句中写函数，因为各大浏览器对他的预解释不一样，如执行上面代码，在Chrome、Firefox、IE Edge、Opera中输出 "偶数"，在IE除了Edge外其他版本、Safari中输出 "奇数"  

>还有比较坑爹的地方就是，各大浏览器对于if语句中声明定义的函数，在Chrome、Firefox、Opera、IE的Edge中会进行声明但不进行定义（即不赋值，这点和基本数据类型的预解释很像），此时在if语句前访问虽然不会报错，但是值为undefined;而在IE 10及之前版本和Safari中预解释时声明和定义都完成了，代码如下：

```
f = function() {
    return true
};
g = function() {
    return false
};
(function() {
    console.log(g); // 高版本Chrome、Firefox、Opera下输出undefined,从而执行if条件判断时报错，而IE除了Edge和Safari输出if中定义的函数g代码字符串,执行if条件判断不会报错
    if (g() && [] == ![]) { //高版本Chrome、Firefox、Opera执行if条件判断时报错，而IE除了Edge和Safari不会报错
        f = function() {
            return false;
        }

        function g() {
            return true;
        }
    }
})();
alert(f());
alert(g());
```
##### 4.如果函数有返回值return：return后面的值不进行预解释；return下面的代码不执行，但是要进行预解释
```
(function() {
    console.log(a); // undefined
    console.log(fn); // Uncaught ReferenceError: fn is not defined
    return function fn() {
        //some codes
    }
    var a = 12;
})();
```
##### 5.在变量名相同的情况下，不重复声明，但会重新赋值  
```
console.log(typeof a); // function
var a = 12;
function a() {
    //some codes
}
console.log(typeof a); // number
var a = {};
console.log(typeof a); // object
```


### 4.函数定义和执行阶段：   
#### 1）函数定义3阶段：  
1. 开辟一个堆内存空间，浏览器默认为其分配一个地址；eg:xxff00;   
2. 把函数体中的JS代码做为字符串存放在这个堆内存空间中   
3. 把地址赋值给函数名；   

#### 2）函数执行3阶段：   
1. 给形参赋值   
2. 预解释   
3. JS代码从上往下执行；  

### 5.作用域链：  
在函数执行的时候，会形成一个私有作用域A，私有作用域中找一个变量,这个变量是否在作用域A中定义过：   
1. 如果变量在A中定义过，那这个A中所有的此变量，都是私有变量；   
2. 如果变量没在A中定义过，就会往A的上级作用域进行查找，有的话就赋值，没有就继续往上找，。。。找到window都没，报错！

### 6.带var和不带var的区别：    
1). 带var：  
- 会预解释   
- 如果带var在全局环境下，它是window的全局属性；

2). 不带var:  
- 不会预解释   
- 首先会往上找；如果是赋值的话，找到window还没有，就变成window上的全局属性   

### 7.JS中的内存
按照功能的不同：

#### 栈内存：`提供了一个供JS代码执行的环境：全局作用域，私有作用域 `  
#### 堆内存：`存储引用数据类型的值； 对象数据类型：存属性和属性值；`   
函数数据类型：代码字符串  
**参数是传地址还是传值？？？？？？？？？？？？？？？？？？？**
```
var a = [1, 2, 3, 4]; //xxff00
function fn(a) {
    //形参a=地址；
    var b = a;
    a[0] = 6; //这里改地址里的值；[6,2,3,4]
    console.log(typeof a); //6,2,3,4
    a = 2; //形参=2；形参a=2;
    console.log(typeof a);
}
fn(a);
console.log(a); //6,2,3,4
```

```
var a=[1,2,3,4];
function fn(a){//形参a=地址；arguments[0]=地址
    console.log(arguments)
    console.log(arguments[0])
    arguments[0]=2; //形参a=2; arguments[0]=2；
    alert(a);//2 2
    a=4;//形参a=4;
}
fn(a);//传 地址 [1,2,3,4]
alert(a);// [1,2,3,4]
```

### 8.闭包   
#### 为什么要使用闭包：

- 防止变量名冲突   
- 可以通过传参的方式，对全局变量的在私有作用域里重新赋值  

    ```
    var name='张三';
    var age='28';
    (function (name,age){
        name='李四';
        age='18';
        window.name=name;
        window.age=age;
        alert(name+','+age);
    })(name,age);
    alert(name+','+age);
    ```
- 闭包里面的值，如果想修改全局的值，window.xx=xx  
- 闭包可以用来封装方法，最后通过window.方法名=方法名；把封装好的函数，让外面可以用到  

    ```
    (function(){
        function getPrev(oEle){
            if(oEle.previousElementSibling){
                return oEle.previousElementSibling;
            }
            var prev=oEle.previousSibling;
            while(prev && prev.nodeType!=1){
                prev=prev.previousSibling;
            }
            return prev;
        }
        window.prev=getPrev;
    })();
    ```

### 9.this   

- **自执行函数里的this，永远都是window**  
- **如果一个元素通过一个行为来触发一个方法，方法中的this就是当前           这个元素**  
- **函数调用的时候，看前面是否有'.'，有'.'的话，'.'前面是谁，
this就是谁**  

    ```
    var num = 10;
    var obj = {
        num: 20,
        fn: (function(num) { //执行自执行函数，返回值为一个函数
                // console.log(this ==window, this.num == num)
                this.num *= 2; //this = window  window.num == 20
                num += 10; // num != window.num 被重新赋值 num == 20
                // console.log(num);
                return function() {
                    this.num *= 3; // this.num != num
                    num += 1; // num为上级作用域中的num，它的值为20
                    console.log(this.num, num); // this.num == ?,num == 21
                }
            })(num) //num == window.num
    };
    console.log(num); //20
    var fn = obj.fn;
    fn(); // 60, 21
    console.log(num); // 60
    obj.fn(); // 60, 22
    console.log(num); // 60
    console.log(window.num, obj.num); // 60, 60
    ```

### 10. 内存释放  
#### 1.内存释放：堆内存的释放和栈内存的释放  

##### 1）堆内存的释放：`当堆内存被变量占用的时候，无法释放`  

-解决办法：变量=null；当变量指针为空的时候，浏览器会在空闲时把被释放的堆内存收回，这种机制-垃圾回收机制  
##### 2）栈内存释放  

######全局作用域：  

在浏览器其加载完整个页面时，形成一个供全局使用的环境-全局作用域，只有关闭浏览器，才能彻底释放；如果不关闭浏览器，这个全局环境中的变量和方法都无法释放  

###### 私有作用域：  
当函数执行完成时，私有作用域一般情况下会自动销毁  

#### 2.私有作用域不销毁情况   
1）不销毁：当函数执行完成时，如果函数里面的东西被外面的变量或者其他东西占用，就无法销毁  
2）不立即销毁  


### 面试题  1  

1. 预解释毫无节操-自执行函数  

```
f=function(){return true};
g=function(){return false};
(function(){
    if(g()&&[]==![]){
        f=function(){return false;}
        function g(){return true;}
    }
})();
alert(f());
alert(g());
```
2. 自执行函数的预解释  

```
(function f(){
    function f(){ return 1; }
    alert (f());
    function f(){ return 2; }
})();
```
3. 作用域链  

```
var a=12;
function show(){
    alert(a);
    a=15;
}
show();
alert(a);
```

4. 预解释：变量声明早于代码运行  

```
var a=12;
function show(){
    alert(a);
    var a=15;
}
show();
alert(a);
```
5. 预解释：变量声明早于代码运行  

```
var uname = 'jack'
function change() {
    alert(uname)
    var uname = '珠峰'
    alert(uname)
}
change();
```
6. 在没有形参的情况下：函数声明早于变量声明  

```
function change() {
    alert(typeof fn) // function
    function fn() {
        alert('hello')
    }
    var fn
}
change();
```
7. 带var和不带var的区别  

```
function abc(){
    a=12;
    alert(a);
}
function b(){
    alert(a)
}
abc();
b();
```
### 面试题 2  
1. 关于内存释放的面试题  

```
function fn() {
    var i = 10;
    return function(n) {
        console.log(n + (++i));
    };
}
var f = fn();
f(15);
f(20);
fn()(15);
fn()(20);
fn()(30);
```
2. 综合面试题 `考察：this指向 `  

```
var num = 10;
var obj = {
    num: 20,
    fn: (function(num) {
        this.num *= 2;
        num += 10;
        return function() {
            this.num *= 3;
            num += 1;
            console.log(num);
        };
    })(num)
};
var fn = obj.fn;
fn();
obj.fn();
console.log(window.num, obj.num);

```
3. 关于this和||的运用  

```
var name = '张三';
var age = 500;
name = (function(name, age) {
    arguments[0] = '李四';
    age = age || this.age;
    console.log(name, age);
})(name);
console.log(name, age);
```
