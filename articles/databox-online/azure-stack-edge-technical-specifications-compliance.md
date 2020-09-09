---
title: Microsoft Azure Stack Edge の技術仕様とコンプライアンス | Microsoft Docs
description: Azure Stack Edge の技術仕様とコンプライアンスについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: 5a4ac2342ca36d83d1a579851c090c4713814c8d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652042"
---
# <a name="azure-stack-edge-technical-specifications"></a>Azure Stack Edge の技術仕様

Microsoft Azure Stack Edge デバイスのハードウェア コンポーネントは、この記事で説明されている技術的仕様と規制標準に準拠します。 技術仕様では、電源装置 (PSU)、ストレージ容量、エンクロージャ、環境基準について説明しています。

## <a name="compute-memory-specifications"></a>コンピューティング、メモリの仕様

Azure Stack Edge デバイスには、コンピューティングとメモリについて次のような仕様があります。

| 仕様           | 値                  |
|-------------------------|----------------------------|
| CPU    | 2 X 10 コア CPU                     |
| メモリ              | 128 GB RAM                  |

## <a name="fpga-specifications"></a>FPGA の仕様

Machine Learning (ML) のシナリオが可能なすべての Azure Stack Edge デバイスには、Field Programmable Gate Array (FPGA) が含まれています。

| 仕様           | 値                  |
|-------------------------|----------------------------|
| FPGA   | Intel Arria 10 <br> 利用可能なディープ ニューラル ネットワーク (DNN) モデルは、[クラウド FPGA インスタンスでサポートされる](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)ものと同じです。|

## <a name="power-supply-unit-specifications"></a>電源装置の仕様

Azure Stack Edge デバイスは 100-240 V の電源装置 (PSU) 2 つと高性能ファンを備えています。 2 つの PSU によって電源が冗長構成になります。 1 台の PSU に障害が発生した場合、障害が発生したモジュールが交換されるまで、デバイスはもう一方の PSU で通常どおり動作し続けます。 次の表は PSU の技術仕様をまとめたものです。

| 仕様           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大出力電力    | 750 W                     |
| 頻度               | 50/60 Hz                   |
| 選択電圧範囲 | オート レンジ: 100-240 V AC |
| ホット プラグ可能           | はい                        |

### <a name="azure-stack-edge-power-cord-specifications-by-region"></a>Azure Stack Edge 電源コードのリージョン別仕様

Azure Stack Edge デバイスに必要な電源コードは、Azure リージョンに応じて異なります。
サポートされているすべての電源コードの技術的仕様については、[Azure Stack Edge 電源コードのリージョン別仕様](azure-stack-edge-technical-specifications-power-cords-regional.md)に関するページをご覧ください。

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Azure Stack Edge device.-->

## <a name="network-interface-specifications"></a>ネットワーク インターフェイスの仕様

Azure Stack Edge デバイスには、PORT1 - PORT6 という 6 つのネットワーク インターフェイスがあります。

| 仕様           | 説明                 |
|-------------------------|----------------------------|
|  ネットワーク インターフェイス    | 1 GbE インターフェイス x 2 - 1 つは管理用で、ユーザー構成不可であり、初期セットアップに使用されます。 他のインターフェイスはユーザー構成可能であり、データ転送に使用でき、既定で DHCP です。 <br>25 GbE インターフェイス x 2 – これらは 10 GbE インターフェイスとしても動作可能です。 これらのデータ インターフェイスは、DHCP (既定) または静的 としてユーザーが構成できます。 <br> 25 GbE インターフェイス x 2 – これらのデータ インターフェイスは、DHCP (既定) または静的 としてユーザーが構成できます。                  |

## <a name="storage-specifications"></a>Storage の仕様

Azure Stack Edge デバイスには、それぞれ 1.6 TB の容量を持つ 9 X 2.5 インチ NVMe SSD が搭載されています。 この SSD のうち、1 つがオペレーティング システム ディスクで、残りの 8 つがデータ ディスクです。 デバイスで利用できる容量の合計は約 12.5 TB となります。 次の表は、デバイスのストレージ容量の詳細です。

|     仕様                          |     値             |
|--------------------------------------------|-----------------------|
|    ソリッド ステート ドライブ (SSD) の数     |    8                  |
|    単一の SSD 容量                     |    1.6 TB             |
|    合計容量                          |    12.8 TB            |
|    合計の使用可能な容量*                  |    12.5 TB 以下            |

**容量の一部は内部仕様のために予約されています。*

## <a name="enclosure-dimensions-and-weight-specifications"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法

次の表に、エンクロージャの寸法を示します (mm およびインチ単位)。

|     エンクロージャ     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    44.45            |    1.75"          |
|    幅          |    434.1           |    17.09"          |
|    長さ          |    740.4           |    29.15"          |

次の表に、発送パッケージの寸法を示します (mm およびインチ単位)。

|     Package     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    311.2            |    12.25"          |
|    幅          |    642.8          |    25.31"          |
|    長さ          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>エンクロージャの重量

デバイス パッケージの重量は 61 ポンド (約 27 kg) であり、 取り扱いには 2 人必要です。 デバイスの重量は、エンクロージャの構成によって変わります。

|     エンクロージャ                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    合計重量には梱包が含まれます       |    61 lbs.          |
|    デバイスの重量                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>エンクロージャの環境仕様

ここでは、温度、湿度、高度など、エンクロージャの環境に関連する仕様を示します。

### <a name="temperature-and-humidity"></a>温度と湿度

|     エンクロージャ         |     周辺温度の範囲     |     相対周辺湿度     |     最大露点     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    運用時        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% (結露なし)         |    29°C (84°F)            |
|    非運用時    |    -40°C - 65°C (-40°F - 149°F)     |    5% - 95% (結露なし)          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>エアフロー、高度、衝撃、振動、定位、安全性、EMC

|     エンクロージャ                           |     運用上の仕様                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    エアフロー                              |    システムのエアフローは前面から背面方向です。 システムは低圧の背面排気環境で運用する必要があります。 <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    最大高度 (運用時)        |    3048 メートル (10,000 フィート)、ディレーティング時の最大動作温度は[動作温度のディレーティング仕様](#operating-temperature-de-rating-specifications)によって決定されます。                                                                                |
|    最大高度 (非運用時)    |    12,000 メーター (39,370 フィート)                                                                                                                                                                                         |
|    衝撃 (運用時)                   |    6 つの方向で 11 ミリ秒間 6 G                                                                                                                                                                         |
|    衝撃 (非運用時)               |    6 つの方向で 2 ミリ秒間 71 G                                                                                                                                                                           |
|    振動 (運用時)               |    0.26 G<sub>RMS</sub> 5 Hz - 350 Hz ランダム                                                                                                                                                                                     |
|    振動 (非運用時)           |    15 分間 1.88 G<sub>RMS</sub> 10 Hz - 500 Hz (6 面すべてをテスト済み)                                                                                                                                                  |
|    定位と取り付け             |    19" ラック マウント                                                                                                                                                                                        |
|    安全性と認可                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A、ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    エネルギー             |    委員会規則 (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |

### <a name="operating-temperature-de-rating-specifications"></a>動作温度のディレーティング仕様

|     動作温度のディレーティング     |     周辺温度の範囲                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    最大 35°C (95°F)                       |    最大温度は、950 m (3,117 ft) より高いところでは、1°C/300 m (1°F/547 ft) の割合で下がります。    |
|    35°C - 40°C (95°F - 104°F)            |    最大温度は、950 m (3,117 ft) より高いところでは、1°C/175 m (1°F/319 ft) の割合で下がります。    |
|    40°C - 45°C (104°F - 113°F)           |    最大温度は、950 m (3,117 ft) より高いところでは、1°C/125 m (1°F/228 ft) の割合で下がります。    |

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge を配置する](azure-stack-edge-deploy-prep.md)
