# “use strict"在函数作用域中的一些限制

> 在平常的函数中，我们可以在一个函数中使用`”use strict“` 来开启严格模式。但是在`es6`中我们需要注意，在某些情况下，在函数中使用`use strict`会导致语法错误。下面这些情况，都会导致出现语法错误。



* 函数的参数有默认值

  ```javascript
  // Uncaught SyntaxError
  function fn(param1, param2 = param1){
    "use strict";
    
    return param1;
  }
  ```

  ​

* 函数的参数存在解构操作

  ```javascript
  // Uncaught SyntaxError
  function fn({param1, param2}){
    "use strict";
    
    return param1;
  }
  ```

* 函数参数使用`rest`形式

  ```javascript
  // Uncaught SyntaxError
  function fn(...arg){
    "use strict";
    
    return arg;
  }
  ```
