介绍
---


throttle 和 debounce 是两个防止频繁调用, 保护硬件性能的实用函数

他们好比是给函数加上了冷却时间. 在很多游戏比如 dota, wow, 我们都有着很多技能, 大部分技能都会有一个冷却时间, 如果游戏允许我们没有冷却的频繁发动技能, 那游戏肯定乱套了.

throttle 和 debounce 在对用户输入, 或者事件响应上格外有用


### 名词解释

throttle 的中文意思是限流, 掐紧.

debounce 是 bounce 的反义词, bounce 是反弹, 弹跳的意思, bounce 的反义词 debounce 就是消除抖动, 比如一些越野装置, 山地车, 为了保护主人, 他们的坐垫等装置通常会有缓冲防颠簸的效果


### 使用区别

throttle 可以想象成班车, 每10分钟一辆, 早来的人只能在站台上等, 最多等10分钟, 运气好的可以碰巧就赶上车

配图:公交车

debounce 可以想象成电梯, 电梯门开的时候, 如果10秒都没人按上下按钮, 则自动关闭. 相反, 如果有人按了按了上楼的按钮, 电梯门就继续打开, 再等待10秒, 也就是说人如果源源不断的来, 电梯门就一直关不上, 每天上班前10分钟, 大家都拿着早餐抢电梯, 往往就是这种场景, 让人急得满头是汗

配图:电梯

### 使用场景


#### scroll 事件

throttle 的使用场景可说是非常广泛, 比如滚动事件的监听, 用户每次滚动页面, 都会触发很多个 scroll 事件, 如果我们每次都去触发 scroll 的回调函数, 那会用户的电脑造成很大的压力, 吃掉大量硬件性能, 因此我们完成可以这么写

```js
$(window).on('scroll', _.throttle(function() {
	console.log('scroll')
}, 300))
```

这样的话, 即便用户滚动的再快再频繁, 我们最多也不过是300毫秒处理一次回调函数, 限制了极端情况的产生

笔者曾写过一个 [jQuery 滚动侦测插件](https://github.com/chunpu/scrollspy/), 这个滚动侦测插件主要用来处理一些资源懒加载, 即用户滚动页面到那个资源的时候, 才去加载这个资源, 大大提升了页面的初始化性能, 其中就用到了 throttle 函数来处理 resize, scroll 这些事件, 来避免对硬件的滥用

#### 防止过快的点击

笔者属于广告部门, 广告中有一个概念, 就是点击收费, 如果用户不停点击一个广告, 会很快消耗广告的库存, 给公司带来损失. 因此 throttle 也被用在广告点击上

```js
$(adElement).click(_.throttle(function() {
	sendPingback()
}, 1000))
```

上述代码表示用户不断点击广告元素, 我们也只会每一秒消耗一次广告库存, 有效的保护了公司财产

在知道 throttle 函数的之前, 笔者处理类似的逻辑需要保存两个时间变量, 一个是上次点击时间, 一个是上次发送监测时间, 并进行一定的数学运算才完成了类似的业务需求


类型
---

throttle 和 debounce 接受的第一个参数和返回的结果都是函数, 属于 `lodash/function`


控制参数
---

我们看到 throttle 和 debounce 前两个函数类似, 第一个是执行函数, 第二个是间隔时间, 但他们都有第三个可选参数, options

- leading, 第一次调用是否立即执行
- trailing, 最后一次调用是否执行
- maxWait, 最长等待时间 (仅 debounce 有)


### leading

当 leading 为 true 的时候, 第一次执行函数就会成功, 就好比第一个来的人开门红, 享受 vip 待遇, 立马为其服务. 如果设置为 false, 则只好和其他的人一样乖乖排队了

如果我们仅仅是为了防止函数频繁调用, 并且想不影响本来的效果, 那 leading 应该为 true

throttle 的 leading 默认值为 true, debounce 的 leading 默认值为 false


### trailing

当班车的末班车时间点已经过了, 后面已经不会再来人了, 但站台上依然有几个没赶上末班车的人, 如果 trailing 为 true, 司机就会好心的再拉一车, 如果为 false, 那就只能说拜拜了

throttle 和 debounce 的 leading 默认值都为 true


### maxWait

前面说到如果电梯持续不断的有人进来, 那电梯门永远都关不上, 这显然是不合理的, 因此 debounce 提供了 maxWait 参数, 如果电梯 maxWait 为一分钟, 那就算有人一直按着电梯按钮不让电梯关门, 电梯也会在一分钟后关门

可以看到这个 debounce 的 maxWait 和 throttle 很像, 因为 throttle 就是这样强制间隔执行. 如果有兴趣去翻看源码, 我们会发现 throttle 就是用 debounce 的 maxWait 实现的

```js
_.throttle = function(fn, wait, opt) {
	wait = wait || 0
	opt = _.extend({
		leading: true,
		trailing: true,
		maxWait: wait // wait 即 maxWait
	})
	return _.debounce(fn, wait, opt)
}
```

### 默认参数

throttle: leading 为 true, trailing 为 true

debounce: leading 为 false, trailing 为 true, 默认没有 maxWait 时间, 也就是说默认情况下, 电梯按钮一直被按着的话, 电梯门永远不会关闭


### cancel

throttle 和 debounce 不是单纯的返回一个函数, 这个函数还带有一个 cancel 属性, 值也是一个函数, 用于我们手动取消执行这个函数

```js
var throttled = _.throttle(function() {
	if (isIllegal) {
		throttled.cancel()
	}
}, 1000)
```

lodash api 官网推荐了这样一篇博客 [Debounce and Throttle: a visual explanation](http://drupalmotion.com/article/debounce-and-throttle-visual-explanation) 来介绍这两个函数的用法和区别
