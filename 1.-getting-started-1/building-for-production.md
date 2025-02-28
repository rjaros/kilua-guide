# Building For Production

To build a complete application optimized for production, run one of these commands:

```
./gradlew clean jsBrowserDistribution                   (Js target on Linux)
./gradlew clean wasmJsBrowserDistribution               (WasmJs target on Linux)
gradlew.bat clean jsBrowserDistribution                 (Js target on Windows)
gradlew.bat clean wasmJsBrowserDistribution             (WasmJs target on Windows)
```

The application files will be generated in the `build/dist/js/productionExecutable` or `build/dist/wasmJs/productionExecutable` directory.
