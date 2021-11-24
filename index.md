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