
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
