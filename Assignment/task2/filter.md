**学院：省级示范性软件学院**

**题目：《作业二：Filter练习》**

**姓名：唐玉亮**

**学号：2100230021**

**班级：软工2202**

**日期：2024-10-01**

# 文档说明

## 实现说明：

1. - **LoginFilter 类**：实现了 `javax.servlet.Filter` 接口，用于创建一个过滤器来验证用户是否已登录。

2. * **@WebFilter 注解**：配置了过滤器，使其应用于所有 URL 路径（"/*"）。

3. - doFilter 方法：实现了主要的过滤逻辑。

     - **检查排除路径**：使用 `isExcluded` 方法检查当前请求是否在排除列表中。

     - **用户登录状态检查**：通过 `HttpSession` 对象检查用户是否已登录。
   
     - **重定向**：如果用户未登录，将请求重定向到登录页面。

4. - **排除列表**：定义了一个列表 `excludePaths`，包含不需要登录就能访问的路径。

5. - **isExcluded 方法**：用于检查当前请求路径是否在排除列表中。

## 额外功能：

- **灵活的排除列表**：通过将排除路径存储在列表中，可以轻松地添加或删除路径。
- **重定向到登录页面**：如果用户未登录，过滤器会将用户重定向到登录页面，而不是简单地返回错误。

## 使用说明：

- 将 `LoginFilter.java` 文件添加到您的 Servlet 容器中。
- 确保您的 Web 应用已经配置了相应的登录和注册页面。
- 测试过滤器以确保它正确地重定向未登录的用户，并允许已登录用户访问受保护的资源。

## 注意事项：

- 确保在部署到生产环境之前，对过滤器进行充分的测试。
- 根据实际需求调整排除列表。
- 考虑安全性，确保登录页面和注册页面的安全性。

## Filter实验：

``` java
/*题目: 实现一个登录验证过滤器
        目标: 创建一个 Servlet的 过滤器,用于验证用户是否已登录。对于未登录的用户,将其重定向到登录页面。
        要求:
        1. 创建一个名为 LoginFilter 的类, 实现 javax.servlet.Filter 接口。
        2. 使用 @WebFilter 注解配置过滤器,使其应用于所有 URL 路径 ("/*")。
        3. 在 doFilter 方法中实现以下逻辑:
        a. 检查当前请求是否是对登录页面、注册页面或公共资源的请求。如果是,则允许请求通过。
        b. 如果不是上述情况,检查用户的 session 中是否存在表示已登录的属性(如 "user" 属性)。
        c. 如果用户已登录,允许请求继续。
        d. 如果用户未登录,将请求重定向到登录页面。
        4. 创建一个排除列表,包含不需要登录就能访问的路径(如 "/login", "/register", "/public")。
        5. 实现一个方法来检查当前请求路径是否在排除列表中。
        6. 添加适当的注释,解释代码的主要部分。
/*
package com.example.demo2;
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;
import javax.xml.stream.events.StartDocument;
import java.io.IOException;
import java.util.Arrays;
import java.util.List;
/* 登录验证的过滤器*/
//访问所有资源，都会被拦截
@WebFilter(filterName = "LoginFilter", urlPatterns = {"/*"})

public class LoginFilter implements Filter {

    private List<String> excludePaths;

    public LoginFilter() {
        // 初始化排除列表,判断访问资源是否和登录注册相关
        this.excludePaths = Arrays.asList("/login", "/register", "/public","/imgs/","/css/");
    }



    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {

        HttpServletResponse response = (HttpServletResponse) servletResponse;
        HttpServletRequest req = (HttpServletRequest)servletRequest;

        //获取请求的URI
        String requestURI = req.getRequestURI();
        //循环判断检查请求是否在排除列表中
        if(isExcluded(requestURI)){
            //如果在排除列表中，放行
            filterChain.doFilter(servletRequest,servletResponse);
            return ;
        }

        //如果不在排除列表中，检查用户时候已经登录
        //1.判断session中是否有user
        HttpSession session = req.getSession();
        String user = (String) session.getAttribute("user");

        //2.判断user是否为null
        if(user != null || "".equals(user)){
            //登陆过了
            //放行
            filterChain.doFilter(servletRequest,servletResponse);
            System.out.println("用户已登录，用户名为："+user);
        }else{
            //没有登录，存储提示信息，跳转到登陆页面
            req.setAttribute("login_msg","您尚未登录");
            response.sendRedirect("/demo2_war_exploded/login.jsp");
            System.out.println("please enter Username and Password");
        }
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }

    private boolean isExcluded(String requestURI){
        for (String url : excludePaths) {
            if(requestURI.contains(url)){
                //找到了
                return true;
            }
        }
        return false;
    }
}

```







