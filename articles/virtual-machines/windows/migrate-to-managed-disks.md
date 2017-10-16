---
title: "Azure VM を Managed Disks に移行する | Microsoft Docs"
description: "非管理対象ディスクを使用してストレージ アカウントに作成された Azure 仮想マシンを移行し、Managed Disks を使用できます。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: e23697b390e03bd2b71f2c905882070d864d62ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM を Azure Managed Disks に移行する

Azure Managed Disks を使用すると、ストレージ アカウントを個別に管理する必要がなくなり、ストレージ管理が簡略化されます。  また、既存の Azure VM を Managed Disks に移行して、可用性セット内の VM の信頼性を向上させることもできます。 可用性セット内の各 VM のディスクは、単一障害点となるのを避けるために、相互に十分に分離されます。 可用性セット内の異なる VM のディスクは異なるストレージ スケール ユニット (スタンプ) に自動的に配置されるため、ハードウェアとソフトウェアの障害を原因とする単一のストレージ スケール ユニット障害の影響が限定されます。
ニーズに基づいて、2 種類のストレージ オプションから選ぶことができます。

- [Premium Managed Disks](../../storage/common/storage-premium-storage.md) は、ソリッド ステート ドライブ (SSD) ベースのストレージ メディアで、I/O を集中的に行うワークロードを実行している仮想マシンに、高パフォーマンスで待ち時間の短いディスク サポートを提供します。 Premium Managed Disks に移行すると、これらのディスクの速度とパフォーマンスを最大限に高めることができます。

- [Standard Managed Disks](../../storage/common/storage-standard-storage.md) は、ハード ディスク ドライブ (HDD) ベースのストレージ メディアで、パフォーマンス変動の影響を受けにくい、開発/テスト ワークロードやアクセス頻度の少ないワークロードに最適です。

次のようなシナリオが、Managed Disks への移行に適しています。

| 移行シナリオ                                            | ドキュメント リンク                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| スタンドアロン VM と可用性セット内の VM を管理対象ディスクに変換する   | [VM を非管理対象ディスクから管理ディスクに変換する](convert-unmanaged-to-managed-disks.md) |
| 1 つの VM をクラシックから管理ディスク上の Resource Manager に移行する     | [1 つの VM を移行する](migrate-single-classic-to-resource-manager.md)方法に関する記事  | 
| VNet 内のすべての VM をクラシックから管理ディスク上の Resource Manager に移行する     | [IaaS リソースをクラシックから Resource Manager に移行する](migration-classic-resource-manager-ps.md)方法に関する記事の参照後、[VM を非管理対象ディスクから管理ディスクに変換する](convert-unmanaged-to-managed-disks.md)方法に関する記事 | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Managed Disks への変換の計画

このセクションの情報を使用して、自社環境に最適な VM とディスクの種類を判断できます。


## <a name="location"></a>Location (場所)

Azure Managed Disks を使用できる場所を選びます。 Premium Managed Disks に移行する場合は、移行を計画しているリージョンで Premium Storage が利用可能であることも確認します。 使用できる場所に関する最新情報については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/#services)」をご覧ください。

## <a name="vm-sizes"></a>VM サイズ

Premium Managed Disks に移行する場合は、VM が配置されているリージョンで利用できる Premium Storage 対応サイズに合うように VM のサイズを更新する必要があります。 Premium Storage で対応できる VM サイズをご確認ください。 Azure VM のサイズの仕様は、「 [仮想マシンのサイズ](sizes.md)」に記載されています。
Premium Storage で動作する仮想マシンのパフォーマンス特性を確認し、ワークロードに最適な VM を選択してください。 ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。

## <a name="disk-sizes"></a>ディスク サイズ

**Premium Managed Disks**

VM で使える Premium 管理ディスクには 7 種類あり、それぞれに特定の IOPS とスループットの制限があります。 VM のディスクの種類として Premium を選択する場合は、容量、パフォーマンス、スケーラビリティ、ピーク負荷に関するアプリケーションのニーズを踏まえると共に、これらの制限も考慮してください。

| Premium ディスクの種類  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| ディスク サイズ           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| ディスクあたりの IOPS       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| ディスクあたりのスループット | 25 MB/秒  | 50 MB/秒  | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 | 250 MB/秒 | 250 MB/秒 |

**Standard Managed Disks**

VM で使用できる Standard Managed Disks は 7 種類あります。 それぞれ容量は異なりますが、IOPS とスループットの制限は同じです。 アプリケーションの容量のニーズに基づいて Standard Managed Disks の種類を選択してください。

| Standard ディスクの種類  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| ディスク サイズ           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| ディスクあたりの IOPS       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| ディスクあたりのスループット | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 60 MB/秒 | 

## <a name="disk-caching-policy"></a>ディスク キャッシュ ポリシー

**Premium Managed Disks**

既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「*読み取り専用*」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「*読み取り/書き込み*」です。 アプリケーションの IO パフォーマンスを最適化するには、この構成をお勧めします。 書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。

## <a name="pricing"></a>価格

[Managed Disks の価格](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)をご確認ください。 Premium Managed Disks の価格は、Premium 非管理対象ディスクと同じです。 一方、Standard Managed Disks の価格は、Standard 非管理対象ディスクとは異なります。



## <a name="next-steps"></a>次のステップ

- [Managed Disks](managed-disks-overview.md) の概要をご覧ください。
