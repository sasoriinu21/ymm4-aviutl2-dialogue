# YMM4からAviUtl2へ、exo出力のように字幕と音声を連携！

YMM4で作成した会話素材を、テキスト・音声・タイミング情報を含むフォルダとして書き出し、AviUtl2のタイムラインへ配置するためのプラグインです。

## 現在の状態

開発中の初期版（alpha）です。

- YMM4の現在のシーンから `dialogue.setting`、`text/`、`audio/` を出力します。
- AviUtl2で `dialogue.setting` を読み込み、字幕と音声のオブジェクトを配置します。
- AviUtl2側の音声参照設定を実装しました。ビルドは確認済みですが、この修正後のAviUtl2上での音声再生確認はこれからです。
- 対象環境はWindows。YMM4 4.57.0.1とAviUtl2 SDKで開発しています。

機能・データ形式は今後変更される可能性があります。プロジェクトのバックアップを取ってからお試しください。

## ソースコード

公開ソース一式は [`ymm4-aviutl2-dialogue-source.zip`](ymm4-aviutl2-dialogue-source.zip) に収録しています。ダウンロードして展開すると、各プラグインのソース、ビルド手順、サンプルを確認できます。



## 出力形式

```text
dialogue[保存名]/
├─ dialogue.setting
├─ text/
│  ├─ 0000.txt
│  └─ ...
└─ audio/
   ├─ 0000.wav
   └─ ...
```

`dialogue.setting` の `timeline` 各項目には、会話のインデックス、キャラクター、開始オフセット、長さ、音声ファイルへの相対パスを記録します。字幕本文と音声は同じインデックスのファイルで対応します。オフセットと長さは `fps` を基準としたフレーム数です。

## 使い方

1. YMM4の「Dialogue出力」から会話フォルダを書き出します。
2. AviUtl2の「ファイル > インポート > Dialogue Timeline読み込み...」を選び、出力フォルダを指定します。
3. AviUtl2は再生ヘッド位置と選択レイヤーを基準に、字幕と音声オブジェクトを配置します。

AviUtl2に配置された音声オブジェクトは、出力フォルダ内のWAVを参照します。フォルダを移動した場合は、再読み込み時に元のフォルダを選択してください。

## ビルド

### AviUtl2プラグイン

Windows x64、Visual Studio C++ Build Tools、Windows SDK、CMake 3.24以降が必要です。

```powershell
cd sorce
.\scripts\setup-sdk.ps1
cmake -S . -B build -A x64
cmake --build build --config Release
```

生成物は `sorce/build/Release/DialogueTimeline.aux2` です。SDKミラーは `setup-sdk.ps1` が取得します。SDKと依存ライブラリの取得にはネットワーク接続が必要です。

### YMM4プラグイン

.NET 10 SDKとYMM4本体が必要です。標準のインストール先以外にYMM4を導入している場合は `YMM4DirPath` を指定します。

```powershell
cd sorce_YMM4
# .NET SDKがPATHにある場合
dotnet build .\src\DialogueExport\DialogueExport.csproj -c Release
```

## フォルダ構成

- `sorce/` — AviUtl2インポートプラグイン（C++）
- `sorce_YMM4/` — YMM4書き出しプラグイン（C# / .NET 10）

## 制限

- 現在は会話を0フレームから順に書き出します。
- AviUtl2側で全てのYMM4キャラクター表示設定を再現するわけではありません。未対応の設定は今後拡張予定です。
- AviUtl2で音声パスが正しく設定されるか、最新ビルドで実機確認が必要です。
- プラグインの配布形式と対応バージョンは今後整備します。

## 開発・利用上の注意

このプラグインはAIを活用したバイブコーディングで制作しています。作者は継続的な保守、サポート、互換性維持を約束しません。利用・改変・導入は利用者自身の責任でお願いします。MIT Licenseに基づき現状有姿で提供し、保証はありません。

## ライセンス

このプロジェクトはMIT Licenseで公開します。詳細は [LICENSE](LICENSE) を参照してください。

