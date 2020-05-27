## wasm tips

-  1 - [c to wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#1---c-to-wasm)
-  0 - [wat to wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#0---wat-to-wasm)

## #1 - c to wasm
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

## #0 - wat to wasm
> 2020-27-05 by [@web3scout](https://github.com/web3scout)  

```wat
(module
  (type (;0;) (func (param i32 i32) (result i32)))
  (func $addf (type 0) (param i32 i32) (result i32)
    get_local 0
    get_local 1
    i32.add)
  (export "add" (func $addf))
)```

wat to wasm
```bash
wat2wasm code.wat -o code.wasm
base64 -w0 code.wasm > code.wasm.b64
```
embedded wasm
```js
 const wasm_b64 = "AGFzbQEAAAABBwFgAn9/AX8DAgEABwcBA2FkZAAACgkBBwAgACABags="
 const wasm_buffer = Uint8Array.from(atob(wasm_b64), c => c.charCodeAt(0)).buffer;    
 const obj = await WebAssembly.instantiate(wasm_buffer);
 wa=obj.instance.exports;
 console.log(wa.add(31,2));
```
