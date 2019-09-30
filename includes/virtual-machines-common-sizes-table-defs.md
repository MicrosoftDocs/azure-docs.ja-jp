---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: ad21dbb356a6630cec0a6f5658b20cb8568890f9
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "67181135"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>サイズ表の定義

- ストレージ容量は GiB (1024^3 バイト) 単位で示されています。 GB (1000^3 バイト) 単位のディスクと GiB (1024^3 バイト) 単位のディスクを比較する場合は、GiB 単位の方が容量の数値が小さく見えることに注意してください。 たとえば、1023 GiB = 1098.4 GB です。
- ディスク スループットの測定単位は、1 秒あたりの入力/出力操作数 (IOPS) および MBps です (MBps = 10^6 バイト/秒)。
- データ ディスクは、キャッシュを有効にしたモードでも無効化したモードでも動作します。 キャッシュを有効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **ReadOnly** または **ReadWrite** に設定されています。  キャッシュを無効にしたデータ ディスクの操作では、ホスト キャッシュ モードは **None**に設定されています。
-   VM のパフォーマンスを最適にするには、データ ディスクの数を vCPU あたり 2 ディスクに制限する必要があります。
- **想定ネットワーク帯域幅**は、すべての宛先について、すべての NIC で [VM の種類ごとに割り当てられた最大集約帯域幅](../articles/virtual-network/virtual-machine-network-throughput.md)です。 上限は保証されませんが、目的のアプリケーションに適した VM の種類を選択するためのガイダンスを示しています。 実際のネットワークのパフォーマンスは、ネットワークの輻輳、アプリケーションの負荷、ネットワーク設定など、さまざまな要因に左右されます。 ネットワーク スループットの最適化については、[Windows および Linux のネットワーク スループットの最適化](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)に関する記事を参照してください。 Linux または Windows で想定ネットワーク パフォーマンスを実現するには、特定のバージョンを選択するか、VM を最適化することが必要になることがあります。 詳細については、[仮想マシンのスループットを確実にテストする方法](../articles/virtual-network/virtual-network-bandwidth-testing.md)に関する記事を参照してください。



