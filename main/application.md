# Application 模块

## AppService

AppService 是 versea 整个流程开始的地方，提供了 registerApp 和 registerApps（一次性注册多个应用）供使用者调用

### registerApp

registerApp 主要完成两件事

1. 生成 App 实例
1. 调用 Router 实例上的 addRoutes 方法开始注册路由流程

## App

### App 简介

用户注册的每一个微应用都对应一个 app 实例，该实例主要包含

- app 名称
- app 当前所处的状态([App 所有状态](#all-status))
- 加载app的方法
- 微应用返回的生命周期钩子函数

当 AppSwitcher 确认了需要切换的应用时，会调用该应用的 load 方法加载该应用

微应用暴露出的 bootstrap 、mount、unmount 钩子，会在相应的生命周期被依次调用

###  <div id="all-status">App 所有状态</div>
- NotLoaded
- LoadingSourceCode
- NotBootstrapped
- Bootstrapping
- NotMounted
- Mounting
- Mounted
- Unmounting
- Unloading
- LoadError
- Broken

