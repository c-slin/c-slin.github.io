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