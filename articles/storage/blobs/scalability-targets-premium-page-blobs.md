---
title: Premium ページ BLOB ストレージ アカウントのスケーラビリティ ターゲット
titleSuffix: Azure Storage
description: Premium パフォーマンス ページ BLOB ストレージ アカウントは、読み取り/書き込み操作用に最適化されています。 この種類のストレージ アカウントは、Azure 仮想マシンの非管理対象ディスクを支援します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 23a176bfa5e3861dbc4ad5c03ea54fc847d3f56b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96922532"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Premium ページ BLOB ストレージ アカウントのスケーラビリティおよびパフォーマンス ターゲット

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Premium ページ BLOB アカウントのスケール ターゲット

Premium パフォーマンス ページ BLOB ストレージ アカウントは、読み取り/書き込み操作用に最適化されています。 この種類のストレージ アカウントは、Azure 仮想マシンの非管理対象ディスクを支援します。

> [!NOTE]
> Microsoft では、可能であれば、Azure 仮想マシン (VM) で管理ディスクを使用することをお勧めします。 マネージド ディスクの詳細については、[VM 向け Azure Disk Storage の概要](../../virtual-machines/managed-disks-overview.md)に関するページを参照してください。

Premium ページ BLOB ストレージ アカウントのスケーラビリティ ターゲットは、次のとおりです。

| 合計アカウント容量                            | ローカル冗長ストレージ アカウントの合計帯域幅                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| ディスク容量:4 TB (個々のディスク)/35 TB (全ディスクの累積合計) <br>スナップショット容量:10 TB | 受信<sup>1</sup> と送信<sup>2</sup> を合わせて最大 50 GB/秒 |

<sup>1</sup> ストレージ アカウントに送信されるすべてのデータ (要求)

<sup>2</sup> ストレージ アカウントから受信されるすべてのデータ (応答)

Premium ページ BLOB アカウントは、Premium パフォーマンス用に構成された汎用アカウントです。 汎用 v2 ストレージ アカウントをお勧めします。

非管理対象ディスクに Premium ページ BLOB ストレージ アカウントを使用しており、アプリケーションが 1 つのストレージ アカウントのスケーラビリティ ターゲットを超えた場合は、管理ディスクに移行することをお勧めします。 マネージド ディスクの詳細については、[VM 向け Azure Disk Storage の概要](../../virtual-machines/managed-disks-overview.md)に関するページを参照してください。

管理ディスクに移行できない場合は、複数のストレージ アカウントを使用するようにアプリケーションをビルドし、それらのストレージ アカウント間でデータをパーティション分割します。 たとえば、複数の VM で合計 51 TB のディスクを接続する場合、ディスクを 2 つのストレージ アカウントに分散します。 Premium Storage アカウント 1 つあたりの上限は 35 TB です。 1 つの Premium パフォーマンス ストレージ アカウントでプロビジョニングするディスクの容量が 35 TB を超えることがないようにしてください。

## <a name="see-also"></a>関連項目

- [標準ストレージ アカウントのスケーラビリティとパフォーマンスのターゲット](../common/scalability-targets-standard-account.md)
- [Premium ブロック BLOB ストレージ アカウントのスケーラビリティ ターゲット](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure サブスクリプションの制限とクォータ](../../azure-resource-manager/management/azure-subscription-service-limits.md)
