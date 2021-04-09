---
title: GPU を備えた Microsoft Azure Stack Edge Pro の技術仕様とコンプライアンス | Microsoft Docs
description: GPU を備えた Azure Stack Edge Pro デバイスの技術仕様とコンプライアンスについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 937f0d1feb0c45c1c158b5e88daf268aeb383509
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034746"
---
# <a name="technical-specifications-and-compliance-for-azure-stack-edge-pro-with-gpu"></a>GPU を備えた Azure Stack Edge Pro の技術仕様とコンプライアンス 

オンボードのグラフィックス プロセッシング ユニット (GPU) を備えた Azure Stack Edge Pro のハードウェア コンポーネントは、この記事で説明されている技術的仕様と規制標準に準拠します。 技術仕様では、ハードウェア、電源装置 (PSU)、ストレージ容量、エンクロージャ、環境基準について説明しています。

## <a name="compute-and-memory-specifications"></a>コンピューティングとメモリの仕様

Azure Stack Edge Pro デバイスには、コンピューティングとメモリについて次のような仕様があります。

| 仕様           | 値                  |
|-------------------------|----------------------------|
| CPU                     | 2 X Intel Xeon Silver 4214 (Cascade Lake) CPU<br> 24 個の物理コア (CPU あたり 12 個)<br>48 個の論理コア (vCPU) (CPU あたり 24 個)       |
| メモリ                  | 128 (8x16 GB) GB RAM <br> Dell 互換 16 GB PC4-23400 DDR4-2933Mhz 2Rx8 1.2v ECC 登録済み RDIMM       |


## <a name="compute-acceleration-specifications"></a>コンピューティング アクセラレーション仕様

Kubernetes、ディープ ラーニング、機械学習のシナリオを可能にするすべての Azure Stack Edge Pro デバイスには、グラフィックス プロセッシング ユニット (GPU) が含まれています。

| 仕様           | 値                  |
|-------------------------|----------------------------|
| GPU   | 1 つまたは 2 つの nVidia T4 GPU <br> 詳細については、「[NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)」を参照してください。| 


## <a name="power-supply-unit-specifications"></a>電源装置の仕様

Azure Stack Edge Pro デバイスは 100-240 V の電源装置 (PSU) 2 つと高性能ファンを備えています。 2 つの PSU によって電源が冗長構成になります。 1 台の PSU に障害が発生した場合、障害が発生したモジュールが交換されるまで、デバイスはもう一方の PSU で通常どおり動作し続けます。 次の表は PSU の技術仕様をまとめたものです。

| 仕様           | 750 W PSU                  |
|-------------------------|----------------------------|
| 最大出力電力    | 750 W                     |
| 頻度               | 50/60 Hz                   |
| 選択電圧範囲 | オート レンジ: 100-240 V AC |
| ホット プラグ可能           | はい                        |


## <a name="network-interface-specifications"></a>ネットワーク インターフェイスの仕様

Azure Stack Edge Pro デバイスには、PORT1 から PORT6 までの 6 つのネットワーク インターフェイスがあります。

| 仕様           | 説明                 |
|-------------------------|----------------------------|
|  ネットワーク インターフェイス    | **1 GbE インターフェイス x 2** - 1 つの管理インターフェイスのポート 1 は初期セットアップに使用され、既定では静的です。 初期セットアップが完了したら、任意の IP アドレスでデータ用のインターフェイスを使用できます。 ただし、リセット時には、インターフェイスは静的 IP に戻ります。 <br>別のインターフェイスのポート 2 はユーザー構成可能であり、データ転送に使用でき、既定で DHCP です。 <br>**25 GbE インターフェイス x 4** – これらのデータ インターフェイス (ポート 3 からポート 6) は、DHCP (既定) または静的 としてユーザーが構成できます。 また、これらは 10 GbE インターフェイスとしても動作可能です。  | 

Azure Stack Edge Pro デバイスには、次のネットワーク ハードウェアがあります。

* **カスタム Microsoft Qlogic Cavium 25G NDC アダプター** - ポート 1 からポート 4。
* **Mellanox デュアル ポート 25G ConnectX-4 チャネル ネットワーク アダプター** - ポート 5 とポート 6。

Mellanox カードの詳細を次に示します。

| パラメーター           | 説明                 |
|-------------------------|----------------------------|
| モデル    | ConnectX®-4 Lx EN ネットワーク インターフェイス カード                      |
| モデルの説明               | 25 GbE デュアルポート SFP28、PCIe3.0 x8、ROHS R6                    |
| デバイスの部品番号 (R640) | MCX4121A-ACAT  |
| PSID (R640)           | MT_2420110034                         |

これらのネットワーク カードでサポートされているケーブル、スイッチ、およびトランシーバーの完全な一覧については、以下を参照してください。

- [Qlogic Cavium 25G NDC アダプターの相互運用性マトリックス](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)。
- [Mellanox デュアル ポート 25G ConnectX-4 チャネル ネットワーク アダプター互換製品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。  

## <a name="storage-specifications"></a>Storage の仕様

Azure Stack Edge Pro デバイスには、それぞれ 1.6 TB の容量を持つ 5 つの 2.5 インチ NVMe DC P4610 SSD が搭載されています。 ブート ドライブは 240 GB の SATA SSD です。 デバイスで利用できる容量の合計は約 4.19 TB です。 次の表は、デバイスのストレージ容量の一覧です。

|     仕様                          |     値             |
|--------------------------------------------|-----------------------|
|    NVMe SSD の数                     |    5                  |
|    単一の NVMe SSD 容量                |    1.6 TB             |
|    ブート SATA ソリッドステート ドライブ (SSD)      |    1                  |
|    ブート SSD 容量                       |    240 GB             |
|    合計容量                          |    8.0 TB             |
|    使用可能な合計容量                   |    約 4.19 TB          |
|    RAID 構成                      |    ミラーリングとパリティを組み合わせた記憶域スペース ダイレクト  |
|    SAS コントローラー                          |    HBA330 12 Gbps     |

<!--Remove based on feedback from Ravi
## Other hardware specifications

Your Azure Stack Edge Pro device also contains the following hardware:

* iDRAC baseboard management
* Performance fans
* Custom ID module-->

## <a name="enclosure-dimensions-and-weight-specifications"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法

次の表に、1U デバイス エンクロージャの寸法を示します (mm およびインチ単位)。

|     エンクロージャ     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    44.45            |    1.75"       |
|    幅          |    434.1            |    17.09"      |
|    長さ         |    740.4            |    29.15"      |

次の表に、発送パッケージの寸法を示します (mm およびインチ単位)。

|     Package     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    311.2            |    12.25"          |
|    幅          |    642.8            |    25.31"          |
|    長さ         |    1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>エンクロージャの重量

デバイス パッケージの重量は 66 lbs. であり、 取り扱いには 2 人必要です。 デバイスの重量は、エンクロージャの構成によって変わります。

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
| イングレス プロテクション (IP)                 |    このような種類の室内用ラック マウント装置は、通常、イングレス プロテクション (電撃防止エンクロージャに対する固体および液体からの保護) のテストは実施されません。 製造元の安全性評価では、IPXO (イングレス プロテクションなし) と示されます。  |
|    最大高度 (運用時)        |    3,048 メートル (10,000 フィート)。ディレーティング時の最大動作温度は、[動作温度のディレーティング仕様](#operating-temperature-de-rating-specifications)によって決定します。                                                                                |
|    最大高度 (非運用時)    |    12,000 メーター (39,370 フィート)                                                                                                                                                                                         |
|    衝撃 (運用時)                   |    6 つの方向で 11 ミリ秒間 6 G                                                                                                                                                                         |
|    衝撃 (非運用時)               |    6 つの方向で 2 ミリ秒間 71 G                                                                                                                                                                           |
|    振動 (運用時)               |    0.26 G<sub>RMS</sub> 5 Hz - 350 Hz ランダム                                                                                                                                                                                     |
|    振動 (非運用時)           |    15 分間 1.88 G<sub>RMS</sub> 10 Hz - 500 Hz (6 面すべてをテスト済み)                                                                                                                                                  |
|    定位と取り付け             |    標準の 19 インチ ラック マウント (1U)                                                                                                                                                                                       |
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

[Azure Stack Edge Pro をデプロイする](azure-stack-edge-gpu-deploy-prep.md)
