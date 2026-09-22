
**PowerShellやコマンドプロンプトを開き、実行中のコンテナを停止・削除します。**

```bash
docker stop open-webui
docker rm open-webui
```

**最新のイメージをプル（取得）します。**


```bash
docker pull ghcr.io/open-webui/open-webui:main
```

**再びコンテナを起動します（インストール時のオプションに合わせて調整してください）。**

```bash
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```
