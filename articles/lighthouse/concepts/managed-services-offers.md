---
title: Azure Marketplace のマネージド サービス オファー
description: マネージド サービス オファーにより、サービス プロバイダーは、Azure Marketplace で顧客にリソース管理オファーを販売することができます。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810876"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Azure Marketplace のマネージド サービス オファー

この記事では、[Azure Marketplace](https://azuremarketplace.microsoft.com) での新しいオファーの種類である、**マネージド サービス**について説明します。 マネージド サービス オファーでは、Azure の委任されたリソース管理機能を利用したリソース管理サービスを顧客に提供できます。 これらのオファーは、潜在顧客すべてに公開することも、1 名または複数の特定の顧客のみに公開することもできます。 こうしたマネージド サービスに関連する費用についてはお客様から顧客に直接請求していただくので、Microsoft から料金が課されることはありません。

## <a name="understand-managed-services-offers"></a>マネージド サービス オファーについて

マネージド サービス オファーにより、Azure の委任されたリソース管理に顧客をオンボードするプロセスを効率化できます。 顧客は、Azure Marketplace でオファーを購入した後、オンボードするサブスクリプションやリソース グループを指定できるようになります。これにより、お客様の組織の指定ユーザーが、組織のテナント内から顧客の代わりに管理タスクを実行可能になります。

その後は、顧客のオンボードについて、顧客とサービス プロバイダーのどちらもこれ以上の処理を行う必要はありません。 その理由は、[Cloud パートナー ポータル](https://cloudpartner.azure.com/)でオファーを定義するときに、Azure の委任されたリソース管理機能を使用して顧客のリソースにアクセスできる Azure AD ユーザー、グループ、サービス原則や、 それらのアクセス レベルを定義するロールを指定するマニフェストを指定するためです。 個々のユーザー アカウントやアプリケーション アカウントではなく、Azure AD グループに権限を割り当てるので、アクセス要件が変わった場合にユーザーを個別に追加、削除できます。

## <a name="public-and-private-offers"></a>パブリック オファーとプライベート オファー

各マネージド サービス オファーには、1 つ以上のプランが含まれます。 これらのプランは、プライベートとパブリックのいずれかにすることができます。

オファーの対象を特定の顧客に限定する場合は、プライベート プランを公開します。 そうすれば、指定したサブスクリプション ID でのみそのプランを購入できるようになります。 詳細については、「[プライベート オファー](https://docs.microsoft.com/azure/marketplace/private-offers)」を参照してください。

パブリック プランでは、新しい顧客に向けてサービスを宣伝できます。 一般に、顧客のテナントへのアクセスが制限付きでかまわない場合には、こちらの方が適しています。 顧客との関係構築後、顧客がお客様の組織に追加のアクセス権を付与することに決めた場合は、お客様がその顧客のみを対象として新しいプライベート プランを公開するか、[Azure Resource Manager テンプレート](../how-to/onboard-customer.md)を使用して顧客を追加のアクセスにオンボードしてください。

プランをパブリックとして公開した後でプライベートに変更することはできませんので、ご注意ください。 また、パブリック プランの対象範囲を特定の顧客に制限したり、対象となる顧客数を限定したりすることもできません。ただし、必要であればプランの販売を完全に中止できます。

必要に応じて、同じオファーにパブリック プランとプライベート プランの両方を含めることができます。

## <a name="publish-managed-service-offers"></a>マネージド サービス オファーの公開

マネージド サービス オファーの公開方法については、「[Azure Marketplace にマネージド サービス オファーを公開する](../how-to/publish-managed-services-offers.md)」を参照してください。 Cloud パートナー ポータルを使用した Azure Marketplace への公開に関する一般的な情報については、「[Azure Marketplace と AppSource の公開ガイド](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)」および「[Azure Marketplace と AppSource Marketplace のオファーを管理する](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers)」を参照してください。

## <a name="next-steps"></a>次の手順

- 「[Azure の委任されたリソース管理](azure-delegated-resource-management.md)」および「[テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)」を確認します。
- Azure Marketplace に[マネージド サービス オファーを公開します](../how-to/publish-managed-services-offers.md)。
