# 第五章 第 10 节 前端基础-this2

> 原文：[`www.nowcoder.com/tutorial/10072/d7cccb73dbad485993c3319662f39957`](https://www.nowcoder.com/tutorial/10072/d7cccb73dbad485993c3319662f39957)

#### 4.4 箭头函数能否当构造函数

**参考答案：**

**箭头函数表达式**的语法比函数表达式更简洁，并且没有自己的`this`，`arguments`，`super`或`new.target`。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数。

#### 4.5 继承，优缺点

**参考答案：**

*   继承的好处
    *   a：提高了代码的复用性
    *   b：提高了代码的维护性
    *   c：让类与类之间产生了关系，是多态的前提
*   继承的弊端
    *   类的耦合性增强了,但是开发的原则：高内聚，低耦合。

#### 4.6 js 继承的方法和优缺点

**参考答案：**

1.  原型链继承

    实现方式：将子类的原型链指向父类的对象实例

    ```cpp
    function Parent(){
      this.name = "parent";
      this.list = ['a'];
    }
    Parent.prototype.sayHi = function(){
      console.log('hi');
    }
    function Child(){

    }
    Child.prototype = new Parent();
    var child = new Child();
    console.log(child.name);
    child.sayHi();
    ```

    原理：子类实例 child 的`__proto__`指向 Child 的原型链 prototype，而 Child.prototype 指向 Parent 类的对象实例，该父类对象实例的`__proto__`指向 Parent.prototype,所以 Child 可继承 Parent 的构造函数属性、方法和原型链属性、方法
    优点：可继承构造函数的属性，父类构造函数的属性，父类原型的属性
    缺点：无法向父类构造函数传参；且所有实例共享父类实例的属性，若父类共有属性为引用类型，一个子类实例更改父类构造函数共有属性时会导致继承的共有属性发生变化；实例如下：

    ```cpp
    var a = new Child();
    var b = new Child();
    a.list.push('b');
    console.log(b.list); // ['a','b']
    ```

2.  构造函数继承

    实现方式：在子类构造函数中使用 call 或者 apply 劫持父类构造函数方法，并传入参数

    ```cpp
    function Parent(name, id){
      this.id = id;
      this.name = name;
      this.printName = function(){
        console.log(this.name);
      }
    }
    Parent.prototype.sayName = function(){
      console.log(this.name);
    };
    function Child(name, id){
      Parent.call(this, name, id);
      // Parent.apply(this, arguments);
    }
    var child = new Child("jin", "1");
    child.printName(); // jin
    child.sayName() // Error
    ```

    原理：使用 call 或者 apply 更改子类函数的作用域，使 this 执行父类构造函数，子类因此可以继承父类共有属性
    优点：可解决原型链继承的缺点
    缺点：不可继承父类的原型链方法，构造函数不可复用

3.  组合继承

    原理：综合使用构造函数继承和原型链继承

    ```cpp
    function Parent(name, id){
      this.id = id;
      this.name = name;
      this.list = ['a'];
      this.printName = function(){
        console.log(this.name);
      }
    }
    Parent.prototype.sayName = function(){
      console.log(this.name);
    };
    function Child(name, id){
      Parent.call(this, name, id);
      // Parent.apply(this, arguments);
    }
    Child.prototype = new Parent();
    var child = new Child("jin", "1");
    child.printName(); // jin
    child.sayName() // jin

    var a = new Child();
    var b = new Child();
    a.list.push('b');
    console.log(b.list); // ['a']
    ```

    优点：可继承父类原型上的属性，且可传参；每个新实例引入的构造函数是私有的
    缺点：会执行两次父类的构造函数，消耗较大内存，子类的构造函数会代替原型上的那个父类构造函数

4.  原型式继承

    原理：类似 Object.create，用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了个可以随意增添属性的实例或对象，结果是将子对象的`__proto__`指向父对象

    ```cpp
    var parent = {
      names: ['a']
    }
    function copy(object) {
      function F() {}
      F.prototype = object;    
      return new F();
    }
    var child = copy(parent);
    ```

    缺点：共享引用类型

5.  寄生式继承

    原理：二次封装原型式继承，并拓展

    ```cpp
    function createObject(obj) {
      var o = copy(obj);
      o.getNames = function() {
        console.log(this.names);
        return this.names;
      }
      return o;
    }
    ```

    优点：可添加新的属性和方法

6.  寄生组合式继承

    原理：改进组合继承，利用寄生式继承的思想继承原型

    ```cpp
    function inheritPrototype(subClass, superClass) {
      // 复制一份父类的原型
      var p = copy(superClass.prototype);
      // 修正构造函数
      p.constructor = subClass;
      // 设置子类原型
      subClass.prototype = p;
    }

    function Parent(name, id){
      this.id = id;
      this.name = name;
      this.list = ['a'];
      this.printName = function(){
        console.log(this.name);
      }
    }
    Parent.prototype.sayName = function(){
      console.log(this.name);
    };
    function Child(name, id){
      Parent.call(this, name, id);
      // Parent.apply(this, arguments);
    }
    inheritPrototype(Child, Parent);
    ```

#### 4.7 new 会发生什么

**参考答案：**

1.  创建空对象；
    　var obj = {};

2.  设置新对象的 constructor 属性为构造函数的名称，设置新对象的**proto**属性指向构造函数的 prototype 对象；
    　obj.**proto** = ClassA.prototype;
    扩展了新对象的原型链。

3.  使用新对象调用函数，函数中的 this 被指向新实例对象：
    　ClassA.call(obj);　　//{}.构造函数();

4.  返回 this 指针。当存在显示的返回时，返回 return 后面的内容。新建的空对象作废。

    ```cpp
    function test() {
     this.name = "test";
     }
     test.prototype = {
     a:{},
     b:{}
     }

    var  c = new test();
    ```