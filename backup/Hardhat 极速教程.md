# 前言

> 作者使用的 Nodejs 版本为 `v18.16.0`

`Hardhat`是一个用于开发以太坊智能合约和DApp的开发框架和工具套件。提供了一套功能强大的工具，用于编译、部署、测试和调试智能合约，并与以太坊网络进行交互。

通常情况下，我们习惯的开发方式是
1. 编译合约
2. 部署合约
3. 测试合约
4. 验证合约

Hardhat 提供了一站式的工具来帮助我们完成这些事情

`Hardhat` 官网: https://hardhat.org/

# 快速开始

> Hardhat 项目依赖于 npm 工程



### <span style="color: red">创建 npm 工程</span>
```cmd
mkdir a1
cd a1
npm init -y
```

### <span style="color: red">引入 hardhat 依赖</span>
```cmd
npm install --save-dev hardhat
```

### <span style="color: red">初始化 hardhat 工程</span>
```cmd
npx hardhat
```
通过键盘上下移动选择`Create a JavaScript project`

初始化后，以后输入`npx hardhat`出来的是 hardhat 的帮助信息


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




# 任务与插件

> 在Hardhat中，任务（task）是一种自定义命令或脚本，可用于执行各种开发任务和操作。


当在终端输入
```cmd
npx hardhat
```
输出:
```cmd
Hardhat version 2.17.0

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:
  ...
AVAILABLE TASKS:

  check                 Check whatever you need
  clean                 Clears the cache and deletes all artifacts
  compile               Compiles the entire project, building all artifacts
  console               Opens a hardhat console
  coverage              Generates a code coverage report for tests
  flatten               Flattens and prints contracts and their dependencies. If no file is passed, all the contracts in the project will be flattened.
  gas-reporter:merge
  help                  Prints this message
  node                  Starts a JSON-RPC server on top of Hardhat Network
  run                   Runs a user-defined script after compiling the project
  test                  Runs mocha tests
  typechain             Generate Typechain typings for compiled contracts
  verify                Verifies a contract on Etherscan

To get help for a specific task run: npx hardhat help [task]
```

`AVAILABLE TASKS` 栏目下的内容即为`任务`。

这种东西简单来说就是可以看作为一些自动化的脚本，可以帮我们完成一些繁琐的事情，比如打印当前节点控制的地址列表这些。

比如，我们之前用的`npx hardhat test`就是运行一个测试任务。

Hardhat内置的常用任务:
- npx hardhat compile：编译Solidity合约代码。
- npx hardhat test：运行测试脚本。
- npx hardhat run [path/to/script.js]：运行一个脚本。
- npx hardhat clean：清除构建输出和缓存文件。
- npx hardhat accounts：列出可用的账户信息。
- npx hardhat node：启动本地开发节点。


### <span style="color: red">创建任务</span>
创建自定义任务，其实本质就是往 `hardhat.config.js`添加一段代码罢了

下面是一个名为 `accounts` 的任务，作用是打印当前节点所控制的地址列表
```js
task("accounts", "Prints the list of accounts", async (taskArgs, hre) => {
  const accounts = await hre.ethers.getSigners();

  for (const account of accounts) {
    console.log(account.address);
  }
});
```

解释:
- 第一个参数是任务名
- 第二个参数是该任务的描述
- 第三个参数是一个异步函数，它在运行任务时执行
  - 第一个参数是具有任务参数的对象
  - 第二个参数是Hardhat运行时环境，包含Hardhat及其插件的所有功能。您还可以在任务执行过程中找到注入全局命名空间的所有属性。
### <span style="color: red">运行任务</span>
如果我们添加了上面的代码到 `hardhat.config.js` 那么在终端运行 `npx hardhat`将会输出
```cmd
Hardhat version 2.17.0

Usage: hardhat [GLOBAL OPTIONS] <TASK> [TASK OPTIONS]

GLOBAL OPTIONS:
  ...
AVAILABLE TASKS:

  accounts              Prints the list of accounts
  check                 Check whatever you need
  clean                 Clears the cache and deletes all artifacts
  compile               Compiles the entire project, building all artifacts
  console               Opens a hardhat console
  coverage              Generates a code coverage report for tests
  flatten               Flattens and prints contracts and their dependencies. If no file is passed, all the contracts in the project will be flattened.
  gas-reporter:merge
  help                  Prints this message
  node                  Starts a JSON-RPC server on top of Hardhat Network
  run                   Runs a user-defined script after compiling the project
  test                  Runs mocha tests
  typechain             Generate Typechain typings for compiled contracts
  verify                Verifies a contract on Etherscan

To get help for a specific task run: npx hardhat help [task]
```

可以发现多了一行:
```cmd
  accounts              Prints the list of accounts
```
这就是我们自定义的命令。事实上运行 `npx hardhat` 会自动扫描 `hardhat.config.js` 下的任务并添加到 Hardhat 中

运行任务:
```cmd
npx hardhat accounts
```
输出
```cmd
0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266
0x70997970C51812dc3A010C7d01b50e0d17dc79C8
0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC
0x90F79bf6EB2c4f870365E785982E1f101E93b906
0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65
0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc
0x976EA74026E726554dB657fA54763abd0C3a0aa9
0x14dC79964da2C08b23698B3D3cc7Ca32193d9955
0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f
0xa0Ee7A142d267C1f36714E4a8F75612F20a79720
0xBcd4042DE499D14e55001CcbB24a551F3b954096
0x71bE63f3384f5fb98995898A86B02Fb2426c5788
0xFABB0ac9d68B0B445fB7357272Ff202C5651694a
0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec
0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097
0xcd3B766CCDd6AE721141F452C550Ca635964ce71
0x2546BcD3c84621e976D8185a91A922aE77ECEc30
0xbDA5747bFD65F08deb54cb465eB87D40e51B197E
0xdD2FD4581271e230360230F9337D5c0430Bf44C0
0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199
```
成功打印当前节点所控制的地址列表





# 控制台

> 这个东西其实没什么好讲的，不好用，关键时候还得是编写脚本而不是在控制台瞎写。

Hardhat内置了一个交互式JavaScript控制台
```cmd
npx hardhat console
```

注意: `编译任务将在打开控制台提示之前被调用，但您可以使用--no编译参数跳过此操作。`

控制台的执行环境与任务、脚本和测试的执行环境相同。这意味着配置已经被处理，并且Hardhat运行时环境已经被初始化并注入到全局范围中。

### <span style="color: red">执行脚本</span>
```js
const scriptResult = await run("scripts/deploy.js");
console.log(scriptResult);
```
### <span style="color: red">合约交互</span>
```js
const hre = require("hardhat");

async function main() {
    const add = await hre.ethers.deployContract("Add");
    await add.waitForDeployment();
    console.log( `Add deployed to ${add.target}`);
}

main()
```






# 运行时

> 可以将Hardhat运行时视为整个Hardhat框架及其相关依赖的总称
> Hardhat运行时包括Hardhat框架本身以及其所依赖的各种模块、库和工具。

简而言之，Hardhat运行时(HRE) = Hardhat

HRE 就是一整个Hardhat工具箱，可以通过 HRE 来实现所有事情，它包含了一切 Hardhat 的依赖

示例`scripts/hre.js`
```js
// 导入所需的模块
const { ethers, run } = require("hardhat");

// 定义一个异步函数来包装我们的代码逻辑
async function main() {
    // 获取默认的以太坊提供商
    const provider = ethers.provider;

    // 打印当前网络的区块高度
    const blockNumber = await provider.getBlockNumber();
    console.log("当前网络的区块高度：", blockNumber);

    // 部署一个简单的合约
    const MyContract = await ethers.getContractFactory("MyContract");
    const contract = await MyContract.deploy();
    console.log("合约地址：", contract.address);
}

// 执行我们的代码逻辑
main()
.then(() => process.exit(0))
.catch(error => {
    console.error(error);
    process.exit(1);
});
```

运行
```cmd
npx hardhat run .\scripts\hre.js
```

输出
```cmd
当前网络的区块高度： 0
合约地址： 0x5FbDB2315678afecb367f032d93F642f64180aa3
```
HRE 作用一目了然。






# Fork 网络

> 本质是创建目标网络的分叉链，注意这不会拉取所有数据，而是要用到什么数据就拉取哪部分数据。


完成这个事情共2步骤
- 1. 节点配置

在 `hardhat.config.js` 配置
```js
networks: {
  hardhat: {
    forking: {
      url: "https://mainnet.infura.io/v3/<key>",
    }
  }
}
```

默认情况下将从最近的主网区块中分叉。但是我们建议从特定的块号进行分叉（防止出现奇奇怪怪的结果）。

```js
networks: {
  hardhat: {
    forking: {
      url: "https://mainnet.infura.io/v3/<key>",
      blockNumber: 14390000
    }
  }
}
```

或者通过命令行启动节点

```cmd
npx hardhat node --fork https://goerli.infura.io/v3/<key>
```

输出

```cmd
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.

Account #0: 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1: 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d

Account #2: 0x3C44CdDdB6a900fa2b585dd299e03d12FA4293BC (10000 ETH)
Private Key: 0x5de4111afa1a4b94908f83103eb1f1706367c2e68ca870fc3fb9a804cdab365a

Account #3: 0x90F79bf6EB2c4f870365E785982E1f101E93b906 (10000 ETH)
Private Key: 0x7c852118294e51e653712a81e05800f419141751be58f605c371e15141b007a6

Account #4: 0x15d34AAf54267DB7D7c367839AAf71A00a2C6A65 (10000 ETH)
Private Key: 0x47e179ec197488593b187f80a00eb0da91f1b9d0b13f8733639f19c30a34926a

Account #5: 0x9965507D1a55bcC2695C58ba16FB37d819B0A4dc (10000 ETH)
Private Key: 0x8b3a350cf5c34c9194ca85829a2df0ec3153be0318b5e2d3348e872092edffba

Account #6: 0x976EA74026E726554dB657fA54763abd0C3a0aa9 (10000 ETH)
Private Key: 0x92db14e403b83dfe3df233f83dfa3a0d7096f21ca9b0d6d6b8d88b2b4ec1564e

Account #7: 0x14dC79964da2C08b23698B3D3cc7Ca32193d9955 (10000 ETH)
Private Key: 0x4bbbf85ce3377467afe5d46f804f221813b2bb87f24d81f60f1fcdbf7cbf4356

Account #8: 0x23618e81E3f5cdF7f54C3d65f7FBc0aBf5B21E8f (10000 ETH)
Private Key: 0xdbda1821b80551c9d65939329250298aa3472ba22feea921c0cf5d620ea67b97

Account #9: 0xa0Ee7A142d267C1f36714E4a8F75612F20a79720 (10000 ETH)
Private Key: 0x2a871d0798f97d79848a013d4936a73bf4cc922c825d33c1cf7073dff6d409c6

Account #10: 0xBcd4042DE499D14e55001CcbB24a551F3b954096 (10000 ETH)
Private Key: 0xf214f2b2cd398c806f84e317254e0f0b801d0643303237d97a22a48e01628897

Account #11: 0x71bE63f3384f5fb98995898A86B02Fb2426c5788 (10000 ETH)
Private Key: 0x701b615bbdfb9de65240bc28bd21bbc0d996645a3dd57e7b12bc2bdf6f192c82

Account #12: 0xFABB0ac9d68B0B445fB7357272Ff202C5651694a (10000 ETH)
Private Key: 0xa267530f49f8280200edf313ee7af6b827f2a8bce2897751d06a843f644967b1

Account #13: 0x1CBd3b2770909D4e10f157cABC84C7264073C9Ec (10000 ETH)
Private Key: 0x47c99abed3324a2707c28affff1267e45918ec8c3f20b8aa892e8b065d2942dd

Account #14: 0xdF3e18d64BC6A983f673Ab319CCaE4f1a57C7097 (10000 ETH)
Private Key: 0xc526ee95bf44d8fc405a158bb884d9d1238d99f0612e9f33d006bb0789009aaa

Account #15: 0xcd3B766CCDd6AE721141F452C550Ca635964ce71 (10000 ETH)
Private Key: 0x8166f546bab6da521a8369cab06c5d2b9e46670292d85c875ee9ec20e84ffb61

Account #16: 0x2546BcD3c84621e976D8185a91A922aE77ECEc30 (10000 ETH)
Private Key: 0xea6c44ac03bff858b476bba40716402b03e41b8e97e276d1baec7c37d42484a0

Account #17: 0xbDA5747bFD65F08deb54cb465eB87D40e51B197E (10000 ETH)
Private Key: 0x689af8efa8c651a91ad287602527f3af2fe9f6501a7ac4b061667b5a93e037fd

Account #18: 0xdD2FD4581271e230360230F9337D5c0430Bf44C0 (10000 ETH)
Private Key: 0xde9be858da4a475276426320d5e9262ecfc3ba460bfac56360bfa6c4c28b4ee0

Account #19: 0x8626f6940E2eb28930eFb4CeF49B2d1F2C9C1199 (10000 ETH)
Private Key: 0xdf57089febbacf7ba0bc227dafbffa9fc08a93fdc68e1e42411a14efcf23656e

WARNING: These accounts, and their private keys, are publicly known.
Any funds sent to them on Mainnet or any other live network WILL BE LOST.
```

- 2. 代码应用

#### <span style="color: red">模拟账户</span>
Hardhat Network 允许您模拟任何地址。即使您无权访问其私钥，这也可以让您从该帐户发送交易。

下面是根据在 `hardhat.config.js`配置 fork 后的代码示例:

- 合约部分
```sol
pragma solidity ^0.8.9;

contract Fork {
    address immutable public owner;

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner {
        require(msg.sender == owner, "Only the owner can call this function.");
        _;
    }

    function test(uint a, uint b) public onlyOwner view returns(uint) {
        return a+b;
    }
}
```
作者已在 goerli 部署该合约，地址:  `0x1BC902734C51711301aee8D78a37CdAbF4123c57`


- 脚本部分`scripts/fork.js`
```js
const { ethers } = require("hardhat");

async function transfer() {
    const [sender] = await ethers.getSigners();
    const recipientAddress = "0x05421c943Db220a71531fD76E0267813a15cAB05"; 
    const amount = BigInt("1000000000000000000");
    const tx = await sender.sendTransaction({
        to: recipientAddress,
        value: amount,
    });
    await tx.wait();
    console.log("转账完成！");
}

(async () => {
    // 冒充所有者地址
  await hre.network.provider.request({
    method: "hardhat_impersonateAccount",
    params: ["0x05421c943Db220a71531fD76E0267813a15cAB05"]
  });

  // 让签名者冒充所有者地址
  const ownerSigner = await ethers.getSigner("0x05421c943Db220a71531fD76E0267813a15cAB05");

  // 获取部署的合约实例
  const contractAddress = "0x1BC902734C51711301aee8D78a37CdAbF4123c57";
  
  // 作者账户余额不足才需要这样，如果你够的话可以不用管这步骤
  await transfer()

  const Fork = await ethers.getContractFactory("Fork");
  const fork = await Fork.attach(contractAddress);

  // 使用所有者签名者连接到已部署的合约
  const connectedContract = fork.connect(ownerSigner);

  // 函数调用
  const result = await connectedContract.test(1, 21111);

  console.log('执行成功！')
})()
```

- 执行
```cmd
npx hardhat run .\scripts\fork.js --network hardhat
```

- 输出
```cmd
转账完成！
执行成功！
```