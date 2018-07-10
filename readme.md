## **xxl-job 分布式job调度中心接入手册**
<br/>
**spring boot 项目接入步骤**
<br/>
1. 引入依赖
2. yml配置
3. 装配配置类
4. 创建jobhandler执行器
5. 进入xxl-job控制台新增执行器
6. 使用新增的执行器开始任务

<br/>

**1 . 引入依赖**
```xml
<!-- xxl-job-core -->
<dependency>
    <groupId>com.xuxueli</groupId>
    <artifactId>xxl-job-core</artifactId>
    <version>1.9.1</version>
</dependency>
```
**2.  yml配置**
```yml
xxl:
  job:
    admin:
      ### xxl-job admin address list, such as "http://address" or "http://address01,http://address02"
      addresses: http://localhost:8080
    ### xxl-job executor address
    executor:
      appname: mktcenter
      ip:
      port: 9888
      ### xxl-job log path
      logpath: /data/applogs/xxl-job/jobhandler
      ### xxl-job log retention days
      logretentiondays: -1
    ### xxl-job, access token
    accessToken:
```

**3.   装配配置类**
```java
package com.bizvane.mktcenterserviceimpl.common.job;
import com.xxl.job.core.executor.XxlJobExecutor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
@Configuration
@ComponentScan(basePackages = "com.bizvane.mktcenterserviceimpl.service.jobhandler")
public class XxlJobConfig {
    private Logger logger = LoggerFactory.getLogger(XxlJobConfig.class);

    @Value("${xxl.job.admin.addresses}")
    private String adminAddresses;

    @Value("${xxl.job.executor.appname}")
    private String appName;

    @Value("${xxl.job.executor.ip}")
    private String ip;

    @Value("${xxl.job.executor.port}")
    private int port;

    @Value("${xxl.job.accessToken}")
    private String accessToken;

    @Value("${xxl.job.executor.logpath}")
    private String logPath;

    @Value("${xxl.job.executor.logretentiondays}")
    private int logRetentionDays;


    @Bean(initMethod = "start", destroyMethod = "destroy")
    public XxlJobExecutor xxlJobExecutor() {
        logger.info(">>>>>>>>>>> xxl-job config init.");
        XxlJobExecutor xxlJobExecutor = new XxlJobExecutor();
        xxlJobExecutor.setAdminAddresses(adminAddresses);
        xxlJobExecutor.setAppName(appName);
        xxlJobExecutor.setIp(ip);
        xxlJobExecutor.setPort(port);
        xxlJobExecutor.setAccessToken(accessToken);
        xxlJobExecutor.setLogPath(logPath);
        xxlJobExecutor.setLogRetentionDays(-1);

        return xxlJobExecutor;
    }
}
```

**4.  创建jobhandler执行器**
```java
package com.bizvane.mktcenterserviceimpl.service.jobhandler;

import com.xxl.job.core.biz.model.ReturnT;
import com.xxl.job.core.handler.IJobHandler;
import com.xxl.job.core.handler.annotation.JobHandler;
import org.springframework.stereotype.Component;

@JobHandler(value="activity")
@Component
public class ActivityJobHandler extends IJobHandler {

    @Override
    public ReturnT<String> execute(String param) throws Exception {
        System.out.println("开始执行生日活动");
        return null;
    }
}

```
**5.  进入xxl-job控制台新增执行器**
<br/>

注意appname与yml中配置名字保持一致 !
![image](/uploads/7100e205c3bbefb1b6d5e609884aef61/image.png)

**6.  使用新增的执行器开始任务**
![image](/uploads/99d1dd138a0b7c9548b21946a487ae92/image.png)
JobHandler名字为步骤4中@JobHandler注解value值
![image](/uploads/c2f8408e3daca4c82979cb65e9c1b432/image.png)

保存即可。
