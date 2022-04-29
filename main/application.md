# Application模块

## AppService

AppService 是 versea 整个流程开始的地方，提供了 registerApp 和 registerApps（一次性注册多个应用）供使用者调用

### registerApp

registerApp主要完成两件事

1. 生成 App 实例
1. 调用 Router 实例上的 addRoutes 方法开始注册路由流程

注册示例

```js
appServiceInstance.registerApps([
  {
    name: 'micro-demo',
    routes: [{
      path: 'demo'
    }],
    loadApp: async () => {
      // 加载微应用代码
      // await ...
      return (window as any).microApp
    }
  }
])
```

## App

### App简介

用户注册的每一个应用都对应一个 app 实例，该实例主要包含
- 当前 app 所处的状态
- 用户注册时传入的 loadApp 方法
- bootstrap 、mount、unmount 钩子

当 AppSwitcher 确认了需要切换的应用时，会调用该应用的 load 方法加载该应用

使用者需要在微应用入口导出 bootstrap 、mount、unmount 钩子，当 AppSwitcher 进入挂载
