# otolithr
[![Travis build status](https://travis-ci.com/akikirinrin/otolithr.svg?branch=master)](https://travis-ci.com/akikirinrin/otolithr)
[![codecov](https://codecov.io/gh/akikirinrin/otolithr/branch/master/graph/badge.svg)](https://codecov.io/gh/akikirinrin/otolithr)
[![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)

## 目的
成長解析の手順を属人化させないためのパッケージです。

### 耳石日輪解析における現状の問題（✓: 本パッケージによって解決済）
- [X] `.hdr`ファイルを`.csv`にマージするひと手間によって、データが二重化している
- [X] 解析結果の保持方法が研究者によって異なる
- [ ] 各人が同じような作業をしている（どんな作業が共通しているのか、話し合っていきたいです）

こまごました機能は、みなさんと作っていきたいと思います。

関数の挙動や生成されるデータフレームの列名などの様式は、あくまでも現時点におけるたたき台です。

使いにくいところ、気に食わないところなど、[Issues](https://github.com/akikirinrin/otolithr/issues)にどんどんお寄せ下さい。

## 使い方
インストール方法は[こちら](https://github.com/akikirinrin/otolithr/wiki/インストール方法)

```
path <- "YOUR_PATH_TO_HDR_FILES"                  # .hdr ファイルが入ったNASのフォルダなど
dat  <- hdr2df_in_dir(path, species = "maiwashi") # 現時点ではマイワシのみに対応
preview(dat)                                      # 日齢--耳石径関係をプロット
```
