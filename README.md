# FPS Validator

iOSでMetalコンテンツを描画しながら、`CADisplayLink` のコールバック時刻からフレーム間隔とFPSを記録・CSV出力する検証アプリです。

## 何を測っているか

このアプリが報告するFPSは、MetalコマンドのGPU完了数やディスプレイへの実提示数ではなく、`CADisplayLink.timestamp` のコールバック頻度です。直近 $n$ 個のタイムスタンプを使い、次の移動窓で計算します。

$$
FPS=\frac{n-1}{t_{last}-t_{first}}
$$

個々のフレーム時間は連続するタイムスタンプの差です。目標FPSを $F$、設定した許容値を $\delta$ msとすると、次の条件を満たすコールバックをdropとして数えます。

$$
\Delta t_{ms}>\frac{1000}{F}+\delta
$$

既定値は移動平均60フレーム、drop許容値2msです。CSVにはフレーム番号、時刻、目標FPS、移動窓FPS、フレーム時間、drop判定を保存します。

## 目標FPSとProMotion

アプリは `MTKView.preferredFramesPerSecond` と `CADisplayLink.preferredFrameRateRange` に同じ目標値を設定します。ただし、これはOSへの要求であり、熱状態、省電力モード、画面内容、端末の対応リフレッシュレート、他の負荷によって実際のコールバック周期は変わります。

プリセットは30 / 60 / 90 / 120 / 144 FPSのうち、`UIScreen.main.maximumFramesPerSecond` 以下の値だけを表示します。現在のUIには80 FPSプリセットはありません。60Hz端末では90/120Hzの検証はできません。

## 再現性のある検証手順

1. 実機で低電力モードを無効にし、端末温度を安定させます。
2. 目標FPS、移動平均窓、drop許容値を記録します。
3. 測定開始直後のウォームアップ区間を分け、同じ時間だけ複数回測ります。
4. 平均FPSだけでなく、フレーム時間とdrop数をCSVで比較します。
5. 外部カメラや別計測器と比較する場合は、表示更新の実測と`CADisplayLink`測定が別の指標であることを明記します。

Simulatorのコールバック周期はMacのディスプレイ、負荷、仮想化の影響を受けるため、端末のframe pacing検証には使わないでください。最終結果は実機で確認してください。

## ビルド

`FPSValidator/FPSValidator.xcodeproj` をXcodeで開き、iOS実機ターゲットでビルドします。

## ライセンス

このリポジトリには現在、再利用を許諾するライセンスを設定していません。
