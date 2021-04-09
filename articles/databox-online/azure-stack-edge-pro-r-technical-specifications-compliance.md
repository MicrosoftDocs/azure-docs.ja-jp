---
title: Microsoft Azure Stack Edge Pro R の技術仕様とコンプライアンス | Microsoft Docs
description: Azure Stack Edge Pro R デバイスの技術仕様とコンプライアンスについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96465605"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R の技術仕様

Azure Stack Edge Pro R デバイスのハードウェア コンポーネントは、この記事で説明されている技術的仕様に準拠します。 技術仕様では、電源装置 (PSU)、ストレージ容量、エンクロージャ、環境基準について説明しています。


## <a name="compute-memory-specifications"></a>コンピューティング、メモリの仕様

Azure Stack Edge Pro R デバイスには、コンピューティングとメモリについて次のような仕様があります。

| 仕様       | 値                  |
|---------------------|------------------------|
| CPU    | 2 X 10 コア CPU Intel Xeon Silver 4114 |
| メモリ              | 256 GB RAM (2,666 MT/秒)     |


## <a name="compute-acceleration-specifications"></a>コンピューティング アクセラレーション仕様

Kubernetes、ディープ ラーニング、機械学習のシナリオを可能にするすべてのデバイスには、グラフィックス プロセッシング ユニット (GPU) が搭載されています。

| 仕様           | 値                  |
|-------------------------|----------------------------|
| GPU   | nVidia T4 GPU x 1 <br> 詳細については、「[NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)」を参照してください。| 

## <a name="power-supply-unit-specifications"></a>電源装置の仕様

Azure Stack Edge Pro R デバイスは 100-240 V の電源装置 (PSU) 2 つと高性能ファンを備えています。 2 つの PSU によって電源が冗長構成になります。 1 台の PSU に障害が発生した場合、障害が発生したモジュールが交換されるまで、デバイスはもう一方の PSU で通常どおり動作し続けます。 次の表は PSU の技術仕様をまとめたものです。

| 仕様           | 550 W PSU                  |
|-------------------------|----------------------------|
| 最大出力電力    | 550 W                      |
| 放熱量 (最大)                   | 2891 BTU/時                |
| 頻度               | 50/60 Hz                   |
| 選択電圧範囲 | オート レンジ: 115-230 V AC |
| ホット プラグ可能           | はい                        |

## <a name="network-specifications"></a>ネットワーク仕様

Azure Stack Edge Pro R デバイスには、PORT1 から PORT4 までの 4 つのネットワーク インターフェイスがあります。 


|仕様  |説明                              |
|----------------------|----------------------------------|
|ネットワーク インターフェイス    |**2 x 1 Gbe RJ45** <br> ポート 1 は、初期セットアップの管理インターフェイスとして使用され、既定では静的です。 初期セットアップが完了したら、任意の IP アドレスでデータ用のインターフェイスを使用できます。 ただし、リセット時には、インターフェイスは静的 IP に戻ります。 <br>別のインターフェイスのポート 2 はユーザー構成可能であり、データ転送に使用でき、既定で DHCP です。     |
|ネットワーク インターフェイス    |**2 x 25 Gbe SFP28** <br> これらのデータ インターフェイス ポート 3 とポート 4 は、DHCP (既定) または静的 として構成できます。            |

Azure Stack Edge Pro R デバイスには、次のネットワーク ハードウェアがあります。

* **Mellanox デュアル ポート 25G ConnectX-4 チャネル ネットワーク アダプター** - ポート 3 とポート 4。 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

これらのネットワーク カードでサポートされているケーブル、スイッチ、およびトランシーバーの完全な一覧については、以下を参照してください。[Mellanox デュアル ポート 25G ConnectX-4 チャネル ネットワーク アダプター互換製品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。

## <a name="storage-specifications"></a>Storage の仕様

Azure Stack Edge Pro R デバイスには、8 つのデータ ディスクと 2 M.2 SATA ディスクが搭載されており、オペレーティング システム ディスクとして機能します。 詳細については、[M.2 SATA ディスク](https://en.wikipedia.org/wiki/M.2)に関するページを参照してください。

#### <a name="storage-for-1-node-device"></a>1 ノード デバイスのストレージ

次の表は、1 ノード デバイスのストレージ容量の詳細を示しています。

|     仕様                          |     値             |
|--------------------------------------------|-----------------------|
|    ソリッド ステート ドライブ (SSD) の数     |    8                  |
|    単一の SSD 容量                     |    8 TB               |
|    合計容量                          |    64 TB              |
|    合計の使用可能な容量*                  |    最大 42 TB          |

**容量の一部は内部仕様のために予約されています。*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法 

次の表に、デバイスおよび堅牢なケースを含む UPS の寸法を示します (mm およびインチ単位)。

|     エンクロージャ     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    301.2            |    11.86       |
|    幅          |    604.5            |    23.80       |
|    長さ         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>エンクロージャの重量 

デバイスの重量は、エンクロージャの構成によって変わります。

|     エンクロージャ                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    1 ノード デバイスとエンド キャップ付きの堅牢なケースの重さの合計     |    最大 114 ポンド。          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>エンクロージャの環境仕様

このセクションでは、温度、振動、衝撃、高度など、エンクロージャの環境に関連する仕様を示します。


|     仕様              |     値    |
|--------------------------------|-------------------------------------------------------------------|
|     温度範囲          |     0 – 43° C (運用時)    |
|     振動                  |     MIL-STD-810 Method 514.7*<br>Procedure I CAT 4, 20                  |
|     衝撃                      |     MIL-STD-810 Method 516.7*<br>Procedure IV, Logistic                 |
|     高度                   |     運用時: 10,000 フィート<br>非運用時:40,000 フィート          |

"**すべての参照は MIL-STD-810G Change 1 (2014) です*"

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge を配置する](azure-stack-edge-placeholder.md)
