# 实现数据绑定的思路(1)

## 数据(model)改变，通知视图(view)更新

一个 HTML:

```html

<div id="view"></div>
```

一段脚本:

```typescript
let view = document.getElementById('view');
let model = {counter: 0};

setInterval(() => {
	model.counter += 1;
	updateView();
}, 1000);

function updateView() {
	view.innerHTML = '秒表 : ' + model.counter ;
}
```

## 通过数据绑定实现自动化

* 定义一个数据绑定的语法，创建与该语法对应的视图更新方法
* 修改属性的 setter，使在属性变更时能接收到变更的事件并触发更新视图的方法

HTML：
```html
<!-- data-bind 为一个数据绑定的指令 -->
<div id="content" data-bind="a"></div>
```

脚本：
```typescript
function bind(el: HTMLElement, model: Object): void {
    // 获取数据绑定的对应属性
    let prop: string = el.getAttribute("data-bind");

    // 初始化数据
    let value: any = model[prop];

    // 定义该属性的 setter
    Object.defineProperty(model, prop, {
        set(newValue: any) {
            if (newValue !== value) {
                // 数据改变后自动调用视图更新方法
                value = newValue;
                el.innerHTML = value;
            }
        },
        get() {
            return value;
        },
        configurable: true,
        enumerable: true
    });
}

let myModel = {};

bind(document.getElementById("content"), myModel);

// div#content will be 'a'
myModel.a = 'a';
```