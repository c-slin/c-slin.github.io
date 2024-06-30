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