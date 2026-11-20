---
title: "极验js逆向"
date: 2026-11-20 09:43:45
categories:
  - "随笔"
tags:
  - "极验"
  - "JS逆向"
  - "验证码"
aging: true
aging_days: 30
---

极验还算简单,记录下过程

<!--more-->

# 分析请求

**初始页面**

初始页面加载了一些`js`,发送了三个`request`

reqeust | parame | response
---|---|---
`register-slide` | 时间戳 | `challenge`、`gt`
`gettype.php` | `gt`、`callback`(也是时间戳) | 一些`js`路径、`status`
`get.php` | `challenge`、`gt`、`w`、`callback` | `c`、`s`、`status`...

**点击按钮**

reqeust | parame | response
---|---|---
`ajax.php` | `challenge`、`gt`、`w` | `slide`、`status`
`get.php` | `gt`、`callback`、`type`、`callback` | `bg、slice、fullbg`(带缺口的背景图 滑块图 不带缺口背景图),`c`,`challenge`,`gt`,`s`...

**开始拖动**

reqeust | parame | response
---|---|---
`ajax.php` | `challenge`、`gt`、`w`、`callback` | 成功: `success: 1`、`message: success`、`score`、`validate`
`ajax.php` | `challenge`、`gt`、`w`、`callback` | 成功: `success: 0`、`message: fail`

**点击提交**

reqeust | parame | response
---|---|---
`demo/gt/validate-slide` | `geetest_challenge`、`geetest_validate`、`geetest_seccode` | `status`、`info`

PS： 前面的`w`置空也能请求成功，干最后一个`w`即可

# 调试

每次滑动滑对滑错都会发一个请求,跟栈进去打断点,一直跟会跟到`w`的定义处

PS:也可直接搜索特征码`"\u0077"`

`w = h+u`

## `u`

  1. 先找`u`(没有参数好弄一点) 打断点跟进，发现`return`了一个`e` -> `u`=`e` -> 看`e`的定义



`var e = new U()[$_CBFJN(392)](this[$_CBGAK(744)](t))` -> `new U()["encrypt"](this["$_CCEc"](t))`

PS:

  1. `typeof(t)=undefind，typeof(this[$_CBGAK(744)](t))=string` 提前看好变量类型 下面`return`避免出错
  2. 我多次调试发现`t()`断不住，后面多尝试了几次发现只有刷新页面第一次点击'点击...进行验证'才会断住，只刷新滑块不行



`this["$_CCEc"](t)` = `Ot`-> `Ot=rt()` -> `rt()`=`t()+t()+t()+t()` -> `t()`

`t()` = `return (65536 * (1 + Math['random']()) | 0)['toString'](16)['substring'](1)`

`this[$_CBGAK(744)](t)` = `t()`四次调用相加,改写替换即可
```js
function randomStr(){
    var data = '';
    for (var index = 0; index < 4; index++) {
        data += (65536 * (1 + Math["random"]()) | 0)["toString"](16)["substring"](1);
    }
    return data;
}
```

  2. new U()['encrypt'] 看到这个词大概猜是rsa


  1. 看U()这个类中的setPublic函数,可以找到公钥模数和公钥指数,得到公钥后我们可以自己复现对照一下看是否是标准rsa加密。
  2. 扣代码



这里选择扣代码， 因为我觉得扣代码比较简单🤭

## `h`
```js
l = V[$_CAHJS(392)](gt[$_CAIAK(254)](o), r[$_CAIAK(744)]())
h = m[$_CAIAK(792)](l)
```

### 参数 `l`

`r[$_CAIAK(744)]()` = `randomStr()`
`l = V["encrypt"](gt["stringify"](o), randomStr())`
手动解混淆后， 发现`gt["stringify"]()`跟`JSON.stringify()`作用一样，
也就是说要找到

  1. 参数`o`
  2. 加密方法`V["encrypt"]`



#### 参数 `o`

向上找几行即可找到o的定义处, 发现是个obj多打印几次看看哪些值有变化

4个参数发生变化
`userresponse`、`passtime`、`aa`、`rp`
找四个参数即可

##### `aa`

`aa` = `e`，`e`又是`function(t, e, n)`传进来的，往上跟栈，`e=l`

`l = n[$_DAAAU(985)][$_CJJJU(1075)](n[$_CJJJU(985)][$_CJJJU(1073)](), n[$_CJJJU(67)][$_CJJJU(1033)], n[$_DAAAU(67)][$_CJJJU(345)]);`
解混淆
`l = n["$_CICO"]["$_BBED"](n["$_CICO"]["$_FD_"](), n["$_CJk"]["c"], n["$_CJk"]["s"])`
大概就是一个函数传了三个参数
分别查看三个参数，发现后两个好像见过 _(在分析请求中)_ 进行对比发现是同一个值`c、s`，所以找第一个参数即可
第一个参数是一个函数返回的 进入函数分析
第一行开始做事的地方
```js
var t = function(t) {
    xxxx
}(this[$_BEGJO(359)])
```

打印`this[$_BEGJO(359)]`像是个轨迹值，包含16个数组的obj，在经过这个函数后返回了一个15个数组的obj
看到函数中做了一些运算，应该是对轨迹数组做了一些处理。
然后定义了三个空列表
```js
r = []
, i = []
, o = [];
```

最后return了
```js
return new ct(t)[$_BEHAO(59)](
function(t) {
    xxx
}),
r[$_BEGJO(405)]($_BEHAO(2)) + $_BEHAO(457) + i[$_BEGJO(405)]($_BEGJO(2)) + $_BEGJO(457) + o[$_BEGJO(405)]($_BEGJO(2));
```

解混淆后
```js
return new ct(t)[$_BEHAO(59)](func(t){}(t)),
r['join']('') + '!!' + i['join']('') + '!!' + o['join']('');
```

`new ct(t)[$_BEHAO(59)]() ` 创建了一个新对象,t是传给构造函数的参数, 调用该对象的`$_BEHAO(59)`方法，然后把传入了一个自执行函数,这个func把t做参数进行运算分别往三个空数组里`push`值，最后把返回三个数据的拼接值

改写加扣代码即可 传入轨迹组track

测试对不对
![测试](./极验/aa.png)

##### `userresponse`

`userresponse = H(t, i["challenge")`

  1. `i["challenge")`就是我们请求/get.php?时候返回的`challenge`
  2. `t`是通过`function(t, e, n)`传进来的 所以往上跟栈
在`r[$_DAAAU(1051)](u, l, n[$_DAAAU(852)])`调用了`function(t, e, n)`
也就是说 `t`=`u` = `_`
`_ = e ? n[$_DAAAU(1028)][$_CJJJU(203)] : t[$_CJJJU(960)]() / a - n[$_CJJJU(930)]`
解混淆
`_ = e ? n["lastPoint"]["x"] : t["$_BGCr"]() / a - n["$_CIAT"]`
`e`是undefind类型所以一定会等于后半段，但是多次测试，前后两个值相等
前面值字面像是最后一个x的位置的值调试对比看看
![](./极验/userresponse_t.png)



##### `passtime`

`passtime` = `n`,也是由`function(t, e, n)`传进来的,继续跟栈，
这里不着急往上跟, 分析一下`userresponse`用到了轨迹值，(x,y,z)是坐标跟时间，大胆猜测`passtime`会不会就是最后的`z`呢？ 测试对比下. 还真是
![](./极验/passtime.png)

##### `rp`

`o[$_CAIAK(793)] = X(i[$_CAIAK(122)] + i[$_CAIAK(134)][$_CAIAK(187)](0, 32) + o[$_CAHJS(725)])`

解混淆
`o["rp"] = X(i["gt"] + i["challenge"]["slice"](0, 32) + o["passtime"])`

比较简单传值即可

#### `V["encrypt"]`

把`V`扣下来就行

### `m[$_CAIAK(792)]`

一样的把`m`方法扣下来即可

# 底图还原

# 小tips

这里有几个需要注意的点

  1. 需要用到的参数都是`get.php`返回的,不能用前面的
  2. 16位随机值需要相同



# 测试

轨迹处打断点，拿参数本地生成`w`,再`request`测试

![](./极验/successs.png)

# 代码

代码过长只截取了自己改写的
```js
function get_o_aa(track, c, s) {
    return W[$_CJFt(236)]["\u0024\u005f\u0042\u0042\u0045\u0044"](W[$_CJFt(236)]["\u0024\u005f\u0046\u0044\u005f"](JSON.parse(track)), c, s)
}


function get_o_userresponse(track, challenge) {
    track = JSON.parse(track)
    t = track[track.length - 1][0]
    return H(t, challenge)
}

function get_o_passtime(track) {
    track = JSON.parse(track)
    passtime = track[track.length - 1][2]
    return passtime
}


function get_o_rp(gt, challenge, passtime) {
    return X(gt + challenge["slice"](0, 32) + passtime);
}


function get_u(randomString) {
    return new U()[$_CBFJN(392)](randomString);
}

function get_l(track, challenge, c, s, gt, randomString) {
    o = JSON.stringify({
        "lang": "zh-cn",
        "userresponse": get_o_userresponse(track, challenge),
        "passtime": get_o_passtime(track),
        "imgload": 63,
        "aa": get_o_aa(track, c, s),
        "ep": {
            "v": "7.9.2",
            "$_BIE": false,
            "me": true,
            "tm": {
                "a": 1705286215423,
                "b": 1705286215833,
                "c": 1705286215833,
                "d": 0,
                "e": 0,
                "f": 1705286215424,
                "g": 1705286215424,
                "h": 1705286215424,
                "i": 1705286215424,
                "j": 1705286215424,
                "k": 0,
                "l": 1705286215482,
                "m": 1705286215829,
                "n": 1705286215831,
                "o": 1705286215835,
                "p": 1705286216067,
                "q": 1705286216067,
                "r": 1705286216069,
                "s": 1705286216069,
                "t": 1705286216069,
                "u": 1705286216069
            },
            "td": -1
        },
        "h9s9": "1816378497",
        "rp": get_o_rp(gt, challenge, get_o_passtime(track))
    })
    return V[$_CAHJS(392)](o, randomString)
}


function get_h(track, challenge, c, s, gt, randomString) {
    var l = get_l(track, challenge, c, s, gt, randomString);
    var i;
    return m[$_CAIAK(792)](l)
}

function get_w(track, challenge, c, s, gt) {
    randomString = randomStr()
    var h = get_h(track, challenge, c, s, gt, randomString);
    var u = get_u(randomString);
    return h + u
}

// var track = '[[-42,-18,0],[0,0,0],[19,0,109],[20,0,124],[21,0,238],[25,0,244],[28,0,253],[30,0,260],[33,0,268],[34,0,279],[36,0,284],[37,0,308],[37,0,484]]'
// var c = [12, 58, 98, 36, 43, 95, 62, 15, 12]
// var s = '6c566a2d'
// var challenge = '5903566be242ec3492a5a39de8c1364elk'
// var gt = '019924a82c70bb123aae90d483087f94'
// console.log(get_w(track, challenge, c, s, gt).length)
```
