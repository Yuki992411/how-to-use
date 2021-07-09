#  **HOW TO USE (git)**

# 0. バージョン管理システムとは
* バージョン管理を手動（フォルダコピー）でなく，自動で行なってくれるシステム
* 過去のバージョンに戻すことも容易
* 異なる目的の開発を同時に進めることが可能（ブランチ）

  | バージョン管理方法 | 特徴 | 例 |
  | :-: | :-: | :-: |
  | 単独型 |ファイル単位でバージョン管理（プログラム全体ではない）| RCS(Revision Control System) |
  | 集中型 |サーバー上（唯一のリポジトリ）にコミットする |CVS(Concurrent Versions System)，Subversion |
  | 分散型 |個々の開発者リポジトリを複製できる |Git, Mercurial |
<br/>

# 1. Gitの初期設定
```
--local  -> ./.git/config に記載
--global -> ~/.gitconfig に記載
```

* 確認
```
$ git config -l
```

* commitするときのユーザー名やメールアドレスの登録
```
$ git config --global user.name "名前"
$ git config --glabal user.email "hoge@foo.com"
```

* そのときのキャッシュ（一時保存のようなもの）の設定
```
$ git config --global credential helper
```

* commit時のエディターの選択
```
$ git config --global core.editor vim
```

* commitメッセージのテンプレ登録
```
.gitmessage.txt（なんでもいい）にテンプレ記載

$vim ~/.gitmessage.txt
$cat ~/.gitmessage.txt
< Subject line >

< What happened >

$ git config --global commit.template ~/.gitmessage.txt
```

* ファイル無視（グローバル設定）
```
$ git config --global core.excludesfile ~/.gitignore_global
```

#### ※`.gitignore`について．
  - `#` : その行をコメントとして処理
  - `hoge.txt` : <b>ディレクトリに関係なく</b>，そのファイルを無視．
  - `/hoge.txt` : .gitignoreのディレクトリにそのファイルがあれば，無視．
  - `build/` : そのディレクトリ以下をすべて無視．
  - `*` : ワイルドカードも使用できる
  - Learn more? → https://github.com/github/gitignore

* CR LFの処理（自動変換）

  | prop | checkout（取り込み） | commit
  | :-: | :-: | :-: |
  | true | LF -> CRLF | CRLF -> LF|
  | input | as-is |  CRLF -> LF|
  | false | as-is | as-is|

  * git for windowsはtrue推奨

```
$ git config --global core.autocrlf prop
```
<br/>

# 2. 開発中のコマンド

* リポジトリの作成
```
$ git init
```

* リポジトリの作成（ワーキングディレクトリなし）
-> 中央リポジトリに保存するときとか
```
$ git --bare init
$ git --bare init --shared (グループ内のアクセス可)
```

* 中央リポジトリから複製
```
$ git clone https://github.com/hoge/foo.git
$ git pull (git fetch と git mergeのコンボ)
```

* 基本操作
```
$ git add . (ステージング)
$ git mv hoge.txt (ファイルの移動・改名)
$ git rm hoge.txt (ファイルを追跡外にし，削除)
$ git commit -m "コミットのメッセージ" (コミット)
$ git commit --amend (最後のコミットに変更分を追加してくれる.コミットした時間は前回のまま．)
```

* 緊急対応
```
$ git stash (現在の作業内容を一時退避)
$ git stash apply (最新の退避していたもの「スタッシュ」を戻す)
$ git stash drop (最新のスタッシュを削除)
$ git stash clear (スタッシュ全削除)
```

* 中央リポジトリの更新
```
$ git push リポジトリ名 ブランチ
```

* タグ
```
$ git tag hoge
$ git tag (タグの一覧表示)
$ git tag -d hoge
```

* コミットの操作
```
$ git merge hoge (今いるブランチにhogeブランチを統合→枝分かれが残る)
$ git rebase hoge (今いるブランチにhogeブランチを統合ジ→枝分かれした記録は消える)
$ git rebase -i コミットid (インタラクティブ「対話的」なリベース)
  -- リベース中に使うコマンド --
    $ git rebase --continue (次のリベースに進む)
    $ git rebase --skip (エラーが出ても，次に進む)
    $ git rebase --abort (リベースを中断．今までのリベースは破棄．)
$ git cherry-pick コミットid (そのコミットを持ってくる．なにかあれば， --abortで中断可能)
$ git reset コミットid (これより最新のコミットは削除, "cherry-pick"で回収可能)
```
  - 例（以前のコミットを分割）
  ```
  $ git checkout コミットId
  $ git reset --soft HEAD~ (直前のコミットをなくし，ステージングされた状態にする)
  $ git restore --staged ファイル (ステージングから削除)
   git add, commit (コミットを再度行なう)
  $ git rebase --contnue
  ```

#### ※`git rebase -i コミットid`において．
  - `r (reword)` : コミットメッセージを変える→<b> コミットした時間は変更前と同じ</b>
  - `e (edit)` : コミット内容を変える→<b> コミットした時間も変わる</b>
  - `s (squash)` : コミットをまとめる(最新→過去へたどり,`pick`が現れればそのコミットIdに全てまとめる．)
  - `p (pick)` : なにもしない

#### ※`git reset オプション コミットid`のオプションにおいて．
  - `--Hard` : ファイルの変更も全て削除
  - `--Soft` : ファイルの変更はステージングされている
  - `--Mixed` : ファイルの変更はステージングされていないが，アンステージングとして残されている(オプションなしの場合，コレ)

* 一括編集 `git filter-branch`
  - (一括で)コミットログのAuthorとCommitを変えるとき
  ```
  $ git filter-branch -f --env-filter "GIT_AUTHOR_NAME='new_name'; GIT_AUTHOR_EMAIL='new_mail'; GIT_COMMITTER_NAME='new_name'; GIT_COMMITTER_EMAIL='new_mail';" HEAD
  ```
