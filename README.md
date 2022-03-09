# 1 准备工作

\[TOC]

## 准备工作

#### 以太坊 Ethereum

> 以太坊（Ethereum）是基于区块链技术的分布式平台，没有中心化的管理，可以运行智能合约程序。
>
> * 以太坊上智能合约程序部署不需要审查，运行中也不会出现停机。
> * 以太坊是基于点对点网络的，每个以太坊节点都是分布式网络上的点，没有中心化服务器。
> * 以太坊虚拟机（Ethereum Virtual Machine） EVM 上可以运行智能合约，在EVM上执行的每一个指令都会被以太坊网络上的每个节点同时执行。EVM 是一个动态运行沙盒，可以将以太坊上所有智能合约和周围环境全部隔离，因此 EVM 上运行的智能合约无法访问网络、文件系统或者在EVM上运行其它进程。
> * 每个以太坊节点都可以读写以太坊的链上数据，全节点会下载区块链上所有区块数据。

\


**以太坊燃料 Gas**

> 燃料（Gas）对应一个交易（Transaction）中以太坊 EVM 的实际运算步数。
>
> * 越简单的交易运算步数就越小需要的燃料就越少，反之，做越复杂的运算需要的燃料就越多。
> * 在以太坊平台任何计算都需要支付燃料费，燃料最小单位是wei，1个以太坊代币（1 ether）= 10的18次方wei = 10的9次方 Gwei。
> * 愿意支付的最大燃料费 = Gas Limit \* Gas Price，Gas Limit 是愿意支付的燃料上限，Gas Price 是燃料单价。
> * 交易中设置的最大费用如果没有消耗完，多余的费用会退回。但是如果燃料耗尽为负数，将会触发一个 out-of-gas 异常，当前调用所做都会被回滚但消耗的费用不会退回，因为这些燃料费已经奖励给矿工。
> * 燃料收费项包括计算操作的固定费用、交易（合约创建或消息调用费用）、存储（存储账户合约数据）费用，存储费用是指因为合约使得状态数据库存储增大所有节点都会增加存储，以太坊鼓励保持少量存储，有操作清除存储条目的操作不收费还会返还释放空间的费用。

\


**燃料组成**

> 燃料分为燃料成本（Gas Cost）、燃料价格（Gas Price）和燃料上限（Gas Limit）。
>
> * 手续费计算公式为 交易手续费（Tx Fee） = 实际运行步数（Actual Gas Cost） \* 单步燃料价格（Gas Price）。

> 燃料成本（Gas Cost）
>
> * 燃料成本是每个操作需要的燃料单位数，以太坊已经预先定义好每个操作的燃料成本，燃料成本不随ether的发币价值发生变化。

> 燃料价格（Gas Price）
>
> * 燃料价格是一单位的燃料等于多少ether，使用Gwei做单位。
> * 当设定燃料价格是20Gwei等同于支付0.00000002 ether。
> * 燃料价格越高交易中每运算一步就支付更多的额ether，但是用户也可以支付更高的燃料费吸进矿工优先处理交易。

> 燃料上限 （Gas Limit）
>
> * 燃料上限是一次交易中心燃料的可用上限，就是交易中心最多会执行多少步运算。
> * 燃料上限会比交易实际所需要的燃料数量要大，因为交易的复杂程度不同，确切的燃料数量在完成交易后才会知道。因此用户在提交交易之前需要为交易设定一个燃料上限。
> * 如果设置的燃料上限太低，矿工会尝试完成交易直到燃料耗尽，然后交易失败。

\


#### Solidity 源文件基本结构

> Solidity 是基于合约的高级编程语言，和 JavaScript 语法相似。
>
> * Solidity 是静态类型语言，它编写的程序在编译成EVM的汇编语言后可以被EVM执行，另外 Solidity 在编译期间也会做静态类型检查（基础语法检查、函数调用合规等）。
> * 除了 Solidity 外，智能合约的编程语言还有Serpent、Vyper 和 LLL，Solidity 是智能合约编程语言中比较热门的。
> * Solidity 合约源文件以 .sol 为后缀，源文件中可以包含任意数量的合约定义、导入源文件、版本标识、结构体、枚举和函数。

\


**SPDX 许可证标识符注释**

> 源文件开头一般是SPDX 许可证标识符注释
>
> * SPDX 许可证标识符注释，如 `// SPDX-License-Identifier: MIT`，这只是一行注释，编译器不会去理睬它，它是给人看的，是告诉别人在什么情况下可以使用这个文件而不需要承担法律责任。
> * 提供源代码会涉及版权方面的法律问题，Solidity 编译器鼓励使用机器可读的 SPDX 许可证标识符。
> * 除了 MIT 还有很多SPDX许可证，如 `// SPDX-License-Identifier: Apache-2.0 OR MIT` 被许可人可以选择在 Apache-2.0 许可或MIT许可下使用该文件，`// SPDX-License-Identifier: Apache-2.0 AND MIT` 该文件受 Apache-2.0 许可证和MIT 许可证的约束，被许可人在使用文件时必须遵守这两个许可。
> * 在智能合约开发中，大部分是使用 `// SPDX-License-Identifier: MIT` ，更多 SPDX 许可相关访问 https://spdx.dev/ids/#how 。

\


**Pragmas 启用编译器检查**

> pragma 关键字是版本标识指令，用于启用某些编译器检查
>
> * 版本标识 pragma 指令通常只对本文件有效，所以需要把这个版本标识 pragma 添加到项目中所有的源文件。
> * 如果使用了 import 导入 其它文件，标识 pragma 并不会从被导入的文件，加入到导入的文件中。

> 编译器版本标识
>
> * 为了避免可能引入不兼容更新的编译器所编译，源文件使用 pragma 版本标识。
> * Solidity 处在快速的发展之中，所以很难保证不引入修改语法的变更，一般情况下编译版本标识都不要超过一个大版本，如 `pragma solidity ^0.8.0` 。
> * Solidity 版本号的形式通常是 0.x.x。
> * 版本标识方式1：`pragma solidity >=0.6.2 <0.8.0;` 大于等于 0.6.2 小于 0.8.0 版本的编译器可编译。
> * 版本标识方式2：`pragma solidity ^0.7.0;` 低于 0.7.0 高于等于 0.8.0 版本的编译器不可编译。
> * 对含重大变更的版本，通过查看变更日志了解，变更日志查看 https://learnblockchain.cn/docs/solidity/080-breaking-changes.html。

> 编码器标识 ABI Coder Pragma
>
> * 通过使用 `pragma abicoder v1` 或 `pragma abicoder v2`，可以在 ABI 编码器和解码器的两种实现之间进行选择，用途是对源码进行编码、解码。
> * 新的 ABI 编码器 （v2） 能够编码和解码任意嵌套的数组和结构，从 Solidity 0.6.0 开始被认为是非实验性的，从 Solidity 0.8.0 开始默认激活（v2）。
> * Solidity 0.8.0以后要使用旧编码器就要显示激活 `pragma abicoder v1;` 。
> * 新编码器支持的类型集是旧编码器支持的类型的严格超集。使用它的合约可以与没有限制的合约进行交互。当非 abicoder v2 的合约不尝试进行需要由新编码器支持的解码类型的调用时，才能反向。编译器可以检测到这一点并将发出错误。只需为合约启用 abicoder v2 就足以消除错误。
> * 编码器标识编译指示适用于激活它的文件中定义的所有代码，无论该代码最终在何处结束。这意味着选择了源文件以使用 ABI coder v1 编译的合约仍然可以包含通过从另一个合约继承来使用新编码器的代码。如果新类型仅在内部使用而不在外部函数签名中使用，则允许这样做。
> * 在 Solidity 0.7.4 之前，可以使用 `pragma Experimental ABIEncoderV2` 选择 ABI coder v2 的实验版本，但无法显式选择 coder v1，因为它是默认设置。

> SMTChecker 验证程序等价性
>
> * SMT（Satisfiability modulo theories），如果使用自己编译的 Solidity 编译器，这个模块就可以启用。
> * 使用 `pragma experimental SMTChecker;` 就可以获得 SMT solver 额外的安全检查。但是这个模块目前不支持 Solidity 的全部语法特性，因此有可能输出一些警告信息。

\


**导入其它源文件 import**

> 通过 import 关键字导入其它源文件。

> import "filename";
>
> * 从 “filename” 中所有全局参数导入到当前全局作用域中，这种形式已经不建议使用，因为它会无法预测地污染当前命名空间导致命名冲突。

> import \* as symbolName from "filename";
>
> * 导入“filename”所有参数到当前全局作用域，所有全局参数都以 `symbolName.symbol` 格式访问。

> import "filename" as symbolName; 等价于 import \* as symbolName from "filename";。

> import {symbol1 as alias, symbol2} from "filename";
>
> * 在导入时重命名符号，创建了新的全局符号 alias 和 symbol2 ，引用的 symbol1 和 symbol2 来自 “filename” 。

> 路径
>
> * `import "filename";` 按路径来处理，以 / 作为目录分割符、以 . 标示当前目录、以 .. 表示父目录。 当 . 或 .. 后面跟随的字符是 / 时，它们才能被当做当前目录或父目录。 只有路径以当前目录 . 或父目录 .. 开头时，才能被视为相对路径。
> * 用 `import "./filename" as symbolName;` 语句导入当前源文件同目录下的文件 filename 。 如果用 `import "filename" as symbolName;` 代替，可能会引入不同的（如在全局 include directory 中）文件。

> 编译器映射路径
>
> * 当运行编译器时，它不仅能指定如何发现路径的第一个元素，还可指定路径前缀 重映射。 例如 `github.com/ethereum/dapp-bin/library` 会被重映射到 `/usr/local/dapp-bin/library`， 此时编译器将从重映射位置读取文件。如果重映射到多个路径，优先尝试重映射路径最长的一个。 这允许将比如 `""` 被映射到 `"/usr/local/include/solidity"` 来进行“回退重映射”。 同时，这些重映射可取决于上下文，允许你配置要导入的包，比如同一个库的不同版本。
> * 对于 solc（命令行编译器），这些重映射以 `context:prefix=target` 形式的参数提供。 其中，`context:` 和 `=target` 部分是可选的（此时 target 默认为 prefix ）。 所有重映射的值都是被编译过的常规文件（包括他们的依赖），这个机制完全是向后兼容的（只要文件名不包含 = 或 : ）， 因此这不是一个破坏性修改。
> * 在 content 目录或其子目录中的源码文件中，所有导入语句里以 prefix 开头的导入文件都将被用 target 替换 prefix 来重定向。

\


**注释**

> 单行注释
>
> * `// 单行注释`

> 多行注释

```
/* 
    多行注释 
    注释
*/
```

> 文档注释

```
/**
 * 文档注释
 * @dev 主要合约代码
 */
```

> 合约的文档注释

```
/**
 * @title 这是xxx合约
 * @dev 基于xxx标准实现的合约
 */
```

> 函数的文档注释

```
/**
 * @dev 函数的作用
 * @param 参数1 这是个什么样的参数...
 * @param 参数2 这个参数的作用描述...
 */
```

\
\


#### 智能合约在线开发环境 remix

> Solidity 智能合约开发环境最方便的莫过于 remix。
>
> * remix 是一个打开浏览器就可以编写、部署、调试智能合约的开发环境。
> * 浏览器打开 https://remix.ethereum.org/ 就可以进行智能合约开发。

\


**钱包 MetaMask**

> MetaMask 是一个浏览器插件钱包，在 chrome 应用商店安装 MetaMask。
>
> * 安装 MetaMask 后创建一个以太坊钱包，记录助记词就完成。
> * 切换到 Rinkeby 测试网，可以到 https://www.rinkeby.io/#faucet 领取一些测试网的 ether 。
> * 后续合约的编写、部署、测试都在 Rinkeby 测试网操作。

![MetaMask 钱包](https://pdmfs.aboshe.cn/a0/a0/zv5c4baii2wjf1h6.png)

MetaMask 钱包\
\


**编译器选择**

> 写一个智能合约 demo1

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.0;

contract demo1{
    function sum(int a,int b)public pure returns(int){
        return a+b;
    }
}
```

> * `pragma` 限制了编译器版本，选择对应的编译器

![编译器选择](https://pdmfs.aboshe.cn/a0/a0/8h8affbyf7kyv98w.png)

编译器选择\
\


**部署**

> 编译后没有错误即可部署，点击部署后 MetaMask 钱包会弹出支付 gas 费用的请求，支付 gas 费用后就会部署到测试网。

![部署合约](https://pdmfs.aboshe.cn/a0/a0/6vxewgv6vu81wh61.png)

部署合约\


> 部署成功后会有部署成功的提示，并且回显区块编号

![部署成功](https://pdmfs.aboshe.cn/a0/a0/4k0w77x6ed0h7107.png)

部署成功\
\


**调试**

> 调试合约就是调用合约暴露在外的各种函数
>
> * 如果调试的函数涉及修改状态变量或支付等操作就会被要求支付 gas 。

![调试](https://pdmfs.aboshe.cn/a0/a0/b5x63wuye27w1x33.png)

调试\
\


**import 引入网络文件**

> remix 引包可以直接引入网络文件
>
> * 如：`import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC777/ERC777.sol";`
> * remix 会直接下载相关依赖到 `.deps` 目录下。

![.deps目录](https://pdmfs.aboshe.cn/a0/a0/hjicdcycd834d7zj.png)

.deps目录\
\
vdf
