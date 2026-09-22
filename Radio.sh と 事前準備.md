
```bash

pkg update && pkg install mpv jq fzf curl
```

Radio.sh

```sh

cat << 'EOF' > ~/radio.sh
#!/bin/bash

echo -n "検索キーワードを入力してください (例: jazz, lofi, japan): "
read -r keyword

if [ -z "$keyword" ]; then
    echo "キーワードが空です。終了します。"
    exit 1
fi

echo "Radio-Browser から局を検索中..."

servers=(
    "de1.api.radio-browser.info"
    "nl1.api.radio-browser.info"
    "at1.api.radio-browser.info"
)

data=""

for server in "${servers[@]}"; do
    echo "接続先: $server"

    data=$(curl -sS --max-time 15 \
        -A "radio.sh/1.0" \
        --get \
        --data-urlencode "name=${keyword}" \
        --data-urlencode "order=clickcount" \
        --data-urlencode "reverse=true" \
        --data-urlencode "limit=100" \
        "https://${server}/json/stations/search" 2>/dev/null)

    # curlの終了コードではなく、JSONとして正常なら採用
    if echo "$data" | jq -e 'type == "array"' >/dev/null 2>&1; then
        break
    fi

    data=""
done

if [ -z "$data" ]; then
    echo "Radio-Browser から有効なデータを取得できませんでした。"
    exit 1
fi

count=$(echo "$data" | jq 'length')

if [ "$count" -eq 0 ]; then
    echo "「${keyword}」で見つかりませんでした。別のキーワードを試してください。"
    exit 0
fi

selected=$(
    echo "$data" |
    jq -r '.[] |
        "\(.name) [\(.countrycode // "--")]\t\(.url_resolved // .url)"' |
    fzf --prompt="局を選んでください (${count}件見つかりました): "
)

if [ -z "$selected" ]; then
    echo "キャンセルしました。"
    exit 0
fi

station_name=$(printf '%s\n' "$selected" | cut -f1)
station_url=$(printf '%s\n' "$selected" | cut -f2-)

if [ -z "$station_url" ]; then
    echo "再生URLを取得できませんでした。"
    exit 1
fi

echo "再生中: $station_name"

mpv "$station_url"
EOF

chmod +x ~/radio.sh
```

後処理
```bash
cp ...
chmod +x ~/radio.sh
alias radio='~/radio.sh'
```
