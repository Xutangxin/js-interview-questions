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