## wasm tips
-  4 - [golang wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#4---golang-wasm)
-  3 - [docker clang](https://github.com/web3scout/wasmtips/blob/master/README.md#3---docker-clang)
-  2 - [node](https://github.com/web3scout/wasmtips/blob/master/README.md#2---node)
-  1 - [c to wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#1---c-to-wasm)
-  0 - [wat to wasm](https://github.com/web3scout/wasmtips/blob/master/README.md#0---wat-to-wasm)

## #4 - golang wasm
```bash
export GOPATH=/usr/local/opt/gop
export PATH=/usr/local/opt/gop/bin:/usr/local/opt/go/bin:$PATH

GOOS=js GOARCH=wasm go build -o main.wasm .
```

## #3 - docker clang
```Dockerfile
FROM alpine:latest

VOLUME ["/code"]

RUN apk update
RUN apk add clang lld

RUN rm -rf /var/cache/apk/*

WORKDIR /code
```

```bash
docker build -t clang .
docker run --rm -v $PWD:/code clang clang --target=wasm32 -O3 -flto -nostdlib -Wl,--no-entry -Wl,--export-all -Wl,--lto-O3 -o code.wasm  code.c
```

## #2 - nodejs
> 2020-29-05 by [@web3scout](https://github.com/web3scout)  
```bash
npm i wabt
```
code.wat
```wat
(module
  (func (result i32)
    (i32.const 42)
  )
  (export "helloWorld" (func 0))
)
```
build.js
```js
const fs = require("fs");
const wabt = require("wabt")();

const wat = process.argv[2]
const wasm = process.argv[3]

const wasmModule = wabt.parseWat(wat, fs.readFileSync(wat, "utf8"));
fs.writeFileSync(wasm, new Buffer.from( wasmModule.toBinary({}).buffer));
```
run build.js
```bash
node build.js code.wat code.wasm
```
test.js
```js
const fs = require("fs");
const wasm = process.argv[2]
const run = async () => {
  const buffer = fs.readFileSync(wasm);
  const module = await WebAssembly.compile(buffer);
  const instance = await WebAssembly.instantiate(module);
  console.log(instance.exports.helloWorld());
};

run();
```
run test
```bash
node test.js code.wasm
```

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
