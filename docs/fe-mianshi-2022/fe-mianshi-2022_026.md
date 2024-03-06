# 第五章 第 7 节 前端基础-异步 2

> 原文：[`www.nowcoder.com/tutorial/10072/5bb0da69257741609ab42e71ae43d2ef`](https://www.nowcoder.com/tutorial/10072/5bb0da69257741609ab42e71ae43d2ef)

#### 3.5 怎么解决 callback 多层嵌套

**参考答案：**

回调地狱有两种解决方案：

1.  Promises
2.  Async/await

#### 3.6 promise 的介绍与使用

**参考答案：**

**Promise 介绍：**

ES6 中的 Promise 是异步编程的一种方案。从语法上讲，Promise 是一个对象，它可以获取异步操作的消息。

Promise 对象, 可以**将异步操作以同步的流程表达出来**。使用 Promise 主要有以下好处：

*   可以很好地解决**回调地狱**的问题（避免了层层嵌套的回调函数）。
*   语法非常简洁。Promise 对象提供了简洁的 API，使得控制异步操作更加容易。

**Promise 使用：**

**语法**

```cpp
var promise = new Promise((resolve, reject) => {/* executor 函数 */
    // ... some code
    if (/* 异步操作成功 */){
        resolve(value);
    } else {
        reject(error);
    }
});
promise.then((value) => {
    //success
}, (error) => {
    //failure
})
```

1.  参数
    `executor` 函数在`Promise`构造函数执行时同步执行，被传递 `resolve` 和 `reject` 函数（`executor`函数在`Promise`构造函数返回新建对象前被调用）。
    `executor` 内部通常会执行一些异步操作，一旦完成，可以调用`resolve`函数来将`promise`状态改成`fulfilled`(完成)，或者将`promise`的状态改为`rejected`(失败)。
    如果在`executor`函数中抛出一个错误，那么该`promise` 状态为`rejected`。`executor`函数的返回值被忽略。

2.  简单使用

```cpp
function timeout(ms) {
    return new Promise((resolve, reject) => {
        setTimeout(resolve, ms, 'done');
    });
}

timeout(2000).then((value) => {
    console.log(value); //done
});
```

**原型方法**

Promise.prototype.then(onFulfilled, onRejected)

1.  语法
    `p.then(onFulfilled, onRejected);`
    `p.then((value) => {// fulfillment}, (reason) => {// rejection});`

2.  含义
    为 `Promise`实例添加状态改变时的回调函数。`then`方法的第一个参数是`resolved`状态的回调函数，第二个参数（可选）是`rejected`状态的回调函数。

3.  链式操作
    `then`方法返回的是一个新的`promise`，因此可以采用链式写法，即`then`方法后面再调用另一个`then`方法。

    ```cpp
    new Promise(resolve => {
        resolve(1);
    })
        .then(result => console.log(result)) //1
        .then(result => {
            console.log(result);              //undefined
            return 2;
        })
        .then(result => {
            console.log(result);             //2
            throw new Error("err");
        })
        .then((result) =>{
            console.log(result);            
        }, (err)=>{
            console.log(err);                //Error: err
            return 3;
        })
        .then((result) => {
            console.log(result);            //3
        })
    ```

    注意：①不管是`then`方法的`onfulfilled`函数参数执行还是`onrejected`（可选参数）函数参数执行，`then`方法返回的都是一个新的`Promise`对象，都可以继续采用链式写法调用另一个`then`方法。②`Promise.prototype.catch()`方法返回的也是一个`Promise`对象。`then`方法和`catch`方法可以链式操作。

4.  返回值
    `then`方法返回一个`Promise`，而它的行为与`then`中的被调用的回调函数(`onfulfilled`函数/`onrejected`函数)的返回值有关。
    (1) 如果`then`中的回调函数返回一个值，那么`then`返回的`Promise`将会成为接受状态，并且将返回的值作为接受状态的回调函数的参数值。

    ```cpp
    new Promise((resolve, reject) => {
        reject();
    })
    .then( () => 99, () => 42 )
    .then( result => console.log(result)); // 42
    ```

    (2) 如果`then`中的回调函数抛出一个错误，那么`then`返回的`Promise`将会成为拒绝状态，并且将抛出的错误作为拒绝状态的回调函数的参数值。

    ```cpp
    new Promise((resolve, reject) => {
        resolve();
    })
        .then( () => {throw new Error('err')}, () => {})
        .then( () => {}, (err) => {console.log(err)}); //Error: err
    ```

    (3) 如果`then`中的回调函数返回一个已经是接受状态的`Promise`，那么`then`返回的`Promise`也会成为接受状态，并且将那个`Promise`的接受状态的回调函数的参数值作为该被返回的`Promise`的接受状态回调函数的参数值。

    ```cpp
    new Promise((resolve, reject) => {
        resolve();
    })
        .then( () => {
            return new Promise(resolve => resolve('ok'));
        })
        .then( (result) => {console.log(result)}); //ok
    ```

    (4) 如果`then`中的回调函数返回一个已经是拒绝状态的`Promise`，那么`then`返回的`Promise`也会成为拒绝状态，并且将那个`Promise`的拒绝状态的回调函数的参数值作为该被返回的`Promise`的拒绝状态回调函数的参数值。

    ```cpp
    new Promise((resolve, reject) => {
        resolve();
    })
        .then( () => {
            return new Promise((resolve, reject) => {
               reject(new Error('err'));
            });
        })
        .then( () => {}, (err) => {console.log(err)}); //Error: err
    ```

    (5) 如果`then`中的回调函数返回一个未定状态（`pending`）的`Promise`，那么`then`返回`Promise`的状态也是未定的，并且它的终态与那个`Promise`的终态相同；同时，它变为终态时调用的回调函数参数与那个`Promise`变为终态时的回调函数的参数是相同的。

    ```cpp
    new Promise((resolve, reject) => {
        resolve();
    })
    .then(() => {
        return new Promise(resolve => {
            setTimeout(resolve, 2000, 'ok');
        });
    })
    .then( (result) => {console.log(result)}); //ok
    ```

    注意：这里是`then`方法中被调用回调函数的返回值与`then`方法返回的`Promise`对象状态之间的关系。

**Promise.prototype.catch(onRejected)**

1.  语法

    ```cpp
    p.catch(onRejected);

    p.catch(function(reason) {
        // 拒绝
    });
    ```

2.  含义
    `Promise.prototype.catch`方法是`.then(null, rejection)`的别名，用于指定发生错误时的回调函数，返回一个新的`promise`对象。

3.  用法

    ```cpp
    const p = new Promise((resolve,reject) => {
        return reject(new Error('err')); //reject 方法的作用，等同于抛出错误
        //throw new Error('err');
    });

    p.then(null, (err) => {
        console.log(err);  //Err: err
    });

    //--------等价写法---------
    p.catch(err => {
        console.log(err); //Err: err
    })
    ```

    注意：由于`.catch`方法是`.then(null, rejection)`的别名，故`.then`中的链式操作(3)、返回值(4)等语法在`.catch`中都适用。

4.  一般总是建议，`Promise` 对象后面要跟`catch`方法，这样可以处理 `Promise`内部发生的错误。`catch`方法返回的还是一个 `Promise` 对象，因此后面还可以接着调用`then`方法。

5.  `Promise`对象的错误具有“冒泡”性质，会一直向后传递，直到被捕获为止。也就是说，错误总是会被下一个`catch`语句捕获。 即：当前`catch`方法可以捕获上一个`catch`方法(包括上一个`catch`)到当前`catch`(不包括当前`catch`)方法之间所有的错误，如果没有错误，则当前`catch`方法不执行。

    ```cpp
    new Promise(() => {
        throw new Error('err1');
    })
        .then(() => {console.log(1);})
        .then(() => {console.log(2);})
        .catch((err) => {
            console.log(err); //Err: err1
            throw  new Error('err2');
        })
        .catch((err) => {console.log(err);})//Err: err2
    ```

6.  一般来说，不要在`then`方法里面定义`Reject`状态的回调函数（即`then`的第二个参数），总是使用`catch`方法。

    ```cpp
    // bad
    new Promise()
        .then((data) => {/* success */ }, (err) => {/* error */ });

    // good
    new Promise()
        .then((data) => { /* success */ })
        .catch((err) => {/* error */ });
    ```

    上面代码中，第二种写法要好于第一种写法，理由是第二种写法可以捕获前面`then`方法执行中的错误，也更接近同步的写法。

7.  与传统的`try/catch`代码块不同的是，即使没有使用`catch`方法指定错误处理的回调函数，`Promise` 对象抛出的错误也不会中止外部脚本运行。

    ```cpp
    new Promise((resolve) => {
        // 下面一行会报错，因为 x 没有声明
        resolve(x + 2);
    }).then(() => {
        console.log('ok');
    });
    setTimeout(() => {console.log('over')});
    //Uncaught (in promise) ReferenceError: x is not defined
    //over
    ```

8.  在异步函数中抛出的错误不会被`catch`捕获到

    ```cpp
    new Promise((resolve, reject) => {
        setTimeout(() => {
            throw 'Uncaught Exception!';
        }, 1000);
    }).catch(() => {
        console.log('err'); //不会执行
    });

    new Promise((resolve, reject) => {
        setTimeout(() => {
            reject();
        }, 1000);
    }).catch(() => {
        console.log('err'); //err
    });
    ```

9.  在`resolve()`后面抛出的错误会被忽略

    ```cpp
    new Promise((resolve, reject) => {
        resolve();
        throw 'Silenced Exception!';
    }).catch(function(e) {
        console.log(e); // 不会执行
    });
    ```

**Promise.all(iterable)**

1.  语法
    `var p = Promise.all([p1, p2, p3]);`

2.  含义
    `Promise.all`方法接受一个数组作为参数，`p1`、`p2`、`p3`都是 `Promise` 实例，如果不是，就会先调用下面讲到的`Promise.resolve`方法，将参数转为 `Promise` 实例，再进一步处理。（`Promise.all`方法的参数可以不是数组，但必须具有 `Iterator` 接口，且返回的每个成员都是 `Promise` 实例。）
    `p`的状态由`p1`、`p2`、`p3`决定，分成两种情况。
    (1) 只有`p1`、`p2`、`p3`的状态都变成`fulfilled`，`p`的状态才会变成`fulfilled`，此时`p1`、`p2`、`p3`的返回值组成一个数组，传递给`p`的回调函数。
    (2) 只要`p1`、`p2`、`p3`之中有一个被`rejected`，`p`的状态就变成`rejected`，此时第一个被`reject`的实例的返回值，会传递给`p`的回调函数。

3.  用法

    ```cpp
    const p1 = 'p1-ok';
    const p2 = Promise.resolve('p2-ok');
    const p3 = new Promise((resolve) => setTimeout(resolve, 3000, 'p3-ok'));
    const p4 = Promise.reject('p4-err');
    Promise.all([p1, p2, p3])
        .then((resolves) => {
          resolves.forEach(resolve => {
              console.log(resolve); //p1-ok   p2-ok  p3-ok
          });
        })
        .catch(() => {
          console.log('err');
        });

    Promise.all([p1, p2, p3, p4])
        .then(() => {
          console.log('ok');
        })
        .catch((err) => {
           console.log(err); //p4-err
        })
    ```

**Promise.race(iterable)**

1.  语法
    `var p = Promise.race([p1, p2, p3]);`

2.  含义
    `Promise.race`方法同样是将多个`Promise`实例，包装成一个新的`Promise`实例。只要`p1`、`p2`、`p3`之中有一个实例率先改变状态，`p`的状态就跟着改变。那个率先改变的 `Promise` 实例的返回值，就传递给`p`的回调函数。
    `Promise.race`方法的参数与`Promise.all`方法一样，如果不是 `Promise` 实例，就会先调用下面讲到的`Promise.resolve`方法，将参数转为`Promise`实例，再进一步处理。

3.  用法

    ```cpp
    var p1 = new Promise(resolve => {setTimeout(resolve, 500, "one");});
    var p2 = new Promise(resolve => {setTimeout(resolve, 100, "two");});
    Promise.race([p1, p2])
        .then(value => {
            console.log(value); // "two"
        });

    var p3 = new Promise(resolve => {setTimeout(resolve, 100, "three");});
    var p4 = new Promise((resolve, reject) => {setTimeout(reject, 500, "four");});

    Promise.race([p3, p4])
        .then((value) => {
            console.log(value); // "three"
        })
        .catch(err => {
            // 未被调用  
        });

    var p5 = new Promise(resolve => {setTimeout(resolve, 500, "five");});
    var p6 = new Promise((resolve, reject) => {
              setTimeout(reject, 100, "six");
          });

    Promise.race([p5, p6])
        .then((value) => {
          // 未被调用             
        }).catch((reason) => {
            console.log(reason); // "six"
        });
    ```

**Promise.resolve(value)**

1.  语法

    ```cpp
    Promise.resolve(value);
    Promise.resolve(promise);
    Promise.resolve(thenable);
    ```

    `Promise.resolve`等价于下面的写法。

    ```cpp
    Promise.resolve(value) ;
    // 等价于
    new Promise(resolve => resolve(value));
    ```

2.  含义
    返回一个状态由给定`value`决定的`Promise`实例。

3.  用法
    (1) 如果该值是一个`Promise`对象，则直接返回该对象；

    ```cpp
    const p = new Promise((resolve) => {resolve()});
    const p2 = Promise.resolve(p);
    console.log(p === p2); //true
    ```

    (2) 如果参数是`thenable`对象(即带有`then`方法的对象)，则返回的`Promise`对象的最终状态由`then`方法的执行决定；

    ```cpp
    const thenable = {
        then(resolve, reject) {
            resolve(42);
        }
    };
    Promise.resolve(thenable)
        .then((value) => {
            console.log(value);  // 42
        });
    ```

    (3) 如果参数是不具有`then`方法的对象或基本数据类型，则返回的`Promise`对象的状态为`fulfilled`，并且将该参数传递给`then`方法。

    ```cpp
    Promise.resolve('Hello')
        .then((s) => {
            console.log(s); //Hello
        });
    ```

    (4) 如果不带有任何参数，则返回的`Promise`对象的状态为`fulfilled`，并且将`undefined`作为参数传递给`then`方法。

    ```cpp
    Promise.resolve()
        .then((s) => {
            console.log(s); //undefined
        });
    ```

4.  通常而言，如果你不知道一个值是否是`Promise`对象，使用`Promise.resolve(value)` 来返回一个`Promise`对象,这样就能将该`value`以`Promise`对象形式使用。

5.  立即`resolve`的`Promise`对象，是在本轮“事件循环”（`event loop`）的结束时，而不是在下一轮“事件循环”的开始时。

    ```cpp
    setTimeout(function () {
        console.log('three');
    }, 0);

    Promise.resolve().then( () => {
        console.log('two');
    });

    console.log('one');
    // one 
    // two
    // three
    ```

**Promise.reject(reason)**

1.  语法

    ```cpp
    Promise.reject(reason);
    ```

    `Promise.reject`等价于下面的写法。

    ```cpp
    var p = Promise.reject(reason);
    // 等同于
    var p = new Promise((resolve, reject) => reject(reason));
    ```

2.  含义
    返回一个状态为`rejected`的`Promise`对象，并将给定的失败信息传递给对应的处理方法。

> **注意：`Promise.resolve(value)`方法返回的`Promise`实例的状态由`value`决定，可能是`fulfilled`，也可能是`rejected`。`Promise.reject(reason)`方法返回的`Promise`实例的状态一定是`rejected`。**

3.  用法

    ```cpp
    Promise.reject("Testing static reject")
        .then((value) => {
            // 未被调用
        }).catch((reason) => {
            console.log(reason); // Testing static reject
        });

    Promise.reject(new Error("fail"))
        .then((value) => {
            // 未被调用
        }).catch((error) => {
            console.log(error); // Error: fail
        });
    ```

4.  `Promise.reject()`方法的参数，会原封不动地作为`reject`的理由，变成后续方法的参数。这一点与`Promise.resolve`方法不一致。

    ```cpp
    const thenable = {
        then(resolve) {
            resolve('ok');
        }
    };

    Promise.resolve(thenable)
        .then(e => {
            console.log(e === 'ok'); //true
        });

    Promise.reject(thenable)
        .catch(e => {
            console.log(e === thenable); // true
        });
    ```