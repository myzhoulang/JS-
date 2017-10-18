# Angularjs 指令

### scope

值: true, false,{...}

* false:  默认值 使用指令位置的作用域

* true: 创建子作用域 ，继承使用指令位置的作用域

* 创建独立作用域 与父作用域完全隔离， 使用以下3中方式可以与父作用域进行通信

  * @  将一个 **值** 插入到指定作用域中。因为插入的是值，意味着在指令中更改都不会影响到父作用域的属性。所以这是一种单向绑定的。在指令中使用`attrs[属性名称]`获取到的都是一个字符串,不管你的值在父作用域中是其他类型的数据， 如果需要将其解析成相应的数据类型 可以使用 `attrs.$eval(attrs[属性名存])`。当插入的是父作用域中的一个属性值的时候，如: <div directive-name  text=“{{ 父作用域中的属性 }}”><div>， 父作用域中的属性值可能会随着用户的某个操作改变，但是因为单向绑定，指令中相对应的`text`值 是不会自动监测到改变。而指令中的某个属性值需要根据父作用域中的这个属性值改变而改变。这时候我可以使用 `attrs`对象中的 `$observe`方法对`text`属性进行监听。

    ```js
    scope:{
      color:'@c',   // 这样指令作用域中color的值将会是属性c的值
      text:'@'	   // 这样指令作用域中text2的值将会是属性text的值
    }
    ```

    ​

  * =  将父作用域的某个属性与指令中的某个属性绑定，达到双向数据绑定。修改某一方的数据，另一方都会自动修改。指令属性上是传入一个父作用域上的一个属性

  * & 设置的表达式是作用域中的一个函数，属性被调用，该表达式也会被执行。用于回调函数。



### link中attrs 和 controller 中 $attrs

* 方法:

  * $normalize(name)

    > 将属性名称（例如，破折号/冒号/下划线分隔的字符串，可选地以x-或data-为前缀）转换为其归一化的camelCase表单。另外还有一个特殊情况，以大写字母开头的如：Moz前缀。

    name: 属性名

    ```js
    console.log(attrs.$normalize('data-is-open'))      	// isOpen
    console.log(attrs.$normalize('-webkit-transtion'))	// webkitTranstion
    console.log(attrs.$normalize('-moz-transtion'))		// MozTranstion
    ```

  * $observe(key, fn)

    > 这个方法将在下一次触发 `$degest`编译之后调用一次，当`key`的值改变，将会调用`fn`

  * $addClass(classVal)

    > 将由classVal参数指定的CSS类值添加到元素。如果启用动画，则会为类添加触发动画。

  * $updateClass(newClasses, oldClasses)

    > 根据新旧CSS类值（指定为newClasses和oldClasses）之间的差异，向元素添加并删除相应的CSS类值。

  * $set(name, value)

    > 设置DOM元素属性值。

  * $removeClass(classVal)

    > 从元素中删除classVal参数指定的CSS类值。如果启用动画，则将触发动画以进行类删除。	

* 属性

### 其他

* 默认使用 `@` 传递过来的 在`link`和`controller` 中获取到的都是字符串， 可以使用`scope` 中 **scope.$eval()** 方法将其解析成相应的数据类型
* 使用`attrs.$observe()`方法对`attrs`上的属性值进行监控,该方法返回一个取消当前监控的函数，执行该函数，将会取消当前监控

