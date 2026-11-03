
何かあったときは、**通信の流れの上流から順番に**確認すると一発で原因が分かります。

|接続エラーの段階|主な原因（怪しい場所）|チェックするコマンドなど|
|---|---|---|
|**① WSLの中で動かない**  <br>（`status failed` など）|・`/run/sshd` フォルダがない  <br>・`sshd_config` のスペルミス  <br>・ファイルの権限（パーミッション）|WSL内で：  <br>`sudo sshd -t`  <br>`sudo service ssh status`|
|**② 実機からは繋がるが、  <br>　外部から繋がらない**|・**Windowsファイアウォール**のブロック  <br>・接続先のIPアドレス間違い|外部PCのRLoginで：  <br>接続先が127.0.0.1ではなく、  <br>Windowsの実機IPか確認|
|**③ 外部から「10061」や  <br>　タイムアウトになる**|・**ポートフォワーディング**のズレ  <br>（WSLのIPが再起動で変わった）|PowerShell（管理者）で：  <br>`netsh interface portproxy show all`|
