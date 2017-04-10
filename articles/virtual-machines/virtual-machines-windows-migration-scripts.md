---
title: "コミュニティ ツール - クラシックのリソースを Azure Resource Manager に移動する | Microsoft Docs"
description: "この記事には、クラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに IaaS リソースの移行するのに役立つコミュニティ提供のツールが掲載されています。"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5e2ad5e5eae97645368797c8cdf848d88719bb64
ms.lasthandoff: 03/29/2017


---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>クラシックから Azure Resource Manager に IaaS リソースを移行するためのコミュニティ ツール
この記事には、クラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに IaaS リソースの移行するのを支援するコミュニティ提供ツールが掲載されています。

> [!NOTE]
> Microsoft サポートは これらのツールを正式にサポートしていません。 そのため、PowerShell スクリプトは GitHub でオープンソースになっており、修正プログラムや追加シナリオの PR を歓迎しています。 問題をレポートするには、GitHub の問題の機能を使用します。
> 
> これらのツールによる移行でクラシック仮想マシンにダウンタイムが発生します。 プラットフォームでサポートされている移行については、次を参照してください。 
> 
> * [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
> * [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
> * [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-windows-ps-migration-classic-resource-manager.md)
> 
> 

## <a name="asm2arm"></a>ASM2ARM
これは、**1 つ**の仮想マシン (VM) をクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行するための PowerShell スクリプト モジュールです。 

[ツールのドキュメントへのリンク](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz
migAz は、クラシック IaaS リソースの完全なセットを Azure Resource Manager の IaaS リソースに移行するための追加オプションです。 移行は、同じサブスクリプション内、または別のサブスクリプションとサブスクリプションの種類 (例: CSP サブスクリプション) の間で発生する可能性があります。

[ツールのドキュメントへのリンク](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)


