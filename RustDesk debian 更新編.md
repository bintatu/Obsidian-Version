
# 最新版のdebパッケージを取得 (バージョン1.4.9の例)
wget https://github.com/rustdesk/rustdesk/releases/download/1.4.9/rustdesk-1.4.9-x86_64.deb

# ファイルを全ユーザーがアクセスできる一時フォルダに移動
mv /home/bintatu/rustdesk-1.4.9-x86_64.deb /tmp/

# 移動先からインストールを実行
sudo apt install -fy /tmp/rustdesk-1.4.9-x86_64.deb


# 依存関係を解決してインストール
sudo apt install -fy ./rustdesk-1.4.9-x86_64.deb

