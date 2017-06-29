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
 
 2. window.open 拦截

   > window.open 在自动触发的时候和在异步回调调用处理中会被拦截，只有在用户和窗口有交互事件的时候触发open 才不会被拦截。
   
   ```js
      // 这样不会被拦截
      btn.onClick = function(){
         window.open("http://xxxx.com", "_blank");
      }

      // 异步回调的处理
      btn.onclick = function(){
         var win = window.open("about:blank");

         fetch(url).then(function(){
            win.location.href = "http://xxxx.com";
         })
      }
   ```
