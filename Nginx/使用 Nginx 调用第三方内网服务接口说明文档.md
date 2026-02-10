# 使用 Nginx 调用第三方内网服务接口说明文档

[TOC]

## 1. 设置 nginx 反向代理

修改 nginx 配置文件:

```shell
# 设置配置文件
# nginx.conf 
http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    server {
        # 设置 nginx 监听端口
        listen       1080;
        # 设置 nginx 服务器 ip, 拼接后 url 地址为: 192.168.xx.xx:1080
        server_name  192.168.xx.xx;
        # 设置 uri 路径, 默认为 /
        location / {
            # 设置反向代理 url 即需要访问的实际地址
            proxy_pass https://xxx.xxx.xxx.xxx;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

完成设置后启动 nginx

## 4. 实现 http 请求接口

```java
// 以 RestTemplate 作为示例: 
public class HttpClientUtil { 

    // 创建获取 RestTemplate 示例的 ClientHttpRequestFactory 方法 
    private ClientHttpRequestFactory clientHttpRequestFactory()  {
        try {
            // 设置超时
            RequestConfig requestConfig = RequestConfig.custom()
                    // 连接超时：10秒
                    .setConnectTimeout(Timeout.of(Duration.ofSeconds(30)))
                    // 从连接池获取连接的超时时间：10秒
                    .setConnectionRequestTimeout(Timeout.of(Duration.ofSeconds(30)))
                    // 读取超时：30秒
                    .setResponseTimeout(Timeout.of(Duration.ofSeconds(30)))
                    .build();
            // 设置 HttpClient
            HttpClient httpClient = HttpClients.custom()
                    .setDefaultRequestConfig(requestConfig)
                    .build();
            return new HttpComponentsClientHttpRequestFactory(httpClient);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    // 测试发送请求
    public String testSendPost() {
        // 创建 RestTemplate 示例
        RestTemplateBuilder builder = new RestTemplateBuilder();
        RestTemplate client = builder.requestFactory(this::clientHttpRequestFactory)
                .build();
        //新建Http头，add方法可以添加参数
        HttpHeaders headers = new HttpHeaders();
        //设置请求发送方式
        HttpMethod method = HttpMethod.POST;
        // 设置 body 数据格式
        headers.setContentType(MediaType.APPLICATION_JSON_UTF8);
        // 设置请求头参数
        headers.set("Connection", "keep-alive");
        // 设置 url 地址为反向代理的地址
        String url = "http://192.168.xx.xx:1080/xxx";
        // 设置 params 请求体
        String params = "{\"Season\": \"24Q1\", \"Stage\": \"销样\"}";
        //将请求头部和参数合成一个请求
        HttpEntity<String> requestEntity = new HttpEntity<>(params, headers);
        //执行HTTP请求，将返回的结构使用String 类格式化（可设置为对应返回值格式的类）
        ResponseEntity<String> response = client.postForEntity(url, requestEntity, String.class);
        return response.getBody();
    }
}
```


