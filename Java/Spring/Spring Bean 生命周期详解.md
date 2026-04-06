# Spring Bean 生命周期详解



Spring Bean 的生命周期是 Spring 源码考察的绝对核心，其完整流程可概括为“实例化 -> 属性赋值 -> 初始化 -> 销毁”四大阶段，并穿插了各种扩展点。

## 1. 实例化 (Instantiation)
Spring 容器通过反射机制（无参构造器或工厂方法）在内存中分配空间，创建出目标 Bean 的一个空壳对象。

## 2. 属性赋值 (Populate Properties)
Spring 容器根据配置（如 `@Autowired`、XML 中的 `<property>`），对 Bean 的属性进行依赖注入。

## 3. 初始化 (Initialization)
这是扩展点最多的阶段，执行顺序严格遵循以下规范：
1. **Aware 接口回调：** 若 Bean 实现了 `BeanNameAware`、`BeanFactoryAware`、`ApplicationContextAware` 等接口，Spring 会将对应的容器组件注入给该 Bean。
2. **`BeanPostProcessor.postProcessBeforeInitialization`：** 容器级后置处理器（前置处理），在初始化方法执行前被调用（AOP 代理通常在此处或之后生成）。
3. **`@PostConstruct` 注解：** 执行标记了此注解的方法。
4. **`InitializingBean` 接口：** 若实现了该接口，执行 `afterPropertiesSet()` 方法。
5. **自定义 `init-method`：** 执行在配置中指定的初始化方法。
6. **`BeanPostProcessor.postProcessAfterInitialization`：** 容器级后置处理器（后置处理），完成所有初始化后的最终拦截操作。

## 4. 销毁 (Destruction)
当容器关闭时：
1. **`@PreDestroy` 注解：** 执行标记了此注解的方法。
2. **`DisposableBean` 接口：** 执行 `destroy()` 方法。
3. **自定义 `destroy-method`：** 执行配置中指定的销毁方法。