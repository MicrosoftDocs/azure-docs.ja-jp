---
title: "Web Apps の概要 | Microsoft Docs"
description: "Azure App Service が Web アプリケーションの開発およびホストにどのように役立つかについて説明します"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 83c063bb003ccda80ba6c1a74aadcd624d77ae47
ms.lasthandoff: 03/21/2017


---
# <a name="web-apps-overview"></a>Web Apps の概要
*App Service Web Apps* は、Web サイトと Web アプリケーションをホストするために最適化された、完全に管理されたコンピューティング プラットフォームです。 Microsoft Azure のこの [サービスとしてのプラットフォーム](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) サービスを使用すると、アプリの実行とスケーリングのためのインフラストラクチャは Azure で管理されるため、お客様はビジネス ロジックに集中できます。

次の 5 分間のビデオでは、Azure App Service Web Apps の概要について説明しています。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Web-Apps-with-Yochay-Kiriaty/player]
>
>

> [!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]
> 
> 

## <a name="what-is-a-web-app-in-app-service"></a>App Service の Web アプリとは
App Service の *Web アプリ* とは、Web サイトまたは Web アプリケーションをホストするために Azure が提供するコンピューティング リソースです。  

コンピューティング リソースは、選択した価格レベルに応じて、共有または専用の仮想マシン (VM) 上に配置されます。 アプリケーション コードは、他のお客様から分離された管理 VM で実行されます。

コードは、ASP.NET、Node.js、Java、PHP、Python など、 [Azure App Service](../app-service/app-service-value-prop-what-is.md)でサポートされている任意の言語またはフレームワークで作成できます。 また、Web アプリでは、 [PowerShell やその他のスクリプト言語](web-sites-create-web-jobs.md#acceptablefiles) を使用するスクリプトを実行することもできます。

Web Apps を使用できる一般的なアプリケーション シナリオの例については、[Web アプリのシナリオ](https://azure.microsoft.com/documentation/scenarios/web-app/)に関するページおよび「[Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較](choose-web-site-cloud-service-vm.md#scenarios)」の「**シナリオと推奨事項**」セクションを参照してください。

## <a name="why-use-web-apps"></a>Web Apps を使用する理由
Web Apps に適用されるいくつかの App Service の主要機能を次に示します。

* **複数の言語とフレームワーク** - App Service は、ASP.NET、Node.js、Java、PHP、Python を最高レベルでサポートしています。 また、App Service VM では、 [PowerShell などのスクリプトや実行可能ファイル](web-sites-create-web-jobs.md) を実行することもできます。
* **DevOps の最適化** - [継続的インテグレーションとデプロイ](app-service-continuous-deployment.md) を、Visual Studio Team Services、GitHub、または BitBucket でセットアップできます。 [テスト環境やステージング環境](web-sites-staged-publishing.md)を介して更新を反映できます。 また、 [A/B テスト](app-service-web-test-in-production-get-start.md)を実行できます。 App Service でのアプリの管理には、[Azure PowerShell](/powershell/azureps-cmdlets-docs) または[クロスプラットフォーム コマンド ライン インターフェイス (CLI)](../cli-install-nodejs.md) を使用します。
* **高可用性を備えたグローバルなスケール** - 手動または自動で[スケールアップ](web-sites-scale.md)または[スケールアウト](../monitoring-and-diagnostics/insights-how-to-scale.md)を実行できます。 Microsoft のグローバルなデータセンター インフラストラクチャのどこででもアプリをホストでき、App Service の [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) によって高可用性が保証されます。
* **SaaS プラットフォームおよびオンプレミス データへの接続** - エンタープライズ システム (SAP、Siebel、Oracle など)、SaaS サービス (Salesforce や Office 365 など)、インターネット サービス (Facebook や Twitter など) 向けに用意された 50 を超える [コネクタ](../connectors/apis-list.md) から選択できます。 また、[ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [Azure Virtual Networks](web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。
* **セキュリティとコンプライアンス** - App Service は [ISO、SOC、および PCI に準拠](https://www.microsoft.com/TrustCenter/)しています。
* **アプリケーション テンプレート** - [Azure Marketplace](https://azure.microsoft.com/marketplace/) にある詳細な一覧からアプリケーション テンプレートを選択し、ウィザードを通じて、WordPress、Joomla、Drupal などの広く普及しているオープン ソース ソフトウェアをインストールできます。
* **Visual Studio の統合** - Visual Studio の専用ツールを使えば、作成、デプロイ、デバッグの作業が効率的になります。

さらに、Web アプリでは、[API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) で提供される機能 (CORS のサポートなど) と [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) で提供される機能 (プッシュ通知など) を利用できます。 App Service でのアプリの種類の詳細については、 [Azure App Service の概要](../app-service/app-service-value-prop-what-is.md)に関するページを参照してください。

App Service の Web Apps に加え、Azure では Web サイトと Web アプリケーションをホストするために利用できるサービスを他にも提供しています。 ほとんどの場合は、Web Apps が最適な方法になります。  マイクロサービス アーキテクチャ向けには、[Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) を検討してください。また、コードの実行に使用する VM をより細かく制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) の利用をご検討ください。 これらの Azure サービスから適切なサービスを選択する方法の詳細については、「[Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較](choose-web-site-cloud-service-vm.md)」を参照してください。

## <a name="getting-started"></a>使用の開始
まず App Service で新しい Web アプリにサンプル コードをデプロイするには、次のドロップダウン ボックスにあるチュートリアルのいずれかに従ってください。 無料の Azure アカウントが必要になります。

> [!div class="op_single_selector"]
> * [初めての HTML サイトを Azure に 5 分でデプロイする](app-service-web-get-started-html-cli-nodejs.md)
> * [初めての ASP.NET Web アプリを Azure に 5 分でデプロイする](app-service-web-get-started-dotnet-cli-nodejs.md)
> * [初めての PHP Web アプリを Azure に 5 分でデプロイする](app-service-web-get-started-php-cli-nodejs.md)
> * [初めての Node.js Web アプリを Azure に 5 分でデプロイする](app-service-web-get-started-nodejs-cli-nodejs.md)
> * [初めての Python Web アプリを Azure に 5 分でデプロイする](app-service-web-get-started-python-cli-nodejs.md)
> * [初めての Java Web アプリを Azure に 5 分でデプロイする](app-service-web-get-started-java.md)
> 
> 

> [!NOTE]
> Azure アカウントがなくても、[App Service を試用](https://azure.microsoft.com/try/app-service/)できます。 スターター アプリを作成し、最大&1; 時間使用できます。クレジット カードも契約も不要です。
> 
> 

