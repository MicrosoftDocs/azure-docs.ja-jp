---
title: コミュニティ ツール - クラシック リソースを Azure Resource Manager に移動する
description: この記事には、クラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに IaaS リソースの移行するのに役立つコミュニティ提供のツールが掲載されています。
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: afc20a39a5b426f37d6a6752056e3dd35dd8438a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504961"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>クラシックから Azure Resource Manager に IaaS リソースを移行するためのコミュニティ ツール

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨の[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](../classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

この記事には、クラシック デプロイ モデルから Azure Resource Manager デプロイ モデルに IaaS リソースの移行するのを支援するコミュニティ提供ツールが掲載されています。

> [!NOTE]
> Microsoft サポートは これらのツールを正式にサポートしていません。 そのため、PowerShell スクリプトは GitHub でオープンソースになっており、修正プログラムや追加シナリオの PR を歓迎しています。 問題をレポートするには、GitHub の問題の機能を使用します。
> 
> これらのツールによる移行でクラシック仮想マシンにダウンタイムが発生します。 プラットフォームでサポートされている移行については、次を参照してください。 
> 
>   * [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](migration-classic-resource-manager-overview.md)
>   * [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../migration-classic-resource-manager-deep-dive.md)
>   * [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
これは、Azure Service Management から Azure Resource Manager へのエンタープライズ移行の一環として作成されたヘルパー ツールのコレクションです。 このツールでは、実稼働のサブスクリプションの移行を実行する前に、移行をテストしあらゆる問題の解決に使用できる別のサブスクリプションにお客様のインフラストラクチャをレプリケートすることができます。

[ツールのドキュメントへのリンク](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz は、クラシック IaaS リソースの完全なセットを Azure Resource Manager の IaaS リソースに移行するための追加オプションです。 移行は、同じサブスクリプション内、または別のサブスクリプションとサブスクリプションの種類 (例: CSP サブスクリプション) の間で発生する可能性があります。

[ツールのドキュメントへのリンク](https://github.com/Azure/migAz)

## <a name="next-steps"></a>次の手順

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Review most common migration errors](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
