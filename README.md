# JS

1. 写一个count函数 实现 count(a)(b)(c)(d)....他们的积  如count(1)(2)(3)  得到 6 

   ```js
   function count(n1){
      return function (n2){
        count.result = n1 * n2;
        return count(n1*n2)
      }
    }
   ```
