<!-- @format -->

# Truffle - 배포용 도구

## 트러플을 사용하는 이유

1. 쉬운 스마트 컨트랙트 연결
2. 자동화된 `ABI` 만들기
3. 스마트 컨트랙트 테스팅이 합쳐져 있음(`chai`랑 `Mocha`도 지원)
4. 여러 네트워크 지원

## 트러플 기본 구조

### 기본으로 설치되는 tree 구조

```shell
├── contracts
    ├── Migrations.sol
├── migrations
    ├── 1_initial_migration.js
└── test
truffle-config.js
truffle.js
```

- `truffle init` 명령어를 실행하면 위와 같은 기본 구조가 설치된다.

### 구조 설명

- `contracts` : 트러플이 내가 만든 컨트랙트를 찾는 곳. 보통 `contracts/tokens` 처럼 `nested`로 정리한다.
- `migrations` : 트러플에게 어떻게 스마트 컨트랙트를 배포할지 말하는 자바스크립트 파일이 위치
- `test` : 유닛 테스트 파일을 넣는 곳
- `truffle.js` : 네트워크 세팅을 저장하는 곳
- `truffle.config.js` : 윈도우 쓰면 `truffle.js` 지우고 이걸 `config` 파일로 사용해야 한다.

## truffle-hdwallet-provider

> 트랜잭션 sign을 하기 위해 설치하는 프로바이더!
> 보통 `truffle init` 하고 바로 설치함!

- 우리는 `Infura` 를 사용해서 이더리움에 코드를 배포할 것이다.
- `Infura` 를 쓰면 이더리움 노드나 지갑을 직접 `run` 할 필요가 없다.
- 하지만 인퓨라의 경우 프라이빗 키를 제공하지 않아서 트랜잭션을 직접 시작할 수 없다.
- 그래서 트랜잭션을 시작할 수 있는 `truffle-hdwallet-provider`를 따로 설치해서 이용한다.

# Compiler

## 컴파일러를 사용하는 이유

- 이더리움 가상 머신은 솔리디티로 작성된 코드를 바로 이해하지 못한다.
- 따라서 머신이 읽을 수 있도록 컴파일 하는 것이 필요하다.

## 스마트 컨트랙트 컴파일 하기

```shell
├── contracts
    ├── Migrations.sol
    ├── CryptoZombies.sol
    ├── erc721.sol
    ├── ownable.sol
    ├── safemath.sol
    ├── zombieattack.sol
    ├── zombiefactory.sol
    ├── zombiefeeding.sol
    ├── zombiehelper.sol
    ├── zombieownership.sol
├── migrations
└── test
```

- 스마트 컨트랙트 코드를 `contracts` 안에 넣고 컴파일 진행

# 로컬 테스트

## Ganache 이용 로컬 테스트 - migrations

### 새 migration 만들기

```js
var Migrations = artifacts.require("./Migrations.sol");
module.exports = function (deployer) {
	deployer.deploy(Migrations);
};
```

1. 이 스크립트는 트러플에게 우리가 `migration` 컨트랙트와 통신하기를 원한다는 것을 알려준다
2. 이 스크립트는 `deployer`라는 객체를 받는 함수를 `export`한다

- 여기서 `deployer`라는 객체는 개발자와 프러플의 개발 엔진 간의 인터렉션을 담당한다.

### 내 앱에 맞게 migrations 수정하기

```js
var CryptoZombies = artifacts.require("./CryptoZombies.sol");
module.exports = function (deployer) {
	deployer.deploy(CryptoZombies);
};
```

- 내 앱에 맞게 변수 이름 수정하기!

# Testnet 이용 - Rinkeby

## Config 파일 수정으로 네트워크 이용 설정하기

> 아래 코드는 메인넷과 rinkeby 의 config 객체 두개에 대한 설정을 담고있다.

```js
networks: {
  // Configuration for mainnet
  mainnet: {
    provider: function () {
      // Setting the provider with the Infura Rinkeby address and Token
      return new HDWalletProvider(mnemonic, "https://mainnet.infura.io/v3/YOUR_TOKEN")
    },
    network_id: "1"
  },
  // Configuration for rinkeby network
  rinkeby: {
    // Special function to setup the provider
    provider: function () {
      // Setting the provider with the Infura Rinkeby address and Token
      return new HDWalletProvider(mnemonic, "https://rinkeby.infura.io/v3/YOUR_TOKEN")
    },
    // Network id is 4 for Rinkeby
    network_id: 4
  }
```

- 트러플에게 메인넷을 알려준다.
- 트러플에게 `rinkeby` 를 사용할거라고 알려준다.

## config 파일 전체 모습

```js
// Initialize HDWalletProvider
const HDWalletProvider = require("truffle-hdwallet-provider");

// Set your own mnemonic here
const mnemonic = "YOUR_MNEMONIC";

// Module exports to make this configuration available to Truffle itself
module.exports = {
	// Object with configuration for each network
	networks: {
		// Configuration for mainnet
		mainnet: {
			provider: function () {
				// Setting the provider with the Infura Rinkeby address and Token
				return new HDWalletProvider(
					mnemonic,
					"https://mainnet.infura.io/v3/YOUR_TOKEN"
				);
			},
			network_id: "1",
		},
		// Configuration for rinkeby network
		rinkeby: {
			// Special function to setup the provider
			provider: function () {
				// Setting the provider with the Infura Rinkeby address and Token
				return new HDWalletProvider(
					mnemonic,
					"https://rinkeby.infura.io/v3/YOUR_TOKEN"
				);
			},
			// Network id is 4 for Rinkeby
			network_id: 4,
		},
	},
};
```

## Faucet으로 이더 받기

https://faucet.rinkeby.io/

- 위 링크에서 주소로 이더 받기 가능!

## migrate 실행

![](https://images.velog.io/images/dandev_sw/post/c56f5a7d-d1e5-4af5-ad84-45c7bc7a9bca/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-03-06%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.42.32.png)

- 위 처럼 `truffle migrate --network rinkeby` 명령어로 실행
