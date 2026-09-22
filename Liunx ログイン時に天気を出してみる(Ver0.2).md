#PC #Tips #tenki 

```bash
  #!/bin/bash

show_weather() {
    local city="$1"
    local api_key="${OPENWEATHERMAP_API_KEY}"

if [[ -z "$api_key" ]]; then
    echo "OPENWEATHERMAP_API_KEY が設定されていません。"
    return 1
fi

    local res
    res=$(curl -fsS "https://api.openweathermap.org/data/2.5/weather?q=${city},JP&units=metric&lang=ja&appid=${api_key}"
)

    local status=$?

    if [[ $status -ne 0 ]]; then
        echo "天気データの取得に失敗しました (終了コード: $status)"
        return 1
    fi


if ! jq -e '.main and .weather' >/dev/null <<< "$res"; then
    echo "JSONの形式が正しくありません。"
    return 1
fi

local name
local temp
local description
name=$(jq -r .name <<< "$res")
temp=$(jq -r .main.temp <<< "$res")
description=$(jq -r .weather[0].description <<< "$res")



    local icon_id
    icon_id=$(jq -r .weather[0].icon <<< "$res")
    case "$icon_id" in
        "01d") icon="☀️" ;;
        "01n") icon="🌙" ;;
        "02d") icon="⛅️" ;;
        "02n") icon="🌙☁️" ;;
        "03d") icon="☁️" ;;
        "03n") icon="🌙☁️" ;;
        "04d") icon="☁️" ;;
        "04n") icon="🌙☁️" ;;
        "09d") icon="☂️" ;;
        "09n") icon="🌙☂️" ;;
        "10d") icon="☔️" ;;
        "10n") icon="🌙☔️" ;;
        "11d") icon="⛈️" ;;
        "11n") icon="🌙⛈️" ;;
        "13d") icon="❄️" ;;
        "13n") icon="🌙❄️" ;;
        "50d") icon="🌫️" ;;
        "50n") icon="🌫️" ;;
        *) icon="❓ (予期しないアイコン)" ;;
    esac

    local format="${2:-%.1f°C (%s)}"
    printf "%s%sの現在の天気: $format\n" "$icon" "$name" "$temp" "$description"
}

# 城市名とフォーマットを指定して関数を呼び出す
city="${1:-Akita}"
show_weather "$city" "%.1f°C (%s)"

```



追加予定(tenki Coreとして)

気圧他の詳細な気象データ
数日分の予報
...and more


2026.08.02
tenki Core 改め
 
 now

と改名

注意事項


恒久的に、

```bash
　OPENWEATHERMAP_API_KEY
```

を環境変数として設定するなら、

```bash
nano ~/.bashrc
```

あたりで、

```bash
export OPENWEATHERMAP_API_KEY='ここにAPIキー'
```

を追加。

保存したら、今のシェルに反映するために、

```bash
source ~/.bashrc
```

その後、

```bash
echo "$OPENWEATHERMAP_API_KEY"
```

で確認できる。

ただし、**APIキーそのものを画面に表示したくない**なら、

```bash
if [ -n "$OPENWEATHERMAP_API_KEY" ]; then
    echo "APIキー設定済み"
else
    echo "APIキー未設定"
fi
```

で確認するのが安全。