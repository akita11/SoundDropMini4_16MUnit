# SoundDropMini4MUnit

<img src="https://github.com/akita11/SoundDropMini4_16MUnit/blob/main/SoundDropMini4_16MUnit.jpg" width="240px">

音声再生IC CH7003を使用した音声再生モジュールです。4MB（または16MB）のフラッシュメモリを搭載しており、PCにUSB Type-Cケーブルで接続するとUSBメモリとして認識され、そこにMP3音声データ等をコピーします。その音声データをGrove端子のシリアル(UART)通信で再生制御できます。UIFlow(v1)用のブロックもあります。

※CH7003に関する情報は[メーカWebページ](https://www.ch2003.com/h-col-124.html)等で検索してください。要点は以下にまとめてあります。


## 使い方（接続方法）

以下のようにスピーカやアンプを接続し、Grove端子からの制御で音声を再生します。必要に応じて基板裏面の半田ジャンパをショート・カットしてください。

<img src="https://github.com/akita11/SoundDropMini4_16MUnit/blob/main/SoundDropMini4_16MUnit_back.jpg" width="240px">


### アンプ付きスピーカに接続する場合

3.5mmジャック（ステレオ：上部中央）に3.5mmステレオケーブル等でアンプに接続します。
またJP1を半田等でショートしますにします。


### スピーカを直接接続する場合

<img src="https://github.com/akita11/SoundDropMini4MUnit/blob/main/SoundDropMini4_16MUnit_spk.jpg" width="240px">

基板上部にある1.25mmピッチコネクタ(Molex 53047-0210用)をとりつける端子があり、市販の小型スピーカ（M5Stack本体に使われているものなど。AliExpressやTaobaoでも同等品があります）を接続することができます。※隙間がせまく差し込みにくいので注意してください。
またJP1を開放（初期状態）します。


### 3.5mmジャックに直接スピーカを接続する方法

3.5mmジャックに直接スピーカを接続することもできます。その場合は、基板裏面のハンダジャンパを以下のように修正してください。（スピーカはモノラル再生となります）

- JP2をカット
- JP3・JP4の中央と△マーク側をカットし、中央と反対側（△マークがない側）をショート


## 本体機能の設定

[メーカWebページ](https://www.ch2003.com/h-col-124.html)から"CH7003语音芯片模块.zip"）をダウンロードし、その中にあるテスト用ソフトウエア（Windows用）で、音量などを設定できます。ただし日本語PCでは文字化けするため、データシート中のソフトウエア画面と比べながら使うことになります。なお後述のコマンドでも設定できます。


## 使い方（ソフトウエア）

あらかじめ、本体基板の一部にあるUSB Type-CにUSB Type-Cケーブルを使ってPCへ接続し、現れるドライブに使用する音声データ（*.mp3）をコピーしておきます。なおファイル名は、「"001"から"999"の3桁の数値」のみが有効（CH7003の仕様）となり、その数値が「曲番号」となります。
※mp3のビットレートが高すぎると、正しく再生されない場合があるようです。その場合はビットレートを落として使ってください。


### UIFlow(v1)での使い方

[SoundDropMiniUNIT](https://github.com/akita11/SoundDropMIniUNIT)のページから「SoundDropMini.m5b」をダウンロードし、UIFlow(v1)の"Custom"の"Open *.m5b"からこのファイルを指定すると、Init、Play、Stopの3つのブロックを使用できます。（SoundDropMiniUNITと主な再生関連のコマンドは同一です）

<img src="https://github.com/akita11/SoundDropMini4_16MUnit/blob/main/SoundDropMini_Block.png" width="240px">

- SoundDropMini_Init : 初期化。最初に1回だけ使用します。"TX"と"RX"には、使用するGroveポート・マイコン本体にあわせて、使用するピン番号を指定します。例えば、M5StackBasicのPortA（本体の赤いコネクタ）の場合は、上図のようにTX=21、RX=22を指定します。M5StackCore2のPortAの場合は、TX=32、RX=33となります。
- SoundDropMini_Play : numに指定した「曲番号」の音声を再生します。
- SoundDropMini_Stop : 再生中の音声を停止します。

※この例では再生した直後に停止していますので、音声がほぼ聞こえません。実際には目的に合わせて使い分けてください。


### その他のソフトウエアでの使い方

搭載されている音声再生IC(CH7003)のデータシート等を参考に、制御コマンドをUART経由で送信して使用してください。通信プロトコルは「9600bps/N81」です。
制御コマンドの一部を以下にまとめます。

- 次の曲の再生: 0x7e 0x11 0x00 0x02 0x00 0x00 0xef
- 前の曲の再生: 0x7e 0x12 0x00 0x02 0x00 0x00 0xef
- 指定した曲(HH:LL＝曲番号(上位/下位の順))の再生: 0x7e 0x13 0x00 0x02 HH LL 0xef
- 音量UP: 7e 0x14 0x00 0x02 0x00 0x00 0xef
- 音量DOWN: 0x7e 0x15 0x00 0x02 0x00 0x00 0xef
- 音量をNNに設定: 0x7e 0x16 0x00 0x02 0x00 NN 0xef
- 再生停止: 0x7e 0x26 0x00 0x02 0x00 0x00 0xef


## Author

Junichi Akita (@akita11) / akita@ifdl.jp
