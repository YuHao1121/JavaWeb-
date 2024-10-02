**学院：省级示范性软件学院**

**题目：《作业三：Listener练习》**

**姓名：唐玉亮**

**学号：2100230021**

**班级：软工2202**

**日期：2024-10-02**

# 文档说明

## 简要说明

- `RequestLoggingListener`类实现了`ServletRequestListener`接口，用于监听请求的初始化和销毁事件。
- 在`requestInitialized`方法中，我们记录了请求的开始时间，并将其存储在请求属性中。
- 在`requestDestroyed`方法中，我们计算了请求的处理时间，并构建了一个包含所有所需信息的日志消息。
- `MyServlet1`是一个简单的Servlet，用于测试我们的日志记录功能。它模拟了一些耗时操作，并返回了一个简单的响应。

## 注意事项

- 最终日志信息格式，便于阅读分析
- 计算处理时间=请求结束时间-请求开始时间

## Listener练习

``` java
/*题目：完成请求日志记录（ServletRequestListener）功能
要求：
1. 实现一个 ServletRequestListener 来记录每个 HTTP 请求的详细信息。
2. 记录的信息应包括但不限于：
  ○ 请求时间
  ○ 客户端 IP 地址
  ○ 请求方法（GET, POST 等）
  ○ 请求 URI
  ○ 查询字符串（如果有）
  ○ User-Agent
  ○ 请求处理时间（从请求开始到结束的时间）
3. 在请求开始时记录开始时间，在请求结束时计算处理时间。
4. 使用适当的日志格式，确保日志易于阅读和分析。
5. 实现一个简单的测试 Servlet，用于验证日志记录功能。
6. 提供简要说明，解释你的实现方式和任何需要注意的事项。*/

```

### RequestLoggingListener类

``` java
@WebListener
public class RequestLoggingListener implements ServletRequestListener {

    //定义Logger实例，用于记录日志信息
    private static final Logger LOGGER = Logger.getLogger(RequestLoggingListener.class.getName());

    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        //加载资源
        //从ServletRequestEvent中获取HttpServletRequest对象
        HttpServletRequest request = (HttpServletRequest) sre.getServletRequest();
        //记录当前时间（ms），也就是请求开始时间
        long startTime = System.currentTimeMillis();
        //将开始时间作为属性存储在请求对象中，以便在请求结束时可以访问它
        request.setAttribute("startTime",startTime);
    }

    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        //销毁资源
        //从ServletRequestEvent中获取HttpServletRequest对象
        HttpServletRequest request = (HttpServletRequest) sre.getServletRequest();
        //获取请求开始时间
        long startTime = (long) request.getAttribute("startTime");
        //记录当前时间（ms），为请求结束时间
        long endTime = System.currentTimeMillis();
        //  请求处理时间（从请求开始到结束的时间）
        long requestProcessingTime = endTime - startTime;

        String logMessage = String.format(" Request Time: %s\n"+
                " Client IP: %s\n"+
                " Request Method: %s\n"+
                " Request URI: %s\n"+
                " Query String: %s\n"+
                " User-Agent: %s\n"+
                " Processing Time: %d ms",
                new java.util.Date(),//获取当前日期和时间
                request.getRemoteAddr(),//获取客户端IP地址
                request.getMethod(),//获取请求方法
                request.getRequestURI(),//获取请求URI
                request.getQueryString(),//获取查询字符串
                request.getHeader("User-Agent"),//获取UA
                requestProcessingTime);

        //记录日志信息
        LOGGER.info(logMessage);
        System.out.println(logMessage);
    }
}
```

### 测试servlet

``` java
@WebServlet("/demo2")
public class MyServlet1 extends HttpServlet {
    @Override
    public void service(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.getWriter().println("Hello servlet,贵州大学软件工程");
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 模拟一些处理
        try {
            Thread.sleep(1000); // 模拟耗时操作
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        response.getWriter().write("Test Servlet Response");
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}

```

### 运行结果

- 在访问http://localhost:8080/demo2_war_exploded/demo2控制台出现如下日志信息

``` java
 /*
 Request Time: Wed Oct 02 16:57:07 CST 2024
 Client IP: 0:0:0:0:0:0:0:1
 Request Method: GET
 Request URI: /demo2_war_exploded/demo2
 Query String: null
 User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/129.0.0.0 Safari/537.36
 Processing Time: 0 ms
 */
```

