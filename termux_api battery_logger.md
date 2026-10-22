
```bash

#!/data/data/com.termux/files/usr/bin/bash

# 10分間隔で24時間記録（144回）
INTERVAL=600
COUNT=144

OUT="$HOME/battery_$(date +%Y%m%d_%H%M%S).csv"

echo 'timestamp,percentage,temperature,voltage,current,current_average,charge_counter,status,plugged,health' > "$OUT"

echo "記録開始: $OUT"
echo "10分間隔で最大24時間記録します。"

for ((i=1; i<=COUNT; i++)); do
    timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    json=$(termux-battery-status)

    if ! printf '%s' "$json" | jq -e '.percentage != null' >/dev/null 2>&1; then
        echo "[$timestamp] データ取得失敗。今回はスキップします。" >&2
    else
        printf '%s' "$json" | jq -r --arg ts "$timestamp" '
          [
            $ts,
            .percentage,
            .temperature,
            .voltage,
            .current,
            .current_average,
            .charge_counter,
            .status,
            .plugged,
            .health
          ] | @csv
        ' >> "$OUT"

        echo "[$timestamp] 残量 $(printf '%s' "$json" | jq -r '.percentage')%"
    fi

    if (( i < COUNT )); then
        sleep "$INTERVAL"
    fi
done

echo "記録終了: $OUT"
```

```
chmod +x battery_logger.sh
```

```
./battery_logger.sh
```
