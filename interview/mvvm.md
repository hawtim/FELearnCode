```html
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>mvvm</title>
</head>
<body>
    <div id="app">
        <input type="text" v-model="text">
        {{ text }}
        <button @click="reset" style="display:block;">重置</button>
    </div>
    <script src="./index.js"></script>
    <script>
        var vm = new Mvvm({
            el: 'app',
            data: {
                text: 'hello world'
            },
            methods: {
                reset() {
                    this.text = ''
                },
            },
        })
    </script>
</body>
</html>
```
```js
class Mvvm {
    constructor(options) {
        const {
            el,
            data,
            methods
        } = options
        this.methods = methods
        this.target = null
        // 初始化dispatcher
        this.observe(this, data)
        // 初始化watcher
        this.compile(document.getElementById(el))
    }

    observe(root, data) {
        for (const key in data) {
            this.defineReactive(root, key, data[key])
        }
    }

    defineReactive(root, key, value) {
        if (typeof value == 'object') {
            return this.observe(value, value)
        }
        const dep = new Dispatcher()
        Object.defineProperty(root, key, {
            set(newValue) {
                if (value == newValue) return
                value = newValue
                // 发布
                dep.notify(newValue)
            },
            get() {
                // 订阅
                dep.add(this.target)
                return value
            }
        })
    }

    compile(dom) {
        const nodes = dom.childNodes
        for (const node of nodes) {
            // 元素节点
            if (node.nodeType == 1) {
                const attrs = node.attributes
                for (const attr of attrs) {
                    if (attr.name == 'v-model') {
                        const name = attr.value
                        node.addEventListener('input', e => {
                            this[name] = e.target.value
                        })
                        this.target = new Watcher(node, 'input')
                        this[name]
                    }
                    if (attr.name == '@click') {
                        const name = attr.value
                        node.addEventListener('click', this.methods[name].bind(this))
                    }
                }
            }
            // text节点
            if (node.nodeType == 3) {
                const reg = /\{\{(.*)\}\}/
                const match = node.nodeValue.match(reg)
                if (match) {
                    const name = match[1].trim()
                    this.target = new Watcher(node, 'text')
                    this[name]
                }
            }
        }
    }
}

class Dispatcher {
    constructor() {
        this.watchers = []
    }
    add(watcher) {
        this.watchers.push(watcher)
    }
    notify(value) {
        this.watchers.forEach(watcher => watcher.update(value))
    }
}

class Watcher {
    constructor(node, type) {
        this.node = node
        this.type = type
    }
    update(value) {
        if (this.type == 'input') {
            this.node.value = value
        }
        if (this.type == 'text') {
            this.node.nodeValue = value
        }
    }
}
```