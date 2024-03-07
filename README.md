# キーボードピカピカシステム
キーボードピカピカシステムは、 2024年3月24日に秋葉原 Mogra で開催予定の Keysounds!!! #2 にお持ちいただいた皆様のキーボードを、演出の一部として外部よりコントロール可能とする仕組みです。

## ざっくりとした仕組み
キーボードから、キーボードピカピカシステムへの接続は、当日会場に設置予定の Raspberry PI 5 に接続された USB ハブに対して行うことを予定しています。

Raspberry PI 5 からキーボードへのLEDの点灯情報の送信は、 QMK ファームウェアが用意する Raw HID と呼ばれる 、HID インタフェースを介して QMK ファームウェアとホストコンピュータ間の双方向通信を可能とする仕組みにより行います。

Raw HID については[こちら](https://github.com/qmk/qmk_firmware/blob/master/docs/ja/feature_rawhid.md)に説明があります。


## キーボードピカピカシステムに接続するキーボードの準備
キーボードピカピカシステムに接続するキーボードは、このドキュメントで説明する Raw HID により LED の点灯をコントロールする仕組みを追加した QMK ファームウェア(以下、QMK ピカピカファームウェアと記載)をコンパイルし書き込む必要があります。

この現時点では以下の条件を満たすキーボード（キーマップ）で QMK ピカピカファームウェアのコンパイルが可能です。

- rgblight を用いてコントロールされる LED を搭載している。
  - rgblight の詳細は[こちら](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_rgblight.md)
  - 現時点では、 rgb_matrix を用いた制御を行う LED を搭載したキーボードには対応していません。これは、手元に対応するキーボードがなく確認ができないためです。しかし、仕組みはシンプルであり、 rgb_matrix は、rgblight と内部的な互換性を保つためのレイヤーを用意しているため、追加での対応は容易かもしれません。
    - rgb_matrix については[こちら](https://github.com/qmk/qmk_firmware/blob/master/docs/feature_rgb_matrix.md)
- Raw HID を用いていないキーマップを利用していること。
  - 例えば、 VIA コンフィグレーターは、内部的には Raw HID を用いて実現されています。このような場合には Raw HID を用いるための定義が重複し、QMK ピカピカファームウェアのコンパイルに失敗します。一般的に VIA コンフィグレータ対応のキーマップを用意しているキーボード向けには、 VIA コンフィグレータを用いないキーマップも用意されていますので、こちらをコンパイルし書き込んでしてください。

ピカピカファームウェアは[こちらの git レポジトリ](https://github.com/sirrow/qmk_firmware/)の pikapika ブランチにあります。pikapika ブランチを checkout した後、qmk compile コマンドでコンパイルの後、書き込みを行ってください。
書き込みに先立ち、念の為現状復帰ができる状態にあることを確認してください。


## キーボードピカピカシステムに接続可能となったかの確認方法
下記 Web サイトにアクセスして「CONNECT」ボタンを押して、キーボードを選択したのち、LED の色の変更ができれば成功です。

[https://hsgw.githug.io/hid-rgbled-tester/](https://hsgw.githug.io/hid-rgbled-tester/)


## 連絡先
不明な点も多いかと思います。ご質問が有りましたら、私の twitter アカウント @sirrow までご連絡ください。