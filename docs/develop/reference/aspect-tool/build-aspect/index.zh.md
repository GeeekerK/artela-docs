# 建立方面

完成方面开发后，有必要使用合同汇编命令验证正确性
方面代码和编译到WebAssembly。

## 命令

您可以使用以下命令来建立合同：

```bash
npm run aspect:build
```

### 例子

```shell
npm run aspect:build
```

### 命令输出

编译的产品放在 `build` 目录。

```shell
├── build
│   ├── debug.d.ts  // debug TypeScript declaration file
│   ├── debug.js    // debug JavaScript declaration file
│   ├── debug.wasm   //debug WebAssembly bytecode file
│   ├── debug.wasm.map  //debug WebAssembly output source map file
│   ├── debug.wat      // debug WebAssembly text output file
│   ├── release.d.ts    // release TypeScript declaration file
│   ├── release.js      // release JavaScript declaration file
│   ├── release.wasm    //release WebAssembly bytecode file
│   ├── release.wasm.map //release WebAssembly output source map file
│   └── release.wat    // release WebAssembly text output file

```

## 定制

这 `aspect-build` 命令，利用汇编的 `asc`，负责编译WebAssembly。 为了
有关编译参数的详细信息，请咨询
这 [编译器选项](https://assemblyscript.bootcss.com/compiler.html#compiler-options) 文档。
