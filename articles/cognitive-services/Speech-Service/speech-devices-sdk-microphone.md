---
title: Speech Devices SDK のマイク配列の推奨事項
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK のマイク配列の推奨事項。 Microsoft Audio Stack での使用には、次の配列のジオメトリをお勧めします。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015343"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Speech Devices SDK のマイク配列の推奨事項

この記事では、Speech Devices SDK のマイク配列を設計する方法について説明します。

Speech Devices SDK は、マイクのジオメトリとコンポーネントの選択を含む、次のガイドラインに従って設計されたマイク配列で最適に動作します。 ガイダンスは、統合と電気的な考慮事項でも提供されます。

## <a name="microphone-geometry"></a>マイクのジオメトリ

Microsoft Audio Stack で使用するために、次の配列のジオメトリをお勧めします。 音源の場所と周囲のノイズの除去は、特定のアプリケーションへの依存関係を持つマイクの数の増加、ユーザー シナリオ、およびデバイスのフォーム ファクターにより向上します。

| マイクとジオメトリ | 循環配列 | 循環配列 | 線形配列 | 線形配列 |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# マイクの数 | 7 | 4 | 4 | 2 |
| ジオメトリ | 外側に 6、中央に 1、半径 42.5 mm、等間隔 | 外側に 3、中央に 1、半径 42.5 mm、等間隔 | 長さ = 120 mm、間隔 = 40 mm | 間隔 = 40 mm |

マイクのチャネルは、上の各配列に示されている番号に従って、0 から増加するように並べ替えるようにします。 Microsoft Audio Stack でエコー キャンセルを実行するには、オーディオ再生の追加の参照ストリームが必要です。

## <a name="component-selection"></a>コンポーネントの選択

ノイズやひずみのない信号を正確に再現するマイク コンポーネントを選択する必要があります。

マイクを選択する際に推奨されるプロパティは次のとおりです。

| パラメーター | 推奨 |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz 信号 94 dBSPL、A で加重されたノイズ) |
| Amplitude Matching (振幅整合) | ± 1 dB @ 1 kHz |
| Phase Matching (位相整合) | ± 2° @ 1 kHz |
| Acoustic Overload Point (AOP: アコースティック オーバーロード ポイント) | \>= 120 dBSPL (全高調波歪み率 = 10%) |
| Bit Rate (ビット レート) | 最小 24 ビット |
| サンプリング レート | 最小 16 kHz\* |
| Frequency Response (周波数応答) | ± 3 dB、200 - 8000 Hz フローティング マスク\* |
| [信頼性] | 保存温度範囲 -40°C から 70°C<br />動作温度範囲 -20°C から 55°C |

\*_高品質なコミュニケーション (VoIP) アプリケーションには、より高いサンプリング レート、または "より広い" 周波数範囲が必要になる場合があります。_

使用するコンポーネントのパフォーマンス低下を回避するには、適切なコンポーネントの選択が、優れた電子音響の統合と組み合わされている必要があります。 固有のユース ケースには、必然的に追加の要件 (動作温度範囲など) も生じます。

## <a name="microphone-array-integration"></a>マイク配列の統合

デバイスに組み込まれたときのマイク配列のパフォーマンスは、コンポーネントの仕様とは異なります。 統合後にマイクが確実に一致するようにすることが重要です。 そのため、固定ゲインまたは EQ の後に測定されるデバイスのパフォーマンスは、次の推奨事項を満たす必要があります。

| パラメーター          | 推奨                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz 信号 94 dBSPL、A で加重されたノイズ) |
| Output Sensitivity (出力感度) | -26 dBFS/Pa @ 1 kHz (推奨)                  |
| Amplitude Matching (振幅整合) | ± 2 dB、200-8000 Hz                                |
| THD % (全高調波歪み率)\*             | ≤ 1%、200 - 8000 Hz、94 dBSPL、5 番目             |
| Frequency Response (周波数応答) | ± 6 dB、200 - 8000 Hz フローティング マスク\*\*              |

\*\*_THD を測定するには低ひずみのスピーカーが必要です (例: Neumann KH120)_

\*\*_高品質なコミュニケーション (VoIP) アプリケーションには、"より広い" 周波数範囲が必要になる場合があります_

## <a name="speaker-integration-recommendations"></a>スピーカーの統合に関する推奨事項

エコー キャンセレーションは、スピーカーが含まれている音声認識デバイスに必要なため、スピーカーの選択と統合に対して追加の推奨事項が提供されます。

| パラメーター | 推奨 |
| --------- | ----------- |
| Linearity Considerations (線形性に関する考慮事項) | スピーカー参照の後に非線形の処理はなし。それ以外の場合は、ハードウェア ベースのループバック参照ストリームが必要 |
| Speaker Loopback (スピーカー ループバック) | WASAPI、プライベート API、カスタム ALSA プラグイン (Linux) を通じて、またはファームウェア チャネル経由で提供 |
| THD % (全高調波歪み率) | 第 3 オクターブ バンド最小 5 番目、70 dBA 再生 @ 0.8 m ≤ 6.3%、315-500 Hz ≤ 5%、630-5000 Hz |
| Echo Coupling to Microphones (マイクへのエコー カップリング) | \> -10 dB TCLw (ITU-T G.122 Annex B.4 メソッドを使用)、マイク レベルに正規化<br />TCLw = TCLwmeasured \+ (測定されたレベル - ターゲット出力感度)<br />TCLw = TCLwmeasured \+ (測定されたレベル - (-26)) |

## <a name="integration-design-architecture"></a>設計アーキテクチャの統合

次のアーキテクチャのガイドラインは、マイクをデバイスに統合するときに必要です。

| パラメーター | 推奨 |
| --------- | -------------- |
| Mic Port Similarity (マイク ポートの類似性) | 配列内のすべてのマイク ポートを同じ長さにする |
| Mic Port Dimensions (Mic ポートの寸法) | ポート サイズ Ø0.8 1.0 mm。 ポートの長さ/ポートの直径 \< 2 |
| Mic Sealing (マイクのシーリング)         | ガスケットのシーリングは積み重ねて均一に行う。 フォーム ガスケットに対して \> 70% の圧縮率を推奨 |
| Mic Reliability (マイクの信頼性)     | ほこりと侵入を防止するためにメッシュを使用 (下部ポートのマイク用の PCB とシーリング ガスケット/上部カバーの間) |
| Mic Isolation (マイクの分離)       | 特に統合したスピーカーによる任意の振動パスを分離するためのゴム ガスケットおよび構造による振動分離 |
| Sampling Clock (サンプリング クロック)      | オーディオ デバイスに、低ドリフトによるジッターとドロップ アウトがないこと |
| Record Capability (記録機能)   | デバイスが別々のチャネルの未加工のストリームを同時に記録できること |
| USB                 | すべての USB オーディオ入力デバイスは、[USB Audio Devices Rev3 の仕様](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement)に従って記述子を設定する必要がある |
| Microphone Geometry (マイクのジオメトリ) | ドライバーは[マイク配列ジオメトリ記述子](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry)を正しく実装する必要がある |
| Discoverability (探索可能性)     | デバイスには、検出できないまたは制御不能なハードウェア、ファームウェア、またはサード パーティ製のソフトウェア ベースのデバイス間の非線形オーディオ処理アルゴリズムを含めない |
| Capture Format (キャプチャ形式)      | キャプチャ形式では、16 kHz の最小サンプリング レートと推奨される 24 ビット深度を使用する必要がある |

## <a name="electrical-architecture-considerations"></a>電気アーキテクチャに関する考慮事項

必要に応じて、配列を USB ホスト (Microsoft Audio Stack を実行している SoC など) と音声サービスやその他のアプリケーションへのインターフェイスに接続することができます。

PDM-to-TDM 変換などのハードウェア コンポーネントは、マイクのダイナミック レンジと SNR が再サンプラー内で確実に保持されるようにする必要があります。

最大 7 つのチャネルに高いサンプル レートとビット深度で必要な帯域幅を提供するためには、任意のオーディオ MCU 内で高速な USB Audio Class 2.0 がサポートされている必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Devices SDK についてさらに詳しく学習する](speech-devices-sdk.md)