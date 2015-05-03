# 实现数据绑定的思路(2)

当model上的一个属性与多个元素进行数据绑定的时候，要通知到所有的视图更新，达到这个上的，可以设计成一个订阅和发布系统。
每个model 的属性创建一个发布中心，每个元素与该 model 属性进行数据绑定时订阅该属性，当数据更新时发布这个更新的消息以
通知所有的 view 更新。

* `Dispatcher` 类的实现:

```typescript

/**
 * 视图更新的方法 
 */
interface Listener {
    (newValue: any, oldValue: any): void;
}

/**
 * 事件派发器类，包含订阅的方法和发布消息的方法
 */
class Dispatcher {
	private listeners: { [index: string]: Listener[] };

	/**
	 * 根据某个属性注册一个视图更新的方法
	 */
    public addListener(name: string, listener: Listener): void {
        if (!this.listeners) {
            this.listeners = {};
        }

        if (!this.listeners[name]) {
            this.listeners[name] = [];
        }

        let listeners = this.listeners[name];
        let index: number = listeners.indexOf(listener);

        if (index < 0) {
            listeners.push(listener);
        }
    }

	/**
	 * 某个属性改变发布这个属性改变的消息，调用该属性注册的所有的视图更新方法
	 */
    public dispatch(name: string, newValue: any, oldValue: any): void {
        if (!this.listeners || !this.listeners[name] || this.listeners[name].length === 0) {
            return;
        }

        let listeners: Listener[] = this.listeners[name];

        listeners.forEach((listener) => {
            listener(newValue, oldValue);
        });
    }
}
```

* 为 model 创建一个 `Dispatcher` 类实例，解析数据绑定的指令后生成一对应于每个元素自己的视图更新方法，并订阅对应的
model 属性的数据更新。

```typescript
let dispatcher;

function bind(el: HTMLElement, model: Object): void {
    let prop: string = el.getAttribute("data-bind");

    let value: any = model[prop];
	
	if (!dispatcher) {
		dispatcher = new Dispatcher();
	}

    // 生成一个视图更新方法
    function listener(newValue) {
        el.innerHTML = newValue;
        console.log(prop, "changed");
    }

	// 订阅该属性
    dispatcher.addListener(prop, listener);

    let descriptor = Object.getOwnPropertyDescriptor(model, prop);

    // 检查是否已经定义过 setter 了，这个有点不太谨慎，不过目前能实现我们的目的
    if (!descriptor || (typeof descriptor.set !== 'function' && typeof descriptor.get !== 'function')) {

        // 定义 setter/getter
        Object.defineProperty(model, prop, {

            set(newValue: any) {
                if (newValue !== value) {
                    let oldValue = value;

                    value = newValue;

                    // 数据更新然后派发消息
                    dispatcher.dispatch(prop, newValue, oldValue);
                }
            },

            get() {
                return value;
            },

            configurable: true,
            enumerable: true
        });
    }
}
```

## 一个示例
```html
<div data-bind="content"></div>
<div data-bind="content"></div>
```
```typescript
var model = {content: ''};

[].slice.call(document.querySelectorAll('[data-bind'])).forEach((el) => {
    bind(el, model);
});

model.content = '123'; // 这句执行后，两个 div 的 innerHTML都更新为 '123'
```