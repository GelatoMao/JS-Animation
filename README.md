## 原生JS实现动画
整理之前的笔记 将关于动画的代码整合在一起 并总结遇到的问题 方便之后复习

### 基本动画框架

#### 核心
通过定时器setInterval()不断移动盒子位置

#### 步骤
1. 获得盒子当前位置
2. 让盒子在当前位置加上1个移动距离
3. 利用定时器不断重复这个操作
4. 加一个结束定时器的条件
5. 注意元素要**添加定位** 才能使用element.style.left

### 缓慢动画框架

#### 核心
使每次移动的步长逐渐变小，(目标值 - 现在的位置) / 10 作为每次移动的距离

#### 关键
使用`(target - obj.offsetLeft) / 10`作为步长，因为有除法运算，会出现小数，导致停止位置不是目标位置，如果步长是正值，需要向上取整使步长值为整数，如果步长为负数，则向下取整

### 轮播图

#### 需求
- 鼠标移入，左右两侧的箭头显示，离开隐藏
- 点击右侧箭头一次，图片向左播放一张
- 图片播放的同时，下方小圆点也跟随变化
- 点击小圆点，播放响应图片
- 鼠标不经过轮播图，也会自动播放，鼠标经过，自动播放停止

#### 关键
- 点击li使用排他思想的函数可以直接在生成li的同时绑定，因为是回调函数，绑定点击事件是同步的，但真正触发回调函数是异步的，所以执行回调函数的时候，所有li都已经生成了
- 轮播图移动的是ul，不是li，并且ul移动的距离是小圆点的索引号*图片的宽度 **注意是负值**
- 获取小圆点的索引号，可以绑定自定义属性
- 实现无缝轮播的关键是在最后插入第一张图片，当图片滚动到克隆的最后一张时，让`ul`快速的，不做动画的跳到最左侧：`left=0`。注意最后要改下`ul`的宽度，在原有图片基础上新增2张
- 自动克隆图片：`bannerList.children[0].cloneNode(true)`
- 当点击下方小圆点切换图片后，再点击箭头切换图片，会存在bug，因为两者是用不同变量控制的，导致获取的图片下标不一致。解决办法，当点击小圆点的时候，要获取到索引号，并且同时将其赋值给控制箭头的`num`值以及圆点的`circle`
- 自动播放就是在定时器里面调用箭头手动点击事件，并且在鼠标移入的时候清除，移出的时候触发

#### 节流阀
- 防止轮播图按钮连续点击造成播放过快
- 当上一个函数动画内容执行完毕，再去执行下一个函数动画，让事件无法连续触发
- 利用回调函数，添加一个变量来控制，锁住函数和解锁函数

#### 踩坑
- 这个bug真是找了好久，当点击第一个小白点的时候，再点击左侧箭头，一直切换不到最后一张图片，显示空白，后来发现`num = 0`的情况下，`if(num === 0)`这个判断条件一直没有成立，原因是点击小圆点后会将圆点的`index`属性赋值给`num`，`index`是`string`类型，`num`原来是整数类型，点击之后变成了`string`类型，而我用的全等号，所以一直匹配不上，果然TS的存在不是没有道理的【摊手
