# carousel_vue

> 轮播组件，支持icon、picture等轮播。  
> 支持手机端的自动播放、手指触摸滑动  
> 支持pc端的自动播放、点击前一页/后一页，鼠标放置在下方每页标志处，可以跳转到该页  
> 支持用户个性化配置

## 组件介绍
可配置属性介绍：

| 序号  |   属性           |   类型   |默认值 |  描述   |
|:---- |:----------      |:-------- |:---- |:------|
|  1  | speed            | Number   | 300  |滑动动画的时间|
|  2  | timeDelta        | Number   | 3000 |两次自动播放的时间间隔|
|  3  | loop             | Boolean  | true |是否循环播放每一页|
|  4  | noDragWhenSingle | Boolean  | true |只有一页时，是否允许用户滑动|
|  5  | showindicators   | Boolean  | true |是否显示轮播组件下方的页面导航|
|  6  | showSlideArrows  | Boolean  | true |是否显示轮播组件左右两侧的箭头（仅pc端有）|

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
