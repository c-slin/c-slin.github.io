# 合约操作

> 通常情况下，我们习惯的开发方式是:
> - 编写合约
> - 编译合约
> - 部署合约
> - 测试合约
> - 验证合约

### <span style="color: red">编写合约</span>
在 `contracts/` 目录下新建 `Add.sol`
```sol
// Hardhat 中文网: http://hardhat.cn
pragma solidity ^0.8.9;

contract Add {
    function add(uint a, uint b) public pure returns(uint) {
        return a+b;
    }
}
```


### <span style="color: red">编译合约</span>
这将会把 `contracts/` 目录下的所有合约进行编译，生成文件在 `artifacts/` 目录。

```cmd
npx hardhat compile
```
注意，为了提升编译速度，Hardhat 有缓存机制。Hardhat会将每个智能合约的编译结果缓存起来，以便在后续的编译过程中重复使用。

这意味着如果您没有对合约进行任何更改，Hardhat将直接从缓存中读取编译结果，而不需要重新编译整个合约。这样可以极大地减少编译时间，特别是在项目中存在多个合约的情况下。

- 清除编译缓存

```cmd
npx hardhat clean
```

- 强制编译
```cmd
npx hardhat compile --force
```

### <span style="color: red">部署合约</span>
默认情况下 Hardhat 会启动一个内部链进行合约部署，如果你想部署在 Ganache 或者其他网络，请参照官网: https://hardhat.org/ 进行修改 `hardhat.config.js`

这件事情分为2个步骤:
- 1. 编写 `scripts/deploy.js` 部署脚本。

这里使用的是 Hardhat 运行时进行编译，当然你也可以使用 `ethers.js` 或者 `web3js`
```js
const hre = require("hardhat");

async function main() {
    const add = await hre.ethers.deployContract("Add");
    await add.waitForDeployment();
    console.log( `Add deployed to ${add.target}`);
}

main().catch((error) => {
    console.error(error);
    process.exitCode = 1;
});

```
- 2. 部署
```cmd
npx hardhat run --network localhost scripts/deploy.js
```
成功后输出
```cmd
Add deployed to 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
### <span style="color: red">测试合约</span>

Hardhat 开发中常用 `Chai` 这个断言库进行合约测试，如果你不会用这个库的话不用慌，因为我也不会。可以去`Chai`官网学习一下https://www.chaijs.com/

这件事情分为2个步骤:
- 1. 创建测试文件

创建 `tests/Add.js`
```js
const { expect } = require("chai");

describe("Add", function () {
  it("should return the sum of two numbers", async function () {
    const Add = await ethers.getContractFactory("Add");
    const add = await Add.deploy();

    const result = await add.add(2, 3);
    expect(result).to.equal(5);
  });
});
```

- 2. 运行测试

事实上 `tests/` 这个目录下的所有文件为测试文件，运行下面的命令将会运行目录下所有 js 文件
```cmd
npx hardhat test
```
输出
```cmd
  Add
    ✔ should return the sum of two numbers (2240ms)


  1 passing (2s)
```

### <span style="color: red">合约开源</span>
> 请注意，下面的代码可能有问题，慎用。

本文使用 Ropsten 测试网络。为什么？作者习惯罢了。

分为2个步骤:

- 1. 配置Ropsten网络

在 `hardhat.config.js` 中添加下列代码:
添加Ropsten网络的配置。配置包括网络ID、RPC URL和帐户私钥。确保您有适当的私钥来签署和发送交易。

```js
module.exports = {
  networks: {
    ropsten: {
      url: "https://ropsten.infura.io/v3/YOUR_INFURA_PROJECT_ID",
      accounts: [PRIVATE_KEY],
    },
  },
};
```
注意将YOUR_INFURA_PROJECT_ID替换为您自己的Infura项目ID，并将PRIVATE_KEY替换为您的账户私钥。

- 2. 开源
```cmd
npx hardhat run --network ropsten scripts/deploy.js
```