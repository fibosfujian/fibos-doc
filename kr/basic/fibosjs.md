# fibos.js와 FIBOS 인터랙션 사용

fibos.js는 통용 JavaScript Library 하나로서 간단한 코딩을 통하여 FIBO의 JavaScript Client 되고 FIBOS 및 EOS 블록체인과 인터랙션 가능합니다. 본장을 읽은 후 fibos.js 와 FIBOS 인터랙션 습득할 수 있습니다. 

* 본문 운행 환경：

오퍼레이팅 시스템：macOS

* 본장 관련되는 문장 목록:

```text
hello_fibos/
├── fibos_client
│   ├── createAccount.js
│   ├── initClient.js
│   ├── queryAccount.js
│   └── package.json
└── start_fibos
    └── node.js
```

* 본장 범례 코드 주소: [https://github.com/FIBOSIO/samples](https://github.com/FIBOSIO/samples)

## fibos.js 설치

FIBOS패키지 관리(Package Management) 방식 지지하고`fibos --install fibos.js` 통하여 설치 가능합니다.

1. 작업 디렉토리 진입

```text
~$ cd fibos_client/
```

2. 초기화 환경

```text
fibos_client$ fibos --init
```

특별 요구 사항 없으면 계속 리턴키 클릭하고 정보 수출됩니다:

```text
Press ^C at any time to quit.
name: (fibos_client)
version: (1.0.0)
description:
repository:
keywords:
author:
license: (ISC)
About to write to $[f}:
{
  "name": "fibos_client",
  "version": "1.0.0",
  "description": "",
  "repository": "",
  "keywords": "",
  "author": "",
  "license": "ISC"
}
Is this ok? (yes)
```

3. fibos.js 설치

오류 피드백 없으면 fibos.js 설치 성공 인증되고 코딩 시작합시다! 

## fibos.js와 FIBOS 인터랙션 사용

경로 신설：

```text
mkdir hello_fibos/fibos_client/
mkdir hello_fibos/fibos_client/hello/
```

다음에 여러분들께 fibos.js와 FIBOS 인터랙션 사용 보여줍니다. 우리는 직접 운행하는 FIBOS 로컬 노드 기억하세요? 그를 운행하는 상태를 유지해 주세요. 우리는 로컬 노드에서 간단한 조작을 진행할 것입니다. 예를 들어, 계정을 창건한 후에 정보를 조회합니다. 

조작 익숙한 후 후속 정절에서 여러분들과 같이 자기만의 계약 창작하고 로컬 노드에 배치합니다.

FIBOS 연결 창건: 목전 우리는 자기의 FIBOS 노드를 가지게 되고 현재 그와 연결합니다. 자기의 FIBOS 서비스를 창건하는 방법을 잘 모르는 분들이 제2장 ‘FIBOS 개발 환경 내용 창건’ 참조하세요. 

이하 코드는 initClient.js에 보존
```javascript
var FIBOS = require("fibos.js");

function initClient(_keyProvider) {
    return FIBOS({
        chainId: "cf057bbfb72640471fd910bcb67639c22df9f92470936cddc1ade0e2f2e7dc4f",
        keyProvider: _keyProvider, 
        httpEndpoint: "http://127.0.0.1:8888",
        logger: {
            log: null,
            error: null
        }
    });
}

module.exports = initClient;
```

1. 계정 창건
이하 코드는 `createAccount.js` 에 보존
```javascript
var FIBOS = require('./initClient.js')
var config = {
    "public-key": "FO6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "private-key": "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3"
};

// new FIBOS client
var fibos = FIBOS(config["private-key"]);

fibos.newaccountSync({
    creator: 'eosio',
    name: "hello",
    owner: config["public-key"],
    active: config["public-key"]
});
```

이상 법례 중에 우리는 `eosio` 라는 계정을 사용하여 `hello`라는 계정을 창건 했습니다. 
후속 예중에서 우리는 이 계정을 사용하여 계약 발포하고 전용할 것입니다. 그러므로 성공하게 창건하는 것 확실하셔야 합니다. 

2. 계정 정보 조회
이하 코드는 `queryAccount.js` 에 보존

```javascript
var FIBOS = require('./initClient.js')
// new FIBOS client
var fibos = FIBOS("5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3");

var user = fibos.getAccountSync("hello");
console.warn('---- user ----', user);
```

