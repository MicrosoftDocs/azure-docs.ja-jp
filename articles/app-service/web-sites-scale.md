---
title: 機能と容量のスケール アップ - Azure App Service |Microsoft Docs
description: Azure App Service のアプリをスケールアップして容量と機能を追加する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727481"
---
# <a name="scale-up-an-app-in-azure"></a>Azure でのアプリのスケールアップ

> [!NOTE]
> 新しい **PremiumV2** レベルでは、CPU、SSD ストレージが高速化され、メモリ対コア比が既存の価格レベルの 2 倍になっています。 パフォーマンスの利点を生かして、少ないインスタンス上でアプリを実行することで、費用を節約することができます。 **PremiumV2** レベルにスケールアップするには、「[Configure PremiumV2 tier for App Service (App Service 向け PremiumV2 レベルの構成)](app-service-configure-premium-tier.md)」をご覧ください。
>

この記事では、Azure App Service でアプリのスケールを変更する方法について説明します。 スケーリングには、スケールアップとスケールアウトという 2 つのワークフローがあり、この記事ではスケールアップ ワークフローについて説明します。

* [スケールアップ](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling):CPU、メモリ、ディスク領域を増やしたり、専用の仮想マシン (VM)、カスタム ドメインと証明書、ステージング スロット、自動スケールのような拡張機能を追加したりします。 スケールアップするには、アプリが属している App Service プランの価格レベルを変更します。
* [スケールアウト](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling):アプリを実行する VM インスタンスの数を増やします。
  価格レベルに応じて、20 個までのインスタンスにスケールアウトすることができます。 [Isolated](environment/intro.md) レベルの **App Service Environment** では、スケールアウト カウントが 100 インスタンスに増えます。 スケールアウトの詳細については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)」を参照してください。 あらかじめ定義されている規則とスケジュールに基づいて、インスタンス数を自動的に変更する自動スケールの使い方が説明されています。

スケール設定は適用に数秒を要するのみで、 [App Service プラン](../app-service/overview-hosting-plans.md)に含まれるすべてのアプリに反映されます。
コードを変更したりアプリケーションを再デプロイしたりする必要はありません。

App Service の個々のプランの価格と機能の詳細については、 [App Service の価格の詳細](https://azure.microsoft.com/pricing/details/web-sites/)に関するページを参照してください。  

> [!NOTE]
> App Service プランを **Free** レベルから切り替える前にまず、ご利用の Azure サブスクリプションに設定されている [使用制限](https://azure.microsoft.com/pricing/spending-limits/) を削除する必要があります。 Microsoft Azure App Service サブスクリプションのオプションを表示または変更するには、[Microsoft Azure のサブスクリプション][azuresubscriptions]に関するページを参照してください。
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>価格レベルのスケールアップ
1. ブラウザーで、[Azure Portal][portal] を開きます。
2. App Service のアプリ ページで、**[すべての設定]**、**[スケールアップ]** の順にクリックします。
   
    ![Navigate to scale up your Azure app.][ChooseWHP]
3. レベルを選び、**[適用]** をクリックします。
   
    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>スケール関連リソース
Azure SQL Database や Azure Storage などの他のサービスにアプリが依存している場合は、これらのリソースも個別にスケールアップできます。 これらのリソースは、App Service プランでは管理されません。

1. **[要点]** で、**[リソース グループ]** リンクをクリックします。
   
    ![Scale up your Azure app's related resources](./media/web-sites-scale/RGEssentialsLink.png)
2. **[リソース グループ]** ページの **[概要]** 部分で、スケールするリソースをクリックします。 以下のスクリーンショットは、SQL Database リソースと Azure Storage リソースを示しています。
   
    ![Navigate to resource group page to scale up your Azure app](./media/web-sites-scale/ResourceGroup.png)
3. SQL Database リソースの場合は、**[設定]** > **[価格レベル]** の順にクリックして価格レベルをスケールします。
   
    ![Scale up the SQL Database backend for your Azure app](./media/web-sites-scale/ScaleDatabase.png)
   
    SQL データベース インスタンス用に、 [[geo レプリケーション]](../sql-database/sql-database-geo-replication-overview.md) を有効にすることもできます。
   
    Azure Storage リソースの場合は、**[設定]** > **[構成]** の順にクリックしてストレージ オプションをスケールアップします。
   
    ![Scale up the Azure Storage account used by your Azure app](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>価格レベルの比較
各価格レベルの VM サイズなど、詳しくは、「[App Service の価格](https://azure.microsoft.com/pricing/details/web-sites/)」をご覧ください。
サービスの制限、クォータ、および制約と、各レベルでサポートされている機能の表については、「[App Service の制限](../azure-subscription-service-limits.md#app-service-limits)」をご覧ください。

<a name="Next Steps"></a>

## <a name="next-steps"></a>次の手順
* 価格、サポート、および SLA については、次のリンクを参照してください。
  
    [データ転送の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure サポート プラン](https://azure.microsoft.com/support/plans/)
  
    [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL Database の料金詳細](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]
  
* スケーラブルで回復力に優れたアーキテクチャの構築など、Azure App Service のベスト プラクティスについては、[Azure App Service Web Apps のベスト プラクティス](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/)に関するページを参照してください。
* App Service アプリのスケーリングに関するビデオについては、以下のリソースを参照してください。
  
  * [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
