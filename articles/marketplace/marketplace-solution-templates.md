---
title: Azure アプリケーション ソリューション テンプレートのオファー発行ガイド
description: この記事では、Azure Marketplace でソリューション テンプレートを発行するための要件を説明します
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: bbf9ab56bd7e070e40e2454a69f0a5e313597f14
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215373"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Azure アプリケーション:ソリューション テンプレートのオファー発行ガイド

ソリューション テンプレートは、Marketplace でソリューションを発行するための 1 つの手段です。 このガイドでは、このオファーの要件について説明します。 

デプロイと構成に関して、単一の VM を超える特別なオートメーションが必要となるソリューションの場合、オファーの種類として Azure アプリ (ソリューション テンプレート) を使用してください。 Azure アプリ (ソリューション テンプレート) を使用して、VM のプロビジョニングを自動化することができます。 ネットワーク リソースやストレージ リソースをプロビジョニングすることもできます。 Azure アプリ (ソリューション テンプレート) タイプのオファーでは、単一の VM および IaaS ベースのソリューション全体に関して、オートメーション ベネフィットが提供されます。

これらのソリューション テンプレートは、Marketplace を通じてデプロイされ課金されるトランザクションのオファーです。 ユーザーに表示される行動喚起は、"今すぐ入手する" です。


## <a name="requirements-for-solution-templates"></a>ソリューション テンプレートの要件

| **要件** | **詳細**  |
| ---------------  | -----------  |
|請求/メータリング    |  リソースが顧客の Azure サブスクリプションでプロビジョニングされます。 従量課金制 (PAYGO) の仮想マシンが Microsoft 経由で顧客とトランザクション処理され、顧客の Azure サブスクリプション (PAYGO) により課金されます。  <br/> ライセンス持ち込み (BYOL) の場合、Microsoft が顧客サブスクリプションで生じるインフラストラクチャ コストを請求し、ユーザーが顧客に対して直接ソフトウェア ライセンス料金を処理します。   |
|Azure と互換性がある仮想ハード ディスク (VHD)  |   VM は、Windows または Linux 上に構築されている必要があります。  詳細については、「[Azure と互換性のある VHD の作成](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)」をご覧ください。 |
| 顧客の利用状況属性 | Azure Marketplace に発行されるすべてのソリューション テンプレートで、顧客の利用状況属性を有効にする必要があります。 顧客の利用状況属性とそれを有効にする方法の詳細については、「[Azure パートナーの顧客の利用状況の属性](./azure-partner-customer-usage-attribution.md)」をご覧ください。  |
|  |  |

## <a name="next-steps"></a>次の手順
まだ行っていない場合は、Marketplace で[登録](https://azuremarketplace.microsoft.com/sell)します。

登録が済み、新しいオファーの作成または既存のオファーについての作業を行っている場合は、[Cloud パートナー ポータル](https://cloudpartner.azure.com)にサインインして、オファーを作成または完了します。
