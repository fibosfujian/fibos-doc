发布一个简单的 JS 合约
==========

在前面的章节我们成功连接了自己的 FIBOS 的节点，并且进行了一些简单操作。在这一章我们将带领大家发布一个简单的 JavaScript 合约，旨在让大家快速的了解发布合约的流程，并且上手在本地节点发布属于自己的 JavaScript 合约。

* 本文运行环境：

系统：macOS

* 本章涉及到文章列表：

```javascript
hello_fibos/
├── fibos_client
│   ├── call.js
│   ├── initClient.js
│   ├── deploy.js
│   ├── hello
│   │   ├── hello.abi
│   │   └── hello.js
│   └── package.json
└── start_fibos
    └── node.js
```

本章示例代码地址：<https://github.com/FIBOSIO/samples>

# 学习准备
本章节的内容基于前面章节知识的铺垫。强烈建议读者学习本章之前阅读[使用 fibos.js 与 FIBOS 交互](./use-fibos.md)和[搭建一个 FIBOS 开发环境](./development-environment.md)，以免在本章的学习过程中遇到困扰。

目录文件说明:
```javascipt
├── fibos_client
│   ├── call.js 调用合约接口脚本文件
│   ├── deploy.js 加载、发布合约脚本文件
│   ├── initClient.js FIBOS连接文件
│   ├── hello
│   │   ├── hello.abi 合约abi文件
│   │   └── hello.js 合约代码文件
```

需要说明的是，在 FIBOS 中一个用户只能发布一个同名的合约，如果再次发布相同内容的合约（即合约代码hash值相同），发布合约会失败，如果合约内容与节点合约内容不同，则会更新合约。

# 发布自己的 JS 合约
1. hello 合约代码
在下面我们通过 JavaScript 来编写合约的功能，在这个合约调用成功后，将在命令行输出传入参数的名称。

以下代码保存至工作目录 ./hello/hello.js
```javascript
exports.hi = user => console.error('in contract:', user);
```

2. 合约 abi 文件
下面定义了一份 abi 文件，对于合约而言，abi 定义了 action 行为和数据储存的的表等基础信息。对于 abi 更详细的解读，我们将在后续的文章中给出。

以下代码保存至工作目录` ./hello/hello.abi`
```json
{
    "version": "eosio::abi/1.0",
    "structs": [{
        "name": "player",
        "base": "",
        "fields": [{
            "name": "title",
            "type": "string"
        }, {
            "name": "age",
            "type": "int64"
        }]
    }, {
        "name": "hi",
        "base": "",
        "fields": [{
            "name": "user",
            "type": "name"
        }]
    }],
    "actions": [{
        "name": "hi",
        "type": "hi",
        "ricardian_contract": ""
    }]
}
```

3. 加载、发布合约脚本文件
以下代码保存至工作目录 `deploy.js`

我们将使用前面章节创建的 hello 用户来发布一个上述合约。

```javascript
var FIBOS = require('./initClient.js')
var fs = require("fs");
var config = {
    "public-key": "FO6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "private-key": "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3",
    "contractName": "hello"
};

// new FIBOS client
var fibos = FIBOS(config["private-key"]);

//setcode
var js_code = fs.readTextFile("./hello/hello.js");
fibos.setcodeSync(config["contractName"], 0, 0, fibos.compileCode(js_code));

//getcode
var code = fibos.getCodeSync(config["contractName"], true);
console.log("code:", code);

//setabi
var abi = JSON.parse(fs.readTextFile("./hello/hello.abi"));
fibos.setabiSync(config["contractName"], abi);
```

执行脚本:
```
fibos deploy.js
```
输出结果(片段):
```
code: {
  "account_name": "hello",
  "code_hash": "383a12daacaf124eea9afc529822d990853b5b99570401b8394534b746ea3977",
  "wast": "504b03042d00000008002cadfe4c6a9400a2360000003900000008001400696e6465782e6a7301001000000000000000000000000000000000004bad28c82f2a29d6cbc854b055282d4e2d52b0b55348cecf2bcecf49d54b2d2aca2fd250cfcc0389941425269758a9eb8055695a0300504b010200001400000008002cadfe4c6a9400a23600000039000000080000000000000001000000000000000000696e6465782e6a73504b0506000000000100010036000000700000000000",
  "wasm": ""
}
```
4. 调用合约接口脚本文件
以下代码保存至工作目录 `call.js`:
```javascript
var FIBOS = require('./initClient.js')
var config = {
    "public-key": "FO6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "private-key": "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3",
    "contractName": "hello"
};

// new FIBOS client
var fibos = FIBOS(config["private-key"]);

//call abi
var ctx = fibos.contractSync(config["contractName"]);
let i = ctx.hiSync('hello', {
    authorization: config["contractName"]
});
```
执行脚本:
```
fibos call.js
```

在 FIBOS 节点服务控制台输出 `trxs:1`，说明调用成功 :
```
2018-07-30T14:28:22.005 thread-1   producer_plugin.cpp:1196      produce_block        ] Produced block 00000e57c573a33b... #3671 @ 2018-07-30T14:28:22.000 signed by eosio [trxs: 1, lib: 3670, confirmed: 0]
```
# 体验 FIBOS 超棒的测试框架
这几章我们学会了如何搭建 FIBOS 的开发环境，也学会了如何使用 fibos.js 与 FIBOS 交互，那么让我们来试试 FIBOS 的测试框架吧!