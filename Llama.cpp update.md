# Debian機 アップデートコマンド
```
cd ~/llama.cpp
git fetch --depth 1 origin tag v1.x.x
git checkout v1.x.x
rm -rf build
cmake -B build -G Ninja -DLLAMA_CURL=OFF -DGGML_NATIVE=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j 4
```

# Termux（G07：Vulkan有効）
```
cd ~/llama.cpp
git fetch --depth 1 origin tag v1.x.x
git checkout v1.0.0
rm -rf build
cmake -B build -G Ninja -DLLAMA_CURL=OFF -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j 4
```
# Termux（G05：CPU標準）

```
cd ~/llama.cpp
git fetch --depth 1 origin tag v1.x.x
git checkout v1.0.0
rm -rf build
cmake -B build -G Ninja -DLLAMA_CURL=OFF -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j 4
```
