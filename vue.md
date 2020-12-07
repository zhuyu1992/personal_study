# vue.js

## 1.项目初始化



1.打开ui命令

```
start vue ui
```

2.

![](C:\Users\Administrator\Desktop\安装\arrest\1590071660649.png)

3.

![](C:\Users\Administrator\Desktop\安装\arrest\1590071916149.png)

4.

![](C:\Users\Administrator\Desktop\安装\arrest\1590071969157.png)

5.

![](C:\Users\Administrator\Desktop\安装\arrest\1590072115982.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1590072368629.png)

6.

![](C:\Users\Administrator\Desktop\安装\arrest\1590072400098.png)

7.

![](C:\Users\Administrator\Desktop\安装\arrest\1590072450534.png)

8.安装element

![](C:\Users\Administrator\Desktop\安装\arrest\1590072593900.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1590072669700.png)

9.安装axios

![](C:\Users\Administrator\Desktop\安装\arrest\1590072770553.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1590072871117.png)

10.运行启动

![](C:\Users\Administrator\Desktop\安装\arrest\1590073752748.png)

11.用vscode 打开项目，并整理

![](C:\Users\Administrator\Desktop\安装\arrest\1590075401111.png)

![](C:\Users\Administrator\Desktop\安装\arrest\1590075430079.png)

12.<script>要与export default 对齐，否则报错

![](C:\Users\Administrator\Desktop\安装\arrest\1590076708508.png)

## 2.登录组件

![59011254009](C:\Users\Administrator\Desktop\安装\arrest\1590112540095.png)

1.出错

![59011256817](C:\Users\Administrator\Desktop\安装\arrest\1590112568170.png)

解决

![59011262306](C:\Users\Administrator\Desktop\安装\arrest\1590112623060.png)

![59011266882](C:\Users\Administrator\Desktop\安装\arrest\1590112668829.png)

重启一下项目

2.设置全局样式表

![59011349474](C:\Users\Administrator\Desktop\安装\arrest\1590113494741.png)

```
html, body, #app{
    height: 100%;
    width: 100%;
    margin: 0;
    padding: 0;
}
```

在main.js 引入

![59011357667](C:\Users\Administrator\Desktop\安装\arrest\1590113576679.png)

![59011360198](C:\Users\Administrator\Desktop\安装\arrest\1590113601986.png)

## 3要使用less,需安装插件，否则报错

![59195098909](C:\Users\Administrator\Desktop\安装\arrest\1591950989097.png)



![59195103859](C:\Users\Administrator\Desktop\安装\arrest\1591951038591.png)

## 4.如何使用CSS Tree 插件

![59195225029](C:\Users\Administrator\Desktop\安装\arrest\1591952250293.png)

## 5，解决ESLint语法报错

![59197723258](C:\Users\Administrator\Desktop\安装\arrest\1591977232587.png)

在项目中新建.prettierrc 文件，配置：

```js
{
    "semi": false , //解决加分号问题
    "singleQuote": true //解决双引号问题，单引号表示一个字符串
}
```

![59197759035](C:\Users\Administrator\Desktop\安装\arrest\1591977590352.png)

在.eslintrc.js添加规则

```js
'space-before-function-paren': 0 //解决函数前括号需留空格问题
```

## 6.配置axios请求

![59197786098](C:\Users\Administrator\Desktop\安装\arrest\1591977860987.png)

在main.js中配置

```js
import axios from 'axios' //引入axios
axios.defaults.baseURL = 'http:loaclhost:8888/api' //配置请求的根路径
Vue.prototype.$http = axios //挂载到vue原型
```

发起请求

```js
 const result = this.$http.post("login",this.data) //地址：http:loaclhost:8888/api/login 数据类型

```

返回结果：Promise对象

![59197854304](C:\Users\Administrator\Desktop\安装\arrest\1591978543040.png)

加入await  async 简化操作 **注意：await只能用在被async 修饰的方法上**

![59197848454](C:\Users\Administrator\Desktop\安装\arrest\1591978484549.png)

返回结果：（被axios封装，注意返回真实的数据在哪里）

![59197872051](C:\Users\Administrator\Desktop\安装\arrest\1591978720515.png)

可以用解构表达式获取真实数据

![59197889612](C:\Users\Administrator\Desktop\安装\arrest\1591978896129.png)

## 7.配置Message全局弹窗组件

![59197918263](C:\Users\Administrator\Desktop\安装\arrest\1591979182632.png)

使用：

![59197931368](C:\Users\Administrator\Desktop\安装\arrest\1591979313689.png)

自己测试：

![59197968659](C:\Users\Administrator\Desktop\安装\arrest\1591979686591.png)



![59197971064](C:\Users\Administrator\Desktop\安装\arrest\1591979710646.png)



##8.编程式导航，保存token到sessionStorage

![59198003043](C:\Users\Administrator\Desktop\安装\arrest\1591980030439.png)

```js
this.$router.push("/home") //编程式导航

window.sessionStorage.setItem("token", res.data.token)//保存token到sessionStorage
```

## 9.路由导航控制访问权限，退出功能，axios拦截器验证token

![59198040236](C:\Users\Administrator\Desktop\安装\arrest\1591980402366.png)

![59198059797](C:\Users\Administrator\Desktop\安装\arrest\1591980597970.png)

退出功能

![59198070069](C:\Users\Administrator\Desktop\安装\arrest\1591980700695.png)



axios拦截器验证token

![59198106540](C:\Users\Administrator\Desktop\安装\arrest\1591981065405.png)

![59198129357](C:\Users\Administrator\Desktop\安装\arrest\1591981293574.png)

config 表示请求对象 ,以上作用：在请求头中添加Authoritarian：token

## 10.解决导航菜单边界突出问题

```less
   .el-aside {
        background-color: #474a52;
        .el-menu{
            border-right: none;
        }

      }
```

## 11.作用域插槽

![59206908070](C:\Users\Administrator\Desktop\安装\arrest\1592069080709.png)

```html
<template slot-scope="scope">
	{{scope.row}}                //获取该行的所有数据
</template>
```

数据绑定

![59209736966](C:\Users\Administrator\Desktop\安装\arrest\1592097369665.png)

## 12.配置安装富文本编辑器

![59213620528](C:\Users\Administrator\Desktop\安装\arrest\1592136205284.png)

在main.js 引入

```
// 富文本编辑器
import VueQuillEditor from 'vue-quill-editor'
// 富文本编辑器样式
import 'quill/dist/quill.core.css' // import styles
import 'quill/dist/quill.snow.css' // for snow theme
import 'quill/dist/quill.bubble.css' // for bubble theme
// 全局挂载
Vue.use(VueQuillEditor /* { default global options } */)
```

```
 <quill-editor
        ref="myQuillEditor"
        v-model="goodForm.goods_detail"
        @blur="onEditorBlur($event)"   //失去焦点
        @focus="onEditorFocus($event)" //获得焦点
        @ready="onEditorReady($event)" //富文本加载时
        @change="onEditorChange($event)" //每次更改时
      />
```

```js
methods: {
    onEditorBlur(quill) {
      console.log('editor blur!', quill)
      console.log(this.goodForm.goods_detail)
    },
    onEditorFocus(quill) {
      console.log('editor focus!', quill)
    },
    onEditorReady(quill) {
      console.log('editor ready!', quill)
    },
    onEditorChange({ quill, html, text }) {
      console.log('editor change!', quill, html, text)
      this.content = html
    }
  }
```

