---
title: "Linux VM のコンピューティング ベンチマーク スコア | Microsoft Docs"
description: "Linux を実行する Azure VM の CoreMark コンピューティング ベンチマーク スコアを比較します。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 93e812c1-79dd-40c5-b97b-aa79f5cd7d76
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 0cc49ad1aac612a2704827e01c9092bef14c69f9


---
# <a name="compute-benchmark-scores-for-linux-vms"></a>Linux VM のコンピューティング ベンチマーク スコア
次の CoreMark ベンチマーク スコアは、Ubuntu を実行している、Azure の 高パフォーマンス VM ラインアップについてのコンピューティング パフォーマンスを示します。 コンピューティング ベンチマーク スコアは [Windows VM](virtual-machines-windows-compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にも利用できます。

## <a name="a-series---compute-intensive"></a>A シリーズ - コンピューティング集中型
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |179 |110,294 |554 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |189 |210,816 |2,126 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |110,025 |1,045 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |188 |210,727 |2,073 |

## <a name="dv2-series"></a>Dv2 シリーズ
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |14,852 |780 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |133 |29,467 |1,863 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |139 |56,205 |1,167 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |108,543 |3,446 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |205,332 |9,998 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |125 |28,598 |1,510 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |131 |55,673 |1,418 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |107,986 |3,089 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |140 |208,186 |8,839 |
| Standard_D15_v2 |20 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |28 |268,560 |4,667 |

## <a name="f-series"></a>F シリーズ
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |154 |15,602 |787 |
| Standard_F2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |126 |29,519 |1,233 |
| Standard_F4 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |147 |58,709 |1,227 |
| Standard_F8 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |224 |112,772 |3,006 |
| Standard_F16 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |42 |218,571 |5,113 |

## <a name="g-series"></a>G シリーズ
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |31,310 |2,891 |
| Standard_G2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |60,112 |3,537 |
| Standard_G3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |107,522 |4,537 |
| Standard_G4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |195,116 |5,024 |
| Standard_G5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |360,329 |14,212 |

## <a name="gs-series"></a>GS シリーズ
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |28,613 |1,884 |
| Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |54,348 |3,474 |
| Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |83 |104,564 |1,834 |
| Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |194,111 |4,735 |
| Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |84 |357,396 |16,228 |

## <a name="h-series"></a>H シリーズ
| サイズ | vCPU 数 | NUMA ノード数 | CPU | 実行 | イテレーション数/秒 | 標準偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |140,782 |2,512 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |35 |275,289 |7,110 |
| Standard_H18m |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |139,071 |3,988 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |275,988 |6,963 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |273,982 |6,069 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |28 |274,523 |5,698 |

## <a name="about-coremark"></a>CoreMark について
Linux の数値は、Ubuntu 上で [CoreMark](http://www.eembc.org/coremark/faq.php) を実行して計算しました。 CoreMark は、スレッド数を仮想 CPU の数に設定し、同時実行数を PThreads に設定して構成しました。 目標の反復の回数は、少なくとも 20 秒 (通常はもっとはるかに長い) のランタイムを提供するために予期されるパフォーマンスに基づいて調整されました。 最終のスコアは、完了した反復の回数をテストの実行にかかった秒数で割った値を表します。 VM ごとに各テストを少なくとも 7 回実行しました。 テスト (H シリーズを除く) は、2015 年 10 月に、実行当日に VM がサポートされているすべての Azure パブリック リージョンで、複数の VM 上で実行されました。

## <a name="next-steps"></a>次のステップ
* ストレージの容量、ディスクの詳細、VM のサイズを選択する際のその他の考慮事項については、 [仮想マシンのサイズ](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。
* Linux VM 上で CoreMark スクリプトを実行するには、 [CoreMark スクリプト パック](http://download.microsoft.com/download/3/0/5/305A3707-4D3A-4599-9670-AAEB423B4663/AzureCoreMarkScriptPack.zip)をダウンロードします。




<!--HONumber=Nov16_HO3-->


