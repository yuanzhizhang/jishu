> 图文并茂 http://t.csdn.cn/G4Avp

最近在做一个模块的时候， 花了点儿时间仔细阅读了一下， 也算是对springboot的请求执行流程有了大概的理解。接下来一一介绍下。

spring boot 在启动的时候是以Application 文件为入口， 启动内嵌的tomcat 容器， 并且扫描给定范围内的configurations， beans， components，services， 注册到 applicationContext 上下文中， 默认是单例模式生成这些实例， 并且解析 静态配置文件的内容 当作常量， 也一并加载到服务中。

当一个client 需要请求API 的时候， spring boot 执行流程如下(具体要结合项目中的配置流程， 这里仅根据当前项目的流程介绍)

Tomcat thread 接受到请求调用 apache 中的 ApplicationFilterChain 执行 doFilter 方法 ， doFilter 执行internalDoFilter， 此方法依次调用每个 Filter 中的doFilter 方法， 并将ApplicationFilterChain 实例传递给每一个Filter。

接着所有doFIlter 执行完毕， 控制权交回ApplicationFilterChain， 开始调用Servlet， 类型是 DispatcherServlet（org.springframework.web.servlet 下面）， spring DispatcherServlet继承 FrameworkServlet， FrameworkServlet 继承 HttpServletBean ， HttpServletBean继承 httpServlet， 依次执行 httpServlet: public service方法 -> FrameworkServlet: protected service方法 -> httpServlet: protected service方法 -> FrameworkServlet: doGet(或者其他的doXX方法, 这些doXX 方法被 FrameworkServlet override 了， 所以实际调用的是FrameworkServlet) -> DispatcherServlet: doService -> doDispatch (这个 逻辑最好去debug会理解的比较清楚。)

doDispatch 方法执行拦截器的preHandle， 然后开始调用spring 的核心类RequestMappingHandlerAdapter: handle-> 执行handleInternal 方法->创建 ServletInvocableHandlerMethod 实例（这个实例是方法级别， 即方法返回，实例会被垃圾回收）-> 注入全局的 的HandlerMethodArgumentResolvers ， handlerMethodReturnValueHandlers， HttpMessageConvertors， ResponseBodyAdvices 等到ServletInvocableHandlerMethod 对象实例中。

并调用ServletInvocableHandlerMethod: invokeAndHandle 方法， 该方法通过java 反射机制 动态调用目标API 方法 进入相对应具体的controller，并执行 具体的业务逻辑， ResponseEntity 作为 controller 的返回值。

执行完毕 ， 控制权返回 到 invokeAndHandle 方法， 接下来选择合适的 HandlerMethodReturnValueHandler 的实现类（HttpEntityMethodProcessor）， 执行 handleReturnValue 方法， 首先会判断是否需要对response entity 进行二次处理（ResponseBodyAdvice: beforeBodyWrite）， 处理完成后， 调用注册进来的MessageConvertor 对返回信息进行转换处理， 比如使用faster Jackson 把对象类型转成 json 字符串，并以application/json 的形式返回 http response。

此时，控制权转会到 RequestMappingHandlerAdapter， 继续执行完毕， 控制权转回 DispatcherServlet: doDispatch， 执行对应的拦截器 postHandle 方法， 最后执行拦截器的afterCompletion 方法。

控制权转回ApplicationFilterChain ， 继续执行Filter 剩下的代码。能够讲的就这么多， 接下来的不属于本次研究范围。