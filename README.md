# JS-
遇到的一些 JS面试题

1. 写一个count函数 实现 count(a)(b)(c)(d)....他们的积  如count(1)(2)(3)  得到 6   
    function count(n1){
      return function (n2){
        count.result = n1 * n2;
        count(n1*n2)
      }
    }
