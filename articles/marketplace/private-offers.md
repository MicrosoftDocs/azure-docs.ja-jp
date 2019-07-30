---
title: 非公開オファー | Azure Marketplace
description: アプリとサービスの公開元向けの Azure Marketplace 上のプライベート オファー。
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: dcba2b40bdcf4558f7a06f7e14d0ce654a9c1ec1
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876078"
---
# <a name="private-offers"></a>プライベート オファー

公開元は、[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) 上でプライベート オファーを使用して、ターゲット顧客のみに表示される SKU を作成できます。

## <a name="unlock-enterprise-deals-with-private-offers"></a>プライベート オファーを使用して企業の取引を開放する

オンライン市場でクラウド ソリューションを見つけて、試して、購入している企業顧客はますます増加しています。 公開元は、Marketplace 上でプライベート オファーを使用して、企業が必要とする能力と共に、カスタマイズされたソリューションをターゲット顧客と非公開で共有できるようになりました。

- "*価格交渉*" によって、公開元は、一般公開されているオファーとは異なる値引きや公表されない価格設定を展開できます。
- "*プライベートな取引条件*" によって、公開元は、特定の顧客に合うように取引条件を調整できます。
- "*構成の特化*" によって、公開元は、各自の仮想マシン、Azure アプリケーション、SaaS アプリを提供するオファーを個々の顧客のニーズに合うように調整できます。 公開元は、このオプションを使用して、すべての顧客に幅広く製品を公開する前に、その新機能に事前にアクセスすることもできます。

公開元は、プライベート オファーを利用して、一般に公開される Marketplace の世界規模の利用可用性を活かすことができるうえに、取引と構成をカスタマイズするために必要な交渉を柔軟に行って、それらを提供できます。 さらに、これらの特性により、企業がクラウド上のマーケットプレースを活発に利用するための道が開きます。  企業は、各自が想定した望みどおりの方法で、購入と販売を行うことができます。

現時点では、プライベート オファーは、仮想マシン、Azure アプリケーション (ソリューション テンプレートまたはマネージド アプリケーションとして実装)、および SaaS アプリを提供するために利用できます。 パブリック オファーと同じように、プライベート オファーも、[Cloud パートナー ポータル](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus)上で作成して管理できます。  顧客に対するプライベート オファーへのアクセス権は、数分で許可することも取り消すこともできます。

## <a name="creating-private-offers-using-skus-and-plans"></a>SKU とプランを使用したプライベート オファーの作成

公開元は、新しい SKU またはプランを作成し、それらをプライベートに設定することによって、"*パブリック SKU またはプランを含む新規または既存のオファー*" のプライベート版を簡単に作成できます。  [プライベート SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) とプランはオファーのコンポーネントであり、ターゲット顧客のみが見ることができ、購入することができます。 プライベート SKU またはプランでは、基本イメージを再利用でき、パブリック SKU またはプランに対してすでに発行されているオファーのメタデータも再利用できます。 公開元は、このオプションを使用して、パブリック オファーのプライベート版を複数作成でき、同じ基本イメージとオファーのメタデータを複数のバージョンで公開する必要はありません。 仮想マシンと Azure アプリケーションを提供するオファーのみ、プライベート SKU とパブリック SKU で基本イメージを共有しているときに、オファーの基本イメージを変更すると、その基本イメージを使用しているすべてのパブリック SKU とプライベート SKU に変更が反映されます。

"*プライベート SKU またはプランのみが含まれている新しいオファー*" の場合、公開元は、他のオファーと同じようにオファーを作成でき、作成後に SKU またはプランをプライベートとしてマークします。 プライベート SKU またはプランのみが含まれているオファーの場合、そのオファーに関連付けられていない顧客は、[Azure Marketplace](https://azuremarketplace.microsoft.com) でも [Azure portal](https://azure.microsoft.com/features/azure-portal/) でも、オファーを検索したりアクセスしたりすることはできません。

## <a name="targeting-customers-with-private-offers"></a>プライベート オファーのターゲット顧客
新規と既存のプライベート オファーの両方で、公開元は、サブスクリプション ID を使用してターゲット顧客を選択できます。 仮想マシンまたは Azure アプリケーションを提供するオファーを利用する公開元は、プライベート SKU の利用可用性を個々の Azure サブスクリプション ID に制限するか、最大 20,000 個の Azure サブスクリプション ID を含む CSV をアップロードすることで制限できます。 SaaS アプリを提供するプライベート オファーを利用する公開元は、手動または CSV のアップロードによって Azure サブスクリプション ID またはテナント ID のいずれかを関連付けることで、プライベート オファーの利用可用性を制限できます。

オファーが認定を受けて公開された後、プライベート サブスクリプションの同期機能を使用することで、数分以内に SKU またはプランの顧客を更新したり、削除したりできます。 この機能により、公開元は、オファーの再認定や再発行なしで、プライベート SKU またはプランを提示する顧客の一覧を迅速かつ簡単に更新できます。

## <a name="deploying-private-offers"></a>プライベート オファーのデプロイ

プライベート オファーは [Azure portal](https://azure.microsoft.com/features/azure-portal/) 上でのみ検索でき、[Azure Marketplace](https://azuremarketplace.microsoft.com) には表示されません。 Azure portal にログインした顧客は、［Marketplace］ ナビゲーション要素を選択して、各自のプライベート オファーにアクセスできます。 プライベート オファーは検索結果内にも表示され、コマンドラインと Azure Resource Manager テンプレートを使用して、他のオファーと同じようにデプロイできます。

![[プライベート オファー]](./media/marketplace-publishers-guide/private-offer.png)

プライベート オファーは検索結果内にも表示されます。 [プライベート] バッジを見つけてください。

> [!Note]
> プライベートオファーは、クラウド ソリューション プロバイダー プログラム (CSP) のリセラーを通じて確立されたサブスクリプションではサポートされていません。

## <a name="next-steps"></a>次の手順

これらの新機能を活用して、[Azure Marketplace](https://azuremarketplace.microsoft.com/sell) での販売を開始できます。
