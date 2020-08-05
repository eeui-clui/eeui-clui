## 安装

```shell script
npm i eeui-clui && eeui plugin install eeui-clui/iconfont
```

## 卸载

```shell script
eeui plugin uninstall eeui-clui/iconfont
```

## 引用

```js
// 可在entry.js添加以下代码，全局注册，也可以单独vue文件配置使用
// 先下载https://www.iconfont.cn/ 中自己项目的字体库包，解压后将iconfont.ttf与iconfont.json文件放在src的子目录下
import {ClIcon} from 'eeui-clui';
ClIcon.props.fontFamily.default = "clIcon"; // 注意：请保持与iconfont中family一致
ClIcon.props.iconfontJson.default = require('./font/iconfont.json'); // 加载项目本地iconfont会用到，远端加载不传, 请填写相对路径路径，文件为iconfont下载的字体文件包里面的json文件
ClIcon.props.fontSrc.default = "src/font/iconfont.ttf"; // 加载项目本地iconfont文件地址, 请填写scr/开头路径，文件为iconfont下载的字体文件包里面的.ttf字体文件
// CLIcon.props.fontSrc.default = "http://at.alicdn.com/t/font_8d5l8fzk5b87iudi.ttf"; // 可使用远端iconfront地址，（长用于调试模式，若能接收网络延迟加载也可以用于正式）
Vue.component('cl-icon', ClIcon);
```
## 组件

```
<cl-icon></cl-icon>
```
## 参数

```
-  name: Icon Name，例如：success
-  size: 图标大小，单位px，例如：36或36px
-  color: icon颜色
-  fontSrc: ttf文件路径，本地：src/**/iconfont.ttf, 远端：http://****/iconfont.ttf, 推荐全局注册传默认参数
-  fontFamily: iconfont的family值，注意：请保持与iconfont中family一致，推荐全局注册传默认参数
-  iconfontJson: 加载项目本地iconfont会用到，远端加载不传, 请填写相对路径路径，文件为iconfont下载的字体文件包里面的json文件，推荐全局注册传默认参数
```


