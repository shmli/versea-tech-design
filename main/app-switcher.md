# AppSwitcher

AppSwitcher 切换应用主要流程如下：

1. 加载匹配的应用。
2. 比较已有的应用和将要加载的应用，解析出需要 unmount 的应用和需要 mount 的应用。
3. unmount。
4. mount。

##  AppSwitcherContext
它需要记录 App 的加载顺序，mount 顺序和卸载顺序。当 changeApp 触发时，会生成一个新的 AppSwitcherContext。然后销毁当前的 AppSwitcherContext，使用新的 AppSwitcherContext 替代它。