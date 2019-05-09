---
title: Azure VM を Managed Disks に移行する | Microsoft Docs
description: 非管理対象ディスクを使用してストレージ アカウントに作成された Azure 仮想マシンを移行し、Managed Disks を使用できます。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: f1f786ea4fbf9cea5afbbd2ff038b2b3f8bc3eaf
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56803585"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Azure VM を Azure Managed Disks に移行する

Azure Managed Disks を使用すると、ストレージ アカウントを個別に管理する必要がなくなり、ストレージ管理が簡略化されます。  また、既存の Azure VM を Managed Disks に移行して、可用性セット内の VM の信頼性を向上させることもできます。 単一障害点を避けるために、可用性セット内の異なる VM のディスクは相互に十分に分離されます。 可用性セット内の異なる VM のディスクは異なるストレージ スケール ユニット (スタンプ) に自動的に配置されるため、ハードウェアとソフトウェアの障害を原因とする単一のストレージ スケール ユニット障害の影響が限定されます。
ニーズに基づいて、4 種類のストレージ オプションから選ぶことができます。 使用できるディスクの種類の詳細については、[ディスクの種類の選択](disks-types.md)に関する記事を参照してください。

## <a name="migrate-scenarios"></a>移行のシナリオ

次のようなシナリオが、Managed Disks への移行に適しています。

| **移行...**                                            | **ドキュメント リンク**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| スタンドアロン VM と可用性セット内の VM をマネージド ディスクに変換する   | [VM を非管理対象ディスクからマネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md) |
| 1 つの VM をクラシックからマネージド ディスク上の Resource Manager に移行する     | [クラシック VHD から VM を作成する](create-vm-specialized-portal.md)  | 
| VNet 内のすべての VM をクラシックからマネージド ディスク上の Resource Manager に移行する     | [IaaS リソースをクラシックから Resource Manager に移行する](migration-classic-resource-manager-ps.md)方法に関する記事の参照後、[VM を非管理対象ディスクからマネージド ディスクに変換する](convert-unmanaged-to-managed-disks.md)方法に関する記事 | 


## <a name="next-steps"></a>次の手順

- [Managed Disks](managed-disks-overview.md) の概要をご覧ください。
- [Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)をご確認ください。
