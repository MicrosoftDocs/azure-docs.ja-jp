---
title: "Azure で Analysis Services サーバーを作成する | Microsoft Docs"
description: "Azure で Analysis Services サーバーのインスタンスを作成する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/16/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 466595773663d43ad8e25fa9ec0ec0163a3f5962
ms.lasthandoff: 03/24/2017


---
# <a name="create-an-analysis-services-server"></a>Analysis Services サーバーを作成する
この記事では、Azure サブスクリプションで Analysis Services サーバー リソースを作成する手順について説明します。

## <a name="before-you-begin"></a>開始する前に
開始するには、以下が必要です。

* **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
* **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。 また、その Azure Active Directory でアカウントを使用して Azure にサインインしている必要があります。 Microsoft アカウントはサポートされていません。 詳しくは、[ユーザー認証](analysis-services-overview.md#secure)に関するセクションをご覧ください。
* **リソース グループ**: 既存のリソース グループを使うか、[新しいリソース グループを作成](../azure-resource-manager/resource-group-overview.md)します。

> [!NOTE]
> Analysis Services サーバーを作成すると、新しい課金対象サービスが作成される可能性があります。 詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。
> 
> 

## <a name="create-an-analysis-services-server"></a>Analysis Services サーバーを作成する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[+ 新規]** > **[インテリジェンス + 分析]** > **[Analysis Services]** の順にクリックします。
3. **[Analysis Services]** ブレードで、必要なフィールドを入力して **[作成]** をクリックします。
   
    ![Create server](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **[サーバー名]**: サーバーの参照に使われる一意の名前を入力します。
   * **[サブスクリプション]**: このサーバーの課金先のサブスクリプションを選びます。
   * **[リソース グループ]**: Azure リソースのコレクション管理のサポートを目的としたコンテナーです。 詳しくは、「[Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
   * **[場所]**: この Azure データセンターの場所でサーバーをホストします。 最大のユーザー ベースに最も近い場所を選びます。
   * **[価格レベル]**: 価格レベルを選びます。 最大 100 GB の表形式モデルがサポートされます。 詳しくは、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」をご覧ください。
4. **[作成]**をクリックします。

通常、作成にかかる時間は 1 分未満であり、たいていはわずか数秒で終わります。 **[Add to Portal]** (ポータルに追加) を選んだ場合は、ポータルに移動して新しいサーバーを確認します。 または、**[その他のサービス]** > **[Analysis Services]** に移動し、サーバーの準備ができていることを確認します。

 ![ダッシュボード](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>サーバー作成の自動化
Azure Resource Manager テンプレート ファイルを使用して即座にサーバー プロビジョニングを自動化できます。 詳しくは、次のビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>次のステップ
サーバーを作成した後は、SSDT または SSMS を使ってサーバーに[モデルをデプロイ](analysis-services-deploy.md)できます。

サーバーにデプロイするモデルがオンプレミスのデータ ソースに接続する場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をネットワーク内のコンピューターにインストールする必要があります。


