---
title: Google Colabで均衡表の活用
author: rauszero
date: 2023-09-26 15:30:00 +0800
categories: [Programming, Python]
tags: [Python]
---

# 環境構築

Google Colabを利用するのが簡単なのでこちらの方法を記載します。

Dockerで実装してましたがPCに負荷がかかってるので…

Googleのコンピュータに負担してもらいます。

無料のサービスですがこの程度の処理なら全然問題なく使えます。

## ライブラリのインポート
```python
pip install mplfinance
pip install yfinance
```

## ライブラリからインストール
```python
import datetime

import pandas as pd
from pandas_datareader import data

import matplotlib.pyplot as plt
%matplotlib inline
import matplotlib.style as style

import mplfinance as mpf
import yfinance as yf
yf.pdr_override()
```

## 一目均衡表作成
```python
def glaph(symbol, start, end, time):
    df = data.get_data_yahoo(symbol, start, end, interval=time)

    date = df.index

    # カラム追加
    high = df['High']
    low = df['Low']

    # 基準線の算出
    max26 = high.rolling(window=26).max()
    min26 = low.rolling(window=26).min()

    df['basic_line'] = ( max26 + min26 ) / 2

    # 転換線の算出
    high9 = high.rolling(window=9).max()
    low9 =  low.rolling(window=9).min()

    df['turn_line'] = ( high9 + low9 ) / 2

    # 先行スパン1の算出
    df['span1'] = (df['basic_line'] + df['turn_line']) / 2

    # 先行スパン2の算出
    high52 = high.rolling(window=52).max()
    low52 =  low.rolling(window=52).min()

    df['span2'] = ( high52 + low52 ) / 2

    # 遅行スパンの算出
    df['slow_line'] = df['Adj Close'].shift(-25)

    # 一目均衡表の表示
    lines = [
            mpf.make_addplot(df['basic_line'].to_numpy()), #基準線
            mpf.make_addplot(df['turn_line'].to_numpy()), #転換線
            mpf.make_addplot(df['slow_line'].to_numpy()), #遅行スパン
            ]

    labels = ["basic","turn","slow","span"]

    fig, ax = mpf.plot(df, type='candle', figsize=(16,6), style='nightclouds', xrotation=0, addplot=lines, returnfig=True,
                      fill_between=dict(y1=df['span1'].values, y2=df['span2'].values, alpha=0.5, color='gray') )
    ax[0].legend(labels)

    # 線の太さを変更
    for line in ax[0].lines:
        line.set_linewidth(0.5)

    plt.show()
```

## 各基本数値足の陰陽数カウント
```python
def multi_basic(symbol, start, end, time, *slow_values):
    df = data.get_data_yahoo(symbol, start, end, interval=time)

    for slow in slow_values:

        # 遅行スパンの算出
        df['slow_line'] = df['Adj Close'].shift(-(slow-1))

        # f'{slow}dayC' 列を追加
        df[f'{slow}dayC'] = ''

        # 初期化
        count = 1
        prev_trend = ''

        for i in range(2, len(df) - slow + 1):
            # slow_line と Open を比較して一致すれば"寄引同時"" 一致しなければ"陽" か "陰" を割り当て
            if df.at[df.index[i], 'slow_line'] == df.at[df.index[i], 'Open']:
                current_trend = '寄引同時'
            elif df.at[df.index[i], 'slow_line'] > df.at[df.index[i], 'Open']:
                current_trend = '陽'
            else:
                current_trend = '陰'

            # 前日と同じトレンドの場合、連続カウントを増やす
            if current_trend == prev_trend:
                count += 1
            else:
                count = 1

            # 2連続カウントを表示
            if count >= 2:
                df.at[df.index[i], f'{slow}dayC'] = f'{count}{current_trend}連'
            else:
                df.at[df.index[i], f'{slow}dayC'] = current_trend

            # 前日のトレンドを更新
            prev_trend = current_trend

    return df
```

# 使用方法

## 表示制限の解除
```python
pd.set_option('display.max_rows', None)
pd.set_option('display.max_columns', None)
```

## 過去1年間の期間を指定
```python
end = datetime.datetime.today()
start = (pd.Period(end, 'D') - 365).start_time
```

## グラフの描画
```python
# 日経平均の週足の例 glaph('^N225', start, end, '1wk')
# 個別株の日足の例  glaph('6098.T', start, end, '1d')
# 各足は[1m, 2m, 5m, 15m, 30m, 60m, 90m, 1h, 1d, 5d, 1wk, 1mo, 3mo]が指定可能
# 1mo以上は過去の期間を長めに調整、1h以下に関しても短めに調整しないとエラーが出ます
# glaph('6098.T', '2023-08-20', '2023-09-22', '1h')のように日付を細かく変えてみてください
glaph('6098.T', start, end, '1wk')
```

## 各基本数値足の陰陽数を表示
```python
# 各基本数値を必要な数だけ指定可能 日足で3と5しか使わないなら以下のようになる
# df = multi_basic('5010.T', start, end, '1d', 3, 5)
df = multi_basic('5010.T', start, end, '1d', 3, 5, 9, 13, 17, 21, 26, 33, 42)
df
```

## 時間論の基本数値・複合数値
基本数値 【9　：一節 × 】 【 17：二節 △ 】 【26：一期（三節) ⚪︎ 】

複合基本数値

・33：一期一節 ⚪︎×

・42：一期二節 ⚪︎△

・51

・65：□

・76：一巡（三期)

・83

・97

・101

・129：□□

・172：□△

・200〜257：□×□

・226：一環（三巡）

・676：一巡環（三環）

## 基本数値足について
例えば3日足であれば3本前の寄りつきを始値として、当日の引けを終値として作成する。

髭の無いローソク足になるのが特徴。

陰陽を見るだけなら遅行スパンの日数を変えれば簡単に見ることができる。

## あとがき
均衡表は自分が最も信頼して使ってる切り札です。特に遅行スパンの動きを見ています。

そのパラメータは26だけでなく、各基本数値を碁石のように並べて使うことができるそうです。

巷に出回ってる情報を当てにすると大損するので、原著を買って本質を学ぼうと思いました。

北海道の方から均衡表の一巻、二巻(完結編)、四巻(型譜)の計3冊の原著を安価でお譲りいただきました。

自分なりに気付いたことを追記していきます。

何か間違いがありましたら、またご指摘くださると幸いです。(もし見られていれば…)

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">日の呼吸は12の型があるけど<br>型譜にも12の型があった🌅<br><br>原著1巻と完結編をざっと読んで<br>型譜の最初の型の項まで読んだ<br>頭が痛くなってきたので休憩☕️ <a href="https://t.co/bKQ7gQKdOB">https://t.co/bKQ7gQKdOB</a> <a href="https://t.co/c43ldu2ARt">pic.twitter.com/c43ldu2ARt</a></p>&mdash; raus (@rauszero) <a href="https://twitter.com/rauszero/status/1700379591330889852?ref_src=twsrc%5Etfw">September 9, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
