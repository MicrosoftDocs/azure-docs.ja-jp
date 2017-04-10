---
title: "Marketplace 用ソリューション テンプレートを作成するためのガイド | Microsoft Docs"
description: "Azure Marketplace で購入できる複数 VM イメージのソリューション テンプレートを作成、認定、およびデプロイする方法を詳しく説明します。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
translationtype: Human Translation
ms.sourcegitcommit: c6190a5a5aba325b15aef97610c804f5441ef7ad
ms.openlocfilehash: 64c7486ae24c31ef2a1116fdfd3cab9b813a09af


---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Azure Marketplace 向けソリューション テンプレートの作成ガイド
手順 1 の[アカウントの作成と登録][link-acct-creation]に関するページの完了後、[ソリューション テンプレートを作成するための技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md)のページで、Azure と互換性のあるソリューション テンプレートの作成について説明しました。 ここでは、Azure Marketplace 向けの[発行ポータル][link-pubportal]で複数の VM を対象としたソリューション テンプレートを作成する手順を紹介します。

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>発行ポータルでソリューション テンプレート プランを作成する
[https://publish.windowsazure.com](http://publish.windowsazure.com) に移動します。 [発行ポータル](https://publish.windowsazure.com/)に初めてサインインする場合は、会社の販売者プロファイルが登録されているアカウントを使用します。 発行ポータルでは、他の従業員を共同管理者として後から追加できます。

### <a name="1-select-solution-templates"></a>1.[ソリューション テンプレート] を選択する
  ![図][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2.新しいソリューション テンプレートを作成する
  ![図][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>手順&3;.最初にトポロジを作成する
ソリューション テンプレートは、作成したすべてのトポロジの 「親」 となります。 1 つのプランまたはソリューション テンプレートでは、複数のトポロジを定義できます。 プランをステージングにプッシュすると、すべてのトポロジも一緒にプッシュされます。 次の手順に従ってプランを定義します。     

* トポロジの作成: 通常、"トポロジ識別子" はソリューション テンプレートのトポロジ名です。 トポロジ識別子は、次に示すように URL で使用されます。

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* 新しいバージョンを追加します。

### <a name="4-get-your-topology-versions-certified"></a>4.トポロジのバージョンの認定を受ける
トポロジの特定バージョンをプロビジョニングするために必要な、すべてのファイルを含む zip ファイルをアップロードします。 この zip ファイルには、次のファイルを含める必要があります。

* ルート ディレクトリの *mainTemplate.json* および *createUiDefinition.json* ファイル。
* リンクされたテンプレートと必要なすべてのスクリプト。

  > [!TIP]
  > 開発者がソリューション テンプレートのトポロジを作成して認定を取得する間、会社のビジネス、マーケティング、法務の部門はマーケティングおよび法的なコンテンツの作業を進めることができます。
  >
  >

## <a name="next-steps"></a>次のステップ
ソリューション テンプレートを作成し、zip ファイルをアップロードしたら、プランをステージングにプッシュする前に、 [Marketplace マーケティング コンテンツ ガイド](marketplace-publishing-push-to-staging.md) の指示に従います。 Marketplace の発行に関する記事をすべて表示する場合は、 [Azure Marketplace へのプランの発行方法の概要](marketplace-publishing-getting-started.md)に関するページをご覧ください。

必要に応じて次の関連する記事も参照してください。

* VM イメージ: [Azure の仮想マシン イメージについて](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* VM 拡張機能: [VM エージェントと VM 拡張機能の概要](https://msdn.microsoft.com/library/azure/dn832621.aspx)および[Azure VM 拡張機能とその機能](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)および[単純なテンプレートの例](https://github.com/rjmax/ArmExamples)
* ストレージ アカウントのスロットル: [ストレージ アカウントのスロットルの監視方法](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx)および[Premium Storage](../storage/storage-premium-storage.md#premium-storage-scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com



<!--HONumber=Dec16_HO4-->


