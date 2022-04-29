# versea 架构

## versea流程

### 注册 App 流程

```plantuml
@startuml
actor User
rectangle "Versea Core" #F5F5F5;line.dashed {
  rectangle "Application" #F8CECC {
    rectangle "AppService"
    rectangle "App"
    AppService -> App: 2.new instance
  }
  rectangle "Router Module" #DAE8FC {
    rectangle "Router"
    rectangle "Route"
    Router --down-> Route: 4.new instance & generate RoutesTree
    Router -> Router: 5.reroute
  }
  App --> Router: 3.registerRoutes
}
User --> AppService: 1.registerApp
@enduml
```

1. 用户注册 app（通过 AppService 实例的 registerApp 方法）
1. 生成 app 实例
1. 根据注册 app 时传入的 routes 生成路由实例并存入 RoutesTree，后续会用这个 RoutesTree 做路由匹配。以下为路由树的简单示例
    ```js
    [
      {
        path: '/path1',
        apps: [app1]
      },
      {
        path: '/path2',
        apps: [app2]
      },
    ]
    ```

### reroute流程

```plantuml
@startuml
actor User
rectangle "Versea Core" as VerseaCore #F5F5F5;line.dashed {
  rectangle "RewriteNavigationEvent"

  rectangle "Router Module" as RouterModule #DAE8FC {
    rectangle "Router"
  }

  rectangle "Application" #F8CECC {
    rectangle "App"
  }

  rectangle "AppSwitcher Module" #D5E8D4 {
    rectangle "AppSwitcher" as AppSwitcher
    rectangle "AppSwitcherContext" as AppSwitcherContext

    AppSwitcher --> AppSwitcherContext: 4.createContext
  }

  Router --> Router: 1.match routes
  Router --> Router: 2.getMatchedRoutesTree
  Router --> AppSwitcher: 3.performance
  AppSwitcher --> App: 5.changeApp
  App -> App: 6. load, mount, unmount
}
User --> VerseaCore: import
VerseaCore -> RewriteNavigationEvent

note top of RewriteNavigationEvent
  1. autorun while import
  2. rewrite pushState, replaceState
  3. rewrite addEventListener hashchange event, popstate event
end note

User --> RouterModule: start or event
@enduml
```

当用户调用start 或 触发浏览器事件（路由切换或前进后退）会进入reroute流程

1. 根据当前访问页面路由匹配路由树，以下为匹配到的路由树的简单示例
    ```js
    // 假设浏览器当前访问路径为 '/path1'
    [
      {
        path: '/path1',
        apps: [app1]
      }
    ]
    ```
1. 加载匹配到的app（加载源代码）
1. 卸载旧的app
1. 挂载新的app


reroute 流程较为复杂，是整个 versea/core 的核心，控制整个应用加载，卸载流程，这里重点介绍一下 AppSwitcherContext，它需要记录 App 的加载顺序，mount 顺序和卸载顺序。当 changeApp 触发时，会生成一个新的 AppSwitcherContext。然后销毁当前的 AppSwitcherContext，使用新的 AppSwitcherContext 替代它。

