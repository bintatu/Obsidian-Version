Coreに関しては交通誘導のみ
各モジュールを呼びに行くためのモジュールとする


現行のtenki.shのアイコンは別途切り出して共通化部品
週間予報、詳細天気のアイコンとしても活用

拡張予定の機能として
GPS-NTP の衛星測位情報、現在時刻、受信状況など
宅内の温度、湿度、一部部屋におけるCo2濃度の監視
（Ｍ5 stack よりデータ採取）

つまりCoreは、

- 起動
- 各モジュールの呼び出し
- 実行順序の管理
- 必要なら結果の受け渡し
- 表示側への引き渡し

くらい。

**「天気を取得する」「GPSを見る」「M5 Stackから温湿度を読む」みたいな仕事はCoreに持たせない。**


# 機能切り分け

```
Now Core
 ├─ tenki モジュール
 ├─ GPS-NTP モジュール
 ├─ 宅内環境モジュール
 │    ├─ 温度
 │    ├─ 湿度
 │    └─ CO₂
 └─ 今後追加される各モジュール
``` 
# tenki.sh改修 → pyに変更
 
 今の

**tenki.sh

から、

**内包型tenki.py**

へ昇格。

# 将来的には
```
tenki/
├── tenki.py          # 外から呼ばれる入口
├── current.py        # 現在天気
├── detail.py         # 詳細天気
├── weekly.py         # 週間天気
├── weather_api.py    # OpenWeatherとの通信
└── icon.py           # 天気アイコン
```
とサブモジュール化を行って管理



### 将来の宅内情報

ここもNow Coreには直接M5 Stackの処理を書かず

```
Now Core
   ↓
environment module
   ↓
M5 Stack
   ↓
温度 / 湿度 / CO₂
のような構造にする
```
# GPS-NTP

```
gps-ntp module
 ├─ UTC / JST
 ├─ GNSS fix
 ├─ satellite information
 └─ reception status
```
単に**現在時刻を表示するモジュール**じゃなくて、

- 衛星測位情報
- 現在時刻
- 受信状態

**時刻情報＋測位ステータスの一まとまり**として


# Core部分の詳細

```
now/
├── core
│   ├── 起動
│   ├── モジュール管理
│   └── 表示への受け渡し
│
├── modules/
│   ├── weather/
│   ├── gps_ntp/
│   └── environment/
│
└── common/
    └── weather_icon/
```
