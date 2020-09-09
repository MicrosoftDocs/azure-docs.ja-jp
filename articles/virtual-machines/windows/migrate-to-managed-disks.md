---
title: Azure VM を Managed Disks に移行する
description: 非管理対象ディスクを使用してストレージ アカウントに作成された Azure 仮想マシンを移行し、Managed Disks を使用できます。
author: roygara
ms.service: virtual-machines-windows
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d64ebb053ef01e375edd52ad0bf2c1f424f1b837
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84660820"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM を Azure Managed Disks に移行する

Azure Managed Disks を使用すると、ストレージ アカウントを個別に管理する必要がなくなり、ストレージ管理が簡略化されます。  また、既存の Azure VM を Managed Disks に移行して、可用性セット内の VM の信頼性を向上させることもできます。 単一障害点を避けるために、可用性セット内の異なる VM のディスクは相互に十分に分離されます。 可用性セット内の異なる VM のディスクは異なるストレージ スケール ユニット (スタンプ) に自動的に配置されるため、ハードウェアとソフトウェアの障害を原因とする単一のストレージ スケール ユニット障害の影響が限定されます。
ニーズに基づいて、4 種類のストレージ オプションから選ぶことができます。 使用できるディスクの種類の詳細については、[ディスクの種類の選択](disks-types.md)に関する記事を参照してください。

## <a name="migration-scenarios"></a>移行シナリオ

次のようなシナリオが、Managed Disks への移行に適しています。

|シナリオ  |[アーティクル]  |
|---------|---------|
|スタンドアロン VM と可用性セット内の VM をマネージド ディスクに変換する     |[VM を非管理対象ディスクからマネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)         |
|1 つの VM をクラシックからマネージド ディスク上の Resource Manager に変換する     |[クラシック VHD から VM を作成する](create-vm-specialized-portal.md)         |
|VNet 内のすべての VM をクラシックからマネージド ディスク上の Resource Manager に変換する     |[IaaS リソースをクラシックから Resource Manager に移行する](migration-classic-resource-manager-ps.md)方法に関する記事の参照後、[VM を非管理対象ディスクからマネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)方法に関する記事         |
|標準のアンマネージド ディスクを持つ VM をプレミアム マネージド ディスクを持つ VM にアップグレードする     | まず、[Windows 仮想マシンをアンマネージド ディスクからマネージド ディスクに変換します](convert-unmanaged-to-managed-disks.md)。 次に、[マネージド ディスクのストレージの種類を更新します](convert-disk-storage.md)。         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>次のステップ

- [Managed Disks](managed-disks-overview.md) の概要をご覧ください。
- [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)をご確認ください。
