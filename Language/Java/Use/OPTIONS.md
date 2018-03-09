#OPTIONS请求

主要是检测服务器是否可达

配置过滤器

```java
@Component
public class CorsFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }
    @Override
    public void doFilter(ServletRequest req, ServletResponse res,FilterChain chain) throws IOException, ServletException {
        HttpServletResponse response = (HttpServletResponse) res;
        HttpServletRequest request = (HttpServletRequest) req;
        if(request.getMethod().equals("OPTIONS")){
            response.setStatus(204);
            //设置允许跨域访问
            response.setHeader("Access-Control-Allow-Origin","*");
            //设置支持post，get
            response.setHeader("access-control-allow-methods","POST,GET");
            response.setHeader("access-control-allow-headers","Origin, No-Cache, X-Requested-With, Cache-Control, Content-Type");
            return;
        };
        chain.doFilter(req, res);
    }
    @Override
    public void destroy() {

    }
}
```