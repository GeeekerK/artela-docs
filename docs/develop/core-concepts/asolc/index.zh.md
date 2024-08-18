---
sidebar_position：10
---

# Asolc

## 什么是Asolc？

ASOLC是Artela生态系统中的基石，旨在弥合以太坊虚拟机（EVM）与方面解释运行时事件的能力之间的差距。 我们制作了Artela EVM，这是EVM的扩展版本，能够在交易执行过程中捕获运行时堆栈跟踪和存储更改。

鉴于以太坊的内置示踪剂已经跟踪了诸如存储插槽更改之类的数据，因此您可能会质疑Artela EVM的必要性。 尽管这部分是正确的，但挑战在于，关键更改是哈希的，使它们无法通过代码进行处理。 例如，考虑一个变量 `a` 类型 `mapping(string => string)`。 什么时候 `a["key"]` 已更改，示踪剂仅显示一个修改的存储插槽，例如 `0xabcedf12354566.....`，没有与状态变量任何直接链接 `a` 和特定的钥匙 `key`.

为了解决此问题，Artela EVM引入了其他操作码，从而使Hashed信息与人类可读源代码的连接。 同时，开发了ASOLC来生成与Artela EVM协同作用的相应状态跟踪中间表示（IRS）。 因此，**Asolc** 可以看作是增强版本的 `SOLC`，在引入突破性功能的同时，保持与Solc的完全兼容性。

## ASOLC的工作原理

以下是说明ASOLC的工作的图：

 ![Asolc](asolc.svg) 

该过程通过以下步骤展开：

1. ASOLC仔细检查了固体智能合约源文件，确定访问或更改存储的表达式。
2. 它生成了补充YUL IR方法，该方法在状态变量和存储插槽之间建立了链接，利用Artela EVM的额外操作代码。
3. 然后将所得的IR汇编为EVM字节码，并准备在Artela EVM上部署。

该增强信息层使Artela EVM能够破译存储插槽和状态变量之间的连接。 结果，它可以追踪合同的状态变化，从而使方面可以使用状态变量名称访问信息，从而绕开了Hashed存储插槽的复杂性。

## Solc还是Asolc？

### tldr;

- **如果：** 
  - Maximum gas efficiency is your dApp's top priority.
  - Your dApp doesn't necessitate state tracing and you have no plans to integrate this feature.

- **如果：** 
  - State tracing capabilities are crucial for your dApp.
  - You're willing to compromise a bit on gas efficiency for the sake of advanced security and functionalities, particularly state tracing in Aspect.

值得注意的是，Asolc及其状态追踪Opcodes倾向于产生比传统溶液的较大量的人工制品，从而导致气体使用率略有增加。

此外，要记住，Asolc文物与标准EVM不兼容，这一点至关重要。 因此，如果您的部署目的地是以太坊，则必须使用常规溶液进行编译。

## ASOLC发布

查看最新的ASOLC版本 [这里](https://github.com/artela-network/solidity/releases/tag/v0.8.21-atl)。 选择与系统对齐的版本（例如，对于MacOS，选择 `macos.tar.gz` ）。