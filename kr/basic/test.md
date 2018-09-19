# FIBOS 좋은 테스트 프레임 체험

자동 테스트 용례를 편집하지 않는 프로그래머는 좋은 테스트 엔지니어가 아닙니다. 우리는 모든 항목 시동 최초에 완전한 자동 테스트 용례를 건립하는 것 격려합니다. 항목의 발전에 따라 전기의 투입은 수백 배의 보답을 얻을 수 있습니다. 
본장을 읽은 후 FIBOS 테스트 용례의 편집을 습득할 수 있습니다. 

* 본문 운행 환경：

오퍼레이팅 시스템：macOS

* 본장 관련되는 문장 목록：
```text
  fibos_client/
  ├── call.js
  ├── deploy.js
  ├── hello
  │   ├── hello.abi
  │   └── hello.js
  ├── initClient.js
  └── test
    ├── case.js
    ├── sample_test.js
    └── test_contract.js
```

본장 범례 코드 주소：[https://github.com/FIBOSIO/samples](https://github.com/FIBOSIO/samples)

## 간단한 테스트 법례

테스트용 폴더 창건：
```text
mkdir test
cd test
```

이하 코드는 작업 디렉토리` sample_test.js`：에 보존

```javascript
var test = require('test');
test.setup();

describe('a sample case', () => {
    var name;

    before(() => {
        name = "FIBOS";
    });

    it('check name', () => {
        assert.equal(name, "FIBOS");
    });
});

test.run();
```

집행:

```text
fibos sample_test.js
```

결과 수출：

```text
    a sample case
    √ check name

  √ 1 tests completed (0ms)
```

## FIBOS 업무 장면 테스트 용례 편집 시작 

아하 코드는 본지 FIBOS 노드 서비스 운행하고 있는 것 확보합니다.

### 새로운FIBOS계정 창건하는 테스트 용례
이하 코드는 작업 디렉토리 `case.js`: 에 보존
```javascript
var test = require('test');
test.setup();

var FIBOS = require('fibos.js')
var config = {
    "chainId": "cf057bbfb72640471fd910bcb67639c22df9f92470936cddc1ade0e2f2e7dc4f",
    "producer-name": "eosio",
    "public-key": "FO6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "private-key": "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3",
    "httpEndpoint": "http://127.0.0.1:8888",
};


describe('new account FIBOS', () => {
    var fibos;

    before(() => {
        fibos = FIBOS({
            chainId: config["chainId"],
            keyProvider: config["private-key"],
            httpEndpoint: config["httpEndpoint"],
            logger: {
                log: null,
                error: null
            }
        });
    });

    it('new account', () => {
        fibos.newaccountSync({
            creator: 'eosio',
            name: "hellofibos",
            owner: config["public-key"],
            active: config["public-key"]
        })
    });

    it("get account", () => {
        var c = fibos.getAccountSync('hellofibos');
        assert.equal(c.account_name, "hellofibos");
    });
});

require.main === module && test.run(console.DEBUG);
```

결과 수출：

```text
 new account FIBOS
    √ new account
    √ get account

  √ 2 tests completed (13ms)
```
### 계약 테스트 용례: 

JavaScript 계약 기억하세요? 다음에 hello 계약에 관하여 테스트 용례 편집합니다. 
이하 코드는 작업 디렉토리 `test_contract.js`: 에 보존
```javascript
var test = require('test');
test.setup();

var FIBOS = require('fibos.js')
var contractName = "hello";
var fs = require("fs");
var config = {
    "chainId": "cf057bbfb72640471fd910bcb67639c22df9f92470936cddc1ade0e2f2e7dc4f",
    "producer-name": "eosio",
    "public-key": "FO6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",
    "private-key": "5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3",
    "httpEndpoint": "http://127.0.0.1:8888",
};


describe('contract test', () => {
    var fibos;

    before(() => {
        fibos = FIBOS({
            chainId: config["chainId"],
            keyProvider: config["private-key"],
            httpEndpoint: config["httpEndpoint"],
            logger: {
                log: null,
                error: null
            }
        });
    });

    it('get code', () => {
        var code = fibos.getCodeSync(contractName, true);
        assert.notEqual(code.code_hash, "0000000000000000000000000000000000000000000000000000000000000000");
    });

    it('setabi', () => {
        var abi = JSON.parse(fs.readTextFile("../hello/hello.abi"));
        fibos.setabiSync(contractName, abi);
    });
});

require.main === module && test.run(console.DEBUG);
```

결과 수출：
```text
  contract test
    √ get code
    √ setabi (405ms)

  √ 2 tests completed (420ms)
```