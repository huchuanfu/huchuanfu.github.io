- [首页](../README.md "Java Interview Docs")
1. SSE流式请求大模型服务，等待1分钟后会报错
   - 首先想到的是排查OKHttpClient的readTimeout，改成2分钟后依然同样问题
   - 然后怀疑EventSource的超时设置有默认覆盖（因为实际调用是EventSource.connect(okHttpClient)发起的），尝试创建Request的时候通过tag方法设置timeout，仍然未解决
   - 开始研究OKHttpClient的原理，在发送SSE请求时候默认走的HTTP/2，尝试降级为HTTP/1.1（更适合SSE长连接）
   - 进一步查阅官方文档，确定HTTP/2默认超时时间为180s，因此排除以上方案
   - 重新审视所有可能的原因，提出猜想：连接超时不是由我方服务导致的，而是由服务提供方
   - 设计对照实验，进行验证：将readTimeout设置为10s，等待10s后必定超时（符合预期），说明当前版本OKHttpClient的响应超时设置确实由readTimeout这个参数控制
   - 将readTimeout设置为超过1分钟的值，但发现等待1分钟后必定超时（不符合预期），且抛出的异常类型与刚才10s时的不一样（非TimeoutException），而是服务端返回非法数据流之类的
   - 得出初步结论：根因是由于服务提供方导致的，疑似网关之类的默认超时设置，强制中断连接
   - 提报服务提供方检查，果然是因为由于近期切换了集群，而新集群环境的网关Nginx Ingress Controller默认1分钟超时（proxy_read_timeout），改成3分钟后问题得以解决
2. 服务在容器中启动缓慢，并且启动成功后会经常突然重启，导致基本不可用
   - 分析问题现象：在本机启动只要20几秒，但是部署到测试环境后要将近300秒才启动成功
   - 启动成功后测试和开发开始去测功能，刚开始一切顺利，但是隔一会服务就挂掉了，然后被k8s自动重启，以此循环
   - 首先怀疑是发生了内存泄露OOM导致的重启，但观察启动日志，没有明显OOM日志
   - 开启PrintGCDetails，分析应用启动日志，发现大量
3. higress+nacos
4. hikari切换druid