---
title: Microsoft Azure Stack Edge Mini R の技術仕様とコンプライアンス | Microsoft Docs
description: Azure Stack Edge Mini R デバイスの技術仕様とコンプライアンスについて説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 3a0b87f04e60fd56d543c7c7a752cd788e087c78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727483"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R の技術仕様

Microsoft Azure Stack Edge Mini R デバイスのハードウェア コンポーネントは、この記事で説明されている技術的仕様と規制標準に準拠します。 技術仕様には、CPU、メモリ、電源装置 (PSU)、ストレージ容量、エンクロージャの寸法と重量が記載されています。


## <a name="compute-memory-specifications"></a>コンピューティング、メモリの仕様

Azure Stack Edge Mini R デバイスには、コンピューティングとメモリについて次のような仕様があります。

| 仕様           | 値                  |
|-------------------------|------------------------|
| CPU    | 16 コア CPU、Intel Xeon-D 1577 |
| メモリ              | 48 GB RAM (2400 MT/s)                  |


## <a name="compute-acceleration-specifications"></a>コンピューティング アクセラレーション仕様

Vision Processing Unit (VPU) がすべての Azure Stack Edge Mini R デバイスに搭載されており、これにより Kubernetes、ディープ ニューラル ネットワーク、Computer Vision ベースのアプリケーションが使用可能になります。

| 仕様           | 値                  |
|-------------------------|------------------------|
| コンピューティング アクセラレーション カード         | Intel Movidius Myriad X VPU <br> 詳細については、「[Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)」をご覧ください。 |


## <a name="storage-specifications"></a>Storage の仕様

Azure Stack Edge Mini R デバイスには、1 つのデータ ディスクと (オペレーティング システムの記憶域として機能する) 1 つのブート ディスクがあります。 次の表にデバイスのストレージ容量の詳細を示します。

|     仕様                          |     値             |
|--------------------------------------------|-----------------------|
|    ソリッド ステート ドライブ (SSD) の数     |    2 x 1 TB ディスク <br> 1 つのデータ ディスクと 1 つのブート ディスク                  |
|    単一の SSD 容量                     |    1 TB (テラバイト)               |
|    合計容量 (データのみ)              |    1 TB (テラバイト)              |
|    合計の使用可能な容量*                  |    最大 750 GB        |

**容量の一部は内部仕様のために予約されています。*

## <a name="network-specifications"></a>ネットワーク仕様

Azure Stack Edge Mini R デバイスには、ネットワークについて次のような仕様があります。


|仕様  |値  |
|---------|---------|
|ネットワーク インターフェイス    |2 x 10 Gbe SFP+ <br> ローカル UI にはポート 3 とポート 4 として表示されます。           |
|ネットワーク インターフェイス    |2 x 1 Gbe RJ45 <br> ローカル UI にはポート 1 とポート 2 として表示されます。          |
|Wi-Fi   |802.11ac         |


## <a name="power-supply-unit-specifications"></a>電源装置の仕様

Azure Stack Edge Mini R デバイスには、電源を供給し、オンボード バッテリを充電するための外部 85 W AC アダプターが付属しています。

次の表に電源装置の仕様を示します。

| 仕様           | 値                      |
|-------------------------|----------------------------|
| 最大出力電力    | 85 W                       |
| 頻度               | 50/60 Hz                   |
| 選択電圧範囲 | オート レンジ: 100-240 V AC |



## <a name="included-battery"></a>付属のバッテリ

Azure Stack Edge Mini R デバイスには、電源装置によって充電されるオンボード バッテリも搭載されています。

追加の [2590 型バッテリ](https://www.bren-tronics.com/bt-70791ck.html)をオンボード バッテリと組み合わせて使用すると、充電の間のデバイスの使用を延長することができます。 このバッテリは、使用する国で適用される安全性、輸送、および環境のすべての規制に準拠している必要があります。


| 仕様           | 値                      |
|-------------------------|----------------------------|
| オンボード バッテリの容量 | 73 WHr                    |

## <a name="enclosure-dimensions-and-weight-specifications"></a>エンクロージャの寸法と重量

次の表に、エンクロージャの寸法と重量の各種仕様を示します。

### <a name="enclosure-dimensions"></a>エンクロージャの寸法

次の表に、ラグド ケースを含むデバイスおよび USP の寸法を示します (ミリメートルおよびインチ単位)。

|     エンクロージャ     |     mm     |     インチ     |
|-------------------|---------------------|----------------|
|    [高さ]         |    68            |    2.68          |
|    幅          |    208          |      8.19          |
|    長さ          |   259           |    10.20          |


### <a name="enclosure-weight"></a>エンクロージャの重量

次の表に、バッテリを含むデバイスの重量を示します。

|     エンクロージャ                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    デバイスの総重量     |    7 ポンド          |

## <a name="enclosure-environment-specifications"></a>エンクロージャの環境仕様


ここでは、温度、湿度、高度など、エンクロージャの環境に関連する仕様を示します。


|     仕様             |     説明                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     温度範囲          |     0 – 43° C (運用時)                                              |
|     振動                  |     MIL-STD-810 Method 514.7*<br> Procedure I CAT 4, 20                  |
|     衝撃                      |     MIL-STD-810 Method 516.7*<br> Procedure IV, Logistic                 |
|     高度                   |     運用時: 10,000 フィート<br> 非運用時:40,000 フィート          |

"**すべての参照は MIL-STD-810G Change 1 (2014) です*"


## <a name="next-steps"></a>次の手順

- [Azure Stack Edge Mini R をデプロイする](azure-stack-edge-placeholder.md)
