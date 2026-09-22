 - Core_main.py ― メイン実行・表示
 - OpenWeather_API.py ― OpenWeather APIとの通信
 - Weather_Core.py ― APIレスポンスの処理
 - APIキーは OPENWEATHERMAP_API_KEY 環境変数から取得
 - 都市はコマンドライン引数で指定可能
 
- debian 
    - python3 Core_main.py akita
    - python3 Core_main.py koriyama
 
 
 - win
     - python Core_main.py akita
     - python Core_main.py koriyama
   
 - 予報は6時間単位で表示
 - 現在天気＋最大5日程度の予報
 - /tenki/idea/old/ は旧版・作業途中の保管場所