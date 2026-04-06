# Spring 核心原理：IoC 与 DI



## 1. 核心概念：IoC (Inversion of Control, 控制反转)
* **定义：** 并非一种具体技术，而是一种软件设计思想。它将对象创建、初始化、销毁等生命周期管理的控制权，从程序员（业务代码）手中交由 Spring 容器（IoC Container）统一管理。
* **优势：** 实现了对象之间的严重解耦。底层组件发生变更时，上层调用方无需修改代码（即无需手动 `new` 新对象）。

## 2. 实现方式：DI (Dependency Injection, 依赖注入)
* **定义：** DI 是 IoC 思想的具体实现方式。即组件之间的依赖关系由容器在运行期决定并动态注入。
* **主要注入方式：**
  * **构造器注入：** 官方强推的模式。强制依赖在对象实例化时就必须就绪，避免 NullPointerException，保证对象的不变性。
  * **Setter 注入：** 适用于可选依赖。
  * **字段注入 (如 `@Autowired`)：** 虽然代码最简洁，但官方不推荐，因为破坏了类的封装性，且脱离 Spring 容器后无法单独实例化进行单元测试。

## 3. 核心容器：BeanFactory 与 ApplicationContext
* **`BeanFactory`：** Spring 框架最底层的接口，提供了高级 IoC 的配置机制。采用延迟加载（Lazy Load）策略，即只有在首次调用 `getBean()` 时才实例化 Bean。
* **`ApplicationContext`：** 继承自 `BeanFactory`，是更高级的容器。它在启动时就会预加载（立即实例化）所有的单例 Bean，并提供了更完整的企业级功能（如事件发布、国际化、环境配置）。