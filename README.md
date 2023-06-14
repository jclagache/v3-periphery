# Uniswap V3 Periphery Java contract wrapper

This repository contains Web3j generated smart contract wrapper code to interact with the periphery smart contracts for the Uniswap V3 Protocol.
For the periphery smart contracts sources, see the [uniswap-v3-periphery](https://github.com/Uniswap/uniswap-v3-periphery) repository.

## Maven
Add the repository to the repositories element of your project pom.xml file.
```xml
<repositories>
    <repository>
        <id>v3-periphery-repo</id>
        <url>https://maven.pkg.github.com/jclagache/v3-periphery/</url>
    </repository>
</repositories>
```

Add the package dependencies to the dependencies element of your project pom.xml file.
```xml
<dependencies>
 <dependency>
    <groupId>me.jclagache.amm.uniswap.v3</groupId>
    <artifactId>v3-periphery</artifactId>
    <version>1.4.3-SNAPSHOT</version>
  </dependency>
</dependencies>
```

## Gradle
Add the repository to your build.gradle file (Gradle Groovy) or build.gradle.kts file (Kotlin DSL) file.

Groovy:
```groovy
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/jclagache/v3-periphery")
    }
}
```
Kotlin:
```kotlin
repositories {
    maven {
        url = uri("https://maven.pkg.github.com/jclagache/v3-periphery")
    }
}
```

Add the package dependencies to your build.gradle file (Gradle Groovy) or build.gradle.kts file (Kotlin DSL) file.

Groovy:
```groovy
dependencies {
    implementation 'me.jclagache.amm.uniswap.v3:v3-periphery:1.4.3-SNAPSHOT'
}
```
Kotlin:
```kotlin
dependencies {
    implementation("me.jclagache.amm.uniswap.v3:v3-periphery:1.4.3-SNAPSHOT")
}
```

## Usage
Refer to the [Uniswap V3 Smart Contracts](https://docs.uniswap.org/contracts/v3/overview) and [Web3j](https://docs.web3j.io) documentation.

```java
Web3j web3j = Web3j.build(new HttpService("<your_node_url>"));
EthBlock.Block block = web3j.ethGetBlockByNumber(DefaultBlockParameterName.LATEST, false).send().getBlock();
// Mainnet
// Max Priority Fee : 2 Gwei
// Max Fee Per Gas = (2 * Base Fee) + Max Priority Fee
ContractEIP1559GasProvider gasProvider = new StaticEIP1559GasProvider(ChainId.MAIN_NET.getId(), block.getBaseFeePerGas().multiply(BigInteger.TWO).add(Convert.toWei("2", Convert.Unit.GWEI).toBigInteger()), Convert.toWei("2", Convert.Unit.GWEI).toBigInteger(), block.getGasLimit());
ISwapRouter router = ISwapRouter.load("0xE592427A0AEce92De3Edee1F18E0157C05861564", web3j, Credentials.create("your_private_key"), gasProvider);
// 1 WETH -> DAI
// fee 0.3%
// transaction expires in 300 seconds (5 minutes)
// slippage not taken into account
ISwapRouter.ExactInputSingleParams params = new ISwapRouter.ExactInputSingleParams("0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2", "0x6b175474e89094c44da98b954eedeac495271d0f", BigInteger.valueOf(3000), "my_address", block.getTimestamp().add(BigInteger.valueOf(300)), Convert.toWei("1", Convert.Unit.ETHER).toBigInteger(), BigInteger.ZERO, BigInteger.ZERO);
// value (1 ETH) will be automatically wrapped by the router
router.exactInputSingle(params, Convert.toWei("1", Convert.Unit.ETHER).toBigInteger()).send();
```