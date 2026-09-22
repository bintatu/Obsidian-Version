
ステップ1：Ollamaのインストールと「常駐の解除」

OllamaのWindows版は、普通にインストールするとバックグラウンドで勝手に常駐（タスクバーのインジケーターに常駐）してしまいます。これだと環境変数のコントロールがしにくいため、まずはこれを手動制御に切り替えます。

1. **公式サイトからダウンロード**  
    [Ollamaの公式サイト](https://ollama.com/) からWindows版をダウンロードしてインストールします。


```bash
irm https://ollama.com/install.ps1 | iex
```
   
2. **自動起動の常駐を終了する**  
    画面右下のタスクバー（インジケーター）にある「ラマのアイコン」を右クリックし、**Quit Ollama** を選んで完全に終了させます。

---

ステップ2：環境変数を仕込んで「手動サーバー起動」

ここからがCUIの本番です。PowerShellを**管理者権限**で開き、以下のコマンドを1行ずつ実行します。後々のスマホ接続（外部アクセス）と、VRAMオーバー時の挙動を制御するための仕込みです。

powershell

```bash
# 1. スマホなど外部からのアクセスを許可する設定
$env:OLLAMA_HOST="0.0.0.0"

# 2. Ollamaのサーバー（デーモン）を手動でフォアグラウンド起動
ollama serve
```

コードは注意してご使用ください。

※ `ollama serve` を実行すると、画面がロックされたようになりますが、これでAPIサーバーが正常に立ち上がっています。このPowerShellウィンドウは閉じずにそのままにしておいてください。

---

ステップ3：別ウィンドウで「初陣のモデル」を喰わせる

もう一つ新しくPowerShell（こちらは通常権限でOK）を開き、いよいよモデルをダウンロードして実行します。

まずはVRAM 6GBにすっぽり収まり、お使いのGPUが本気を出すとどれだけ速いかを体感するため、Googleの最新4Bモデルを呼び出します。

powershell

```bash
ollama run gemma3:4b
```

コードは注意してご使用ください。

初回は数GBのダウンロードが始まります。完了すると、ターミナルが `>>>` という入力待ち状態に変わります。これであなたのPCの中に「完全オフラインのAI」が誕生しました。

最短で舗装道を作るロードマップ

足元（メインPC）を最強のAIサーバーにするための、Docker導入からOpen WebUI接続までの手順です。

1. Docker Desktopのインストール

2. [Docker Desktopの公式サイト](https://www.docker.com/products/docker-desktop/) からWindows版のインストーラーをダウンロードします。
3. インストール中に「Use WSL 2 instead of Hyper-V（推奨）」というチェックボックスが出るので、**チェックを入れたまま**進めます。
4. インストールが終わったらPCを一度再起動します。

5. Open WebUIコンテナの起動

PCが立ち上がったら Docker Desktop を起動し（バックグラウンドで動いていればOK）、新しくPowerShellを開いて以下のコマンドをそのままコピペして実行します。

powershell

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

コードは注意してご使用ください。

3. スマホからのアクセス確認

4. メインPCのIPアドレス（例: `192.168.x.x`）を `ipconfig` コマンドなどで確認します。
5. 同じWi-Fiに繋いだスマホのブラウザから `http://[PCのIPアドレス]:3000` にアクセスします。
6. ログイン画面が出れば**マイAIサーバーの完成**です！初回のアカウント作成（ローカル保存）を済ませれば、スマホからいつでも `qwen2.5:7b` の泥沼や `gemma3:4b` の爆速環境を叩き込めます。
   
   ステップ2：GUIアクセス（Open WebUI）という舗装道の建築

泥沼の洗礼を受けたら、次はそれをスマホやブラウザからChatGPT風に美しく使うためのWebUI（Docker）を建てます。

お使いのメインPCにもし **Docker Desktop** が入っていれば、新しいPowerShellから以下のコマンドをコピペして実行するだけで、一発で舗装道が出来上がります。

powershell

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

コードは注意してご使用ください。

舗装道の歩き方

1. **PCのブラウザから**: `http://localhost:3000` にアクセスします。
2. **スマホのブラウザから**: 同じWi-Fiに繋いだスマホから、`http://[PCのIPアドレス]:3000` にアクセスします。

※初回アクセス時にアカウント作成（メール・パスワード）を求められますが、これは完全に**あなたのPC内に保存されるローカルアカウント**なので、適当なもので大丈夫です。

ログインすると、画面上部のモデル選択に、先ほどコンソールから召喚した `gemma3:4b` や `qwen2.5:7b` がずらりと並んでおり、スマホからいつでも自由に切り替えてチャットできるようになります（会話ログも自動でデータベースに美しく保存されます）。

Syncthing（NAS同期）の設定方針

SyncthingでこのデータをNASへ片方向同期（Send-Only）する場合、2つのアプローチがあります。

アプローチA：WSLのパスをそのままSyncthingに吸わせる（手軽）

Windows版のSyncthingから、上記の `\\wsl.localhost\docker-desktop-data\...` のパスを直接「同期フォルダー」として指定します。

- **注意点**: Docker（WSL）が起動している間は、データベースファイル（`webui.db`）がロックされていてSyncthingが一時的に読み取れないタイミングが発生することがあります。

アプローチB：Windows側の普通のフォルダにマウントし直す（茨の道・超おすすめ）

現在の「Dockerボリューム」ではなく、Windows上の普通のフォルダ（例: `C:\ai_logs`）をコンテナに直接マウント（ホストマウント）する方式に変更します。これならSyncthingでの同期エラーが起きにくく、管理が圧倒的に楽になります。

もしアプローチB（Windowsフォルダへの直接マウント）に変更する場合、一度現在のコンテナを消して、以下のようにコマンドを少し書き換えて再生成します。

powershell

```bash
# 1. 現在のコンテナを停止して削除（データはまだボリュームに残っています）
docker stop open-webui
docker rm open-webui

# 2. Windows上の好きな場所に同期用フォルダを作成（例: C:\ai_logs）
mkdir E:\Documents\AI_Logs

# 3. フォルダを直接マウントして再起動
	docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v E:\Documents\AI_Logs:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

コードは注意してご使用ください。

※これを実行すると、指定した `C:\ai_logs` の中にOpen WebUIのデータベース一式がジャラジャラと自動生成されます。あとはSyncthing側で、この `C:\ai_logs` をNASへ片方向同期するように設定するだけです。



【茨の道チューニング】Modelfileを書いて記憶容量を1万以上に拡張する

お使いのPCはメインメモリが **32GB** と非常に潤沢なため、この部屋の広さを `4096` から **`16384`（16k：約1万2千文字分）** あたりまで一気にブーストしても余裕で耐えられます。

CUI好きの本領発揮です。Ollamaの「`Modelfile`」を自分で書いて、記憶力を限界突破させた「俺専用Qwen-VL」を密造しましょう。

1. 作業用の適当なフォルダを作る

エクスプローラーでもPowerShellでも構いません。データ用ドライブ（Dドライブなど）に `D:\ollama_build` などのフォルダを作ります。

2. 設定ファイル（Modelfile）を作る

そのフォルダの中に、**`Modelfile`** という名前のファイル（拡張子なしのテキストファイル）を新規作成し、メモ帳などで以下の2行だけを書き込んで保存します。

text

```bash
FROM qwen2.5vl:7b
PARAMETER num_ctx 16384
```

コードは注意してご使用ください。

（※「ベースは既存のQwen2.5-VLを使い、記憶容量パラメータだけを16384に拡張しろ」という命令です）

3. 自分専用モデルをビルド（生成）する

PowerShellを開き、そのファイルがあるフォルダ（`D:\ollama_build`）に移動（`cd`）して、以下のコマンドをブチ込みます。

powershell

```bash
ollama create my_qwen_vl -f ./Modelfile
```

コードは注意してご使用ください。

数秒で `success` と返ってくれば、あなたオリジナルの大容量モデル **`my_qwen_vl`** があなたのPC内に誕生します。

---

仕上げとリベンジ

ビルドが完了したら、スマホのOpen WebUIの画面をリロード（更新）してください。モデル選択の中に、今作った **`my_qwen_vl`** が生えてきているはずです。

これを選択して、もう一度新しくチャットを開き直してカードリーディングを投げてみてください。今度は部屋の広さが4倍（16k）になっているため、画像を送ろうが、途中でどれだけツッコミを入れようが、一切エラーを吐かずに深層心理まで泥臭くリーディングし切ってくれるようになります。

タスクマネージャーの「共有システムメモリ（32GB）」がどれくらいモリモリ消費されるか、ニヤニヤしながら監視してみてください。

テキストベースなら、エラーは「過去の会話履歴」が原因

今回、画像を使っていないのに `exceed context size` のエラーが出た理由は、おそらく**「同じチャット部屋で何度も会話を往復させていたから」**です。

Ollamaのデフォルト（4096トークン）だと、テキストだけでも会話が数往復（特にAIの長文の解釈が重なると）するだけで、過去のログが部屋を満杯にしてしまいます。

もしテキストベースのカードリーディングで、また途中で切れたりエラーが出たりする場合は、先ほどのModelfileの仕組みをテキストモデル側に応用して、以下のように記憶力を拡張したモデルを1つ作っておくと、今後の検証がめちゃくちゃ快適になります。

text

```bash
# 例：テキスト用のQwenの記憶力を16kにブーストするModelfile
FROM qwen2.5:7b
PARAMETER num_ctx 16384
```

コードは注意してご使用ください。

これを `ollama create my_qwen_text -f ./Modelfile` でビルドしておけば、どれだけ長文のやり取りをしてもビクともしなくなります。

💡 さらに「茨の道」を極めるなら：Google公式の特殊量子化（QAT）

実はGoogleのGemmaシリーズには、通常の量子化よりもさらに賢い **`QAT`（Quantization-Aware Training / 量子化認識トレーニング）** という特殊な4ビットモデルが公式から用意されています。 [[1](https://ollama.com/library/gemma3:4b-it-qat), [2](https://ai.google.dev/gemma/docs/core?hl=ja)]

- **普通の量子化**: 100%の完成品モデルを、後から無理やり4ビットに削るため、少し知性が劣化します（水仙の誤認はこれも原因の1つです）。
- **QAT版**: 「最初から4ビットに圧縮されること」を前提にGoogleが訓練したモデルです。そのため、**容量は4ビットの軽さ（約3GB）のまま、無圧縮（8GB版）とほぼ変わらない驚異的な賢さを保っています**。 [[1](https://ai.google.dev/gemma/docs/core?hl=ja), [2](https://gigazine.net/gsc_news/en/20260608-google-ai-gemma-4-qat/)]

もし、Gemma 3 4Bの「爆速さ」をキープしたまま、もう少し知性の質（認識率など）を底上げしてみたい場合は、コンソールから以下のコマンドで**QAT版**を召喚することができます。 [[1](https://ollama.com/library/gemma3:4b-it-qat)]

powershell

```bash
ollama run gemma3:4b-it-qat
```

コードは注意してご使用ください。

これをダウンロードすると、Open WebUIのモデル選択に新しく生えてきます。 [[1](https://ollama.com/library/gemma3:4b-it-qat)]

4ビットの恩恵で爆速なのは変わらないのに、通常の4Bモデルより少し賢くなったGemmaを体感できるので、インフラが完成した今、次の比較実験の対象としてはかなり面白い存在になるはずです。 [[1](https://hiieung.substack.com/p/unlock-local-ai-power-why-google)]