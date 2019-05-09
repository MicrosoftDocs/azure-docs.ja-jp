---
title: Microsoft Azure での仮想マシンの前提条件 | Microsoft Docs
description: Azure Marketplace に VM オファーを発行するために必要な前提条件の一覧です。
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010853"
---
# <a name="virtual-machine-prerequisites"></a>仮想マシンの前提条件

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) に VM オファーを発行するために満たしておく必要がある技術要件とビジネス要件の一覧を示します。  まだお読みになっていない場合は、「[仮想マシンのオファー発行ガイド](../../marketplace-virtual-machines.md)」をご確認ください。


## <a name="technical-requirements"></a>技術的な要件

仮想マシン (VM) ソリューションを公開するための技術的な前提条件は簡単です。

- アクティブな Azure アカウントが必要です。 ない場合は、[Microsoft Azure サイト](https://azure.microsoft.com)でサインアップできます。  
- Windows VM または Linux VM の開発をサポートするように環境が構成されている必要があります。  詳しくは、以下の関連する VM ドキュメント サイトをご覧ください。
    - [Linux VM のドキュメント](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows VM のドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>ビジネス要件

ビジネス要件には、手続き、契約、および法律に関する義務が含まれます。 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- 登録済みのクラウド マーケットプレース パブリッシャーである必要があります。  まだ登録していない場合は、「[クラウド マーケットプレース パブリッシャーになる](../../become-publisher.md)」の手順に従ってください。

    > [!NOTE]
    > Microsoft デベロッパー センター登録アカウントと同じアカウントを使用して、[Cloud パートナー ポータル](https://cloudpartner.azure.com)にサインオンする必要があります。
    > 複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用してください。 個々のサービスおよびオファーに対して固有のアカウントを作成してはなりません。
    
- 貴社 (またはその子会社) は、Azure Marketplace によってサポートされる販売元の国に所在している必要があります。  これらの国の最新の一覧については、「[Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)」をご覧ください。
- Azure Marketplace でサポートされている課金モデルに対応した方法で、製品のライセンスを取得する必要があります。  詳細については、「[Azure Marketplace の課金オプション](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace)」を参照してください。 
- 商取引上合理的な方法で、顧客に技術サポートを提供していただきます。 このサポートは、無償、有償、コミュニティ アプローチのいずれでもかまいません。
- 貴社のソフトウェアおよび依存関係にあるすべてのサード パーティ ソフトウェアのライセンス供与を行っていただきます。
- Azure Marketplace および Azure portal にオファーが掲載されるための条件を満たすコンテンツを提供する必要があります。 <!-- TD: Meaning/links? -->
- [Microsoft Azure Marketplace 参加ポリシー](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)と発行者契約の条項に同意する必要があります。
- [Microsoft Azure Web サイト利用規約](https://azure.microsoft.com/support/legal/website-terms-of-use/)、[Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)、[Microsoft Azure 認定プログラム契約](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)に従う必要があります。


## <a name="next-steps"></a>次の手順

これらの前提条件を満たした後、[VM オファーを作成](./cpp-create-offer.md)できます。
