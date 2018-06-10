
《ECMAScript 6 入门》

简介

ES6支持程度

- node支持度高
      $ node --v8-options | grep harmony
- 浏览器检查
      http://ruanyf.github.io/es-checker/



Babel转码器

- Babel 是一个广泛使用的 ES6 转码器，可以将 ES6 代码转为 ES5 代码，从而在现有环境执行 
  

 let和const

let命令

for循环计数器

-     var a = [];
      for (let i = 0; i < 10; i++) {
        a[i] = function () {
          console.log(i);
        };
      }
      a[6](); // 6
- for特性
  - let使得每一轮的循环变量都是重新声明的，那它怎么知道上一轮循环的值？
    - 这是因为JavaScript引擎内部会记住上一轮循环的值
  - 设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域
        for (let i = 0; i < 3; i++) {
          let i = 'abc'
          console.log(i)
        }
        //abc
        //abc
        //abc

不存在变量提升

    console.log(bar); // 报错ReferenceError
    let bar = 2;

暂时性死区

- 只要块级作用域内存在let命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。 
      if (true) {
        // TDZ开始
        tmp = 'abc'; // ReferenceError
        console.log(tmp); // ReferenceError
      
        let tmp; // TDZ结束
        console.log(tmp); // undefined
      
        tmp = 123;
        console.log(tmp); // 123
      }
- ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。 
  - 因此，typeof不再是一个百分之百安全的操作
        typeof x; // ReferenceError
        let x;
        typeof undeclared_variable // "undefined"
- 比较隐蔽的‘死区’
      var y = 1;   //或者let y = 1;
      function bar(x = y, y = 2) {
        return [x, y];
      }
      
      bar(); // 报错   ---y未被定义
- 时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。 
      // 不报错
      var x = x;
      
      // 报错
      let x = x;
      // ReferenceError: x is not defined

不允许重复声明

- let不允许在相同作用域内重新声明同一个变量
      var a = 1
      let a = 1 //报错
- 不能再函数内部重新声明参数

块级作用域

作用

- 防止内层变量(循环语句内的覆盖外层变量
- 防止用来计数的循环变量泄露为全局变量

特点

- 外部作用域无法读取内层作用域的变量
- 内部作用有可以定义外层作用域的同名变量（但实质上不是同一个变量）
- 
- 使得立即执行函数表达式（IIFE） 不再必要了
- ES6 的块级作用域允许声明函数的规则，只在使用大括号的情况下成立，如果没有使用大括号，就会报错。 
          function f1() {
            let a = 1
            if (true) {
              let a = 2
              console.log(a) //2
            }
            console.log(a); //1
          }
  注意：使用以下方法在控制台测试的时候，第一次会得到正确结果，第二次如果还在控制台中下面测试的话，会出现报错。因为相当于在全局里定义了两次a 
          const a = 1
          if (true) {
            const a = 3
            console.log(a);  //3
          }
          console.log(a)  //1

const 命令

基本用法

- const声明一个只读的常量。一旦声明，常量的值就不能改变。 
- 只声明不赋值，会报错
- 只在声明的块级作用域内生效
- 声明不能提升，存在暂时性死区
- 不可重复声明

本质

- const实际上保证的，并不是变量的值不得改动，而是变量指向的那个内存地址不得改动。 
- 因此当将一个对象声明为常量时必须小心
      const a = [];
      a.push('Hello'); // 可执行
      a.length = 0;    // 可执行
      a = ['Dave'];    // 报错
  

顶层对象的属性

- 为了保持兼容性，var命令和function命令声明的全局变量，依旧是顶层对象的属性 
- let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性 
      let b = 1;
      window.b // undefined

global对象

- 全局环境中，this会返回顶层对象。但是，Node 模块和 ES6 模块中，this返回的是当前模块。
- 函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined。
- 不管是严格模式，还是普通模式，new Function('return this')()，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全政策），那么eval、new Function这些方法都可能无法使用。



变量的解构赋值

数组的解构赋值

基本用法

- ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构 
- 只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。 
       //模式匹配完全解构
      let [foo, [[bar], baz]] = [1, [[2], 3]]; 
      foo // 1
      bar // 2
      baz // 3
      
      //解构不成功，变量的值就等于undefined
      let [x, y, ...z] = ['a'];  
      x // "a"
      y // undefined
      z // []
      
       //不完全解构，可以成功
      let [a, [b], d] = [1, [2, 3], 4];  
      a // 1
      b // 2
      d // 4
      
- 解构赋值允许指定默认值
  - ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效 ，而null不严格等于undefined，因此不会生效
      let [x, y = 'b'] = ['a', undefined];  // x = 'a', y = 'b'
  - 如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。 
        function f() {
          console.log('2');
        }
        
        let [x = f()] = [1];

对象的解构赋值

- 对象的解构与数组有一个重要的不同。数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。 
- 对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。 
      let { foo: baz } = { foo: "aaa", bar: "bbb" };
      baz // "aaa"
      foo // error: foo is not defined
- 如果解构失败，变量的值等于undefine 
      let {foo} = {bar: 'baz'};
      foo // undefined
- 如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错 
      // 报错-----foo这时为undefined，再取子属性就会报错
      let {foo: {bar}} = {baz: 'baz'}
- 注意大括号不能写在首行，不然js引擎会理解为一个代码块，从而发生语法错误
      // 错误的写法
      let x;
      {x} = {x: 1};
      // SyntaxError: syntax error
      
      // 正确的写法
      let x;
      ({x} = {x: 1});
- 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构 
      let arr = [1, 2, 3];
      let {0 : first, [arr.length - 1] : last} = arr;
      first // 1
      last // 3

字符串的解构赋值

- 字符串也可以解构赋值。这是因为此时，字符串被转换成了一个类似数组的对象。 
      const [a, b, c, d, e] = 'hello';
      a // "h"
      b // "e"
      c // "l"
      d // "l"
      e // "o"
- 类似数组的对象都有一个length属性，因此还可以对这个属性解构赋值。 
      let {length : len} = 'hello';
      len // 5

数值和布尔值的解构赋值

- 解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。 
- 由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。 

函数参数的解构赋值

- -     //函数move的参数是一个对象，通过对这个对象进行解构，得到变量x和y的值。
        //如果解构失败，x和y等于默认值。
        function move({x = 0, y = 0} = {}) { 
          return [x, y];
        }
        move({x: 3, y: 8}); // [3, 8]
        move({x: 3}); // [3, 0]
        move({}); // [0, 0]
        move(); // [0, 0]
        
        
        //为函数move的参数指定默认值，而不是为变量x和y指定默认值
        function move({x, y} = { x: 0, y: 0 }) {
          return [x, y];
        }
        move({x: 3, y: 8}); // [3, 8]
        move({x: 3}); // [3, undefined]
        move({}); // [undefined, undefined]
        move(); // [0, 0]

圆括号问题

- 三种不能使用圆括号的情形
  - 变量声明语句  //有let
  - 函数参数  //也属于变量声明
  - 赋值语句的模式----模式放在括号中了
- 可以使用圆括号的情形，赋值语句的非模式部分

用途

- 交换变量的值
- 从函数返回多个值
- 函数参数的定义
- 提取json数据
- 函数参数的默认值
- 遍历Map结构
- 输入模块的指定方法

字符串的扩展

codePointAt() / String.fromCodePoint()

字符串的遍历器接口

- 字符串可以被for...of循环遍历
      for (let codePoint of 'foo') {
        console.log(codePoint)
      }
      // "f"
      // "o"
      // "o

at() 

- 可以识别Unicode编号大于0xFFFF的字符，返回正确的字符（charAt()不行）

normalize()

includes(), startsWith(), endsWith()

    let s = 'Hello world!';
    
    s.startsWith('Hello') // true    
    s.endsWith('!') // true
    s.includes('o') // true     是否找到了参数字符串

    let s = 'Hello world!';
    //第二个参数，表示开始搜索的位置
    s.startsWith('world', 6) // true
    s.endsWith('Hello', 5) // true   
    s.includes('Hello', 6) // false

endsWith的行为与其他两个方法不同，它是针对前n个字符，而其它两个方法针对从第n个位置知道字符串结束

repeat()

    // 将原字符串重复n次并返回字符串
    'x'.repeat(3) // "xxx"
    
    // 小数则会取整
    'na'.repeat(2.9) // "nana"
    
    // 如果repeat的参数是负数或者Infinity，会报错
    'na'.repeat(Infinity)
    // RangeError
    'na'.repeat(-1)    //0 到 -1之间的小数等同于0
    // RangeError
    
    //参数NaN等同于 0。
    //如果repeat的参数是字符串，则会先转换成数字。

padStart(),padEnd()

- padStart和padEnd一共接受两个参数，第一个参数用来指定字符串的最小长度，第二个参数是用来补全的字符串。 
- 如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。 
- 如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。 
- 如果省略第二个参数，默认使用空格补全长度。 

模板字符串

- 模板字符串（template string）是增强版的字符串，用反引号（`）标识 
- 如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中。 
  - 如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的toString方法。
- 模板字符串中嵌入变量，需要将变量名写在${}之中。 
  - 如果模板字符串中的变量没有声明，将报错 
- 大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。 
  - 由于模板字符串的大括号内部，就是执行 JavaScript 代码，因此如果大括号内部是一个字符串，将会原样输出。 
- 模板字符串之中还能调用函数 
- 模板字符串还能嵌套 ，内层在变量的大括号中从而达到嵌套的目的
- 模板编译





正则的扩展

RegExp构造函数

允许RegExp构造函数第一个参数是一个正则对象，那么可以使用第二个参数指定修饰符

返回的正则表达式会忽略原有的正则表达式的修饰符，只使用新指定的修饰符。

y修饰符----叫做“粘连”（sticky）修饰符。

- y修饰符的作用与g修饰符类似，也是全局匹配，后一次匹配都从上一次匹配成功的下一个位置开始 
- 不同之处在于，g修饰符只要剩余位置中存在匹配就可，而y修饰符确保匹配必须从剩余的第一个位置开始 
- y修饰符的设计本意，就是让头部匹配的标志^在全局匹配中都有效。 
- 一个y修饰符对match方法，只能返回第一个匹配，必须与g修饰符联用，才能返回所有匹配。 
- ES6 的正则实例对象多了sticky属性，表示是否设置了y修饰符。 
      var r = /hello\d/y;
      r.sticky // true
      
      
      
      // ES5 的 source 属性
      // 返回正则表达式的正文
      /abc/ig.source
      // "abc"
      
      // ES6 的 flags 属性
      // 返回正则表达式的修饰符
      /abc/ig.flags
      // 'gi's 修饰符：dotAll 模式

s修饰符：dotAll模式

- 在这个模式下，点可以匹配任何字符。包括/n
- dotAll属性用来判断正则模式是否用了s修饰符

后行断言

先行断言---之前只支持先行断言

- ”先行断言“指的是，x只有在y前面才匹配，必须写成/x(?=y)/    
- ”先行否定断言“指的是，x只有不在y前面才匹配，必须写成/x(?!y)/    
- 上两者均不返回括号里的那个判断

后行断言

- 后行断言，x只有在y后面才匹配，必须写成/(?<=y)x/ 
- ”后行否定断言“ ，x只有不在y后面才匹配，必须写成/(?<!y)x/ 
- 上两者均不返回括号里的那个判断

后行断言的执行顺序

- “后行断言”的实现，需要先匹配/(?<=y)x/的x，然后再回到左边，匹配y的部分。这种“先右后左”的执行顺序，与所有其他正则操作相反，导致了一些不符合预期的行为。 
      /(?<=(\d+)(\d+))$/.exec('1053') // ["", "1", "053"]
      /^(\d+)(\d+)$/.exec('1053') // ["1053", "105", "3"]
- 上面代码中，需要捕捉两个组匹配。没有“后行断言”时，第一个括号是贪婪模式，第二个括号只能捕获一个字符，所以结果是105和3。而“后行断言”时，由于执行顺序是从右到左，第二个括号是贪婪模式，第一个括号只能捕获一个字符，所以结果是1和053。 

具名组匹配

简介

- 允许为每一个组匹配指定一个名字，既便于阅读代码，又便于引用。 
- “具名组匹配”在圆括号内部，模式的头部添加“问号 + 尖括号 + 组名” 
      const RE_DATE = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/;

解构赋值和替换

- 有了具名组匹配以后，可以使用解构赋值直接从匹配结果上为变量赋值。 
      let {groups: {one, two}} = /^(?<one>.*):(?<two>.*)$/u.exec('foo:bar');
      one  // foo
      two  // bar
- 字符串替换时，使用$<组名>引用具名组 
      let re = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/u;
      
      '2015-01-02'.replace(re, '$<day>/$<month>/$<year>')  //第二个参数为字符串
      // '02/01/2015'
- 引用：
  - 如果要在正则表达式内部引用某个“具名组匹配”，可以使用\k<组名>的写法。 
  - 数字引用（\1）依然有效。 

String.prototype.matchAll

- String.prototype.matchAll方法，可以一次性取出所有匹配。不过，它返回的是一个遍历器（Iterator），而不是数组。 
      const string = 'test1test2test3';
      
      // g 修饰符加不加都可以
      const regex = /t(e)(st(\d?))/g;
      
      for (const match of string.matchAll(regex)) {
        console.log(match);
      }
      // ["test1", "e", "st1", "1", index: 0, input: "test1test2test3"]
      // ["test2", "e", "st2", "2", index: 5, input: "test1test2test3"]
      // ["test3", "e", "st3", "3", index: 10, input: "test1test2test3"]
- 遍历器转化成数组
      // 转为数组方法一
      [...string.matchAll(regex)]
      
      // 转为数组方法二
      Array.from(string.matchAll(regex));

数值的扩展

二进制和八进制

- 从 ES5 开始，在严格模式之中，八进制就不再允许使用前缀0表示，ES6 进一步明确，要使用前缀0o表示。 

 Number.isFinite()和Number.isNaN() 

- Number.isFinite()用来检查一个数值是否为有限的（finite），即不是Infinity。 
- Number.isNaN()用来检查一个值是否为NaN。
- 与传统的区别
  - 传统方法先调用Number()将非数值的值转为数值，再进行判断 
  - 这两个新方法只对数值有效，Number.isFinite()对于非数值一律返回false, Number.isNaN()只有对于NaN才返回true，非NaN一律返回false 

Number.parseInt()和Number.parseFloat()

- ES6 将全局方法parseInt()和parseFloat()，移植到Number对象上面，行为完全保持不变。 
- 逐步减少全局性方法，使得语言逐步模块化。 

Math对象的扩展

Math.trunc()

- 用于去除一个数的小数部分，返回整数部分
- 对于非数值，先用Number方法将其转为数值
- 对于空值或者无法取整数的值，返回NaN

指数运算符

- 2 ** 3  //8            2 **4 //16

函数的扩展

函数参数的默认值

函数参数的默认值

- 阅读代码的人，可以立刻意识到哪些参数是可以省略的，不用查看函数体或文档； 
- 有利于将来的代码优化，即使未来的版本在对外接口中，彻底拿掉这个参数，也不会导致以前的代码无法运行。 
- 参数变量是默认声明的，所以不能用let或const再次声明。 
- 使用参数默认值时，函数不能有同名参数。 
      // 报错,在使用默认值的时候，有2个x参数，会报错
      function foo(x, x, y = 1) {
        // ...
      }  
- 参数默认值不是传值的，而是每次都重新计算默认值表达式的值。也就是说，参数默认值是惰性求值的。 
- 参数默认值的位置
  - 通常情况下，定义了默认值的参数，应该是函数的尾参数。因为这样比较容易看出来，到底省略了哪些参数。如果非尾部的参数设置默认值，实际上这个参数是没法省略的。
- 函数的length属性
  - 指定了默认值以后，函数的length属性，将返回没有指定默认值的参数个数。也就是说，指定了默认值后，length属性将失真。
        (function (a) {}).length // 1
        (function (a = 5) {}).length // 0
        (function (a, b, c = 5) {}).length // 2 
  - 如果设置了默认值的参数不是尾参数，那么length属性也不再计入后面的参数了。
        (function (a = 0, b, c) {}).length // 0
        (function (a, b = 1, c) {}).length // 1 
- 作用域
  - 一旦设置了参数的默认值，函数进行声明初始化时，参数会形成一个单独的作用域（context）。等到初始化结束，这个作用域就会消失。这种语法行为，在不设置参数默认值时，是不会出现的 
        var x = 1;
        function foo(x, y = function() { x = 2; }) {
          var x = 3;
          y();
          console.log(x);
        }
        
        foo() // 3
        x // 1
        
    上面代码中，函数foo的参数形成一个单独作用域。这个作用域里面，首先声明了变量x，然后声明了变量y，y的默认值是一个匿名函数。这个匿名函数内部的变量x，指向同一个作用域的第一个参数x。函数foo内部又声明了一个内部变量x，该变量与第一个参数x由于不是同一个作用域，所以不是同一个变量，因此执行y后，内部变量x和外部全局变量x的值都没变。
    如果将var x = 3的var去除，函数foo的内部变量x就指向第一个参数x，与匿名函数内部的x是一致的，所以最后输出的就是2，而外层的全局变量x依然不受影响
        var x = 1;
        function foo(x, y = function() { x = 2; }) {
          x = 3;
          y();
          console.log(x);
        }
        
        foo() // 2
        x // 1

rest参数

- ES6 引入 rest 参数（形式为...变量名）(扩展运算符)，用于获取函数的多余参数，这样就不需要使用arguments对象了。rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
- 用 rest 参数，可以向该函数传入任意数目的参数。 
- 与arguments区别： 
  - arguments对象不是数组，而是一个类似数组的对象。所以为了使用数组的方法，必须使用Array.prototype.slice.call先将其转为数组 
  - rest 参数就不存在这个问题，它就是一个真正的数组，数组特有的方法都可以使用
- 注意，rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错 
- 函数的length属性，不包括 rest 参数。 
      (function(a, ...b) {}).length  // 1

严格模式

- 从 ES5 开始，函数内部可以设定为严格模式。   'use strict';
- ES2016 做了一点修改，规定只要函数参数使用了默认值、解构赋值、或者扩展运算符，那么函数内部就不能显式设定为严格模式，否则会报错。 
  - 这样规定的原因是，函数内部的严格模式，同时适用于函数体和函数参数。但是，函数执行的时候，先执行函数参数，然后再执行函数体。这样就有一个不合理的地方，只有从函数体之中，才能知道参数是否应该以严格模式执行，但是参数却应该先于函数体执行。 
    - 解决方法：设定全局性的严格模式 
    - 解决方法：把函数包在一个无参数的立即执行函数里面。 

name属性

- 函数的name属性，返回该函数的函数名。 
- 如果将一个匿名函数赋值给一个变量，ES5 的name属性，会返回空字符串，而 ES6 的name属性会返回实际的函数名。 
  - 解决方法，可以给把变量的名字加给匿名函数
- Function构造函数返回的函数实例，name属性的值为anonymous（匿名）。 
- bind返回的函数，name属性值会加上bound前缀。 

箭头函数

-     var sum = (num1, num2) => num1 + num2;
      // 等同于
      var sum = function(num1, num2) {
        return num1 + num2;
      };
- 由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。 
- 头函数的一个用处是简化回调函数 
- 注意点：
  - 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。 
  - 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。 
  - 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。 
  - 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。 
  - function------ this
  - this指向的固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正是因为它没有this，所以也就不能用作构造函数 
    - this对象的指向是可变的，但是在箭头函数中，它是固定的。 
    - 箭头函数可以让setTimeout里面的this，绑定定义时所在的作用域，而不是指向运行时所在的作用域 
    - 除了this，以下三个变量在箭头函数之中也是不存在的，指向外层函数的对应变量：arguments、super、new.target。 

双冒号运算符

尾调用优化

函数参数的尾逗号


