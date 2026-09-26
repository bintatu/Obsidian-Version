
センサー全部
```bash
for z in /sys/class/thermal/thermal_zone*; do echo "$(cat $z/type): $(cat $z/temp)"; done
```
CPU温度
```bash
for z in /sys/class/thermal/thermal_zone*; do
    type=$(cat $z/type)
    if [[ "$type" == *"cpu"* ]]; then
        temp=$(cat $z/temp 2>/dev/null)
        # 1000で割って℃表記にする
        echo "$type: $((temp / 1000)).$((temp % 1000 / 100))°C"
    fi
done
```
リアルタイム計測
```bash
while true; do
    clear
    for z in /sys/class/thermal/thermal_zone*; do
        type=$(cat $z/type)
        if [[ "$type" == *"cpu"* ]]; then
            temp=$(cat $z/temp 2>/dev/null)
            echo "$type: $((temp / 1000))°C"
        fi
    done
    sleep 1
done
```
一定の温度以上で通知するスクリプト
```bash
#!/bin/bash
# 警告を出す温度（45度）
THRESHOLD=45

while true; do
    max_temp=0
    
    # CPU関連のセンサーから最高温度を探す
    for z in /sys/class/thermal/thermal_zone*; do
        if [[ "$(cat $z/type 2>/dev/null)" == *"cpu"* ]]; then
            temp=$(cat $z/temp 2>/dev/null)
            temp_c=$((temp / 1000))
            if [ "$temp_c" -gt "$max_temp" ]; then
                max_temp=$temp_c
            fi
        fi
    done

    # 画面に現在の最高CPU温度を表示
    clear
    echo "========================================="
    echo " メイン機 CPU温度監視中（Ctrl+C で終了）"
    echo " 現在の最高温度: $max_temp °C"
    echo "========================================="

    # 設定温度を超えたらTermuxのビープ音（ベル音）を鳴らす
    if [ "$max_temp" -ge "$THRESHOLD" ]; then
        echo -e "\a" # ビープ音
        echo "⚠️ 端末が高温になっています！使用を中断してください。"
    fi

    sleep 3
done
```