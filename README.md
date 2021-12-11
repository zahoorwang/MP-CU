# MP CU

<p><a href="update.md">更新日志(Beta V3.2.6)</a> | 在线文档（开发中...）</p>


<img width="120px" src="https://github.com/iZaiZaiA/iZaiZaiA/blob/img/mp-cu/mp-cu.jpg" />

<hr/>

### 介绍

本项目为 `colorUI3.x` 微信小程序原生版。

`colorUI3.x` 默认只支持 `uni-app`，本项目中 `colorUI` 框架为移植修改版。

`colorUI3.x` 地址： [https://github.com/weilanwl/coloruiBeta](https://github.com/Color-UI/MP-CU)

内置的vuex 渲染引擎参考了 [https://github.com/xiaoyao96/wxMiniStore](https://github.com/xiaoyao96/wxMiniStore) 的项目。

<hr/>

### 准备配置

需要先升级小程序开发工具到 `2021-10-11` 之后的版本,
然后，检查根目录下，`project.config.json` 配置文件内的 `"setting"` 节点下，是否配置了：

``` json

"useCompilerPlugins": [
    "sass"
]

```

如果没有配置，需要手动配置一下

由于小程序默认开启了 `v2` 的样式，在v2模式下，`colorUI` 部分样式会失效。
完整 `colorUI` 样式，需要在 `app.json` 文件内，删除 `"style": "v2"` 即可

``` json

"style": "v2"

```

<hr/>


### 框架配置

您可单独设置一个 `config.js` 里面配置相关信息，然后暴露方法给 `app.js` 在全局引用

``` js

import { ColorUi } from './config'
App({  
    ColorUi,        //挂载到app上，此步骤必须要有！
    onLaunch() {
        
    }
})

```

然后在根目录的 `app.scss` 文件里引入相关框架的css文件。


``` css

@import './mp-cu/colorUI/scss/ui';

/* 实际项目中，可删除下面的相关文件和引用，因为图标太多，体积较大，可能你项目里并不需要这么多图标，建议自行添加需要的扩展icon图标引用。*/
/* @import './mp-sdk/icon/doc'; */

```

<hr/>

相关文件路径：

``` js
/mp-cu         // ColorUi主框架的文件夹，不建议修改这里面的文件，以免后续升级时，被覆盖。
/mp-sdk        // ColorUi框架的辅助文件夹，封装了一些常用的方法函数、扩展图标库等，按需使用，如不需要，可删除此文件夹。
/packageA      // 项目演示demo的分包，此包为模板包路径
/packageB      // 项目演示demo的分包，此包为实验室包路径
   ...
/config.js     // 框架的配置项，建议在此文件中配置，后续更新升级时，不会被覆盖

```

<hr/>

### 组件使用方式

挂载组件，在 `app.json` 或 `xxx.json` 文件里配置

``` json

"usingComponents": {
  "ui-sys": "mp-cu/colorUI/components/ui-sys/ui-sys"
}

```


<hr/>


### colorUI MP-CU 扩展包

> 由于微信小程序无法使用vue.key来全局命名变量，导致习惯vue的用户很不习惯，而且微信内部并不存在像vuex这样的状态管理机制，为满足上述想法，colorUI  MP-CU 为您定制了一套自有的扩展包，你可以在全局导入变量、函数、方法，同时您也可以定义您自己的状态（` vuex `）.


- colorUI 的配置文件需要您定义成类似以下的内容，参考 `/config.js` 文件。

```javascript

//引入框架的方法函数库
import ColorUI from './mp-cu/main'
export const colorUI = new ColorUI({
   config: {
      // colorUI的配置文件
   },
   data: {
      //全局data
   },
   method: {
      //全局函数
   }
})

```

#### 注意，只要您在app.js 里面挂载上colorUI以后，您不需要在 `page`，`components` 中获取系统信息，因为colorUI 已经帮您写好在对应的data 里面了，您只需要使用就可以了

- 例如在demo的home 页面中，在page的data中并没有定义sys_navBar，但是在页面和js中仍然可以使用，关键的是，这个数据是根据访问手机实施变化的！

```javascript

// colorUI 为您在data中事先定义好的数据
sys_info
sys_navBar
sys_statusBar
sys_capsule

```


#### 您在config里面配置的data数据也可以在页面的data里面访问到，colorUI将配置的数据定义到$cuData属性里面中了


> 配置的config你可以在 `page`，`components` 中通过 `this.data.$cuConfig` 获得
>
> 配置的data内容你可以在 `page`，`components` 中通过 `this.data.$cuData` 获得
>
> 配置的函数方法，都会在`page`，`components`里面注册在相应位置，您只需要调用即可


例如：

```javascript

import ColorUI from './mp-cu/main'
export const colorUI = new ColorUI({
   config: {
      theme: 'auto',
      main: 'blue',
      text: 1,
      footer: true,
      homePath: '/pages/home/home',
      tabBar: [],
   },
   data: {
      name: 'hello'
   },
   methods: {
      cuLog (message, ...optionalParams) {
         console.log(message, ...optionalParams)
      }
   }
})


```

该配置文件，在项目所有页面中，都可以直接调用`this.cuLog ()`,就可以调用该config 文件中的函数体，当然data和config均可以在页面中获取，为了方便您获取当前项目配置，ColorUI为您将配置文件也一同注册进data里面了。当您在其他页面修改后，store 内部的内容便会实时更新（包括已经打开过的页面和未打开过的页面）。
具体可以参考ColorUI的主题配置。


<span style='color : red'>注意，您设置的函数，data，等配置名称不能存在以下情况 
   [`"data"` ,
   ` "onLoad" `,
   ` "onShow" `,
   ` "onReady" `,
   ` "onHide" `,
   ` "onUnload" `,
   ` "onPullDownRefresh" `,
   ` "onReachBottom" `,
   ` "onShareAppMessage" `,
   ` "onPageScroll" `,
   ` "onTabItemTap" `,
   ` "setTheme" `,
   ` "setMain" `,
   ` "setText" `,
   ` "_toHome" `,
   ` "_backPage" `,
   ` "sys_isFirstPage"`
]
</span>


### 内置方法
- [x] 内置 store		[使用方法参考](#ColorUi.store)
- [x] 页面返回函数	[使用方法参考](#_backPage)
- [x] 切换主题		[使用方法参考](#_setTheme)
- [x] 设置主颜色		[使用方法参考](#_setMain)
- [x] 设置字号等级	[使用方法参考](#_setText)

<hr/>


### 内置方法详解

#### <span id='_backPage'>页面返回函数</span>
> colorui 3.x微信小程序版本因为所有页面都需要包裹 `ui-sys` ，所以对于大多数返回函数都无法使用，ColorUI为您在所有页面注册了返回函数，在需要返回的函数里调用 `this._backPage()` 注意this 作用域的问题。


#### <span id='_toHome'>返回首页</span>
> ColorUI为您在所有页面注册了返回函数，在需要返回的函数里调用 `this._toHome()` 注意this 作用域的问题。

#### <span id='_setTheme'>切换主题</span>

> ColorUI为您在所有页面注册了切换主题函数，在需要切换主题的地方调用 `this. _setTheme()` 注意this 作用域的问题。

#### <span id='setMain'>设置主颜色</span>

> ColorUI为您在所有页面注册了设置主颜色函数，在需要设置主颜色的地方调用 `this. _setMain()` 注意this 作用域的问题。

#### <span id='setText'>设置字号等级</span>

> ColorUI为您在所有页面注册了设置字号等级函数，在需要设置字号等级的地方调用 `this. _setText()` 注意this 作用域的问题。

<hr/>

### 其它说明

自动跟随系统主题，切换主题功能，在 `app.json` 文件里配置

``` json

"darkmode": true

```

开启自动跟随后，在安卓机端切换主题时，会重载小程序，具体原因，参考微信官方说明：[https://developers.weixin.qq.com/community/develop/doc/000a88c66f00183d414c9879451400](https://developers.weixin.qq.com/community/develop/doc/000a88c66f00183d414c9879451400)

<hr/>

### 鸣谢

感谢 `文晓港（colorui作者 / [@weilanwl](https://github.com/weilanwl)）` , `胖虎（[@bypanghu](https://github.com/bypanghu)）` 

<hr/>


[![Stargazers repo roster for @Color-UI/MP-CU](https://reporoster.com/stars/Color-UI/MP-CU)](https://github.com/Color-UI/MP-CU/stargazers)


<hr/>

### License

MIT © [MP CU](https://github.com/Color-UI/MP-CU)
