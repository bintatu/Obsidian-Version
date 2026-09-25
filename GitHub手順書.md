
初期設定（最初の一度のみ）

PowerShellで：
```bash
git config --global user.name "bintatu"
```

次に：
```bash
git config --global user.email "GitHubに登録しているメールアドレス"
```

例：
```bash
git config --global user.email "xxxxx@example.com"
```

確認：
```bash
git config --global --list
```


照合（以下登録内容が出ればOK）
```bash
user.name=bintatu
user.email=xxxxx@example.com
```

## ① GitHub側でリポジトリ作成

GitHubで新規リポジトリ：

```bash
tenki
```

を作る。

この時、最初は

- README追加 → なし
- .gitignore → なし
- License → なし

で空のリポジトリにしておく方が楽。

---

## ② PowerShellでローカルへ移動

今の場所が、

```bash
GitHub\tenki
```

なら、

```bash
cd GitHub\tenki
```

（すでにそこなら不要）

---

## ③ Git初期化

初回だけ。

```bash
git init
```

---

## ④ ファイル確認

```bash
ls
```

で、

```bash
tenki.py
tenki.md
README.md
```

みたいなのが見えればOK。

---

## ⑤ 追加

```bash
git add .
```

これで現在のフォルダ以下を登録対象にする。

確認：

```bash
git status
```

---

## ⑥ 最初のコミット

例えば、

```bash
git commit -m "tenki v0.1 first take"
```

みたいな感じ。

---

## ⑦ GitHubへ接続

GitHubで作ったリポジトリのURLを使う。

例：

```bash
git remote add origin https://github.com/ユーザー名/tenki.git
```

確認：

```bash
git remote -v
```

---

## ⑧ プッシュ

初回なら：

```bash
git branch -M main
git push -u origin main
```

これでGitHubに上がる。

---

次回からは、

```bash
git add .
git commit -m "変更内容"
git push
```

だけ。

Windows PowerShellのGitの初期設定

```bash
core.autocrlf=true
```

になっている

これはWindows向けの設定で、

- チェックアウト時 → LFをCRLFへ
- コミット時 → CRLFをLFへ

という変換をする。

Linux寄りの開発ではよく使う設定。

確認：

```bash
git config --global core.autocrlf
```

で、

```bash
input
```

と出ればOK。

---

すでに作ったファイルを揃えるなら、一度：

```bash
git rm --cached -r .
git reset --hard
```

という方法もあるけど、今まだファーストコミット前ならそこまでやらなくてもいいと思う。

それと、これからGitに慣れてきたらぜひ覚えてほしいコマンドが3つ。

```bash
git status
```

今どんな状態か。

```bash
git diff
```

何を変更したか。

```bash
git log --oneline
```

今まで何を積み重ねてきたか。

この3つは本当に毎日使う。

新規環境

git clone git@github.com:bintatu/tenki.git

更新

```bash
git pull
```
コミット

```bash
git status
```
```bash
git add .
git commit -m "..."
git push
```

おまけ:SHA-256の確認
```bash
sha256sum ~/ファイル名.sh
```
