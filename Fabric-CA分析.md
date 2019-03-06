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

1. 程序入口的main函数
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/main.go:26`)

1. 创建ServerCmd类，其中封装了cobra.Command类作为命令行入口库
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/main.go:42`)
    * 其中，在对象函数func (s *ServerCmd) init()中，配置了各子命令对应的执行函数。
    (`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:63`)

1. 执行对象函数func (s *ServerCmd) Execute()，启动相应函数
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/main.go:45`)。
命令fabric-ca-server init的实现为initCmd
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:109`)。

1. initCmd执行时，进一步调用
（`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:102`）
