---
title: App Service の概要 - Azure | Microsoft Docs
description: Azure App Service が Web アプリケーションの開発およびホストにどのように役立つかについて説明します
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5ce8abd08f82a2a8b69cc996bfd8f7f060bb8c23
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205047"
---
# <a name="app-service-overview"></a>App Service の概要

*Azure App Service* は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするための HTTP ベースのサービスです。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。 アプリケーションの実行とスケーリングは、Windows ベースの環境と Linux ベースの環境の両方で容易に行うことができます。 Linux ベースの環境については、[App Service on Linux](containers/app-service-linux-intro.md) に関するページを参照してください。 

App Service は、セキュリティ、負荷分散、自動スケーリング、自動管理などの Microsoft Azure の機能を、アプリケーションに追加するだけではありません。 Azure DevOps、GitHub、Docker Hub およびその他のソースからの継続的デプロイ、パッケージ管理、ステージング環境、カスタム ドメイン、SSL 証明書など、DevOps 機能を利用することもできます。 

App Service では、使用した Azure コンピューティング リソースに応じて課金されます。 使用するコンピューティング リソースは、アプリが実行されている "_App Service プラン_" によって決まります。 詳細については、「[Azure App Service プランの概要](overview-hosting-plans.md)」を参照してください。

## <a name="why-use-app-service"></a>App Service を使用する理由

App Service の主な機能として、次のようなものがあります。

* **複数の言語とフレームワーク** - App Service では、ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、および Python が最高レベルでサポートされています。 また、[PowerShell などのスクリプトや実行可能ファイル](webjobs-create.md)をバックグラウンド サービスとして実行することもできます。
* **DevOps の最適化** - [継続的インテグレーションと継続的デプロイ](deploy-continuous-deployment.md)を、Azure DevOps、GitHub、BitBucket、Docker Hub、または Azure Container Registry で設定できます。 [テスト環境やステージング環境](deploy-staging-slots.md)を介して更新を反映できます。 App Service でのアプリの管理には、[Azure PowerShell](/powershell/azureps-cmdlets-docs) または[クロスプラットフォーム コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用します。
* **高可用性を備えたグローバルなスケール** - 手動または自動で[スケールアップ](web-sites-scale.md)または[スケールアウト](../monitoring-and-diagnostics/insights-how-to-scale.md)を実行できます。 Microsoft のグローバルなデータセンター インフラストラクチャのどこででもアプリをホストでき、App Service の [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) によって高可用性が保証されます。
* **SaaS プラットフォームおよびオンプレミス データへの接続** - エンタープライズ システム (SAP など)、SaaS サービス (Salesforce など)、インターネット サービス (Facebook など) 向けに用意された 50 を超える[コネクタ](../connectors/apis-list.md)から選択できます。 また、[ハイブリッド接続](app-service-hybrid-connections.md)と [Azure Virtual Networks](web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。
* **セキュリティとコンプライアンス** - App Service は [ISO、SOC、および PCI に準拠](https://www.microsoft.com/en-us/trustcenter)しています。 [Azure Active Directory](configure-authentication-provider-aad.md) またはソーシャル ログイン ([Google](configure-authentication-provider-google.md)、[Facebook](configure-authentication-provider-facebook.md)、[Twitter](configure-authentication-provider-twitter.md)、および[Microsoft](configure-authentication-provider-microsoft.md)) でユーザーを認証します。 [IP アドレス制限](app-service-ip-restrictions.md)を作成し、[サービス ID を管理](overview-managed-identity.md)します。
* **アプリケーション テンプレート** - WordPress、Joomla、Drupal など、[Azure Marketplace](https://azure.microsoft.com/marketplace/) にある詳細な一覧からアプリケーション テンプレートを選択します。
* **Visual Studio の統合** - Visual Studio の専用ツールを使えば、作成、デプロイ、デバッグの作業が効率的になります。
* **API とモバイル機能** - App Service は、RESTful API シナリオに対してターンキー CORS サポートを提供するほか、認証、オフライン データ同期、プッシュ通知などを有効にして、モバイル アプリのシナリオを簡素化します。
* **サーバーレス コード** - コード スニペットまたはスクリプトをオンデマンドで実行します。その際、インフラストラクチャを明示的にプロビジョニングまたは管理する必要はありません。また、コードによって実際に使用されたコンピューティング時間のみが課金対象となります ([Azure Functions](/azure/azure-functions/) に関するページを参照)。

App Service の他に、Azure では Web サイトと Web アプリケーションをホストするために利用できるサービスも提供しています。 ほとんどの場合は、App Service が最適な方法になります。  マイクロサービス アーキテクチャについては、[Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) を検討してください。 また、コードの実行に使用する VM をより細かく制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) の利用をご検討ください。 これらの Azure サービスから適切なサービスを選択する方法の詳細については、「[Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較](overview-compare.md)」を参照してください。

## <a name="next-steps"></a>次の手順

最初の Web アプリを作成する。

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linux の場合)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.JS](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (Linux 版)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
