# 学习笔记

### Proxy 实现 reactive 双向绑定
    > - 收集依赖
    > - 触发绑定

### 如何给依赖添加绑定

    > `effect` 处理 `callback` 时可以收集 属性引用，在此时将 `callback` 和 `属性` 绑定起来

    > 优化点：如何避免重复收集依赖、如何按需触发依赖。

    ``` js
    // 对象
    let obj = {
        a: 1
    }

    // 响应式对象
    let po = reactive(obj)

    // 绑定回调
    effect(() => {
        console.log(po.a)
    })

    let effect = (callback) => {
        // 依赖和回调 绑定在一起
    }

    // 响应式
    let reactive = (object) => {
        return new Proxy (object, {
            get (obj, prop) {
                // 收集依赖
            },
            set (obj, prop, value) {
                // 触发回调
            }
        })
    }
    ```

### 基本拖拽
> - 当move事件挂载在drag节点上，拖动过快时容易脱离挂掉，所以应该挂载在document上。
> - 利用css 的 transform 可以最优性能，不会引起重排

``` js
let drag = document.getElementById('drag')

// drag开始位置
let baseX = 0, baseY = 0

drag.addEventListener('mousedown', event => {
    // 鼠标开始位置
    let startX = event.clientX, startY = event.clientY

    let up = event => {
        // 记住中间位置
        baseX = baseX + event.clientX - startX
        baseY = baseY + event.clientY - startY

        document.removeEventListener('mousemove', move)
        document.removeEventListener('mouseup', up)
    }
    let move = event => {
        drag.style.transform = `translate(${baseX + event.clientX - startX}px, ${baseY + event.clientY - startY}px)`
    }

    document.addEventListener('mousemove', move)
    document.addEventListener('mouseup', up)
})
```

### 利用Range实现正常流拖拽

- 获取文档所有的Range列表
- 根据drag的(x, y)坐标计算Range列表中最近的一个range
- 在最近的range后面插入drag `range.insertNode(drag)`