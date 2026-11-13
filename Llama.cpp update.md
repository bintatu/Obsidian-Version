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
# Vulkanインストール：Snapdragon（Adreno GPU）搭載機の場合
Turnip（`mesa-vulkan-icd-freedreno`）と汎用ローダーの組み合わせを入れると、衝突を解消しつつ最も高速に動きます。

```
pkg install -y vulkan-loader-generic mesa-vulkan-icd-freedreno
```
※もし `vulkan-loader-android` がすでに入っていて衝突する場合は、先に `pkg uninstall vulkan-loader-android` してから上記を実行してください。

```
pkg install -y vulkan-loader-generic mesa-vulkan-icd-freedreno
```

```
cd ~/llama.cpp
git fetch --depth 1 origin tag v1.x.x
git checkout v1.0.0
rm -rf build
cmake -B build -G Ninja -DLLAMA_CURL=OFF -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
cmake --build build --config Release -j 4
```

```
cd ~/llama.cpp
rm -rf build
cmake -B build -G Ninja -DLLAMA_CURL=OFF -DGGML_VULKAN=ON -DCMAKE_BUILD_TYPE=Release
```

ここでログの中に **`-- Including Vulkan backend`** と表示されるか確認し、問題なければビルドを実行します。

```
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
