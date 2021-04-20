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
