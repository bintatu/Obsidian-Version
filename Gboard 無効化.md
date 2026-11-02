
PCを使って強制的に無効化する（ADBコマンド）

もしどうしてもGboardを完全に黙らせたい（無効化したい）場合、端末単体の設定画面からではグレーアウトして押せませんが、**パソコンとTORQUEをケーブルで繋ぎ、コマンドを送ることでシステム強制的にGboardを無効化（凍結）する**ことが可能です。

1. TORQUEの「設定」＞「デバイス情報」＞「ビルド番号」を7回連打して「開発者向けオプション」を出します。
2. 「開発者向けオプション」の中で **「USBデバッグ」をオン** にします。
3. パソコン（WindowsやMac）に「[SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools?hl=ja)」をダウンロードします。
4. コマンドプロンプト等で、以下のコマンドを実行します。
    
    bash
    ```
    adb shell pm disable-user --user 0 com.google.android.inputmethod.latin
```
   
    コードは注意してご使用ください。
    

このコマンドを実行すると、TORQUE側でグレーアウトしていたGboardが**強制的に「無効」**になり、画面から一切出てこなくなります（ATOK一本になります）。  
※もし元に戻したくなったら、最後を `enable` にしたコマンドを打てばいつでも戻せます。