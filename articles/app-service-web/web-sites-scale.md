---
title: "Azure でのアプリのスケールアップ | Microsoft Docs"
description: "Azure App Service のアプリをスケールアップして容量と機能を追加する方法について説明します。"
services: app-service
documentationcenter: 
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
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 75ddbacbd4dd14597b786d26f0730477f6c85811
ms.lasthandoff: 03/09/2017


---
# <a name="scale-up-an-app-in-azure"></a>Azure でのアプリのスケールアップ
この記事では、Azure App Service でアプリのスケールを変更する方法について説明します。 スケーリングには、スケールアップとスケールアウトという&2; つのワークフローがあり、この記事ではスケールアップ ワークフローについて説明します。

* [スケールアップ](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): CPU、メモリ、ディスク領域を増やしたり、専用の仮想マシン (VM)、カスタム ドメインと証明書、ステージング スロット、自動スケールのような拡張機能を追加したりします。 スケールアップするには、アプリが属している App Service プランの価格レベルを変更します。
* [スケールアウト](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): アプリを実行する VM インスタンスの数を増やします。
  価格レベルに応じて、20 個までのインスタンスにスケールアウトすることができます。 [Premium](../app-service/app-service-app-service-environments-readme.md) レベルの **App Service Environment** では、スケールアウト カウントが 50 インスタンスに増えます。 スケールアウトの詳細については、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)」を参照してください。 あらかじめ定義されている規則とスケジュールに基づいてインスタンス数を自動的に変更する自動スケールの使い方が説明されています。

スケール設定は適用に数秒を要するのみで、 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)に含まれるすべてのアプリに反映されます。
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
2. ご使用のアプリのブレードで、**[すべての設定]**、**[スケールアップ]** の順にクリックします。
   
    ![Navigate to scale up your Azure app.][ChooseWHP]
3. レベルを選び、 **[選択]**をクリックします。
   
    操作が完了すると、**[通知]** タブに緑色で "**成功**" が点滅します。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>スケール関連リソース
Azure SQL Database や Azure Storage などの他のサービスにアプリが依存している場合は、これらのリソースもニーズに合わせてスケールアップできます。 これらのリソースのスケールは、App Service プランでは変更されません。個別に変更する必要があります。

1. **[要点]** で、**[リソース グループ]** リンクをクリックします。
   
    ![Scale up your Azure app's related resources](./media/web-sites-scale/RGEssentialsLink.png)
2. **[リソース グループ]** ブレードの **[概要]** 部分で、スケールするリソースをクリックします。 以下のスクリーンショットは、SQL Database リソースと Azure Storage リソースを示しています。
   
    ![Navigate to resource group blade to scale up your Azure app](./media/web-sites-scale/ResourceGroup.png)
3. SQL Database リソースの場合は、**[設定]** > **[価格レベル]** の順にクリックして価格レベルをスケールします。
   
    ![Scale up the SQL Database backend for your Azure app](./media/web-sites-scale/ScaleDatabase.png)
   
    SQL Database インスタンス用に、 [[geo レプリケーション]](../sql-database/sql-database-geo-replication-overview.md) を有効にすることもできます。
   
    Azure Storage リソースの場合は、**[設定]** > **[構成]** の順にクリックしてストレージ オプションをスケールアップします。
   
    ![Scale up the Azure Storage account used by your Azure app](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>

## <a name="learn-about-developer-features"></a>開発者向け機能について
価格レベルに応じて、次の開発者向け機能を使用できます。

### <a name="bitness"></a>ビット
* **Basic**、**Standard**、および **Premium** レベルでは、64 ビットおよび 32 ビットのアプリケーションがサポートされます。
* **Free** プラン レベルと **Shared** プラン レベルでは、32 ビットのアプリケーションのみがサポートされます。

### <a name="debugger-support"></a>デバッガー サポート
* **Free**、**Shared**、および **Basic** モードでデバッガー サポートを利用する場合、App Service プランあたりの接続数は&1; です。
* **Standard** および **Premium** モードでデバッガー サポートを利用する場合、App Service プランあたりの同時接続数は&5; です。

<a name="OtherFeatures"></a>

## <a name="learn-about-other-features"></a>その他の機能について
* すべてのユーザー (開発者を含む) が関心を持つ料金や機能など、App Service プランのその他すべての機能の詳細については、 [App Service の料金の詳細](https://azure.microsoft.com/pricing/details/web-sites/)に関するページを参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、 [App Service の試用](https://azure.microsoft.com/try/app-service/) に関するページにアクセスすると、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 試用にあたり、クレジット カードや契約は必要ありません。
> 
> 

<a name="Next Steps"></a>

## <a name="next-steps"></a>次のステップ
* Azure を利用し始めるには、「 [Microsoft Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
* 価格、サポート、および SLA については、次のリンクを参照してください。
  
    [データ転送の料金詳細](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Microsoft Azure サポート プラン](https://azure.microsoft.com/support/plans/)
  
    [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL Database の料金詳細](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Microsoft Azure の仮想マシンおよびクラウド サービスのサイズ][vmsizes]
  
    [App Service の料金の詳細](https://azure.microsoft.com/pricing/details/app-service/)
  
    [App Service の料金の詳細 - SSL 接続](https://azure.microsoft.com/pricing/details/web-sites/#ssl-connections)
* スケーラブルで回復力に優れたアーキテクチャの構築など、Azure App Service のベスト プラクティスについては、 [Azure App Service Web Apps のベスト プラクティス](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)に関するページを参照してください。
* App Service アプリのスケーリングに関するビデオについては、以下のリソースを参照してください。
  
  * [Azure Websites のスケールを設定するタイミング - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Azure Websites、CPU、またはスケジュールの自動スケール - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Azure Websites のスケールを設定する方法 - Stefan Schackow 共演](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png

