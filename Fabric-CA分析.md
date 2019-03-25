# Fabric-CA分析

流程：
1. CA Server 初始化
1. CA Server 启动
1. CA Client 登入CA admin，获得证书私钥
1. CA Client 注册peer新用户
1. CA Client 登入peer新用户，获得证书私钥
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
命令fabric-ca-server init的执行时，依次调用rootCmd、initCmd
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:109`)。

1. 首先调用rootCmd，执行对象函数func (s *ServerCmd) configInit()读取配置信息，保存到ServerCmd自身对象属性中
(`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:81`)

1. initCmd执行时，调用func (s *ServerCmd) getServer() *lib.Server，根据属性参数生成Server对象，
（`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/servercmd.go:102`）
进一步，调用func (s *Server) Init(renew bool)，处理初始化过程
(`src/github.com/hyperledger/fabric-ca/lib/server.go:88`)。
    
1. 初始化过程中
    * 调用对象方法func (s *Server) initConfig()，初始化配置
    (`src/github.com/hyperledger/fabric-ca/lib/server.go:98`)
    
    * 调用对象方法func (s *Server) initDefaultCA(renew bool)，初始化默认CA
    (`src/github.com/hyperledger/fabric-ca/lib/server.go:103`)

1. 完成初始化，结束

