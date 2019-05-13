# はじめに

このテキストは、Gitを利用したことがないエンジニア向けのテキストです。

# 導入

## git for windows 

* https://gitforwindows.org/ よりモジュールをダウンロードして導入します
* 導入時の設定では以下の項目
  * Use Default Editor にて Visual Studio Code を選択
  * Use Windows default console を選択
* CMDプロンプトウィンドウ上にて `git` コマンドが利用できることを確認

## Google Cloud Source Repository の設定

* 今回リモートGitサーバはCloud Source Repositoryを利用します。
* 特にGithubやGitlabのような管理UIは用意されていない純粋なリポジトリとなります。



# ローカルリポジトリでの操作

## リポジトリの作成

リポジトリは、ローカルで作り始めることもできますし、リモートから取得して行うこともできます。Gitで管理するコードは特定のフォルダ以下を管理します。Gitで管理するファイルは、Gitの設定で行うことができます。

```
mkdir <work directory name>
cd <work directory name>
git init
```

* 作業ディレクトリ以下で初期化を行うと `.git` ディレクトリが作成され管理が始まります。
* ディレクトリを管理したくなくなった際には `.git` ディレクトリを削除すれば問題ありません。

### 例 clone の場合

外部のリモートリポジトリから取得する場合には `クローン(clone)` を行うことことで利用することができます。 github などのサイトの場合には `download` できる場合もあります。その際に `.git` ディレクトリが含まれていれば git での管理を引き続き行うことができます。

```
git clone https://xxxxx.xxxx.xxx/xxxxx.git
cd xxxxx
```

* リモートの git リポジトリへアクセスは `http/https` または `ssh` を利用して行うことができます。

## 初期構成

git config

```
git config --global user.name "Your Name"
git config --global user.email you@example.com
```

##  変更の反映

git add , git commit 

以下のコマンドでファイルを作成しリポジトリに追加してみましょう

```
echo "README" > README.md
git add README.md
git commit -m "add readme.md file"
```

* git でファイルを管理するためには、`ファイルを追加` する必要があります。
* Addで追加されたファイルに対して`変更完了`を示すコマンドがCommitです。Commitを行うことで変更管理の仕組みはひとくくりの変更とみなします。
* ファイルが追加されると、作成したファイルは変更をトラッキングされます。追加を行わない限りは変更管理の対象とはなりません、またさまざまなGitの操作を行った際にも変更が加わることはありません。

```
touch 001.txt
touch 002.txt
git add 001.txt 002.txt
git commit -m "add some files"
```

* 上記では二つのファイルを追加(add)しています。それに対してCommitは一つしかありません。
* これは一つの変更で二つのファイルを追加していることになります。このように変更の単位はCommitで管理されます。Commitの意味合いとしては単一のファイルを示すわけでなく複数のファイルを同時に取り扱うので注意してください。

## ログの確認

git log 

```
git log --oneline
```

* ログの確認方法は様々です。
* Visual Studio Code などのツールを利用している場合には拡張機能を利用することでより可視化することができます。


## 特定条件

### 特定のファイルの特定のログ

```
git log -L <start>,<end>:<file>
```

### 特定のファイル

```
git blame <file>
```

### コミット単位の変更

```
git show <blameでのHash値>
```

## 状態確認

git status 

管理しているファイルの状態がどのようになっているかを確認するために利用します。変更がある場合にはその情報が表示出ます。

```
$ git status 
On branch master
nothing to commit, working tree clean
```

* 変更がない場合には上記のように表示されます。

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

* README.mdに変更（modify) がある場合に表示されます。



## 差分の確認

```
git diff <filename>
```

* 現在のファイルと最後にCommitした状態を比較してDiffを生成します。

```
$ git diff a.txt
diff --git a/a.txt b/a.txt
index ccc3e7b..be4e668 100644
--- a/a.txt
+++ b/a.txt
@@ -1 +1,2 @@
 aaaaa
+bbbbb
```

### 特定のコミットとコミットの比較（変更のあるファイル名の取得)

```
git diff --name-only <commit id> <commit id>
```



## ファイルの削除

ファイルを削除する際には、ローカルPCのOS上の操作としての削除とリポジトリの管理上の削除がありますので注意してください。

```
git rm README.md
git status 
git commit -m "delete README.md
```

* `rm` を行った段階でローカルディレクトリ上から　README.mdが削除されます
* その後 status を確認すると `deleted` フラグが表示されています
* OS上の操作で削除した場合にも deleted フラグが表示されます。

## その他の操作

### 特定のファイルのみを復元する

```
git checkout <commit hash> -- <file name path>
```

* 特定のコミット上のファイルを復元します

### 最新のコードとファイルの差を検索する

```
git diff HEAD <変更前のcommit id> --name-only
```

* どのファイルが削除されているかなどを調べる際に利用します

# リモートリポジトリの操作

## 概要

gitは、分散リポジトリとして管理されます。ローカルのリポジトリをリモート上のGitサーバと同期させることでチーム開発を行います。リモートリポジトリ上のソースコードをローカルPC側のコードと同期させるためにこの節で記載される `pull/push` の動作を行います。


## (準備)Google Cloud Source Repository の作成

* https://source.cloud.google.com/?hl=ja に移動します
* `リポジトリの追加` を選択し `空のリポジトリ` を作成します

* Google Source Repository の設定を git コマンドから利用するためには認証の設定が必要となります。一般的には以下のいずれかの方法で行います（どちらもでも問題はありません）
  * `gcloud init && git config --global credential.https://source.developers.google.com.helper gcloud.sh` の形でヘルパースクリプトを利用することで git が利用できるように構成されます
  * または、認証情報を追加して利用する方法もあります。こちらの場合には gcloud コマンドの導入は不要です。画面上で `クローン` -> `手動で生成した認証情報` -> `Git 認証情報を生成して保存します。` の順操作すると ローカルで実施するコマンドが表示されますので入力すると利用できます。

## 登録

git remote add 

一つのリポジトリに対して複数のリモートサーバの設定を追加することが可能です。git のテキストによく出てくる `git push origin master` という表記の `origin` がリモートサーバの接続名称となります。一番最初のリモートサーバは `origin` と呼ばれます。この名称は任意の文字列で問題ありません。

```
git remote add google https://source.developers.google.com/p/nic-tranning-enablement/r/git-sample
```

設定がされていると以下のように確認ができます

```
❯❯❯ git remote -v
google  https://source.developers.google.com/p/nic-tranning-enablement/r/git-sample (fetch)
google  https://source.developers.google.com/p/nic-tranning-enablement/r/git-sample (push)
```


## リモートリポジトリの取得

git clone , git pull


## 変更の反映

git push 

設定を変更するのは以下のコマンドで行います

```
git push google master
```

* 上記は `google` というリモートサーバに対して ローカルの`master` ブランチを送信（同期）しています。
* pushした際に

## 競合の解消

git merge


# 開発フロー

Gitでの開発フローではブランチやタグを利用して管理を行います。

## ブランチの概要

これまでのテキスト内で利用してきた `master` はブランチを示しています。 `master`は一般的には最初のブランチであり本番用途で利用されることが多いです。

## ローカルブランチとチェックアウト

git branch , git checkout 

## リモートリポジトリへの反映

## 特定のブランチへのマージ処理

ある程度動作が確定した段階で `master` や `dev` といった主要なブランチにワークブランチで行った変更を取り込みたい場合があります。この際にマージ処理を行います。


# チップス

## 利用のヒント
* エラーになった際にはメッセージをよく読みましょう。Gitはたいてい何をしたらよいのかコマンドで教えてくれています。
* GUIツールは多くありますが結果的にはコマンドで覚えるのが早道です。
* よくあるコメントの書き方（）
* リモートとローカルの変更の差が大きくなるとマージできないケースがよく起こりえます。一回の変更(issue) は1日で終われる程度に収めることが望ましいです

# 練習教材

## 個人演習(1) 

### 事前
* このテキストではgitコマンドを利用して行いました。次の演習ではGUIを利用し同じことを実施してみます。
* Visual Studio Codeに以下のプラグインを導入します
  * GitLens (マウスオーバーで変更が表示されます)
  * 

### 演習
* Visual Studio Codeの機能を利用して本テキストのコマンドを実行してください。


## グループ練習(1)

### 事前
* リポジトリを一つ作成し、初期状態で各自のローカルにCloneしている状態にします
* 1名はコーチ役となります。

### 演習
* コーチは、ローカルPC上で、birthday.txt を作成し自身の名前、誕生日を記載しRemoteにPushします。
* 各自ローカルPC上で、birthday.txt を作成し自身の名前、誕生日を記載します。
* リモートリポジトリ上の birthday.txt に全員の名前が記載された状態になるように作業を行ってください

演習の補足
* コーチ役は最初にRemoteリポジトリに自分の名前、誕生日を入れるデモを行い始めます（これにより参加者全員がconflictの状態で始めることができます）今回利用するリポジトリは master のみで行います。
* 競合が行った場合にどのように解消を行っていくのかを学びます。

```
# ローカルのmasterを最新に
git checkout master
git pull
# tokidaに移動してマージ
git checkout tokida
git merge master
# コンフリクトを解消する
git add .
git commit -m 'Merge'
git push -u origin tokida
```


# FAQ

### ローカルで誤った変更をコミットしてしまった場合の取り消し
### 新しいコードがローカルにマージできなくなってしまった際の方法

