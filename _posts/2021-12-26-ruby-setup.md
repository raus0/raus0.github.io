---
title: Ruby環境構築
author: rauszero
date: 2021-12-26 15:30:00 +0800
categories: [Programming, Ruby]
tags: [Ruby]
---

## 環境構築

#### rbenvのインストール
rubyのバージョン管理ツール

とりあえずはdockerを使わずに手元の環境でセットアップ

Homebrew(Macのパッケージ管理ツール)を先にインストールしておく(方法は割愛)

Homebrew経由でrbenvをインストール

```bash
$ brew install rbenv
```

#### rbenvの初期設定
```bash
$ rbenv init
```

実行するとevalから始まる以下の1行が表示される

筆者はzshを使ってるので.zshrcに記述(.zprofileでも可)

無い場合は作成する

```bash
eval "$(rbenv init - zsh)"
```

記述したらターミナルを再起動して準備完了

#### セットアップ診断用スクリプトの実行
```bash
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash
```

筆者の環境で実行するとこんなエラーが出た

rbenv shimsの環境パスが通ってないぽい

```bash
Checking for rbenv shims in PATH: not found
```

.zshrcに先ほどの1行を以下の3行に記述

```bash
## rbenv
eval "$(rbenv init -)"
export PATH="~/.rbenv/bin:$PATH"
export PATH="~/.rbenv/bin:~/.rbenv/shims:$PATH"
```

再度診断用スクリプトを実行すると以下のようになった

```bash
Checking for `rbenv' in PATH: /usr/local/bin/rbenv
Checking for rbenv shims in PATH: OK
Checking `rbenv install' support: /usr/local/bin/rbenv-install (ruby-build 20211109)
Counting installed Ruby versions: 1 versions
Checking RubyGems settings: OK
Auditing installed plugins: OK
```

rbenv shimsの環境パスがちゃんと通ってる 大丈夫そう

#### Rubyのバージョンを指定してインストール
末尾に-Lオプションを指定するとインストール可能なバージョンの一覧が表示される

```bash
$ rbenv install -L
```

今回は現時点での最新の安定版？である3.0.2もインストールした(時間かかった)

```bash
$ rbenv install 3.0.2
```

#### 使用するrubyのバージョンの切り替え
インストールしたばかりのrubyの場合はrbenv rehashで切り替え可能な状態になる

```bash
$ rbenv rehash
```

インストールされてるrubyの一覧を表示する

```bash
$ rbenv versions
```

現状ではrbenvでインストールした3.0.2ではなく

rbenvを使わずに入れたシステムのrubyが使用される状態になってる

```bash
* system
  3.0.2
```

rbenv globalで切り替える

```bash
$ rbenv global 3.0.2
```

再度rbenv versionsで確認(ruby -vで確認してもOK)

```bash
system
* 3.0.2 (set by /Users/xuqxu/.rbenv/version)
```

無事切り替わったことを確認

## Rubyを実行してみる

#### irbの起動
```bash
$ irb
```

こんな感じになって対話的(インタラクティブ)にrubyを実行できる(pryというものもある)

抜けるときはexitと入力

ちなみにBom Diaはポルトガル語でおはよう

```terminal
[~] raus0 $ irb
irb(main):001:0> puts 'Bom Dia'
Bom Dia
=> nil
irb(main):002:0> exit
[~] raus0 $
```

#### rbファイルを作成して実行
とりあえず作業用ディレクトリを適当に作成して移動

筆者はホームディレクトリにrubyディレクトリを作成しているので

その中にplaygroundという作業用ディレクトリを作成して移動

sample.rbというrubyファイルを作成してVSCodeで開いた

```terminal
[~] raus0 $ cd ruby
[~/ruby] raus0 $ mkdir playground
[~/ruby] raus0 $ ls
playground	raus0.github.io
[~/ruby] raus0 $ cd playground
[~/ruby/playground] raus0 $ touch sample.rb
[~/ruby/playground] raus0 $ code .
```

VSCodeか任意のエディタでsample.rbを以下のように編集

```ruby
puts 'Bom Dia'
```

ターミナルでこのように実行して出力されればOK

```terminal
[~/ruby/playground] raus0 $ ruby sample.rb
Bom Dia
```

#### pryについて
pryはirbと同じように対話的(インタラクティブ)にrubyを実行するためのツール

こういったツールのことを***REPL*** (Read-Eval-Print Loop)と呼ぶらしい

入力されたコードを読んで評価して表示することを繰り返す

irbは標準でrubyに付いてくるけど pryはGemでインストールするパッケージ

pryはirbの機能に加えてコードに色付けをしたり自動的にインデントを入れてくれたりする

コードの補完機能もあるらしい Tabで補完 2回Tabを押すと候補表示

#### pryのインストール
```bash
$ gem install pry
```

#### pryの起動

```bash
$ pry
```

このように自動でインデントを入れてくれたり

色分けされたりする(色は実際にターミナルで確かめてください)

```terminal
[~/ruby/playground] raus0 $ pry
[1] pry(main)> class Author
[1] pry(main)*   attr_accessor :name
[1] pry(main)* end
=> [:name, :name=]
[2] pry(main)> author1 = Author.new
=> ##<Author:0x00007f7df73250b0>
[3] pry(main)> author1.name = '羅'
=> "羅"
[4] pry(main)> author1.name
=> "羅"
[5] pry(main)> author1
=> ##<Author:0x00007f7df73250b0 @name="羅">
[6] pry(main)>
```
## Rubyの特徴

#### インタプリタ型言語
明示的にプログラムをコンパイルする必要がない

実際にコードを実行するタイミングで逐一機械語にコンパイルしてくれる

手軽にコードを実行して結果を確認できる

#### オブジェクト指向言語
Rubyではあらゆるものがオブジェクトとして扱われる

オブジェクトを簡単に言うとデータと特定の処理を行うメソッドをまとめたもの

#### 構文の自由度が高い
一つの機能を作るにも様々な書き方ができる

自由度が高いと楽しい反面 どんなコードになるかは書いた人の技量次第

複雑で汚いコードにも簡潔で綺麗なコードにもなりえる

#### 国産の言語(島根県松江市が聖地)
**Ruby**の開発者は**島根県**の**松江市**在住

「**島根**にパソコンなんてあるわけないじゃん」ってフレーズが流行ったけど

富士通の工場があるし 当然PCもある

島根にないものはパソコンではなく**新幹線**

同じ山陰地方でも鳥取はまだ恵まれている(島根並感)
