# FIBOS 개발 환경 창건

본장을 읽은 후 FIBOS 개발 환경 창건하고 후속의 개발에 준비가 잘 됩니다. 우선 curl공구를 통하여 FIBOS 빠르게 설치하는 방법 배울 뿐만 아니라 UNIX 시스템에서 FIBOS 편역하고 설치하는 방법도 배우게 됩니다. 
FIBOS 자주 쓰는  UNIX 조작 시스템을 지지합니다. 예를 들어 Mac OSX, Linux 및 FreeBSD.
초보 사용자에게 우리는 빠르게 설치하는 방식을 추천하고 고급 사용자는 본장 UNIX 조작 시스템을 참고하여 편역하면 됩니다. 
다음에 우리는 여러분들과 같이 간단한 본지 FIBOS 노드를 창건하고 여러분들 공부의 수요를 확보합니다. 
 
## 빠른 설치

```text
curl -s https://fibos.io/download/installer.sh |sh
```
설치 결속 후 FIBOS executable file시스템 bin 목록 하에 있어 사용시 FIBOS 판본 참조：

```text
~$ which fibos
/usr/local/bin/fibos

~$ fibos --version
v0.27.0-dev
```

FIBOS는 executable file입니다, FIBJS의 JavaScript CLI 명령줄 콘솔 기능을 계승하고 FIBOS 엔터키 집행하면 명령줄 인터랙션 모식에 들어갑니다. 예를 들어: 

```text
~$ fibos
Welcome to fibjs 0.26.0-dev.
Type ".help" for more information.
> console.log("hello,FIBOS!")
hello,FIBOS!
> .info
{
  "fibjs": "0.27.0-dev",
  "git": "v0.26.0-4-gb2bc253c4",
  "clang": "9.1",
  "date": "Aug 19 2018 17:40:51",
  "vender": {
    "ev": "4.24",
    "expat": "2.2.5",
    "gd": "2.2.4",
    "jpeg": "8.3",
    "leveldb": "1.17",
    "mongo": "0.7",
    "pcre": "8.21",
    "png": "1.5.4",
    "mbedtls": "2.6.1",
    "snappy": "1.1.2",
    "sqlite": "3.23.0",
    "tiff": "3.9.5",
    "uuid": "1.6.2",
    "v8": "6.8.275.14",
    "v8-snapshot": true,
    "zlib": "1.2.7",
    "zmq": "3.1"
  }
}
>
```

### FIBOS 자주 쓰는 명령： 

1. package.json 설정 초기화
효과는 npm init 와 같음

```text
fibos --init
```

2.	패키지
효과는 `npm install fibos.js`와 같음

```text
fibos --install fibos.js
```

## 자기의 FIBOS 노드 운행

목전까지 우리는 집행이 가능한 FIBOS 가지게 되고 JavaScript 계약을 편집한다고 크게 솜씨를 떨칩니다. 성급하지 마시고 다음에 여러분들과 같이 간단한 FIBOS 개발 환경을 창건합니다. 후속 내용이 전부 이 환경에 근거하기 때문에 자세히 읽고 후속 공부 과정 중에 이 노드 정상적으로 운행하는 것 확보해야 합니다. 

* 본문 운행환경：
  
  오퍼레이팅 시스템： macOS

* 본장 관련되는 문장 목록：
  
  ```text
  hello_fibos/
  └── start_fibos
    └── node.js
  ```

  * 본장 범례 코드 주소：[https://github.com/FIBOSIO/samples](https://github.com/FIBOSIO/samples)

### 환경 설정 각본

```javascript
var fibos = require('fibos');

fibos.load("http");
fibos.load("chain");
fibos.load("net");
fibos.load("chain_api");
fibos.load("history_api");
fibos.load("producer", {
    'producer-name': 'eosio',
    'enable-stale-production': true
});
fibos.config_dir = "fibos_config_dir/";
fibos.data_dir = "fibos_data_dir/";

fibos.start();
```

이상 코드는 작업 디렉토리 `node.js` 에 보존

```text
~$ cd start_fibos/
fibos$ vim node.js
fibos$ ls
node.js
```

FIBOS 개발 환경 운행:
```text
fibos node.js
```

운행 결과 일지（부분）:

```text
fibos$ fibos node.js
……
2018-07-30T03:29:01.004 thread-1   producer_plugin.cpp:1194      produce_block        ] Produced block 00000002e091c956... #2 @ 2018-07-30T03:29:01.000 signed by eosio [trxs: 0, lib: 0, confirmed: 0]
```

이상 메시지 보이면 운행 성공 인증하고 `eosio` 블록 생산 시작합니다.
FIBOS 노드 서비스를 성공하게 운영하는 것 축하드립니다. 이제 본지 코딩 테스트 시작하고 fibos.js와 FIBOS 인터랙션 사용할 수 있습니다. 

### 고급용법
FIBOS중 load 방법은 파라미터 전송 지지하고 상세한 설명은 아래와 같습니다.

1. FIBOS 감청 포트 및 주소 수정
   HTTP 서비스 모든 주소에 대한 8889 포트 감청 오픈
   P2P 서비스 모든 주소에 대한 9877 포트 감청 오픈

```javascript
fibos.load("http", {
    "http-server-address": "0.0.0.0:8889"
});

fibos.load("net", {
    "p2p-listen-endpoint": "0.0.0.0:9877"
});
```

(tips: FIBOS 목인 HTTP 포트 8888，P2P 포트 감청9876)

2. FIBOS 설정 및 데이터 목록의 수정 및 살펴봄

```javascript
// 查看 FIBOS 配置以及数据目录
console.notice("config_dir:", fibos.config_dir);
console.notice("data_dir:", fibos.data_dir);

// 修改 FIBOS 配置以及数据目录
fibos.config_dir = "fibos_config_dir/";
fibos.data_dir = "fibos_data_dir/";
```

3.	FIBOS 서비스 시동시 환경 데이터 재취득 설치 

개발 과정중 환경 데이터 재취득 필요시 아래와 같은 설정 사용 가능:
```javascript
fibos.load("chain", {
    "delete-all-blocks": true
});
```



