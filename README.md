#+TITLE: tidyNAS
#+STARTUP: overview
[![Travis build status](https://travis-ci.org/smxshxishxad/tidyNAS.svg?branch=master)](https://travis-ci.org/smxshxishxad/tidyNAS)

* これは何か？
浮魚資源グループの NAS 内のデータを，使いやすい形で蓄積していくためのプロジェクト．
下位プロジェクトに
- [[./reformr.org][reformr]]: 浮魚資源Gで生成するデータを管理していくパッケージ．過去の散乱データの整理も含む
- otolithr: 耳石日輪解析データを管理していくパッケージ
- gyokaikyo: 漁海況業務に関するパッケージ．今は別リポジトリとして運用．資源評価と統合して１つのパッケージとするか，重複処理部分を別パッケージとして切り出す予定．
各パッケージで共通する関数をユーティリティパッケージとして書き出し，最終的には本プロジェクトをパッケージ化する予定．
↓イメージ
#+BEGIN_SRC R
library(tidyNAS)
#+END_SRC
* プロジェクトの構成
** データフォーマットの決定
将来の横断的解析に堪えるためには，データをどのような形で蓄積してゆくべきかを議論し，データのフォーマットを（[[https://github.com/smxshxishxad/tidyNAS/issues][Issues]] で意見を集約したのち）決定する．
** 過去の蓄積データ統合・出力パッケージ/ [[./reformr.org][reformr]]
新しいデータフォーマットがいくら合理的だとしても，過去の蓄積データとの連続性がなくなってしまうのは問題である．
この問題に対処するために，過去の蓄積データについても全て，整理統合する必要がある．
 [[./reformr.org][reformr]] パッケージが，これを解決する．
*** 注意すべきポイント
- データ解析に適したデータ構造は，入力もしやすいとは限らない．
- 入力に適したデータ構造（野帳，測定台帳）は，データ解析には適さない．
そこで，整理統合されたデータをまず作り，そこから野帳形式のデータを自動生成することにする．
*** 機能と関数イメージ
- merge_olds(): これまで蓄積してきたデータを統合する
- conv2inputSheet(): 入力しやすいように野帳形式に変換する
- export_inputSheet(): 過去の全ての年ごとに出力する（あたかも，最初からその形式の野帳で入力されたかのように）
出力されるデータの命名規則は，inputSheet_YYYY_Routput.csv
** システム移行作業
過去のデータからの流れを断ち切るために，ここは敢えてパッケージに組み込まず，隔離しておく．
- シェルスクリプトで，ファイル名から_Routputを消去
- 今後のデータは inputSheet_YYYY.csv をgit管理下で蓄積していく
- 過去のデータに入力ミスが見つかった場合も，inputSheet_YYYY.csv を修正する
万一，過去の蓄積データの再出力が必要になっても，git管理下なので適宜"theirs"と"ours"を区別してマージ可能
** 現在以降のデータ蓄積パッケージ/ tidyfishr
浮魚GのNASの中にあるデータの品質をチェックし，統合データとドキュメント類を出力するパッケージ．
*** 機能と関数イメージ
**** 品質チェック
以下のような関数を作るか，あるいは，単なる自動テストでまかなう．
***** check_colnames(): 行名
***** check_class(): 各行のクラス
***** warn_outlier(): 異常値について警告する．チェック済みを1にしたら，警告は抑制される．
異常値検出にはT2 統計量，Q 統計量を使う？
https://datachemeng.com/t2qstatistics/
**** 統合データ出力
***** 航海データの変形
trans_cruise()

入力に便利な野帳フォーマットを，解析しやすいフォーマットに変形するためにこれが必要．

***** データ統合
build(dir.measure, dir.otolith, dir.survey = option, range.yr = option)

チェックを通過したものだけを統合．

チェック通過したかの値を保持しておく．

どれか1つのせいで全体のビルドが失敗しないようにする．

***** データの種類
****** 調査
- 採集
- CTD
- 測定台帳
- 耳石
- 鱗
- 脂質分析
****** 鮮魚等
- 測定台帳
- 耳石
- 鱗
- 脂質分析
**** データ目録を作成
make_list(data, outdir)
**** ディレクトリの見取り図を作成
tree -N ./ > dir_structure.txt
**** 入力シート，野帳を生成
make_sheets(outdir)

Imports: XLconnect

**** 要約図表を出力
- make_fig(data, outdir)
- make_table(data, outdir)

Imports: ggplot
**** ドキュメントを作成
make_docs(data, outdir, range)
LaTeX と連携
** 耳石データ統合パッケージ/ [[./otolithr.org][otolithr]]
** 漁海況/ gyokaikyor
** 資源評価
** CI部分
データ目録等のドキュメントと，実際のデータとの間に乖離を生じさせないために必要
- データの更新を監視しておく
- データ更新があるたびに上記パッケージの関数群が実行され，統合データとドキュメント類が自動的に出力される．

* 想定されるディレクトリ構造
- 航海データ/
  - 2017
  - 2018
    - 6月
      - 採集結果.csv
    - 8月
      - 採集結果.csv
- 測定データ/
  - survey2017.csv
  - survey2018.csv
  - sengyo2017.csv
  - sengyo2018.csv

- CTD/
  - 2017
    - st1.asc
    - st2.asc
    - ...
  - 2018
    - st2.asc
    - st1.asc
    - ...
  - tidyNAS/
    - README
    - I/O設定ファイル
    - figs/
      - Sc-j_blhist.pdf
      - Sc-j_blbw.pdf
      - Sc-j_agehist.pdf
      - Sc-j_hdate.pdf
      - Sc-j_cpue.pdf
      - Sc-a...
      - Ja-m...
      - Sa-m...
      - Et-t...
      - En-j...
  
    - tables/
      - all.pdf
      - 1997.pdf
      - ...
      - 2018.pdf
    - reports/
      - 1997.pdf
      - ...
      - 2018.pdf
      - ...
      - Sc-j.pdf
      - Sa-m.pdf
      - En-j.pdf
      - ...

* やらないこと
以下のデータの整備
- CTDデータ（海洋環境Gに任せる）
- NORPAC（生態系変動Gに任せる）
* 改善案の募集
改善案は [[https://github.com/smxshxishxad/tidyNAS/issues][Issues]] にて随時募集中
- データ形式の使いやすさ（解析のしやすさ，入力のしやすさ，ファイルの見つけやすさ）について
- 各調査の呼称，各県データのサンプル名の規格化について
- その他プロジェクトや関数の構成，わかりにくい箇所全てについて
