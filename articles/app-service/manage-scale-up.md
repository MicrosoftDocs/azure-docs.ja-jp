---
title: 機能と容量をスケールアップする
description: Azure App Service でアプリをスケールアップする方法について説明します。 CPU、メモリ、ディスク領域を増加し、追加の機能を取得します。
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582478"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Azure App Service でアプリをスケールアップする

この記事では、Azure App Service でアプリのスケールを変更する方法について説明します。 スケーリングには、スケールアップとスケールアウトという 2 つのワークフローがあり、この記事ではスケールアップ ワークフローについて説明します。

* [スケールアップ](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): CPU、メモリ、ディスク領域を増やしたり、専用の仮想マシン (VM)、カスタム ドメインと証明書、ステージング スロット、自動スケールのような拡張機能を追加したりします。 スケールアップするには、アプリが属している App Service プランの価格レベルを変更します。
* [スケールアウト](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): アプリを実行する VM インスタンスの数を増やします。
  価格レベルに応じて、30 個までのインスタンスにスケールアウトすることができます。 [Isolated](environment/intro.md) レベルの **App Service Environment** では、スケールアウト カウントが 100 インスタンスに増えます。 スケールアウトの詳細については、「[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md)」を参照してください。 あらかじめ定義されている規則とスケジュールに基づいて、インスタンス数を自動的に変更する自動スケールの使い方が説明されています。

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

> [!NOTE]
> **PremiumV3** レベルにスケールアップするには、[App Service 向け PremiumV3 レベルの構成](app-service-configure-premium-tier.md)に関するページをご覧ください。
>

1. ブラウザーで、[Azure Portal][portal] を開きます。

1. ご利用の App Service アプリ ページの左側のメニューから、 **[スケール アップ (App Service プラン)]** を選択します。
   
3. ご利用のレベルを選んで、 **[適用]** をクリックします。 さまざまなカテゴリ ( **[運用]** など) を選択し、さらに **[その他のオプションを参照する]** を選択すると、より多くのレベルが表示されます。
   
    ![Navigate to scale up your Azure app.][ChooseWHP]

    操作が完了すると、緑色の成功チェック マークが付いた通知ポップアップ アイテムが表示されます。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>スケール関連リソース
Azure SQL Database や Azure Storage などの他のサービスにアプリが依存している場合は、これらのリソースも個別にスケールアップできます。 これらのリソースは、App Service プランでは管理されません。

1. ご利用のアプリの **[概要]** ページで、 **[リソース グループ]** リンクを選択します。
   
    ![Scale up your Azure app's related resources](./media/web-sites-scale/RGEssentialsLink.png)

2. **[リソース グループ]** ページの **[概要]** 部分で、スケールするリソースを選択します。 以下のスクリーンショットは、SQL Database リソースを示しています。
   
    ![Navigate to resource group page to scale up your Azure app](./media/web-sites-scale/ResourceGroup.png)

    関連リソースをスケールアップするには、特定の種類のリソースに関するドキュメントを参照してください。 たとえば、単一の SQL Database をスケールアップするには、[Azure SQL Database での単一データベース リソースのスケーリング](../azure-sql/database/single-database-scale.md)に関するページを参照してください。 Azure Database for MySQL リソースをスケールアップするには、[MySQL リソースのスケール](../mysql/concepts-pricing-tiers.md#scale-resources)に関するページを参照してください。

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>価格レベルの比較

各価格レベルの VM サイズなど、詳しくは、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service)」をご覧ください。

サービスの制限、クォータ、および制約と、各レベルでサポートされている機能の表については、「[App Service の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)」をご覧ください。

<a name="Next Steps"></a>

## <a name="more-resources"></a>その他のリソース

[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md)  
[App Service の PremiumV3 レベルの構成](app-service-configure-premium-tier.md)

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