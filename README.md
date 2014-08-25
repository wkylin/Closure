Closure---闭包
=============


*作用域链* ----标识符的查找是先从内部作用域查找，若查找不到，会继续向上查找，一直到全局作用域，这样查找方式使得
作用域像一个链条。由于标识符的查找方向是向上的，所以变量只能向外访问，而不能向内访问。

    var foo=function(){
        var local="local var";
        var bar=function(){
            var local="another var";
            var baz=function(){
                console.log(local);
            }
            baz();
        }
        bar();
    }
    foo();//another var

若查到，则不会向上继续查找，尽管在再上一层的作用域中也有local的定义。如果查找一个不存在的变量，将会一直沿着作用域链查找到
全局作用域，最后抛出未定义错误。

    var foo=function(){
        var local="local var";
        var bar=function(){
            //var local="another var";
            var baz=function(){
                console.log(local);
            }
            baz();
        }
        bar();
    }
    foo();//local var

我们知道作用域上的对象访问只能向上，这样外部无法向内部访问。如下代码可以正常打印：

    var foo=function(){
        var local="local";
        (function(){
            console.log(local);
        }());
    }
    foo();//local

但在下面的代码中，却会得到local未定义的异常：

    var foo=function(){
        (function(){
            var local="local";
        }());
        console.log(local);
    }
    foo();//ReferenceError: local is not defined

在JavaScript中，实现外部作用域访问内部作用域中变量的方法叫做闭包（closure).这得益于高阶函数的特性：函数可以作为
参数或者返回值。示例代码如下：

    var foo=function(){
        var bar=function(){
            var local="local";
            return function(){
                return local;
            };
        };
        var baz=bar();
        return(baz);
    }

    var fo=foo();
    fo();//local

一般而言，在bar()函数执行完成后，局部变量local将会随着作用域的销毁而被回收。
但是注意这里的特点在于返回值是一个匿名函数，且这个函数中具备了访问localr的条件。
虽然在后续的执行中，在外部作用域中还是无法直接访问local,但是若要访问它，只要通过这个中间函数稍作周转即可。

闭包是JavaScript的高级特性，利用它可以产生很多巧妙的效果。它的问题在于，一旦有变量引用这个中间函数，这个中间函数将不会释放，
同时也会使原始的作用域不会得到释放，作用域中产生的内存占用也不会得到释放。除非不再引用，才会逐步释放。


####参考：http://www.tuicool.com/articles/nIZvmaY

        function f() {
          var localVariable = 100; //局部变量
          function innerPlus() {
            localVariable += 1;
            alert(localVariable);
          }
          function innerMinus() {
            localVariable -= 1;
            alert(localVariable);
          }
          return {
            plus: innerPlus,
            minus:innerMinus
          }; //这次返回的不再是一个单独的函数,而是一个对象,对象的属性是两个内部函数
        }
        var temp = f();
        temp.plus();  //显示101
        temp.minus(); //显示100
        //只能通过temp的特定API操作变量,实现了封装性和更好的安全性