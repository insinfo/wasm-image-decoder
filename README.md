# Wasm Image Decoder

Decodes images using Rust's `image` crate compiled to WebAssembly. By default it uses 4 threads (though some decoders are single-threaded) since it doesn't seem to get much faster if I add any more. You can edit `mod.js` to change the number of threads.

Works in the browser and should work in Deno when [this PR](https://github.com/denoland/deno/pull/10116) lands and 1.10.0 is released (around May 2021).

As of writing it's about 3x slower than native Rust. Drawing the image to an `OffscreenCanvas` and then extracting the image data is the same speed as native Rust, so use that if your JS runtime has it available. The communication overhead between JS and the wasm module is negligible, so the slowness is probably due to missing functionality and lack of optimisation in wasm runtimes (e.g. thread/simd/etc. optimisations), so the performance should improve with time.

## File Formats Supported

It supports decoding PNG, JPEG, WEBP, GIF, BMP, ICO, TGA, and several others, but some formats don't have full support as of writing (April 2021). [See here](https://github.com/image-rs/image/blob/2a79d33e663d27e402c76bfc6aa5ca78b1cc9895/README.md#supported-image-formats) for the support table. Check the [latest image-rs readme](https://github.com/image-rs/image) to see if there is increased support, and if so you can follow the build instructions below to build a version of this library that supports the new formats.

## Demo

```js
import decode from "https://deno.land/x/wasm_image_decoder@v0.0.3/mod.js";
let buf = await fetch("https://i.imgur.com/LYVUrUf.jpg").then(r => r.arrayBuffer());
let result = decode(buf); 
console.log(result); // {width, height, data} where data is a Uint8Array array of RGBA values like [R,G,B,A,R,G,B,A,R,G,B,A,...]
```

## Build
```
git clone https://github.com/josephrocca/wasm-image-decoder
wasm-pack build --target=web --out-dir=wasm
```
