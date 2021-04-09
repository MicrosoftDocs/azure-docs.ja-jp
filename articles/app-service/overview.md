---
title: 概要
description: Azure App Service が Web アプリケーションの開発およびホストにどのように役立つかについて説明します
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 668988ae34c2f97f3baca3f360c13e3ec3e30731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586351"
---
# <a name="app-service-overview"></a>App Service の概要

*Azure App Service* は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするための HTTP ベースのサービスです。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。 アプリケーションの実行とスケーリングは、Windows ベースの環境と [Linux](#app-service-on-linux) ベースの環境の両方で容易に行うことができます。

App Service は、セキュリティ、負荷分散、自動スケーリング、自動管理などの Microsoft Azure の機能を、アプリケーションに追加するだけではありません。 Azure DevOps、GitHub、Docker Hub およびその他のソースからの継続的デプロイ、パッケージ管理、ステージング環境、カスタム ドメイン、TLS/SSL 証明書など、DevOps 機能を利用することもできます。 

App Service では、使用した Azure コンピューティング リソースに応じて課金されます。 使用するコンピューティング リソースは、アプリが実行されている "_App Service プラン_" によって決まります。 詳細については、「[Azure App Service プランの概要](overview-hosting-plans.md)」を参照してください。

## <a name="why-use-app-service"></a>App Service を使用する理由

App Service の主な機能として、次のようなものがあります。

* **複数の言語とフレームワーク** - App Service では、ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、および Python が最高レベルでサポートされています。 また、[PowerShell などのスクリプトや実行可能ファイル](webjobs-create.md)をバックグラウンド サービスとして実行することもできます。
* **マネージド運用環境** - App Service が自動的に [パッチを適用し、OS と言語フレームワークを管理](overview-patch-os-runtime.md)します。 高品質なアプリの作成に専念し、プラットフォームにまつわる問題は Azure に任せましょう。
* **コンテナー化と Docker** - アプリを Docker でコンテナー化し、App Service でカスタムの Windows または Linux コンテナーをホストできます。 Docker Compose で複数コンテナー アプリを実行できます。 Docker のスキルを App Service に直接移行できます。
* **DevOps の最適化** - [継続的インテグレーションと継続的デプロイ](deploy-continuous-deployment.md)を、Azure DevOps、GitHub、BitBucket、Docker Hub、または Azure Container Registry で設定できます。 [テスト環境やステージング環境](deploy-staging-slots.md)を介して更新を反映できます。 App Service でのアプリの管理には、[Azure PowerShell](/powershell/azure/) または[クロスプラットフォーム コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用します。
* **高可用性を備えたグローバルなスケール** - 手動または自動で [スケールアップ](manage-scale-up.md)または [スケールアウト](../azure-monitor/autoscale/autoscale-get-started.md)を実行できます。 Microsoft のグローバルなデータセンター インフラストラクチャのどこででもアプリをホストでき、App Service の [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) によって高可用性が保証されます。
* **SaaS プラットフォームおよびオンプレミス データへの接続** - エンタープライズ システム (SAP など)、SaaS サービス (Salesforce など)、インターネット サービス (Facebook など) 向けに用意された 50 を超える [コネクタ](../connectors/apis-list.md)から選択できます。 また、[ハイブリッド接続](app-service-hybrid-connections.md)と [Azure Virtual Networks](web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。
* **セキュリティとコンプライアンス** - App Service は [ISO、SOC、および PCI に準拠](https://www.microsoft.com/en-us/trustcenter)しています。 [Azure Active Directory](configure-authentication-provider-aad.md)、[Google](configure-authentication-provider-google.md)、[Facebook](configure-authentication-provider-facebook.md)、[Twitter](configure-authentication-provider-twitter.md)、または [Microsoft アカウント](configure-authentication-provider-microsoft.md)でユーザーを認証します。 [IP アドレス制限](app-service-ip-restrictions.md)を作成し、[サービス ID を管理](overview-managed-identity.md)します。
* **アプリケーション テンプレート** - WordPress、Joomla、Drupal など、[Azure Marketplace](https://azure.microsoft.com/marketplace/) にある詳細な一覧からアプリケーション テンプレートを選択します。
* **Visual Studio と Visual Studio Code の統合** - Visual Studio と Visual Studio Code の専用ツールを使用することで、作成、デプロイ、デバッグの作業を効率化することができます。
* **API とモバイル機能** - App Service は、RESTful API シナリオに対してターンキー CORS サポートを提供するほか、認証、オフライン データ同期、プッシュ通知などを有効にして、モバイル アプリのシナリオを簡素化します。
* **サーバーレス コード** - コード スニペットまたはスクリプトをオンデマンドで実行します。その際、インフラストラクチャを明示的にプロビジョニングまたは管理する必要はありません。また、コードによって実際に使用されたコンピューティング時間のみが課金対象となります ([Azure Functions](../azure-functions/index.yml) に関するページを参照)。

App Service の他に、Azure では Web サイトと Web アプリケーションをホストするために利用できるサービスも提供しています。 ほとんどの場合は、App Service が最適な方法になります。  マイクロサービス アーキテクチャの場合は、[Azure Spring-Cloud サービス](../spring-cloud/index.yml)または [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) を検討してください。  また、コードの実行に使用する VM をより細かく制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) の利用を検討してください。 これらの Azure サービスから適切なサービスを選択する方法の詳細については、「[Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較](/azure/architecture/guide/technology-choices/compute-decision-tree)」を参照してください。

## <a name="app-service-on-linux"></a>App Service on Linux

App Service では、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることもできます。 また、カスタム Linux コンテナー (Web App for Containers とも呼ばれます) を実行することもできます。

### <a name="built-in-languages-and-frameworks"></a>組み込みの言語とフレームワーク

App Service on Linux では、さまざまな言語に固有の組み込みイメージがサポートされています。 コードをデプロイするだけで済みます。 以下の言語がサポートされています。Node.js、Java (JRE 8 と JRE 11)、PHP、Python、.NET Core、および Ruby。 [`az webapp list-runtimes --linux`](/cli/azure/webapp#az-webapp-list-runtimes) を実行して、最新の言語とサポートされているバージョンを表示します。 アプリケーションに必要なランタイムが組み込みイメージでサポートされていない場合は、カスタム コンテナーを使用してデプロイできます。

古くなったランタイムは、ポータルの Web Apps の [作成] および [構成] ブレードから定期的に削除されます。 これらのランタイムは、管理している組織によって非推奨とされた場合や、重大な脆弱性が見つかった場合、ポータルに表示されません。 これらの選択肢は、最もうまく動作する最新のランタイムへと顧客を導くために非表示になります。 

古くなったランタイムがポータルで非表示になっても、そのバージョンを使用している既存のどのサイトも引き続き実行されます。 ランタイムが App Service プラットフォームから完全に削除される場合、Azure サブスクリプションの所有者は、削除前に電子メール通知を受け取ります。

ポータルに表示されなくなった古いランタイム バージョンを使用して別の Web アプリを作成する必要がある場合は、自分のサイトのランタイム バージョンを取得する方法に関する手順について、言語構成ガイドを参照してください。 Azure CLI を使用すると、同じランタイムを使用して別のサイトを作成できます。 あるいは、ポータルの Web アプリ ブレードにある **[テンプレートのエクスポート]** ボタンを使用して、サイトの ARM テンプレートをエクスポートできます。 このテンプレートを再利用して、同じランタイムと構成で新しいサイトをデプロイできます。

### <a name="limitations"></a>制限事項

- App Service on Linux は、[共有](https://azure.microsoft.com/pricing/details/app-service/plans/)価格レベルではサポートされていません。 
- Windows と Linux のアプリを同じ App Service プランに混在させることはできません。  
- 従来、同じリソース グループ内に、Windows と Linux のアプリを混在させることはできませんでした。 ただし、2021 年 1 月 21 日以降に作成されたすべてのリソース グループでは、このシナリオがサポートさえます。 2021 年 1 月 21 日より前に作成されたリソース グループについては、混在するプラットフォーム デプロイを追加する機能は、近日中に Azure リージョン (国内クラウド リージョンを含む) 全体にロールアウトされます。
- Azure portal では、Linux アプリで現在動作している機能のみが表示されます。 機能が有効になると、ポータルでアクティブになります。
- 組み込みイメージにデプロイされているコードとコンテンツには、Azure Storage によってサポートされる、Web コンテンツ用のストレージ ボリュームが割り当てられます。 このボリュームのディスク待ち時間は、コンテナー ファイルシステムの待ち時間よりも長く、可変です。 コンテンツ ファイルに対する高負荷の読み取り専用アクセスが必要なアプリでは、コンテンツ ボリュームではなく、コンテナー ファイルシステムにファイルを配置するカスタム コンテナー オプションを使用した方が有益な場合があります。

## <a name="next-steps"></a>次のステップ

最初の Web アプリを作成する。

> [!div class="nextstepaction"]
> [ASP.NET Core (Windows または Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET (Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP (Windows または Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linux の場合)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js (Windows または Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java (Windows または Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python (Linux 版)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML (Windows または Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [カスタム コンテナー (Windows または Linux)](tutorial-custom-container.md)
