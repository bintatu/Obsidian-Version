#PC #ChatAI #藍ちゃん 


「Security-SPP Event ID 16394/16384」は、Windowsのライセンス認証やライセンス状態を管理する「Software Protection」サービス（`sppsvc`）が不要な再起動や再トリガーを繰り返していることを示します。このイベントが継続的に記録される場合、タスクスケジュールの不具合やサードパーティ製アプリとの競合、システムファイルの破損が原因として挙げられます。 [[1](https://learn.microsoft.com/en-us/answers/questions/3904991/security-spp-event-id-16384-16394), [2](https://windowsforum.com/threads/spp-security-event-id-16394-16384.396075/)]

主な対処法と解決策は以下の通りです。

1. サービスの再起動とモジュールの再登録

コマンドプロンプトを管理者権限で起動し、以下のコマンドを一行ずつ実行してサービスを再起動・修復します。

cmd

```bash
net stop sppsvc
regsvr32 %windir%\system32\sppwmi.dll
net start sppsvc
```

コードは注意してご使用ください。

※モジュールの登録時にエラーが出る場合は、[Microsoft Learn](https://learn.microsoft.com/en-us/answers/questions/3904991/security-spp-event-id-16384-16394) のディスカッションが参考になります。

2. システムファイルの修復

PowerShellを管理者として実行し、システムファイルの破損をチェック・修復します。 [[1](https://learn.microsoft.com/en-my/answers/questions/1582203/event-id-16394-and-16384-getting-spammed-repeatedl), [2](https://learn.microsoft.com/ja-jp/troubleshoot/windows-server/backup-and-storage/vss-error-8193-restart-cryptographic-services), [3](https://recoverit.wondershare.jp/windows-error/stop-code-system-thread-exception-not-handled.html)]

powershell

```bash
sfc /scannow
dism /online /cleanup-image /restorehealth
```

コードは注意してご使用ください。

3. タスクスケジューラと権限の確認

サービスのスケジュール設定が正しく行われているか確認してください。

- `タスクスケジューラ`を開き、`Microsoft` > `Windows` > `SoftwareProtectionPlatform` に移動して、スケジュール設定が異常でないか確認します。
- `NETWORK SERVICE` アカウントが対象フォルダに対するアクセス権を持っていることを確認します。 [[1](https://learn.microsoft.com/ja-jp/troubleshoot/windows-server/system-management-components/failed-schedule-software-protection)]

詳細な動作や他のユーザーの解決事例については、[Windows Forumのスレッド](https://windowsforum.com/threads/spp-security-event-id-16394-16384.396075/) を確認してください。

このイベントに関連して、**PCのフリーズ**やゲーム中のカクつき（マイクロフリーズ）といった具体的な症状は発生していますか？また、症状が出始めた時期に特定のアップデートや**新しいセキュリティソフトのインストール**などを行っていれば教えてください。