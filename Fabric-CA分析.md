# Fabric-CA分析

流程：
1. CA Server 初始化
1. CA Server 启动
1. CA Client 登记admin
1. CA Client 注册新用户
1. CA Client 登入新用户
1. CA Client 吊销用户证书

## CA Server 初始化
对应命令入口
```bash
fabric-ca-server init -b adminname:adminpassword
```


