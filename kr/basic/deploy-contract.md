# 간단한 JS계약 발포

앞장에서 우리는 자기의 FIBOS 성공하게 연결했을 뿐만 아니라 간단한 조작도 했습니다. 본장에서 우리는 여러분들과 같이 간단한JavaScript 계약을 발포하겠습니다. 여러분들이 계약 발포하는 프로세스 빠르게 익숙하고 로컬 노드에서 자기의 JavaScript 계약 발포하는 데에 목적이 있습니다. 

* 본문 운행 환경：

오퍼레이팅 시스템：macOS

* 본장 관련되는 문장 목록：
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

본장 범례 코드 주소：[https://github.com/FIBOSIO/samples](https://github.com/FIBOSIO/samples)

## 학습준비

본 장절의 내용은 앞 장절의 지식에 기초합니다. 본장을 공부하는 과정중 어려움을 겪지 않도록 학습자가 본장을 공부하기 전에 [fibos.js와 FIBOS 인터랙션 사용](fibosjs.md) 및 [FIBOS 개발 환경 창건](install.md) 읽다고 강력하게 건의합니다. 

목록문서 설명:

```text
├── fibos_client
│   ├── call.js 调用合约接口脚本文件
│   ├── deploy.js 加载、发布合约脚本文件
│   ├── initClient.js FIBOS连接文件
│   ├── hello
│   │   ├── hello.abi 合约abi文件
│   │   └── hello.js 合约代码文件
```

설명 필요해야 하는 것은 아래와 같습니다. FIBOS 중에 같은 사용자가 동명 계약 하나만 발포 가능하고 내용이 똑같은 계약(즉 계약 코드hash치 같음)을 다시 발포하면 발포 계약 실패되고 계약 내용은 노드 계약 내용과 다르면 계약  갱신됩니다. 

## 자기의 JS계약 발포

1. hello 계약 번호
다음에 우리는 JavaScript를 통하여 계약의 기능을 편집합니다. 이 계약의 전용이 성공된 후 명령 줄에 수입하는 파라미터의 명칭이 수출됩니다. 
이하 코드는 작업 디렉토리 `./hello/hello.js`에 보존

```javascript
exports.hi = user => console.error('in contract:', user);
```

2. 계약 abi 문서

 다음에 abi 문서 정의합니다. 계약에게 abi는 행위 및 데이터 저장하는 표 등 기초 정보를 정의합니다. Abi에 대한 상세한 설명은 후속 글에 보여 줍니다. 
이하 코드는 작업 디렉토리` ./hello/hello.abi`에 보존

```javascript
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

3. 계약 각본 문서 로딩/ 발포
이하 코드는 작업 디렉토리 `deploy.js`에 보존
우리는 앞 장절에서 창건하는hello 라는 계정을 사용하여 상기 계약을 발포할 것입니다. 

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
집행 각본:

```text
fibos deploy.js
```

결과 수출\(부분\):
```text
code: {
  "account_name": "hello",
  "code_hash": "383a12daacaf124eea9afc529822d990853b5b99570401b8394534b746ea3977",
  "wast": "504b03042d00000008002cadfe4c6a9400a2360000003900000008001400696e6465782e6a7301001000000000000000000000000000000000004bad28c82f2a29d6cbc854b055282d4e2d52b0b55348cecf2bcecf49d54b2d2aca2fd250cfcc0389941425269758a9eb8055695a0300504b010200001400000008002cadfe4c6a9400a23600000039000000080000000000000001000000000000000000696e6465782e6a73504b0506000000000100010036000000700000000000",
  "wasm": ""
}
```

4. 전용 계약 이음새 각본 문서
이하 코드는 작업 디렉토리 `call.js` 에 보존


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
var ctx = fibos.contractSync(config["contractName"]); let i = ctx.hiSync('hello', { authorization: config["contractName"] });

```
집행 각본 

```text
fibos call.js
```

FIBOS 노드 서비스 콘솔에서 trxs:1 수출되면 전용 성공 인증합니다. 

```text
2018-07-30T14:28:22.005 thread-1 producer\_plugin.cpp:1196 produce\_block \] Produced block 00000e57c573a33b... \#3671 @ 2018-07-30T14:28:22.000 signed by eosio \[trxs: 1, lib: 3670, confirmed: 0\]
```