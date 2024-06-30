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