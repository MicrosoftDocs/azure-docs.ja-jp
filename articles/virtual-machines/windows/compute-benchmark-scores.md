---
title: Azure Windows VM のコンピューティング ベンチマーク スコア | Microsoft Docs
description: Windows Server を実行する Azure VM の SPECint コンピューティング ベンチマーク スコアを比較します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: cynthn;davberg
ms.openlocfilehash: 43d0ab6552847df7f1f2a8599dcc7cb9a8fcb57b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698831"
---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Windows VM のコンピューティング ベンチマーク スコア
次の SPECInt ベンチマーク スコアは、Windows Server を実行している高いパフォーマンスの Azure の VM ラインアップについてのコンピューティング パフォーマンスを示します。 コンピューティング ベンチマーク スコアは [Linux VM](../linux/compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にも利用できます。

> [!NOTE]
> Linux に関する値は最近更新され、より包括的な VM のセットが含まれるようになっています。

## <a name="a-series---compute-intensive"></a>A シリーズ - コンピューティング集中型
| Size | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 \@ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2 シリーズ
| Size | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |130.5 |4.4. |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 \@ 2.4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G シリーズ、GS シリーズ
| Size | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1、Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |31 |71.8 |6.5 |
| Standard_G2、Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |5 |133.4 |13.0 |
| Standard_G3、Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |6 |242.3 |6.0 |
| Standard_G4、Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 \@ 2 GHz |15 |398.9 |6.0 |
| Standard_G5、Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 \@ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>H シリーズ
| Size | vCPU 数 | NUMA ノード数 | CPU | 実行 | 平均のベース レート  | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 \@ 3.2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>SPECint について
Windows の数値は、Windows Server 上で [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) を実行して計算しました。 SPECint は、vCPU あたり 1 つのコピーを使用し、ベース レート オプション (SPECint_rate2006) を使用して実行しました。 SPECint は 12 個の個別のテストで構成され、それぞれを 3 回実行します。各テストの中央値を採用し、重み付けして複合スコアを割り出します。 これらのテストが複数の VM にわたって実行され、ご覧の平均スコアが算出されました。

## <a name="next-steps"></a>次の手順
* ストレージの容量、ディスクの詳細、VM のサイズを選択する際のその他の考慮事項については、 [仮想マシンのサイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

