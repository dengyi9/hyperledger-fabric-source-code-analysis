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

执行后，显示如下信息，以及生成如下文件
```text
dengyi@localhost:~/Documents/devs/playground/fabric-ca-server-init$ fabric-ca-server init -b admin:adminpw
2019/03/10 00:17:59 [INFO] Created default configuration file at /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/fabric-ca-server-config.yaml
2019/03/10 00:17:59 [INFO] Server Version: 1.3.1-snapshot-4f6586e
2019/03/10 00:17:59 [INFO] Server Levels: &{Identity:1 Affiliation:1 Certificate:1 Credential:1 RAInfo:1 Nonce:1}
2019/03/10 00:17:59 [WARNING] &{69 The specified CA certificate file /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/ca-cert.pem does not exist}
2019/03/10 00:17:59 [INFO] generating key: &{A:ecdsa S:256}
2019/03/10 00:17:59 [INFO] encoded CSR
2019/03/10 00:17:59 [INFO] signed certificate with serial number 243634774654207749951690340642683812233691894174
2019/03/10 00:17:59 [INFO] The CA key and certificate were generated for CA
2019/03/10 00:17:59 [INFO] The key was stored by BCCSP provider 'SW'
2019/03/10 00:17:59 [INFO] The certificate is at: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/ca-cert.pem
2019/03/10 00:17:59 [INFO] Initialized sqlite3 database at /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/fabric-ca-server.db
2019/03/10 00:17:59 [INFO] The issuer key was successfully stored. The public key is at: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/IssuerPublicKey, secret key is at: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/msp/keystore/IssuerSecretKey
2019/03/10 00:17:59 [INFO] Idemix issuer revocation public and secret keys were generated for CA ''
2019/03/10 00:17:59 [INFO] The revocation key was successfully stored. The public key is at: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/IssuerRevocationPublicKey, private key is at: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init/msp/keystore/IssuerRevocationPrivateKey
2019/03/10 00:17:59 [INFO] Home directory for default CA: /Users/dengyi/Documents/devs/playground/fabric-ca-server-init
2019/03/10 00:17:59 [INFO] Initialization was successful

dengyi@localhost:~/Documents/devs/playground/fabric-ca-server-init$ tree
.
├── IssuerPublicKey
├── IssuerRevocationPublicKey
├── ca-cert.pem
├── fabric-ca-server-config.yaml
├── fabric-ca-server.db
└── msp
    └── keystore
        ├── 01678422b2ee8105849c3571ebde2266ac935a92e240caf1d95fdd7351625146_sk
        ├── IssuerRevocationPrivateKey
        └── IssuerSecretKey
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
    * 若无配置文件，则调用func (s *ServerCmd) createDefaultConfigFile，
    (`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/config.go:488`)
    使用默认配置模板，生成文件。模板变量位于`src/github.com/hyperledger/fabric-ca/cmd/fabric-ca-server/config.go:33`

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

