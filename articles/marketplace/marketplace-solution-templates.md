---
title: Azure アプリケーション ソリューション テンプレート プランの発行ガイド - Azure Marketplace
description: この記事では、Azure Marketplace でソリューション テンプレートを発行するための要件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 04/22/2020
ms.openlocfilehash: c7074981c8491460d6f2a8e7d40d086f261dfeb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879345"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure アプリケーション ソリューション テンプレート プランの発行ガイド

この記事では、Azure Marketplace で Azure アプリケーションのプランを発行するための 1 つの方法である、ソリューション テンプレート プランを発行するための要件について説明します。 ソリューション テンプレート プラン タイプでは、ソリューション インフラストラクチャを自動的にデプロイするために [Azure Resource Manager テンプレート (ARM テンプレート)](../azure-resource-manager/templates/overview.md) が必要になります。

次の条件の場合に、Azure アプリケーション "*ソリューション テンプレート*" プラン タイプを使用します。

- ソリューションで、仮想マシン (VM)、ネットワーク、およびストレージ リソースの組み合わせなど、単一の VM を超える追加のデプロイと構成の自動化が必要となる。
- 顧客がソリューションを自分で管理する。

このプランの種類についてユーザーに表示される掲載オプションは、 *[今すぐ入手する]* です。

## <a name="requirements-for-solution-template-offers"></a>ソリューション テンプレート プランの要件

| **必要条件** | **詳細**  |
| ---------------  | -----------  |
|請求/メータリング    |  ソリューション テンプレート プランは、トランザクション プランではありませんが、Microsoft のコマーシャル マーケットプレースを通じて課金される有料 VM プランをデプロイする目的に使用できます。 ソリューションの Resource Manager テンプレートによってデプロイされるリソースは、顧客の Azure サブスクリプション内に設定されます。 従量課金制の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプションにより課金されます。<br/> ライセンス持ち込み (BYOL) 請求の場合、顧客サブスクリプションで発生するインフラストラクチャ コストについては Microsoft が請求しますが、ソフトウェア ライセンス料金についてはユーザーが顧客に対して直接処理します。   |
|Azure と互換性がある仮想ハード ディスク (VHD)  |   VM は、Windows または Linux 上に構築されている必要があります。 詳細については、次を参照してください。 <ul> <li>[Azure アプリケーション プランを作成する](./create-new-azure-apps-offer.md) (Windows VHD 用)。</li><li>[Azure で動作保証済みの Linux ディストリビューション](../virtual-machines/linux/endorsed-distros.md) (Linux VHD 用)。</li></ul> |
| 顧客の利用状況属性 | Azure Marketplace に発行されるすべてのソリューション テンプレートで、顧客の利用状況属性を有効にする必要があります。 顧客の利用状況属性とそれを有効にする方法の詳細については、「[Azure パートナーの顧客の使用状況の属性](./azure-partner-customer-usage-attribution.md)」をご覧ください。  |
| マネージド ディスクの使用 | [マネージド ディスク](../virtual-machines/managed-disks-overview.md)は、Azure のサービスとしてのインフラストラクチャ (IaaS) VM の永続化ディスクの既定オプションです。 ソリューション テンプレートではマネージド ディスクを使用する必要があります。 <ul><li>ソリューション テンプレートを更新するには、「[Azure Resource Manager テンプレートでの管理ディスクの使用](../virtual-machines/using-managed-disks-template-deployments.md)」のガイダンスに従って、提供されている[サンプル](https://github.com/Azure/azure-quickstart-templates)を使用します。<br><br> </li><li>VHD をイメージとして Marketplace に発行するには、次の方法のいずれかに従って、マネージド ディスクの基盤となる VHD をストレージ アカウントにインポートします。<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、[Azure Marketplace を使用してクラウド ビジネスを拡大する](https://azuremarketplace.microsoft.com/sell)方法について学習します。

パートナー センターで登録して作業を開始するには、次のようにします。

- [パートナー センターにサインイン](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)し、オファーを作成するか完成させます。
- 詳細については、「[Azure アプリケーション プランを作成する](./create-new-azure-apps-offer.md)」を参照してください。