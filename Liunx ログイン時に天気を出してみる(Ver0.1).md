#PC #Tips #tenki

```bash
#!/usr/bin/env bash

show_weather() {
    local api_key="APIキー"
    local city="${1:-Akita}"

       local res

if ! res=$(
    curl -fsS --max-time 15 \
        "https://api.openweathermap.org/data/2.5/weather?q=${city},JP&units=metric&lang=ja&appid=${api_key}"
); then
    echo "OpenWeatherへ接続できませんでした。"
    return 1
fi

if ! IFS=$'\t' read -r city_name temp desc_en desc_ja <<< "$(
    jq -r '[.name, .main.temp, .weather[0].main, .weather[0].description] | @tsv' <<<"$res"
)"; then
    echo "JSONの解析に失敗しました。"
    return 1
fi

    # JSONが正常か確認
    if jq -e '.main and .weather' >/dev/null 2>&1 <<<"$res"; then

    local city_name temp desc_en desc_ja

if ! IFS=$'\t' read -r city_name temp desc_en desc_ja <<< "$(
    jq -r '[.name, .main.temp, .weather[0].main, .weather[0].description] | @tsv' <<<"$res"
)"; then
    echo "JSONの解析に失敗しました。"
    return 1
fi
        local icon=""

        case "$desc_en" in
    Clear)  icon="☀️ " ;;
    Clouds) icon="☁️ " ;;
    Rain)   icon="☔ " ;;
    Drizzle) icon="🌦️ " ;;
    Thunderstorm) icon="⛈️ " ;;
    Mist|Fog|Haze) icon="🌫️ " ;;
    Snow)   icon="❄️ " ;;
    *)      icon="✨ " ;;
esac

        printf "%s%sの現在の天気: %.1f°C (%s)\n" \
            "$icon" "$city_name" "$temp" "$desc_ja"

    else
        echo "天気情報を取得できませんでした。"
        return 1
    fi
}

show_weather "$@"
```