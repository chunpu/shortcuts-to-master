介绍
---

`_.random`, `_.sample`, `_.shuffle` 是三个在 lodash 中和随机相关的工具函数, 这三个函数在 underscore 也都存在

`_.random`
---

```js
_.random(1, 4)
// 返回1, 2, 3, 4 中的一个, 比如4
```

这是个非常实用的函数, 为什么这么说呢, 因为笔者不知道 lodash 之前, 每次写项目的 util 都会写一个类似的函数

```js
_.random = function(min, max) {
	return min + Math.floor(Math.random() * (max - min + 1))
}
```

在笔者实际业务中, 直接使用 `Math.random()` 的机会很少, 大部分都是要明确的取, 0到多少的整数

underscore 和 lodash 都支持单参数: `_.random(x) = _.random(0, x)`

lodash 的 random 支持浮点数, 而且比 underscore 多了一个参数, `_.random([min=0], [max=1], [floating])`

```js
_.random(1.2, 5.2)
// => 返回1.2 ~ 5.2 的某个浮点数, 比如4.5
```

lodash 自动判断提供的 min 和 max 有不是整数的, 就认为是浮点模式, 而且你也可以指浮点模式, 最后一个参数是 true 就行

```js
_.random(5, true)
// => 返回0 ~ 5 之间的浮点
```

下面的情况都是浮点模式

```js
_.random(5.2)
_.random(1, 5.2)
_.random(2.5, 5)
_.random(2.5, 5.2)
_.random(5, true)
```

random 的两个主要参数, 一个 min, 一个 max, 听起来 max 都是大于 min 的, 那如果 max 比 min 小会怎样呢

如果 max = min - 1, 可以推出 `_.random(min, min - 1) = min + Math.floor(Math.random() * (min - 1 - min + 1)) = min`, 始终返回 min 本身

而如果 max < min - 1, `Math.floor(Math.random() * (max - min + 1))` 始终小于 0, 因此返回值也会小于 min

而 `Math.random() * (max - min + 1) > max - min + 1 > 0`, 因此 `Math.floor(Math.random() * (max - min  + 1)) > max - min`, 可以推断出 返回值不仅肯定小于 min, 而且肯定大于 max

也就是说 _.random(min, max) max 比 min 小的时候, 总是随机返回他们中间的数, 不包含他们自身

例如: `_.random(5, 1)` 始终返回 2, 3, 4 中的一个
```

`_.sample`
---

sample 真的是一个救星, 简单来说就是从一个数组中随机返回一个值

```js
_.sample([1, 3, 5, 7])
// 返回1, 3, 5, 7 中的一个, 比如3
```

就是这么简单, 语义清晰, 取一个样品

sample 支持第二个可选参数, 就是返回几个样品, 这时候 sample 返回的就是一个数组了

```js
_.sample([1, 3, 5, 7], 2)
// 可能返回 [1, 3]
```

要注意的是, 即便第二个参数写的是1, sample 返回的也是一个数组

```js
_.sample([1, 3, 5, 7], 1)
// 可能返回 [7]
```



`_.shuffle`
---

洗牌, shuffle 的用途是把一个数组打乱, 也称洗牌

笔者经常碰到需要打乱数组的情况, 最常见就是把选择题的答案乱序排列

但笔者是这样实现随机数组的

```js
var arr = [1, 2, 3, 4]
arr.sort(function() {
	return Math.random() - 0.5
})
```

但没想到这种错误竟然被测试测了出来, 测试说, 感觉这个列表不是真的随机排列, 我很无语, 最后老老实实按照 lodash 实现了一下洗牌算法

这种写法虽然简单, 而且看起来作用差不多随机, 但实际上他的复杂度超过了正常的洗牌算法, 而且确实不是真正均衡的随机

洗牌算法是面试中非常经典的题目, 方法也有很多

lodash 使用的是 [Fisher-Yates shuffle](https://en.wikipedia.org/wiki/Fisher-Yates_shuffle)

TODO 各种洗牌算法撑场面!
