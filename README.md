# データベース 演習

本演習では、[奈良先端大でのカーシェアリング実証実験](https://naist-carshare.github.io/)の利用実績データの分析・可視化を行います。

## Downloads

このリポジトリからデータをダウンロードして，各自それぞれのデータについて分析を行います。

ダウンロードするには、zipファイルをダウンロードする方法と、git CLIを使用してダウンロードする方法の2種類あります。

#### 方法1: zipファイルをダウンロード

`<> Code ▼`ボタンと`Download ZIP`ボタンを押して、zipファイルをダウンロードしてください。ダウンロード後、各自でzipファイルを展開してください。

![image](https://github.com/piderlab/23-databese-class/assets/40050810/c1519be2-d0b1-421c-9a49-edc89521c4f5)

#### 方法2: git CLIでダウンロード

PCにgit CLIをインストール済みの方は、以下のコマンドを実行することでデータをダウンロードすることができます。

```bash
git clone git@github.com:piderlab/24-databese-class.git
```

## 扱うデータについて

データはCSVファイルで配布されます。

本講義の演習で扱うデータは以下の通りです。

- 乗車履歴データ: [history.csv](./history.csv)
  - ユーザーが実際に車に乗車した履歴。
  - 1回の乗車がcsvの1行分のデータに相当します。
- 駐車履歴データ: [trip.csv](./trip.csv)
  - 車に乗車した際のユーザーの目的地。
  - 1回の乗車で複数の目的地へ行っている場合があります。
- 目的地データ: [spot.csv](./spot.csv)
  - 目的地に関する詳しい情報。
  - 駐車履歴データには目的地id(`spot_id`)のみが入っています。目的地に関する詳しい情報(店舗名や位置)は、このファイルから取得する必要があります。
- ユーザー情報データ: [user.csv](./user.csv)
  - 全ユーザーのリストが入っています。
  - ユーザーは`staff`(教職員)と`student`(学生)の2種類あります。
 
それぞれのcsvファイルには以下のようなデータが含まれています。(クリックで展開)

<details>
<summary>

#### history.csv
  
</summary>

|列名|内容|
|---|---|
history_id|乗車履歴のid
started_at|乗車し始めた時刻
ended_at|乗車し終わった時刻
from_parking_lot|乗車開始地点 (NAISTまたはATR)
to_parking_lot|乗車終了地点 (NAISTまたはATR)
car|使用した車の名前
passengers_count|乗車人数（1の場合: 運転手のみ、2以上の場合: 運転手を含めた団体利用）※運転手同士の相乗りではない
distance|移動距離
user_id|車に乗車したユーザーのユーザーid

</details>

<details>
<summary>

#### trip.csv
  
</summary>

|列名|内容|
|---|---|
created_at|目的地へ行った日時
lat|緯度
lon|経度
car|使用した車の名前
user_id|目的地へ行ったユーザーのユーザーid
spot_id|目的地id
history_id|history.csv内の対応する行のhistory_id

</details>
<details>
<summary>

#### spot.csv
  
</summary>

|列名|内容|
|---|---|
spot_id|地点id
spot_name|地点の名称
lat|地点の緯度
lon|地点の経度
count|ユーザーが地点に行った回数
spot_types|地点のタイプ (例: `restaurant`=レストラン) 関連する順に、複数のタイプが入っています
is_parking|その場所がカーシェアの乗車/返却地点として用意された駐車場かどうか (Trueの場合カーシェアで用意された駐車場、Falseの場合カーシェアで用意された駐車場ではない)

</details>
<details>
<summary>

#### user.csv
  
</summary>

|列名|内容|
|---|---|
user_id|ユーザーid
user_type|ユーザーが教職員(staff)か学生(student)か

</details>

各csvファイル間のデータの関係は以下の図を参考にしてください。

![image](https://github.com/piderlab/23-databese-class/assets/40050810/867f2e2a-0e8a-4c2e-ae2b-d29f23f89e1d)


## Example Analysis

Pythonを使用したデータ分析の例を以下に示します。

提供するcsvファイルの具体的な読み込み方法や、データの可視化方法はこちらを参考にしてください。

- [sample_1](./sample1.ipynb) : 乗車人数が 3 人以上の時，どの spot へ行ったか
- [sample_2](./sample2.ipynb) : 最も目的地利用している学生の目的地別ランキング

## Data Handling

提供するcsvファイルを、Pythonを使って読み込む例を以下に示します。

ここでは、[pandas](https://pandas.pydata.org/)というライブラリを使用してcsvファイルを読み込む例を示します。

pandasでは、「データフレーム」と呼ばれるデータ構造を利用することができます。データフレームを使うと、SQLのようなテーブル結合・集計・ソートなどの操作を行うことができます。

```py
# csvファイルをデータフレームとして読み込む
import pandas as pd
df = pd.read_csv("相対パス")
```

```py
# テーブル結合(内部結合)
marged_df=pd.merge(結合したいデータフレームの格納された変数名1, 結合したいデータフレームの格納された変数名2, on="結合対象となる列名")
#　必要な列のみ抽出
marged_df = marged_df[["必要な列名","必要な列名","必要な列名"]]
```

提供データの中には、そのままでは扱いづらいデータ形式があります。必要に応じてデータ形式を変換することで、プログラムから扱いやすくなります。

```py
# csvファイル内の日付データ列をdatetime型にしてデータフレームとして読み込む
import pandas as pd
df = pd.read_csv("相対パス", parse_dates=["列名"])
```

```py
# 読み込んだデータフレーム内にあるリストデータ列をlist型に変換する
import json
df["列名"] = df["列名"].map(json.loads)
```

もちろん、pandas以外のライブラリを使用してcsvファイルを読み込むこともできます。また、Python以外の言語を使用してデータ分析を行っても問題ありません。

#### 参考リンク

pandasにおけるデータ処理方法は以下のサイトも参考にしてください。

- テーブル結合： https://note.nkmk.me/en/python-pandas-merge-join/
- データ集計： https://note.nkmk.me/python-pandas-groupby-statistics/
- データの重複削除： https://note.nkmk.me/python-pandas-duplicated-drop-duplicates/
- ソート： https://note.nkmk.me/python-pandas-sort-in-any-order/
- ソート： https://note.nkmk.me/python-pandas-sort-values-sort-index/

## Data Visualize

本演習では，データを分析するだけではなく，分析結果としてデータの可視化も行います。
以下に，データの可視化の方法について示します。

pythonにおける可視化する際に用いられるライブラリには[Matplotlib](https://matplotlib.org/stable/gallery/index)や[plotly](https://plotly.com/python/)など様々なものがありますが，ここでは主にplotlyを用いた可視化の方法について記載します。勿論，他の可視化ライブラリを用いても良いです！！

```py
# 棒グラフで可視化する場合の基本コード
# https://plotly.com/python/bar-charts/
import plotly.express as px
df = ...
fig = px.bar(df, x="x軸の値として使用する列の名前", y="y軸の値として使用する列の名前", title="棒グラフ")
fig.show()
```
```py
# 折れ線グラフで可視化する場合の基本コード
# https://plotly.com/python/line-charts/
import plotly.express as px
df = ...
fig = px.line(df, x="x軸の値として使用する列の名前", y="y軸の値として使用する列の名前", title="折れ線グラフ")
fig.show()
```
```py
# 円グラフで可視化する場合の基本コード
# https://plotly.com/python/pie-charts/
import plotly.express as px
df = ...
fig = px.pie(df, values="割合として使用する列の名前", names="valuesに対応するラベル", title="円グラフ")
fig.show()
```
上で示した可視化方法はほんの一部でしかありません。他にもいろいろなグラフで可視化が出来ます。是非，[plotly](https://plotly.com/python/)にいろいろな可視化方法がありますので，他の可視化方法（グラフ）も試してみてください！！
