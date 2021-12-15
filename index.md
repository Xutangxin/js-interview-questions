js实现new的过程：  

1.创建空对象
2.将对象的__proto__连接到构造函数的prototype
3.绑定对象this
4.返回该对象
```js
function _new(constructor, ...args) {
    let obj = {}
    obj.__proto__ = constructor.prototype
    let res = constructor.apply(obj, args)
    if (Object.prototype.toString.call(res) === '[object Object]') return res
    else return obj
}
```

深拷贝：
```js
function deepClone(target) {
    if (typeof target === 'object') {
        let cloneTarget = Array.isArray(target) ? [] : {};
        for (const key in target) {
            cloneTarget[key] = deepClone(target[key]);
        }
        return cloneTarget;
    } else {
        return target;
    }
}
```

节流：
```js
function throttle(fn, delay) {
    let lastTime = 0;
    return function () {
        let nowTime = Date.now();
        if (nowTime - lastTime > delay) {
            fn.apply(this);
            lastTime = Date.now();
        }
    }
}
```

防抖：
```js
function debounce(fn, delay) {
    let timer = null;
    return function () {
        clearTimeout(timer);
        timer = setTimeout(() => {
            fn.apply(this)
        },delay);
    }
}
```

instanceof:
```js
function _instanceof(left, right) {
    let prototype = right.prototype
    left = left.__proto__
    while (true) {
        if (left === null) {
            return false
        }
        if (left === prototype) {
            return true
        }
        left = left.__proto__
    }
}
```

eventBus:   
简陋版：
```js
class EventBus {
    constructor() {
        this.events = {}
    }

    on(event, fn) {
        this.events[event] = fn
    }

    emit(event) {
        if (this.events[event]) {
            let fn = this.events[event]
            fn()
        }
    }

    off(event) {
        if (this.events[event]) {
            delete this.events[event]
        }
    }

    once(event, fn) {
        this.on(event, fn)
        this.emit(event)
        this.off(event)
    }
}
```
标准版：
```js
class Events {
  constructor() {
    this.events = new Map();
  }

  addEvent(key, fn, isOnce, ...args) {
    const value = this.events.get(key) ? this.events.get(key) : this.events.set(key, new Map()).get(key)
    value.set(fn, (...args1) => {
        fn(...args, ...args1)
        isOnce && this.off(key, fn)
    })
  }

  on(key, fn, ...args) {
    if (!fn) {
      console.error(`没有传入回调函数`);
      return
    }
    this.addEvent(key, fn, false, ...args)
  }

  fire(key, ...args) {
    if (!this.events.get(key)) {
      console.warn(`没有 ${key} 事件`);
      return;
    }
    for (let [, cb] of this.events.get(key).entries()) {
      cb(...args);
    }
  }

  off(key, fn) {
    if (this.events.get(key)) {
      this.events.get(key).delete(fn);
    }
  }

  once(key, fn, ...args) {
    this.addEvent(key, fn, true, ...args)
  }
}
```   

排序算法：   
快速排序
```js
function quickSort(arr) {
    if (arr.length <= 1) return arr
    let pivotIndex = Math.floor(arr.length / 2)
    let pivot = arr.splice(pivotIndex, 1)[0]
    let left = [], right = []

    for (let val of arr) {
        if (val < pivot) left.push(val)
        else right.push(val)
    }
    return quickSort(left).concat([pivot], quickSort(right))
}
```
冒泡排序：
```js
function bubbleSort(arr) {
    let len = arr.length;
    for (let i = 0; i < len; i++) {
        for (let j = 0; j < len - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
            }
        }
    }
    return arr;
}
```
插入排序：
```js
function insertionSort(arr) {
    let len = arr.length;
    let preIndex, curr;
    for (let i = 1; i < len; i++) {
        preIndex = i - 1;
        curr = arr[i];
        while (preIndex >= 0 && arr[preIndex] > curr) {
            arr[preIndex + 1] = arr[preIndex];
            preIndex--;
        }
        arr[preIndex + 1] = curr;
    }
    return arr;
}
```   
call实现：
```js
Function.prototype.myCall = function (context, ...args) {
    context = context || window
    let fn = Symbol()
    context[fn] = this
    let res = context[fn](...args)
    delete context[fn]
    return res
}
```
or:
```js
Function.prototype.myCall = function () {
    let args = Array.prototype.slice.call(arguments)
    let obj = args.shift()
    let self = this

    obj[fn] = self
    let res = obj[fn](...args)
    delete obj[fn]
    return res
}
```
apply:
```js
Function.prototype.myApply = function () {
    let args = [...arguments]
    let obj = args.shift()
    let self = this
    obj['fn'] = self

    let res
    if (args.length) res = obj['fn'](...args[0])
    else res = obj['fn']()
    delete obj['fn']
    return res
}
```
bind:
```js
Function.prototype.myBind = function () {
    let self = this
    let args = [...arguments]
    let obj = args.shift()

    return function () {
        return self.apply(obj, args)
    }
}
```