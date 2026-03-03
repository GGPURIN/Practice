# GitHubの練習
Git（とターミナル）の基本的な操作を理解をする

## よく使うGitコマンド
```bash
# 現在の状態を確認
git status

# コミット履歴を確認
git log

# 変更差分を確認
git diff                    # 作業ディレクトリの変更を確認
git diff <ファイル名>       # 特定ファイルの変更を確認
git diff --staged           # ステージングされた変更を確認
git diff <ブランチ名>       # 指定ブランチとの差分を確認
git diff <コミットID>       # 特定コミットとの差分を確認

# ブランチ一覧を確認（ローカル）
git branch

# ブランチ一覧を確認（リモート）
git branch -r

# ブランチ一覧を確認（すべて）
git branch -a
```


## リポジトリの準備
本リポジトリをクローン
```bash
git clone <リポジトリのURL>
```

以下は各自で調べること
- リポジトリのURLはどこで確認できるか
- HTTPSとSSHの違い


## 作業1. ディレクトリとファイルの作成
**新規ブランチの作成**
```bash
git checkout -b feature/practice/{your_name}

# または
git switch -c feature/practice/{your_name}
```

以下は各自で調べること
- `checkout`と`switch`の違い
- 各オプションの意味（`-b`など）

**ディレクトリとファイルの作成**
```bash
# ディレクトリ作成
mkdir {your_name}

# ファイル作成
touch {your_name}/practice.txt

# ファイルに追記
echo "hello" >> {your_name}/practice.txt

# （補足）ファイル作成+上書き
echo "hello" > {your_name}/practice.txt

# ディレクトリの中身確認
ls {your_name}
```

**変更内容の確認**
```bash
# 変更を確認
git diff
# 新規ファイル（Untracked）は表示されない
```

**既存ファイル（README.md）を編集してdiffを確認**
```bash
# 既存のREADME.mdに追記
echo "" >> README.md
echo "{your_name}" >> README.md

# 変更を確認
git status

# 既存ファイルの差分を確認（今度は表示される）
git diff

# 出力例:
# diff --git a/README.md b/README.md
# index xxx..xxx 100644
# --- a/README.md
# +++ b/README.md
# @@ -xxx,x +xxx,x @@
# +
# +{your_name}が編集しました

# 特定のファイルの差分を確認
git diff README.md
```

**変更を破棄（restore）する**
```bash
# ステージング前の変更を破棄（Git 2.23以降）
git restore README.md

# または（従来の方法）
git checkout -- README.md

# 確認（変更が元に戻っている）
git status
git diff

# ファイルの内容も確認（追記した部分が消えている）
tail README.md
```

補足
- 変更を破棄すると元に戻せない
- ステージング後の変更を取り消す方法：`git restore --staged`

**リモートに変更をPush**
```bash
# ステージングエリアに追加
git add {your_name}/
# または
git add .

# ステージング後の差分を確認
git diff --staged

# コミット内容を最終確認
git status

# コミット（メッセージを付ける）
git commit -m "Add practice file"

# リモートにプッシュ
git push origin feature/practice/{your_name}

```

ブラウザでGitHubに移動し、以下を実行
- mainブランチに対するpull requestの作成
- mainブランチに対してmerge


## 作業2. 既存ブランチの編集
**ユースケース**  
誰かが作業している内容に対して変更の提案

**mainブランチに戻る**
```bash
git checkout main
# または
git switch main
```

**リモートの最新状態を取得**
```bash
# リモートの最新情報を取得
git fetch

# リモートの変更をローカルに反映
git pull origin main
```

**既存ブランチから新しいローカルブランチを作成**
```bash
# リモートの既存ブランチをベースに、別名でローカルブランチを作成
git checkout -b edit-someone-by-{your_name} origin/feature/practice/someone

# または
git switch -c edit-someone-by-{your_name} origin/feature/practice/someone
```

以下は各自で調べること
- なぜローカルブランチを別名で作成するのか
- `origin/ブランチ名`の意味

**ファイルを編集**
```bash
# ファイルを編集（エディタで開く、または以下のコマンド）
echo "{your_name}" >> someone/practice.txt

# 確認
cat someone/practice.txt
```

**変更をコミットしてプッシュ**
```bash
# 変更を確認
git status

# 変更内容（差分）を確認
git diff someone/practice.txt

# ステージングエリアに追加
git add someone/practice.txt
# または
git add .

# ステージングした内容を確認
git diff --staged

# コミット
git commit -m "Update practice file by {your_name}"

# 自分のブランチにプッシュ
git push origin edit-someone-by-{your_name}
```

**Pull Requestを作成**
ブラウザでGitHubに移動し、以下を実行
1. 自分がプッシュしたブランチ（edit-someone-by-{your_name}）が表示される
2. 「Compare & pull request」ボタンをクリック
3. **base:** `feature/practice/someone` ← **compare:** `edit-someone-by-{your_name}` となっていることを確認
4. Pull Requestのタイトルと説明を記入
5. 「Create pull request」をクリック
6. 元のブランチのオーナーにレビューを依頼

**（参考）元のブランチに直接プッシュする方法**
```bash
# 自分のローカルブランチから元のリモートブランチに直接プッシュ
git push origin edit-someone-by-{your_name}:feature/practice/someone
```
この方法も可能ですが、Pull Requestを経由する方が：
- 変更履歴が明確になる
- レビュープロセスが組み込める
- GitHubのワークフローとして一般的

以下は各自で調べること
- Pull Requestのベストプラクティス
- base と compare の違い
- `git push`の`ローカルブランチ名:リモートブランチ名`の構文

**（補足）よりシンプルな方法**  
少人数開発や、元のブランチへの直接プッシュ権限がある場合：
```bash
# リモートブランチをそのまま同名でチェックアウト
git checkout feature/practice/someone
# または
git switch feature/practice/someone

# ファイルを編集
echo "追加のメッセージ" >> someone/practice.txt

# コミット
git add someone/practice.txt
git commit -m "Update practice file"

# 元のブランチに直接プッシュ
git push origin feature/practice/someone
```

**どちらを使うべきか？**
- **別名ブランチ作成 + Pull Request（推奨）**: レビュープロセス、変更履歴の明確化、実務的なワークフロー
- **同名チェックアウト + 直接プッシュ**: シンプルだが、レビューなし、権限が必要

ブラウザでGitHubに移動し、元のPull Request（feature/practice/someone → main）に変更が反映されていることを確認

## 作業3. コンフリクトの解消
**ユースケース**  
複数人が同じファイルの同じ箇所を編集した際に発生するコンフリクトを解消する

前提条件：作業1で作成した自分のブランチがmainにマージ済みであること

**mainブランチに移動**
```bash
git checkout main

# 最新の状態を取得
git pull origin main
```

**コンフリクト発生用のブランチを作成（1つ目）**
```bash
# 新しいブランチを作成
git checkout -b conflict-{your_name}-1

# 自分が作成したpractice.txtの1行目を編集
echo "Hello World" > {your_name}/practice.txt

# 変更内容を確認
git diff {your_name}/practice.txt

# コミット
git add {your_name}/practice.txt
git commit -m "Update practice.txt in conflict-test-1"
```

**別のブランチで同じ箇所を編集（2つ目）**
```bash
# mainに戻る
git checkout main

# 2つ目のブランチを作成
git checkout -b conflict-{your_name}-2

# 同じファイルの同じ行を異なる内容で編集
echo "こんにちは世界" > {your_name}/practice.txt

# 変更内容を確認
git diff {your_name}/practice.txt

# コミット
git add {your_name}/practice.txt
git commit -m "Update practice.txt in conflict-{your_name}-2"
```

**コンフリクトを発生させる**
```bash
# conflict-test-1の内容をconflict-test-2にマージしようとする
git merge conflict-{your_name}-1
```

この時点で、以下のようなメッセージが表示されコンフリクトが発生します：
```
Auto-merging {your_name}/practice.txt
CONFLICT (content): Merge conflict in {your_name}/practice.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**コンフリクトを確認**
```bash
# コンフリクトが発生しているファイルを確認
git status

# ファイルの内容を確認
cat {your_name}/practice.txt

# 差分を確認（コンフリクト状態の表示）
git diff {your_name}/practice.txt
```

ファイルを開くと以下のような表示になっています：
```
<<<<<<< HEAD
こんにちは世界
=======
Hello World
>>>>>>> conflict-test-1
```

- `<<<<<<< HEAD` から `=======` まで：現在のブランチ（main）の内容
- `=======` から `>>>>>>>` まで：マージしようとしているブランチ（conflict-test-1）の内容

**コンフリクトを解消**
```bash
# エディタでファイルを開いて編集
# コンフリクトマーカー（<<<<<<, =======, >>>>>>>）を削除し、
# 最終的に残したい内容に修正

# 例：両方の内容を残す場合
echo "Hello World" > {your_name}/practice.txt
echo "こんにちは世界" >> {your_name}/practice.txt

# または、どちらか一方を選択
echo "Hello World" > {your_name}/practice.txt

# 確認
cat {your_name}/practice.txt
```

**解消後のコミット**
```bash
# 変更をステージング
git add {your_name}/practice.txt

# 解消内容を確認
git diff --staged

# マージコミットを作成
git commit -m "Resolve merge conflict"

# プッシュ（conflict-{your_name}-2ブランチに）
git push origin conflict-{your_name}-2
```

**確認**
```bash
# ログを確認してマージコミットができていることを確認
git log --oneline --graph

# ファイルの内容を確認
cat {your_name}/practice.txt
```

**GitHubでPull Requestを作成**
ブラウザでGitHubに移動し、以下を実行
- conflict-test-2ブランチからmainブランチへのPull Requestを作成
- コンフリクトが解消されていることを確認してmerge

**ブランチの削除（クリーンアップ）**
```bash
# mainブランチに移動
git checkout main

# 最新の状態を取得
git pull origin main

# ローカルブランチを削除
git branch -d conflict-test-1
git branch -d conflict-test-2

# リモートブランチを削除（GitHub上でmerge時に削除されていない場合）
git push origin --delete conflict-test-1
git push origin --delete conflict-test-2
```

以下は各自で調べること
- コンフリクトマーカーの意味
- `git merge`と`git rebase`の違い
- コンフリクトを避けるための実践的な方法


## 番外編. テンプレートリポジトリから新しいリポジトリを作成
**ユースケース**  
既存のテンプレートリポジトリをベースに、新しいプロジェクトを開始する

### 方法1: GitHubのテンプレート機能を使う（推奨）
**GitHub上で操作（最も簡単）**
1. テンプレートリポジトリのページにアクセス
2. 「Use this template」ボタンをクリック
3. 新しいリポジトリ名を入力して作成
4. 作成されたリポジトリをクローン

```bash
# 作成した新しいリポジトリをクローン
git clone <新しいリポジトリのURL>
```

以下は各自で調べること
- GitHubでテンプレートリポジトリとして設定する方法
- テンプレート機能と通常のforkの違い

### 方法2: クローンしてリモートを変更する
**テンプレートリポジトリをクローン**
```bash
# テンプレートリポジトリをクローン（別名で）
git clone <テンプレートリポジトリのURL> <新しいディレクトリ名>

# 例
git clone https://github.com/original/template-repo.git my-new-project

# 作成したディレクトリに移動
cd my-new-project
```

**現在のリモート設定を確認**
```bash
# リモートリポジトリの情報を確認
git remote -v

# 出力例:
# origin  https://github.com/original/template-repo.git (fetch)
# origin  https://github.com/original/template-repo.git (push)
```

**既存のリモート接続を削除**
```bash
# originを削除
git remote remove origin

# 確認（何も表示されないはず）
git remote -v
```

**GitHubで新しいリポジトリを作成**
1. GitHubにアクセス
2. 「New repository」をクリック
3. リポジトリ名を入力（例: my-new-project）
4. 「Create repository」をクリック
5. リポジトリのURLをコピー

**新しいリモートリポジトリを設定**
```bash
# 新しいリモートリポジトリを追加
git remote add origin <新しいリポジトリのURL>

# 例
git remote add origin https://github.com/yourname/my-new-project.git

# 確認
git remote -v
```

**新しいリポジトリにプッシュ**
```bash
# mainブランチをプッシュ
git push -u origin main

# すべてのブランチをプッシュする場合
git push -u origin --all

# タグもプッシュする場合
git push -u origin --tags
```

**履歴を残さない場合（完全に新しいリポジトリとして開始）**
```bash
# .gitディレクトリを削除してGit履歴をリセット
rm -rf .git

# 新しいGitリポジトリとして初期化
git init

# ファイルをすべて追加
git add .

# 初回コミット
git commit -m "Initial commit from template"

# リモートリポジトリを追加
git remote add origin <新しいリポジトリのURL>

# プッシュ
git branch -M main
git push -u origin main
```

以下は各自で調べること
- `git remote`コマンドの役割
- `-u`オプション（`--set-upstream`）の意味
- `git push --all`と`git push --tags`の違い
- Git履歴を残す場合と削除する場合のメリット・デメリット



