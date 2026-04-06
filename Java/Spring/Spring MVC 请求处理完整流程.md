# Spring MVC 请求处理完整流程



Spring MVC 遵循传统的前端控制器（Front Controller）模式，所有请求均统一交由 `DispatcherServlet` 调度。

## 核心执行步骤
1. **请求到达 `DispatcherServlet`：** 用户发送的 HTTP 请求首先被核心调度器拦截。
2. **查找 Handler：** `DispatcherServlet` 调用 `HandlerMapping`（处理器映射器）。它根据请求 URL，去寻找匹配的后端控制器（Handler，即我们编写的 Controller 方法），并连同配置的拦截器（Interceptor）一起返回一个执行链（HandlerExecutionChain）。
3. **适配 Handler：** `DispatcherServlet` 调用 `HandlerAdapter`（处理器适配器）。由于 Controller 的编写方式多种多样（实现接口、注解等），适配器的作用是统一调用接口，提取请求参数并执行目标 Controller 方法。
4. **执行 Controller：** Controller 执行具体的业务逻辑，并将结果和视图名称封装为 `ModelAndView` 对象返回给适配器。*(注：在前后端分离架构中，通常使用 `@ResponseBody` 或 `@RestController`，此时直接由 `HttpMessageConverter` 将数据转换为 JSON 返回，后续视图解析步骤省略)*。
5. **视图解析：** `DispatcherServlet` 将 `ModelAndView` 传递给 `ViewResolver`（视图解析器）。解析器根据逻辑视图名查找并返回真实的物理视图（View）对象。
6. **视图渲染：** 将 Model 中的模型数据填充到 View 中进行页面渲染。
7. **响应返回：** 将渲染后的最终结果返回给客户端浏览器。