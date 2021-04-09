---
title: StorSimple の技術仕様 | Microsoft Docs
description: StorSimple ハードウェア コンポーネントの技術仕様と規制標準対応情報について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68965283"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>StorSimple デバイスの技術的仕様とコンプライアンス

## <a name="overview"></a>概要

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Microsoft Azure StorSimple デバイスのハードウェア コンポーネントは、この記事で説明されている技術的仕様と規制標準に準拠します。 技術的仕様では、電源と冷却モジュール (PCM)、ディスク ドライブ、記憶域容量、エンクロージャについて説明します。 対応情報では、国際標準、安全性と排出量、ケーブルなどについて説明します。

## <a name="power-and-cooling-module-specifications"></a>電源および冷却モジュールの仕様

StorSimple デバイスには、100-240 V デュアル ファンの SBB 対応電源冷却モジュール (PCM) が 2 台搭載されています。 そのため、電源が冗長構成になります。 1 台の PCM に障害が発生した場合、障害が発生したモジュールが交換されるまで、デバイスはもう一方の PCM で通常どおり動作し続けます。

EBOD エンクロージャは 580 W PCM を使用し、主エンクロージャは 764 W PCM を使用します。 次の表に、PCM に関連する技術的仕様を示します。

| 仕様 | 580 W PCM (EBOD) | 764 W PCM (主) |
| --- | --- | --- |
| 最大出力電力 |580 W |764 |
| 頻度 |50/60 Hz |50/60 Hz |
| 選択電圧範囲 |オート レンジ: 90 ～ 264 V AC、47/63 Hz |オート レンジ: 90 ～ 264 V AC、47/63 Hz |
| 最大突入電流 |20 A |20 A |
| 力率補正 |>95% の公称入力電圧 |>95% の公称入力電圧 |
| 高調波 |EN61000-3-2 に準拠 |EN61000-3-2 に準拠 |
| 出力 |5V スタンバイ電圧 \@ 2.0 A |5V スタンバイ電圧 \@ 2.7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| ホット プラグ可能 |はい |はい |
| スイッチと LED |AC オン/オフ スイッチと 4 つのステータス インジケーター LED |AC オン/オフ スイッチと 6 つのステータス インジケーター LED |
| エンクロージャの冷却 |軸流冷却ファン (可変ファン速度制御) |軸流冷却ファン (可変ファン速度制御) |

## <a name="power-consumption-statistics"></a>消費電力の統計

次の表に、StorSimple デバイスの各モデルの一般的な消費電力データ (実際の値は公開値と異なる場合があります) を示します。

| 条件 | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  ファンは低速、ドライブはアイドル状態 |1.45 A |0.31 kW |1057.76 BTU/時 |3.19 A |0.34 kW |1160.13 BTU/時 |
|  ファンは低速、ドライブはアクセス中 |1.54 A |0.33 kW |1126.01 BTU/時 |3.27 A |0.36 kW |1228.37 BTU/時 |
|  ファンは高速、ドライブはアイドル状態、2 台の PSU が稼働 |2.14 A |0.49 kW |1671.95 BTU/時 |4.99 A |0.54 kW |1842.56 BTU/時 |
|  ファンは高速、ドライブはアイドル状態、PSU は 1 台が稼働し 1 台がアイドル状態 |2.05 A |0.48 kW |1637.83 BTU/時 |4.58 A |0.50 kW |1706.07 BTU/時 |
|  ファンは高速、ドライブはアクセス中、2 台の PSU が稼働 |2.26 A |0.51 kW |1740.19 BTU/時 |4.95 A |0.54 kW |1842.56 BTU/時 |
|  ファンは高速、ドライブはアクセス中、PSU は 1 台が稼働し 1 台がアイドル状態 |2.14 A |0.49 kW |1671.95 BTU/時 |4.81 A |0.53 kW |1808.44 BTU/時 |

## <a name="disk-drive-specifications"></a>ディスク ドライブの仕様

StorSimple デバイスでは 3.5 インチのフォーム ファクターの Serial Attached SCSI (SAS) ディスク ドライブが 12 台までサポートされます。 実際のドライブは、製品の構成に応じてソリッド ステート ドライブ (SSD) とハード ディスク ドライブ (HDD) の組み合わせになる場合があります。 12 個のディスク ドライブ スロットは、エンクロージャの前面に 3 x 4 で配置されています。 EBOD エンクロージャでは別の 12 台のディスク ドライブによる追加の記憶域を利用できます。 これらはすべて HDD にする必要があります。

## <a name="storage-specifications"></a>Storage の仕様

StorSimple デバイスには、8100 と 8600 のどちらにもハード ディスク ドライブとソリッド ステート ドライブが搭載されています。 8100 と 8600 の使用可能な容量の合計は、それぞれ約 15 TB と 38 TB です。 次の表は、StorSimple ソリューション容量のコンテキストの SSD、HDD、クラウド容量の詳細について説明した一覧です。

| デバイス モデル/容量 | 8100 | 8600 |
| --- | --- | --- |
| ハード ディスク ドライブ (HDD) の数 |8 |19 |
| ソリッド ステート ドライブ (SSD) の数 |4 |5 |
| 単一の HDD 容量 |4 TB |4 TB |
| 単一の SSD 容量 |400 GB |800 GB |
| 予備の容量 |4 TB |4 TB |
| 使用可能な HDD 容量 |14 TB |36 TB |
| 使用可能な SSD 容量 |800 GB |2 TB |
| 合計の使用可能な容量* |～ 15 TB |～ 38 TB |
| ソリューションの最大容量 (クラウドを含む) |200 TB |500 TB |

<sup>* </sup>- *使用可能な容量の合計には、データ、メタデータ、およびバッファーに使用できる容量が含まれています。8100 デバイスには最大 8.5 TB、より大きな 8600 デバイスには最大 22.5 TB のローカル固定ボリュームをプロビジョニングできます。詳細については、[StorSimple のローカル固定ボリューム](storsimple-8000-local-volume-faq.md)に関するページを参照してください。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法

次の表に、エンクロージャの寸法を示します (mm およびインチ単位)。

| エンクロージャ | mm | インチ |
| --- | --- | --- |
| [高さ] |87.9 |3.46 |
| 取り付け用フランジを含む幅 |483 |19.02 |
| エンクロージャ本体の幅 |443 |17.44 |
| 前面の取り付け用フランジからエンクロージャ本体末端までの奥行 |577 |22.72 |
| 操作パネルからエンクロージャ最遠端までの奥行 |630.5 |24.82 |
| 取り付け用フランジからエンクロージャ最遠端までの奥行 |603 |23.74 |

### <a name="enclosure-weight"></a>エンクロージャの重量

フル装備の主エンクロージャの重量は構成に応じて 21 ～ 33 kg になり、2 人で取り扱う必要があります。

| エンクロージャ | Weight |
| --- | --- |
| 最大重量 (構成によって異なる) |30 ～ 33 kg |
| 空 (ドライブ未搭載) |21 ～ 23 kg |

## <a name="enclosure-environment-specifications"></a>エンクロージャの環境仕様

ここでは、エンクロージャの環境に関連する仕様を示します。 温度、湿度、高度、衝撃、振動、定位、安全性、電磁両立性 (EMC) が記載されています。

### <a name="temperature-and-humidity"></a>温度と湿度

| エンクロージャ | 周辺温度の範囲 | 相対周辺湿度 | 最高湿球温度 |
| --- | --- | --- | --- |
| 運用時 |5 ～ 35°C (41 ～ 95°F) |20 ～ 80% (結露なし) |28°C (82°F) |
| 非運用時 |-40 ～ 70°C (40 ～ 158°F) |5 ～ 100% (結露なし) |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>エアフロー、高度、衝撃、振動、定位、安全性、EMC

| エンクロージャ | 運用上の仕様 |
| --- | --- |
| エアフロー |システムのエアフローは前面から背面方向です。 システムは低圧の背面排気環境で運用する必要があります。 ラックの扉と障害物によって生じる背圧が 5 パスカル (0.5 mm 水位計) を超えないようにしてください。 |
| 高度 (運用時) |-30 ～ 3,045 m (-100 ～ 10,000 フィート)、7,000 フィート以上は運用時の最高温度を 5°C 引き下げ。 |
| 高度 (非運用時) |-305 ～ 12,192 m (-1,000 ～ 40,000 フィート) |
| 衝撃 (運用時) |5g 10 ms ½ サイン |
| 衝撃 (非運用時) |30g 10 ms ½ サイン |
| 振動 (運用時) |0.21 g RMS 5 ～ 500 Hz ランダム |
| 振動 (非運用時) |1.04 g RMS 2 ～ 200 Hz ランダム |
| 振動 (移動時) |3 g 2 ～ 200 Hz サイン (正弦) |
| 定位と取り付け |19 インチ ラックに取り付け (EIA ユニット x 2) |
| ラック レール |IEC 297 に準拠している奥行が最低 700 mm (31.50 インチ) のラックに対応 |
| 安全性と認可 |CE および UL EN 61000-3、IEC 61000-3、UL 61000-3 |
| EMC |EN55022 (CISPR - A)、FCC A |

## <a name="international-standards-compliance"></a>国際標準への準拠

Microsoft Azure StorSimple デバイスは次の国際標準に準拠しています。  

* CE - EN 60950 - 1
* IEC 60950 - 1 に対する CB レポート
* UL 60950 - 1 に対する UL および cUL

## <a name="safety-compliance"></a>安全性コンプライアンス

Microsoft Azure StorSimple デバイスは次の安全性規格を満たしています。

* システム製品タイプ承認: UL、cUL、CE
* 安全性コンプライアンス: UL 60950、IEC 60950、EN 60950

## <a name="emc-compliance"></a>EMC コンプライアンス

Microsoft Azure StorSimple デバイスは次の EMC 規格を満たしています。

### <a name="emissions"></a>排出量

デバイスは、伝導および放射される排出量レベルについて EMC に準拠しています。

* 伝導性排出物規制レベル:CFR 47 Part 15B Class A、EN55022 Class A、CISPR Class A
* 放射性排出物規制レベル:CFR 47 Part 15B Class A、EN55022 Class A、CISPR Class A

### <a name="harmonics-and-flicker"></a>高調波と明滅

デバイスは EN61000-3-2/3 に準拠しています。

### <a name="immunity-limit-levels"></a>電磁波耐性限界レベル

デバイスは EN55024 に準拠しています。

## <a name="ac-power-cord-compliance"></a>AC 電源コード対応

プラグおよび電源コード アセンブリ全体は、デバイスを使用する国または地域の適切な標準を満たす必要があり、その国または地域で許容される安全性の認定を受けている必要があります。 次の表では、米国およびヨーロッパの標準を示します。

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC 電源コード - 米国 (NRTL にリストされている必要があります)

| コンポーネント | 仕様 |
| --- | --- |
| コードの種類 |SV または SVT、18 AWG 以上、3 線、2.0 m 以下 |
| プラグ |NEMA 5-15P 接地タイプ アタッチメント プラグ、定格 120 V、10 A、または IEC 320 C14、250 V、10 A |
| ソケット |IEC 320 C-13、250 V、10 A |

### <a name="ac-power-cords---europe"></a>AC 電源コード - ヨーロッパ

| コンポーネント | 仕様 |
| --- | --- |
| コードの種類 |整合、H05-VVF-3G1.0 |
| ソケット |IEC 320 C-13、250 V、10 A |

## <a name="supported-network-cables"></a>サポートされるネットワーク ケーブル

10 GbE ネットワーク インターフェイス、DATA 2、DATA 3 については、 [サポートされるネットワーク ケーブルとモジュールの一覧](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

StorSimple デバイスをデータセンターにデプロイする準備ができました。 詳細については、「 [オンプレミスの StorSimple デバイス (Update 2) のデプロイ](storsimple-8000-deployment-walkthrough-u2.md)」を参照してください。

