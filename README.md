# YMM4からAviUtl2へ、exo出力のように字幕と音声を連携！

YMM4で作成した会話素材を `dialogue.setting` 形式で書き出し、AviUtl2のタイムラインに字幕と音声を配置するプラグインです。.exoファイル自体は生成せず、会話フォルダを介して連携します。

## 編集スタイル

この連携は、**YMM4の字幕設定を厳密にエクスポートしません。** 会話本文・音声・タイミングをAviUtl2へ渡し、字幕の見た目や装飾はAviUtl2側でアレンジして仕上げる編集スタイルを前提にしています。フォントサイズや色など一部の基本値は反映しますが、YMM4上の字幕表示を完全再現する機能ではありません。

## ダウンロード

- [AviUtl2プラグイン: DialogueTimeline.aux2](DialogueTimeline.aux2)
- [YMM4プラグイン: DialogueExport.dll](DialogueExport.dll)
- [ソースコード・サンプル一式](ymm4-aviutl2-dialogue-source.zip)

## 使い方

1. YMM4の「Dialogue出力」から会話フォルダを書き出します。
2. AviUtl2の「ファイル > インポート > Dialogue Timeline読み込み...」を選び、出力フォルダを指定します。
3. 再生ヘッド位置と選択レイヤーを基準に、字幕と音声オブジェクトが配置されます。
4. 字幕の細かなデザインや位置はAviUtl2で調整します。

出力フォルダには `dialogue.setting`、`text/`、`audio/` が入ります。`dialogue.setting` のタイムライン項目がオフセット、長さ、WAVへの相対パスを保持し、テキストと音声は同じインデックスで対応します。

## 現在の状態

開発中の初期版（alpha）です。Windows向けに開発し、YMM4 4.57.0.1を参照してビルドしています。仕様や対応状況は今後変わる場合があります。

## ビルド

### AviUtl2プラグイン

Windows x64、Visual Studio C++ Build Tools、Windows SDK、CMake 3.24以降が必要です。

```powershell
cd sorce
.\scripts\setup-sdk.ps1
cmake -S . -B build -A x64
cmake --build build --config Release
```

生成物は `sorce/build/Release/DialogueTimeline.aux2` です。SDKミラーとnlohmann/jsonの取得にはネットワーク接続が必要です。

### YMM4プラグイン

.NET 10 SDKとYMM4本体が必要です。

```powershell
cd sorce_YMM4
dotnet build .\src\DialogueExport\DialogueExport.csproj -c Release
```

生成物は `sorce_YMM4/src/DialogueExport/bin/Release/net10.0-windows/DialogueExport.dll` です。

## 制限

- 現在は会話を0フレームから順に書き出します。
- YMM4の字幕設定は厳密に再現しません。字幕の仕上げはAviUtl2で行う前提です。
- 対応するYMM4やAviUtl2のバージョンは今後整備します。

## 開発・利用上の注意

このプラグインはAIを活用したバイブコーディングで制作しています。作者は継続的な保守、サポート、互換性維持を約束しません。利用・改変・導入は利用者自身の責任でお願いします。MIT Licenseに基づき現状有姿で提供し、保証はありません。

## ライセンス

このプロジェクトはMIT Licenseで公開します。詳細は [LICENSE](LICENSE) を参照してください。

## 動画ファイル

YMM4上の動画素材も `video/` にコピーし、`dialogue.setting` の `videos` 配列に配置オフセット、長さ、素材内の開始位置、再生速度、元のレイヤーを記録します。AviUtl2側では動画ファイルオブジェクトとして読み込み、カット位置と再生速度を設定します。動画素材に音声が含まれている場合も、二重再生を避けるため動画オブジェクト側の音声は無効にします。

```json
"videos": [
  {
    "index": 0,
    "offset": 120,
    "duration": 90,
    "file": "video/0000.mp4",
    "source_offset_seconds": 2.5,
    "playback_rate": 1.0,
    "layer": 0
  }
]
```
