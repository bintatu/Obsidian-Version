
cat << 'EOF' > ~/llm.sh
#!/data/data/com.termux/files/usr/bin/bash

SERVER="$HOME/llama.cpp/build/bin/llama-server"
MODEL_DIR="$HOME/models"
SESSION="llm"

case "$1" in
  qwen3)
    MODEL="$MODEL_DIR/Qwen3-4B-Q3_K_M.gguf"
    CTX=6144
    ;;
  qwen25)
    MODEL="$MODEL_DIR/qwen2.5-3b-instruct-q4_k_m.gguf"
    CTX=4096
    ;;
  stop)
    tmux kill-session -t "$SESSION" 2>/dev/null
    echo "LLMサーバーを停止しました。"
    exit 0
    ;;
  log)
    tmux attach -t "$SESSION"
    exit 0
    ;;
  *)
    echo "使い方: ~/llm.sh [qwen3 | qwen25 | stop | log]"
    exit 1
    ;;
esac

# すでに起動中なら一度止める
tmux kill-session -t "$SESSION" 2>/dev/null

# WakeLockを確保してバックグラウンドで起動
termux-wake-lock
tmux new-session -d -s "$SESSION" "$SERVER -m $MODEL --host 0.0.0.0 --port 8080 -c $CTX -t 4 -ctk q8_0 -ctv q8_0"

echo "[$1] をバックグラウンドで起動しました！（Tailscale IP:8080）"
EOF

chmod +x ~/llm.sh


2. 使い方（これだけでOK）

以降はホームディレクトリから短いコマンドを打つだけで操作できます。

- **じっくり賢い Qwen3-4B を起動（バックグラウンド常駐）**
    
    bash
    
    ```
    ~/llm.sh qwen3
    ```
    
    コードは注意してご使用ください。
    
- **軽快な Qwen2.5-3B に切り替え（自動再起動）**
    
    bash
    
    ```
    ~/llm.sh qwen25
    ```
    
    コードは注意してご使用ください。
    
- **動いている裏画面のログを覗き見る**
    
    bash
    
    ```
    ~/llm.sh log
    ```
    
    コードは注意してご使用ください。
    
    （※ログ画面から抜けるときは `Ctrl` + `b` を押してから `d` を押します）
- **サーバーを完全に停止する**
    
    bash
    
    ```
    ~/llm.sh stop
    ```
    
    コードは注意してご使用ください。
    

ポイント

- 起動時に [termux-wake-lock](https://wiki.termux.com/wiki/Termux-wake-lock) も自動で叩くようにしてあるため、スリープによる強制終了対策もセットで完了します。
- モデルを切り替えるときは、わざわざ `stop` しなくても `~/`llm.sh `qwen25` のように上書きで叩けば、古い方を落として新しい方を立ち上げてくれます。




使い方（G07側）

- **通常チャット・思考モード（Qwen3-4B Q4）**
    
    bash
    
    ```
    ~/llm.sh text
    ```
    
    コードは注意してご使用ください。
    
- **画像解析モード（Qwen2.5-VL-3B）**
    
    bash
    
    ```
    ~/llm.sh vision
    ```
    
    コードは注意してご使用ください。
    
- **裏画面のログ・速度確認（抜けるときは `Ctrl + b` → `d`）**
    
    bash
    
    ```
    ~/llm.sh log
    ```
    
    コードは注意してご使用ください。
    
- **完全停止**
    
    bash
    
    ```
    ~/llm.sh stop
    ```
    
    コードは注意してご使用ください。