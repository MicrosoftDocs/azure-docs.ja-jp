---
title: プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細 | Microsoft Docs
description: この記事では、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について技術的に詳しく説明します。
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ee40d32-a5e8-42a2-97d0-3232fd3cbb98
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 996dc10b3937cad781dcc19a1c37efd511409964
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089444"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細
Azure クラシック デプロイ モデルから、Azure Resource Manager デプロイ モデルへの移行を詳しく見ていきましょう。 Azure Platform 上の 2 つのデプロイメント モデルの間で、どのようにリソースが移行されるかを理解できるように、リソースと機能レベルでリソースについて説明していきます。 詳細については、次のサービス告知記事を参照してください。「[プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」。

[!INCLUDE [virtual-machines-common-migration-deep-dive](../../../includes/virtual-machines-common-classic-resource-manager-migration-deep-dive.md)]

## <a name="next-steps"></a>次の手順

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway クラシックから Resource Manager への移行](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
