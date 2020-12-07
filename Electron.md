# Electron

## 1.快速入门

安装环境 node.js

```
# 克隆这仓库
git clone https://github.com/electron/electron-quick-start
# 进入仓库
cd electron-quick-start
# 安装依赖库
npm install
# 运行应用
npm start

```

![](C:\Users\Administrator\Desktop\安装\arrest\1591191878288.png)

![59119191998](C:\Users\Administrator\Desktop\安装\arrest\1591191919987.png)

![59119199954](C:\Users\Administrator\Desktop\安装\arrest\1591191999546.png)

## 2.调试技巧

### 2.1渲染进程（页面）

![59119265167](C:\Users\Administrator\Desktop\安装\arrest\1591192651678.png)

```
Crtl+Shift+I
```

### 2.2主进程,使用 VSCode 进行主进程调试

![59119277782](C:\Users\Administrator\Desktop\安装\arrest\1591192777824.png)

![59119282354](C:\Users\Administrator\Desktop\安装\arrest\1591192823545.png)

在package.json配置

```
"start": "electron --inspect=5858 ."
```

在 .vscode/launch.json配置

```
{
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Debug Main Process",
        "type": "node",
        "request": "launch",
        "cwd": "${workspaceFolder}",
        "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron",
        "windows": {
          "runtimeExecutable": "${workspaceFolder}/node_modules/.bin/electron.cmd"
        },
        "args" : ["."]
      }
    ]
  }
  
```

## 3.electron整合vue

vue ui

要安装的插件

![59120383591](C:\Users\Administrator\Desktop\安装\arrest\1591203835919.png)

依赖

![59120387662](C:\Users\Administrator\Desktop\安装\arrest\1591203876624.png)

![59120389899](C:\Users\Administrator\Desktop\安装\arrest\1591203898993.png)

运行

![59120393405](C:\Users\Administrator\Desktop\安装\arrest\1591203934054.png)

## 4.常用api事件

```
//当窗口关闭触发
app.on('window-all-closed', function () { 
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') app.quit()
})
```

（错误处理）出现：Electron中提示:Refused to execute inline event handler because it violates

```

需要这么写
<button id="buttonProcess">获取信息</button>


var btn=document.getElementById('buttonProcess');
   
btn.onclick=getProcessInfo;
 
 function getProcessInfo()
{
    console.log("getProcessInfo**********************",process.getCPUUsage());
 
}

```

（错误处理）出现：renderer.js:13 Uncaught ReferenceError: process is not defined
    at HTMLButtonElement.getProcessInfo (renderer.js:13)

```
const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: path.join(__dirname, 'preload.js'),
      nodeIntegration: true
    }
  })
```

