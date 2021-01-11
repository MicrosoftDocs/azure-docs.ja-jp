---
title: Azure App Service Web アプリと API を保護する
description: この記事は、Azure Security Center で Azure App Service Web アプリと API の保護を開始する際に役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 35b91aab8c228fc7dced5dfe06e33f939f4634f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080796"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service Web アプリと API を保護する

Azure App Service は、インフラストラクチャを管理せず、Web アプリや API をビルドし、ホストするためのフル マネージド プラットフォームです。 エンタープライズ クラスのパフォーマンス、セキュリティ、コンプライアンス要件を満たすための管理機能、監視機能、運用に関する分析情報を提供します。 詳細については、[Azure App Service](https://azure.microsoft.com/services/app-service/)に関するページを参照してください。

Azure App Service プランの Advanced Threat Protection を有効にするには、次の作業を行う必要があります。

* Azure Security Center の Standard 価格レベルをサブスクライブします
* App Service プランを下の画像のように有効にします。 Security Center は App Service とネイティブで統合されており、デプロイやオンボーディングの必要がありません。統合は透過的となっています。
* App Service プランを専用のコンピューターに関連付けます。 サポートされているプラン:Basic、Standard、Premium、Isolated、または Linux です。 Security Center では、Free プラン、Shared プラン、Consumption プランがサポートされていません。 詳細については、[Azure App Service プラン](https://azure.microsoft.com/pricing/details/app-service/plans/)に関するページを参照してください。

App Service プランを有効にすると、Security Center では、App Service プランの対象となるリソースが評価され、その結果に基づき、セキュリティ上の推奨事項が生成されます。 Security Center では、App Service が実行されている VM インスタンスと管理インターフェイスが保護されます。 App Service で実行されているアプリとの間で送受信される要求と応答も監視されます。

Security Center では、Web 上のアプリに対する一般的な攻撃を監視する目的で、クラウドの規模と、クラウド プロバイダーとしての Azure に与えられる視界が活用されます。 Security Center では、Azure でホストされる複数のアプリケーションをスキャンし、脆弱性を特定することで、攻撃者が偵察段階でも、アプリケーションに対する攻撃を発見したり、新しい攻撃を特定したりできます。 Security Center はまた、Azure ネイティブ サービスとして、この PaaS の計算ノードをカバーするホストベースのセキュリティ分析を提供するというユニークな位置にもあります。Security Center では、既に悪用されている、Web アプリケーションに対する攻撃を検出できます。 詳細については、[Azure App Service に対する脅威保護](threat-protection.md#app-services)に関する記事を参照してください。


## <a name="enabling-monitoring-and-protection-of-app-service"></a>App Service の監視と保護を有効にする

1. Azure portal で Security Center を選択します。
2. **[Pricing & settings]\(価格と設定\)** に移動し、サブスクリプションを選択します。
3. **[価格レベル]** の **[App Service]** 行でプランを **[有効]** に切り替えます。

    [![Standard レベルのサブスクリプションでアプリ サービスを有効にする](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> **リソース数量**に一覧表示されているインスタンスの数は、このサブスクリプションのすべての App Service プランに含まれるコンピューティング リソースのうち、価格レベル ブレードを開いた時点で実行されているコンピューティング リソースの総数を表わします。
>
> Azure App Service にはさまざまなプランがあります。 App Service プランによって、Web アプリを実行するための一連のコンピューティング リソースが決まります。 そのようなリソースは、従来の Web ホスティングのサーバー ファームに相当します。 1 つまたは複数のアプリを同じコンピューティング リソース (または、同じ App Service プラン) で実行するように構成することができます。
>
>カウントを検証するには、Azure portal で "App Service プラン" に移動します。App Service プランでは、プラン別に使用されているコンピューティング リソースの数を確認できます。 






App Service の監視と推奨を無効にするには、このプロセスを繰り返し、 **[App Service]** プランを **[無効]** に切り替えます。



## <a name="see-also"></a>関連項目
この記事では、Azure Security Center での監視機能の使用方法について説明しました。 Azure Security Center の詳細については、次の記事をご覧ください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md):Azure Security Center でセキュリティ設定を構成する方法について説明します。
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
* [App Service](security-center-virtual-machine-protection.md#app-services):App Service Environment と正常性の概要を一覧で表示します。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/):Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
