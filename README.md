## wasm tips

-  0 - [c to wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#0---c-to-wasm)

## #0 - c to wasm
> 2020-27-05 by [@web3scout](https://github.com/web3scout)  
```bash
clang --target=wasm32 -nostdlib -Wl,--no-entry --export-all -o code.wasm  code.c
```
```js
const response = await fetch("/code.wasm");
const buffer = await response.arrayBuffer();
const obj = await WebAssembly.instantiate(buffer);
const wa=obj.instance.exports;
// wa.f();
```

