# versea的DI设计

### 概述
versea致力于解决复杂的微前端场景，其设计具有很强的扩展能力，支持插件机制，以便满足开发者的自定义需求。为了实现这个目标，versea底层全部基于DI（依赖注入）开发，使用InversifyJS作为控制反转容器 (IoC)。

### autobind
使用inversify需要手动将依赖项绑定到容器，为了简化此流程，versea基于inversify和reflect-metadata提供了@provide和@provideValue装饰器和buildProviderModule方法用于自动绑定依赖，使用示例如下
```ts
import { Container } from "inversify";
import { provide, buildProviderModule } from "@versea/core";

@provide("Test")
class Test {}

@provide("TestConstructor", "Constructor")
class TestConstructor {}

const container = new Container({ defaultScope: "Singleton" });
container.load(buildProviderModule());

// 测试
console.log(container.get("Test") instanceof Test);
console.log(container.get("TestConstructor") === TestConstructor);
``