# 《JavaScript设计模式与开发实践》读书笔记

## 第一章-多态的运用
> 1.26如何科学使用多态


- 多态的最根本好处在于，你不必再向对象询问“你是什么类型”而后根据得到的答
案调用对象的某个行为——你只管调用该行为就是了，其他的一切多态机制都会为你安
排妥当。

> 普通写法

```js
var googleMap = {
    show: function(){
    console.log( '开始渲染谷歌地图' );
    }
};
var baiduMap = {
    show: function(){
    console.log( '开始渲染百度地图' );
    }
};

// 函数以字符串形式的传参形式
// 如果判断条件一旦增多，就会变全是if-else语句
var renderMap = function( type ){
    if ( type === 'google' ){
        googleMap.show();
    }else if ( type === 'baidu' ){
        baiduMap.show();
    }
};

renderMap( 'google' ); // 输出：开始渲染谷歌地图
renderMap( 'baidu' ); // 输出：开始渲染百度地图
```

> 改进写法，多态适用于同种模式

```js
var googleMap = {
    page: 10,
    show: function(){
        console.log( '开始渲染谷歌地图' );
    }
};
var baiduMap = {
    page: 1,
    show: function(){
        console.log( '开始渲染百度地图' );
    }
};

// 将render传入的参数换成相应的对象
var renderMap = function( map ){
    if ( map.show instanceof Function ){
        map.show();
        console.log(mape.page);
    }
};

renderMap( googleMap ); // 输出：开始渲染谷歌地图 10
renderMap( baiduMap ); // 输出：开始渲染百度地图 1
```

# 第四章-单例模式

- 1.不需要进来就进行初始化
- 2.只需要在头一次点击的时候初始化

## 实例：点击按钮生成浮窗案例
```js
// 1.不需要进来就进行初始化，2.只需要在头一次点击的时候初始化
var createLoginLayer = (function(){
    var div;
    return function(){
        if ( !div ){
            div = document.createElement( 'div' );
            div.innerHTML = '我是登录浮窗';
            div.style.display = 'none';
            document.body.appendChild( div );
        }
        return div;
    }
})();

document.getElementById( 'loginBtn' ).onclick = function(){
    var loginLayer = createLoginLayer();
    loginLayer.style.display = 'block';
};
```

# 改造完的单例模式

```js
        var getSingle = function( fn ){
            var result;// 为了存储变量
            return function(){
                // 改变this指向
                // 通过arguments可以将未知的参数按照原样传入
                return result || (result = fn.apply(this, arguments));
            }
        };

        var output = function () {
            console.log(1);// 该语句只会触发一次
            return this;// 方便链式调用，与存储
        };

        var singleOutput = getSingle(output);
```

# 第五章-策略模式
- 策略模式的定义是：定义一系列的算法，把它们一个个封装起来，并且使它们可以相互替换。

```js
/*
 * 编写一个名为calculateBonus 的函数来计算每个人的奖金数额,需要接收两个参数：
 * 员工的工资数额和绩效考核等级。
 */
var calculateBonus = function( performanceLevel, salary ){

    // 问题是充斥着if语句
    if ( performanceLevel === 'S' ){
        return salary * 4;
    }

    if ( performanceLevel === 'A' ){
        return salary * 3;
    }

    if ( performanceLevel === 'B' ){
        return salary * 2;
    }
};

calculateBonus( 'B', 20000 ); // 输出：40000
calculateBonus( 'S', 6000 ); // 输出：24000
```
## 缺点
- calculateBonus 函数比较庞大，包含了很多if-else 语句，这些语句需要覆盖所有的逻辑
分支。
- calculateBonus 函数缺乏弹性，如果增加了一种新的绩效等级C，或者想把绩效S 的奖金
系数改为5，那我们必须深入calculateBonus 函数的内部实现，这是违反开放封闭原则的。
- 算法的复用性差，如果在程序的其他地方需要重用这些计算奖金的算法呢？我们的选择
只有复制和粘贴。

# 改进
- 以json对象的形式除去json
```js
var strategies = {
    "S": function( salary ){
        return salary * 4;
    },
    "A": function( salary ){
        return salary * 3;
    },
    "B": function( salary ){
        return salary * 2;
    }
};


var calculateBonus = function( level, salary ){
    return strategies[ level ]( salary );
};

console.log( calculateBonus( 'S', 20000 ) ); // 输出：80000
console.log( calculateBonus( 'A', 10000 ) ); // 输出：30000
```
## 实例
- 这段代码如果要增加按键，就要添加else-if语句
```js
 $(document).on("keydown", function(event) {

    if(event.keyCode === 13) {// 回车键
        console.log(13)
    } else if (event.keyCode === 39) {// 向右
        console.log(39)
    } else if(event.keyCode === 37){// 向左
        console.log(37)
    } else if (event.keyCode === 40) {
        console.log(40)
    }
});
```
## 改进实例

```js
// 一个个独立的配置项
var codeConfig = {
    "13": function() {
        console.log(13);
    },
    "39": function() {
        console.log(39);
    },
    "37": function() {
        console.log(37);
    },
    "40": function() {
        console.log(40);
    }
}

// 这部分不需要改动，只需要改动配置项
$(document).on("keydown", function(event) {
    if (codeConfig[event.keyCode]) {
        codeConfig[event.keyCode]();
    }
});
```

# 第六章-代理模式 

- 代理模式就是在主函数执行之前做一些其他操作，使整个函数体保持低耦合，高内聚，单一功能

# 普通模式进行图片路径替换
```js
var MyImage = (function () {
    var imgNode = document.createElement('img');
    document.body.appendChild(imgNode);
    var img = new Image;
    img.onload = function () {
        imgNode.src = img.src;
    };
    return {
        setSrc: function (src) {
            imgNode.src = 'file:// /C:/Users/svenzeng/Desktop/loading.gif';
            img.src = src;
        }
    }
})();
MyImage.setSrc('http:// imgcache.qq.com/music/photo/k/000GGDys0yA0Nk.jpg');
```
> 实现一个小小的图片预加载功能，即使不需要引入任何模式也能办到，那么引入代理模式的好处究竟在哪里呢？
# 代码模式实例
```js
    // 防止重复生成元素，只开放一个设路径的src       
    var myImage = (function () {
        var imgNode = document.createElement('img');
        document.body.appendChild(imgNode);
        return {
            setSrc: function (src) {
                imgNode.src = src;
            }
        }
    })();

    // 代理层
    var proxyImage = (function () {
        var img = new Image;
        img.onload = function () {
            // 外图加载成功后即可再次设置链接
            myImage.setSrc(this.src);
        } 
        return {
            setSrc: function (src) {
                // 第一次设置菊花图
                myImage.setSrc('https://www.baidu.com/img/baidu_jgylogo3.gif');
                img.src = src;
            }
        }
    })();

    proxyImage.setSrc('http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071001.gif');
```
# 代理模式下的函数节流

```js
        var sendMessage = function (message) {
            console.log(message)
        };

        // 节流
        var throttle = function (callback, time) {
            var fn = callback;

            return function () {
                if (fn) {
                    // 保持this指向与参数的正常传递
                    fn.apply(this, arguments);

                    // 清空回调
                    fn = null;

                    setTimeout(function () {
                        fn = sendMessage;
                    }, time || 1000);
                }
            };
        };

        var demo = throttle(sendMessage, 2000);

        $("#loginBtn").click(function () {
            demo("我发消息了");
        });
```

# 第八章-发布订阅模式

- 实例网站登录
- 网站登录后，会通过异步请求拿到数据，然后对页面各部分比如购物车，订单，用户名，搜藏等进行渲染,使函数解耦，不需要在原函数处添加代码。

## 这里普通的方式需要改登录模块的代码
> 如果此时别人添加了一个收获地址的模块，就需要动你的登录模块代码
```js
// 登录模块的代码
// 目前的模式，组装模式
login.succ(function (data) {

    // 这是针对具体实现编程的典型例子，针对具体实现编程是不被赞同的。
    header.setAvatar(data.avatar); // 设置header 模块的头像
    nav.setAvatar(data.avatar); // 设置导航模块的头像
    message.refresh(); // 刷新消息列表
    cart.refresh(); // 刷新购物车列表
});

// 如果又增加了一个收货地址的模块,需要不断堆积木
login.succ(function (data) {
    header.setAvatar(data.avatar);
    nav.setAvatar(data.avatar);
    message.refresh();
    cart.refresh();
    address.refresh(); // 既增加这行代码，又要在别的地方增加函数
});
```

# JQuery订阅模式实例

```js
        $("#loginBtn").click(function() {
            obj.trigger("aaa");
        });

        var obj = $({});

        obj.on("aaa", function () {
            console.log(111111111111);
        })

        obj.on("aaa", function () {
            console.log(222222222222);
        })
```

# 第十一章-模板方法模

> 模板方法模式是将一套逻辑的各个步骤都规定好，你只需要填写不同参数与回调函数体即可。

```js
// 建立一个请求ajax并渲染数据的一个模板
var RenderDom = function (obj) { 

    // 安全构造函数
    if(!(this instanceof RenderDom)) {
        return new RenderDom(obj);
    }
};

RenderDom.prototype.getData = function () {
    console.log('请求异步数据');
};

// 渲染模板生成html字符串
RenderDom.prototype.renderTemp = function () { 
    console.error("还未确定渲染模板");
};

// 渲染模板
RenderDom.prototype.input = function () { 
    console.error("还未确定填充对象");
};

RenderDom.prototype.init = function () {
    this.getData();
    this.renderTemp();
    this.input();
};

// 开始继承，这边就不再需要罗列初始化步骤
var F7Render = function (obj) {};
F7Render.prototype = new RenderDom();
F7Render.prototype.renderTemp = function() {
    console.log("div1")
};
F7Render.prototype.input = function () {
    console.log("input2")
};

var f7 = new F7Render();
f7.init();
```

# 钩子方法的使用

- 钩子方法（hook）可以用来解决这个问题，放置钩子是隔离变化的一种常见手段

# 第十四章 - 中介者模式

> 当一个事物受到两个及以上事物或者变量的干扰时，将其判断逻辑抽离出来独立进行操作。

> 可以用闭包进行元素缓存

```js
var mediator = (function () {
    var colorSelect = document.getElementById('colorSelect'),
        memorySelect = document.getElementById('memorySelect'),
        numberInput = document.getElementById('numberInput'),
        colorInfo = document.getElementById('colorInfo'),
        memoryInfo = document.getElementById('memoryInfo'),
        numberInfo = document.getElementById('numberInfo'),
        nextBtn = document.getElementById('nextBtn');
    return {
        changed: function (obj) {
            var color = colorSelect.value, // 颜色
                memory = memorySelect.value,// 内存
                number = numberInput.value, // 数量
                stock = goods[color + '|' + memory]; // 颜色和内存对应的手机库存数量
            if (obj === colorSelect) { // 如果改变的是选择颜色下拉框
                colorInfo.innerHTML = color;
            } else if (obj === memorySelect) {
                memoryInfo.innerHTML = memory;
            } else if (obj === numberInput) {
                numberInfo.innerHTML = number;
            }
            if (!color) {
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择手机颜色';
                return;
            }
            if (!memory) {
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择内存大小';
                return;
            }
            if (((number - 0) | 0) !== number - 0) { // 输入购买数量是否为正整数
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请输入正确的购买数量';
                return;
            }
            nextBtn.disabled = false;
            nextBtn.innerHTML = '放入购物车';
        }
    }
})

```

# 第十五章 - 装饰者模式

> 通过前后添加after以及before来处理杂碎的事物。

# 案例
```js
var showLogin = function () {
        console.log('打开登录浮层');
       console.log('发送埋点信息');
    }
```
# 改进
```js
    // 需要挂载在Function原型之上。
    Function.prototype.after = function (afterfn) {
        var __self = this;
        return function () {
            var ret = __self.apply(this, arguments);
            afterfn.apply(this, arguments);
            return ret;
        }
    };
    var showLogin = function () {
        console.log('打开登录浮层');
    }
    var log = function () {
        console.log('上报标签为: ' + this.getAttribute('tag'));
    }
    // 用语义化的方式添加后置触发函数
    showLogin = showLogin.after(log); // 打开登录浮层之后上报数据
    document.getElementById('button').onclick = showLogin;
```

# 第十六章 - 状态模式

```js
        var Light = function () {
            this.currState = FSM.off; // 设置当前状态
            this.button = null;
        };
        Light.prototype.init = function () {
            var button = document.createElement('button'),
                self = this;
            button.innerHTML = '已关灯';
            this.button = document.body.appendChild(button);

            
            this.button.onclick = function () {
                self.currState.buttonWasPressed.call(self); // 把请求委托给FSM 状态机
            }
        };

        // 状态机
        var FSM = {
            off: {
                buttonWasPressed: function () {
                    console.log('关灯');
                    this.button.innerHTML = '下一次按我是开灯';
                    this.currState = FSM.on;
                }
            },
            on: {
                buttonWasPressed: function () {
                    console.log('开灯');
                    this.button.innerHTML = '下一次按我是关灯';
                    this.currState = FSM.off;
                }
            }
        };
        var light = new Light();
        light.init();
```

# 第十七章 - 适配器模式

> 在调用前进行标准化处理

```js
var googleMap = {
show: function(){
console.log( '开始渲染谷歌地图' );
}
};
var baiduMap = {
display: function(){
console.log( '开始渲染百度地图' );
}
};
var baiduMapAdapter = {
show: function(){
return baiduMap.display();
}
};
renderMap( googleMap ); // 输出：开始渲染谷歌地图
renderMap( baiduMapAdapter ); // 输出：开始渲染百度地图
```

# 例：后台返回数据格式不一致

> 返回我需要的，一致合理的数据

```js
// 处理生产模块的数据信息
fn.processData = function (config, data) {

    // 生产管理号。历史数据productionSeqNum
    var productId = "productionSeqNum";

    // 特性列表默认参数
    var enums = {
        configCode: "--",// 配置编号 
        configText: "特性",// 配置描述
        valueText: "--",//配置详情描述
        fileName: ""// 图片文件名
    };

    // 无数据的数据初始化
    if (kit.isEmpty(data) === true) {
        return {
            configEnums: [enums, enums],// 特性列表，最多两个特性
            productNum: "",// 产品标识号
            productId: "",// 生产管理号。历史数据productionSeqNum
            productNumFormat: "--",// 产品标识号
            productIdFormat: "--",// 生产管理号。历史数据productionSeqNum
            carDescription: "--",// 产品规格
            remarkremark: "--",// 备注
        };
    }

    // 填充空数据为"--",防字段undefined或者null报错
    kit.forEach(data, function (value, key) {
        if (value === null || value === undefined) {
            data[key] = "--";
        }
    });

    // 键值消歧
    data.productId = data.productId || data[productId];

    // 生成简短型产品标识号，生产管理号。
    data.productIdFormat = data.productId ? data.productId.slice(-5) : "";
    data.productNumFormat = data.productNum ? data.productNum.slice(-6) : "";

    // 截断
    data.remark = data.remark.slice(0, 50);
    // data.carDescription = data.carDescription.slice(0, 18);

    // 处理图片链接
    data.picture = this.processImg(config, data);

    // 处理特性列表，目前只展示两个且要参照configCodes
    data.configEnums = this.processEnums(config, data);

    kit.forEach([0, 1], function (value, key) {
        if (data.configEnums[key] === undefined) {
            data.configEnums[key] = enums;
        }
    });

    return data;
};


```
